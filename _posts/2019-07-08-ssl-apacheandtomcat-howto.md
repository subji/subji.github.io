---
title: "Apache + Tomcat 연동 및 SSL 적용"
date: 2019-07-08
layout: single
classes: wide
categories: posts
tags: ["ssl", "tomcat", "apache"]
---

첫번째로 apache 와 tomcat 을 연동하기 위해선 jk_mod 라는 아파치 플러그인을 설치해야 한다.

```bash
sudo apt install libapache2-mod-jk
```
그 다음 jk_mod 가 설치된 디렉토리로 이동하여 파일 하나를 수정한다.
```bash
cd /etc/libapache2-mod-jk

vim worker.properties

# worker.properties
worker.list="{사용자 정의 모듈 이름}" ex) ajp13_worker

worker."{사용자 정의 모듈 이름}".port="포트 번호 (Port Number)"
worker."{사용자 정의 모듈 이름}".host="아이피 주소 (IP Address)"
worker."{사용자 정의 모듈 이름}".type="ajp13"
worker."{사용자 정의 모듈 이름}".type

worker."{사용자 정의 모듈 이름}".lbfactor=1
```

다 작성했으면 아파치가 설치된 폴더로 이동하자
```bash
# ubuntu
cd /etc/apache2
```
첫번째로 위에서 작성한 worker.properties 파일의 경로를 확인한다.
```bash
vim mod-availables/jk.conf

# jk.conf

JkWorkerFile /etc/libapache2-mod-jk/workers.properties 
```
위에서는 기본적으로 jk_mod 에서 설치된 프로퍼티 파일경로 되어있지만 다른 경로에 추가하였을 경우 해당 경로로 변경해야 한다.

다음으로 sites-available 에 가상 호스트를 추가한다.
여기에서는 톰캣과의 연동과 SSL 인증서를 추가한다.
```bash
vim sites-available/"{xxx}".conf

# "{xxx}".conf
```
```xml
<IfModule mod_ssl.c>
    <VirtualHost _default_:443>
    ServerName "{도메인 네임 (Domain name)}"
    DocumentRoot "{톰캣의 ROOT 디렉토리 경로를 설정한다 (Tomcat Directory path)}"

    JkMount /* ajp13_worker <!-- Apache + Tomcat 연동 -->

    SSLEngine on <!-- SSL 연동 -->

    SSLProtocol all -SSLv2 -SSLv3
    SSLCipherSuite ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128:DHES-RSA-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHAAES256-SHA:AES:CAMESIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
    SSLHonorCipherOrder on
    <!-- SSL 인증서 및 키 파일 설정 -->
    SSLCertificateFile "{xxx}".crt
    SSLCertificateKeyFile "{xxx}".key
    SSLCertificateChainFile "{xxx}".pem
    SSLCACertificateFile "{xxx}".crt
    <!-- default-ssl.conf 기본 설정 -->
    SSLOptions +FakeBasicAuth +ExportCertData +StrictRequire

    <FilesMatch "\.(cgi|shtml|phtml|php)">
        SSLOptions +StdEnvVars
    </FilesMatch>
    <Directory /usr/lib/cgi-bin>
        SSLOptions +StdEnvVars
    </Directory>
    <!-- 톰캣 디렉토리에 대한 다음과 같은 설정을 해야 SSL 이 적용된다. -->
    <Directory "{Tomcat ROOT Directory path}">
        Options Indexes FollowSymLinks
        AllowOverride None
        Order allow,deny
        Allow from all
        Require all granted
    </Directory>
</VirtualHost>
```
마지막으로 톰캣 디렉토리에서 server.xml 을 수정한다.
```bash
vim conf/server.xml

#server.xml
```
```xml
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" /> <!-- 주석 제거 -->
```

이제 아파치와 톰캣으로 재시작 하면 적용된다.
```bash
# restart apache
sudo systemctl restart apache2
# restart tomcat
sh shutdown.sh
sh startup.sh
```

마지막으로 http 로 오는 접속을 https 로 리다이렉트해주는 방법이다.
```bash
# "{user defined configuration file in apache sites-available}".conf
<VirtualHost *:80>
    ServerName "{Domain name}"
    ServerAlias "{Domain name alias}"
    ProxyPass / "{url}"
    ProxyPassReverse / "{reverse url}"
    # HTTP -> HTTPS Redirect
    RewriteEngine On
    RewriteCond %{SERVER_PORT} !443 # '%{HTTPS} off' is not work
    RewriteRule ^(/(.*))?$ https://%{HTTP_HOST}/$1 [R=301,L] # ^(/(.*))?$ = all url patterns

    SetOutputFilter DEFLATE
    SetEnvIfNoCase Request_URI "\.(?:gif|jpe?g|png)$" no-gzip
</VirtualHost>