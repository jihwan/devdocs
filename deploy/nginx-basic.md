
# nginx 

### [설치](http://www.shako.net/blog/installing-lemp-stack-on-ubuntu-server-14-04/)

nginx home directory => /etc/nginx


추가 설정 관리가 용이하다.

nginx.conf
```
include /etc/nginx/conf.d/*.conf;
// nginx내 각 서비스별 설정을 아래 경로에 할 수 있다.
// 디렉토리가 없다면 만들어야 하고, 아래 내용 역시 없다면 추가해야 함
include /etc/nginx/sites-enabled/*; 
```
- sites-enabled
  - default -> /etc/nginx/sites-available/default
- sites-available
  - default


```
server {
  listen 80 default_server;
  listen [::]:80 default_server;

  root /var/www/html;
  index index.html index.htm index.nginx-debian.html;
  server_name _;

  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

### nginx - tomcat 연동

- [nginx 성능 최적화](http://blog.naver.com/parkjy76/220883903567)
- https://brunch.co.kr/@alden/11
- https://brunch.co.kr/@alden/17

`ubuntu nginx tomcat 연동` 키워드로 검색

sudo vi /etc/nginx/sites-available/app-basic

```
upstrem tomcat {
  server 127.0.0.1:8080;
}

server {
  location / {
    proxy_redirect    off;
    proxy_set_header  Host                $host;
    proxy_set_header  X-Real-IP           $remote_addr;
    proxy_set_header  X-Forwarded-For     $proxy_add_x_forwarded_for; # $remote_addr;
    # proxy_set_header  X-Forwarded-Proto   $scheme;
    # proxy_set_header  X-NginX-Proxy       true;
    
    proxy_pass http://tomcat;
    charset utf-8;

    # index index.jsp;

    if ($request_filename ~* ^.*?/([^/]*?)$) {
      set $filename $1;
    }

    if ($filename ~* ^.*?\.(eot)|(ttf)|(woff)$) {
      add_header Access-Control-Allow-Origin *;
    }
  }
}
```

- `sites-enabled 디렉토리에서 symbolic link를 재 설정한다.`
- `sudo nginx -s reload`


```centOS
vi /etc/sysconfig/iptables
-A INPUT-m state --state NEW -m tcp -p tcp --dport 80  -j ACCEPT
service iptables restart
```

### nginx 점검 페이지

[점검 페이지 template](https://graygrids.com/item/level-responsive-coming-soon-template/)

점검 페이지 역시 repositories 에 둔다.


cd /etc/nginx/sites-available
sudo cp default app-basic-pm
vi app-basic-pm

root 에 설정된 경로를 점검페이지 경로로 변경한다.

sudo rm -rf app-basic-pm
ln -s ../sites-available/app-basic-pm app-basic
sudo nginx -s reload

### 쉘 스크립트 enhance
---

`deploy.sh`
```
#!/bin/bash

REPOSITORY_DIR=~/repositories/app
TOMCAT_DIR=~/tomcat
RELEASE_DIR=~/releases/app
NGINX_HOME=/etc/nginx


### 점검페이지 변경
cd $NGINX_HOME/sites-enabled
sudo rm -rf app-basic
ln -s ../sites-available/app-basic-pm app-basic
sudo nginx -s reload


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

정상 여부 확인!!! => http://localhost:8080

`deply-finish.sh`
```
#!/bin/bash
### tomcat 페이지로 변경
NGINX_HOME=/etc/nginx


cd $NGINX_HOME/sites-enabled
sudo rm -rf app-basic
ln -s ../sites-available/app-basic app-basic
sudo nginx -s reload
```

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