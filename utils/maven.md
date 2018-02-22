# NEXUS 설치

[CentOS Nexus 설치](https://zetawiki.com/wiki/CentOS_Nexus_%EC%84%A4%EC%B9%98)

[저장소와 권한 관리](https://www.lesstif.com/pages/viewpage.action?pageId=20775153)

[사설 Repository Nexus 설치 / 연동](https://gs.saro.me/#!m=elec&jn=774)

[nexus3 설치!!!](http://kingbbode.tistory.com/37)

```sh
[root@hello ~]# useradd nexus
[root@hello ~]# su - nexus

[nexus@hello ~]# wget http://www.sonatype.org/downloads/nexus-latest-bundle.tar.gz --no-check-certificate
[nexus@hello ~]# tar zxvf nexus-2.14.0-01-bundle.tar.gz

[root@hello ~]# cp /nexus/nexus-2.14.0-01/bin/nexus /etc/init.d/nexus
[root@hello ~]# vi /etc/init.d/nexus
```

```vi
#NEXUS_HOME=".."
NEXUS_HOME="/home/nexus/nexus-2.14.0-01"

#RUN_AS_USER=
RUN_AS_USER=nexus

#PIDDIR="."
PIDDIR="/home/nexus"
```

```sh
[root@hello ~]# service nexus start
```

http://localhost:8081/nexus

## custom maven repository 등록

pom.xml에 repository 등록 없이 사용하기 위해서는 아래처럼 수행.

${사용자계정}/.m2/settings.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
	<mirrors>
		<mirror>
			<id>적당한아이디</id>
			<name>적당한이름</name>
			<url>http://localhost:8081/nexus/content/groups/public/</url>
			<mirrorOf>*</mirrorOf>
		</mirror>
	</mirrors>
</settings>
```