
# 배포 자동화 환경 구축

### ubuntu 운영체제 한글 인코딩 이슈 해결
---
```sh
sudo locale-gen ko_KR.EUC-KR ko_KR.UTF-8
sudo dpkg-reconfigure locales
```

### 각 계정별 인코딩 설정
---
.bash_profile

```sh
LANG="ko_KR.UTF-8"
LANGUAGE="ko_KR:ko:en_US:en"
```

설정 확인은 `env` 명령어를 통해 확인한다.

### jdk 설정
---
oracle 에서 라이센스 승인 후 download url 복사.
`ubuntu wget jdk install` 으로 검색 하여 찾아보자.

```
wget --header "Cookie: oraclelicense=accept-securebackup-cookie" [download url]
```

java 설정을 symbolic link로 관리하면,
환경설정 파일을 변경하지 않고, symbolic link 변경만 하면 되므로, 편리하게 사용 할 수 있다.

```script
ln -s [java 설치 directory] [symbolicname]
```

.bash_profile 에 java path 설정
```script
export JAVA_HOME=[symbolic 설정 경로]
export PATH=$PATH:$JAVA_HOME/bin
```

### maven 설정
---
maven 설정 역시 jdk 설정과 같다.

### git 설정
---
```script
sudo apt-get -y install git
```

### 배포할 source directory 생성
---
```
mkdir repositories
cd repositories
git clone [url]
cd [app-dir]
mvn clean package

java -cp target/classes:target/dependency/* a.b.Main $PORT &
```


```
git pull
mvn clean package

ps -ef | grep ???
kill -9 [pid]
java -cp target/classes:target/dependency/* a.b.Main $PORT &
```

### tomcat 설정
---
jdk, maven 설정과 같다.

server.xml 에서 ajp 와 같은 connector 는 
apache or nginx 웹서버와 연동시 사용하는 부분인데,
만약 http 로 연동을 한다면 불필요하므로, 주석 처리 하도록 한다.

tomcat webapps 에서 사용하지 않는 app 은 모두 삭제.


### sample code and deploy
---

[source](https://github.com/slipp/jwp-basic) step2-user-with-mvc-framework branch 사용

```
git checkout -b [branch name] origin/[remote branch name]

git branch -vv

mvn -DskipTests clean package

mvn -Dmaven.test.skip=true clean package
```

### 쉘 스크립트를 활용한 배포 자동화 #1
---

scripts directory 생성 후 app 별 deploy sh script 작성

```
#!/bin/bash

REPOSITORY_DIR=~/repositories/app
TOMCAT_DIR=~/tomcat

cd $REPOSITORY_DIR
pwd

git pull

mvn -DskipTests clean package

$TOMCAT_DIR/bin/shutdown.sh

rm -rf $TOMCAT_DIR/webapps/ROOT
mv $REPOSITORY_DIR/target/app $TOMCAT_DIR/webapps/ROOT

$TOMCAT_DIR/bin/startup.sh

tail -500f $TOMCAT_DIR/logs/catalina.out
```

### 쉘 스크립트를 활용한 배포 자동화 #2
---

배포된 것이 장애 발생 했을 경우 어떻게 rollback 할 것인가?
배포 목록 관리를 위한 배포 경로를 만든다.

~/releases/app/[시간별 direcoty] 로 구성

`deploy.sh`
```
#!/bin/bash

REPOSITORY_DIR=~/repositories/app
TOMCAT_DIR=~/tomcat
RELEASE_DIR=~/releases/app

cd $REPOSITORY_DIR
pwd

git pull

mvn -DskipTests clean package

#### 
C_TIME=$(date +%s)
echo "time  : $C_TIME"
mv $REPOSITORY_DIR/target/app $RELEASE_DIR/$C_TIME

$TOMCAT_DIR/bin/shutdown.sh

rm -rf $TOMCAT_DIR/webapps/ROOT

ln -s $RELEASE_DIR/$C_TIME $TOMCAT_DIR/webapps/ROOT


$TOMCAT_DIR/bin/startup.sh

tail -500f $TOMCAT_DIR/logs/catalina.out
```


> shell script find all directory sort date

`rollback.sh`
```
#!/bin/bash

RELEASE_DIR=~/releases/app

RELEASES=$(ls -1t $RELEASE_DIR)
echo "$RELEASES"

// newline 을 기준으로 배열을 만든다.
// shell script split string to array
REVISIONS = ( ${RELEASES//\n/} )
echo "rollback revision dir : ${REVISIONS[1]}"

// 최근 배포된 것은 잘못 된 것이기 때문에 삭제
rm -rf $RELEASE_DIR/${REVISIONS[0]}

// 배포
$TOMCAT_DIR/bin/shutdown.sh

rm -rf $TOMCAT_DIR/webapps/ROOT
ln -s $RELEASE_DIR/${REVISIONS[1]} $TOMCAT_DIR/webapps/ROOT


$TOMCAT_DIR/bin/startup.sh

tail -500f $TOMCAT_DIR/logs/catalina.out
```