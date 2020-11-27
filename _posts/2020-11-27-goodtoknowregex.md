---
title: "Apache 설정 (with SSL)"
date: 2020-11-27
categories: posts
tags: ["apache", "httpd"]
---

### **아파치(httpd) 설정**
---
- Ubuntu 에서는 /etc/apache2 에 있고 CentOS 같은 Redhat 계열은 /etc/httpd 아래에 존재한다.
- 가상 호스트(Virtual Host) 별 Config 파일을 sites-availiable 밑에 생성하고, sites-enabled 에 소프트 링크를 추가하였다.

### **httpd.conf**
---
```sh
# virtual host setting
IncludeOptional sites-enabled/*.conf
```

### **가상호스트 SSL 설정**
---
- mod_ssl 을 반드시 설치하고 활성화 해야한다.
- ***checking mobile device*** 는 모바일 기기로 접속하였을 때, 다른 도메인으로 이동시키기 위한 설정이다.
  - 모바일 기기 리다이렉팅은 조금 고생했는데. virtualhost:80 번에서 모바일 기기를 검사하는 RewriteCond,Rule 설정을 추가해도 계속 같은 페이지만 불러오고 모바일 페이지로는 이동이 되지 않았다.
  - 곰곰히 생각해보니 앞에서 http 에서 https 로 보내는 설정이 있었다. 그렇다면 모바일 기기 체크는 http 가 아니라 https 에서 해야되는게 아닌가? 
  - 그래서 80 포트에 작성한 설정을 443 번으로 옮겼다.
  - 결과는 성공했다. 다만 무한 리다이렉트라는 또다른 문제가 발생했다.
  - 그래서 R=301 설정으로 강제 리다이렉팅하고, L 로 rule 을 끝내버렸다. NE 는 이스케이프 문자를 허용하지 않는 조건이다.(여기서는 크게 상관없는듯하다).
- ***Redirect http to https*** 는 http 로 접속했을 때, 강제로 https 로 리다이렉트하는 설정이다.
- example.conf
```sh
<IfModule mod_ssl.c>
        <VirtualHost {domain name(DNS)}:443>
                ServerName {domain name(DNS) include "www"}
                ServerAlias {domain name(DNS)}
                DocumentRoot {web application path}

                SSLEngine on
                SSLProtocol All -SSLv2 -SSLv3
                SSLCipherSuite ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128:DHES-RSA-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHAAES256-SHA:AES:CAMESIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
                SSLHonorCipherOrder on
								# case of Setigo RSA
                SSLCertificateFile {SSL file path}/_wildcard_{...}.crt
                SSLCertificateKeyFile {SSL file path}/_wildcard_{...}.key
                SSLCertificateChainFile {SSL file path}/ChainCA/rsa-dv.chain-bundle.pem
                SSLCACertificateFile {SSL file path}/ChainCA/AddTrustExternalCARoot.crt

                SSLOptions +FakeBasicAuth +ExportCertData +StrictRequire

                ProxyPass / http://127.0.0.1:{PORT}/ connectiontimeout=900 timeout=900
                ProxyPassReverse / http://127.0.0.1:{PORT}/
                ProxyPreserveHost On

                RequestHeader set X-Forwarded-Proto "https"
                RequestHeader set X-Forwarded-Port "443"

                # CORS Config
                Header always set Access-Control-Allow-Origin "*"
					
                ErrorDocument 503 {error html file path}
								# Checking Mobile device
                RewriteEngine On
                RewriteCond %{QUERY_STRING} !^desktop
                RewriteCond %{HTTP_USER_AGENT} "android|blackberry|googlebot-mobile|iemobile|ipad|iphone|ipod|#opera mobile|palmos|webos" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "acs|alav|alca|amoi|audi|aste|avan|benq|bird|blac|blaz|brew|cell|cldc|cmd-" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "dang|doco|eric|hipt|inno|ipaq|java|jigs|kddi|keji|leno|lg-c|lg-d|lg-g|lge-" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT}  "maui|maxo|midp|mits|mmef|mobi|mot-|moto|mwbp|nec-|newt|noki|opwv" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "palm|pana|pant|pdxg|phil|play|pluc|port|prox|qtek|qwap|sage|sams|sany" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "sch-|sec-|send|seri|sgh-|shar|sie-|siem|smal|smar|sony|sph-|symb|t-mo" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "teli|tim-|tosh|tsm-|upg1|upsi|vk-v|voda|w3cs|wap-|wapa|wapi" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "wapp|wapr|webc|winw|winw|xda|xda-" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "up.browser|up.link|windowssce|iemobile|mini|mmp" [NC,OR]
                RewriteCond %{HTTP_USER_AGENT} "symbian|midp|wap|phone|pocket|mobile|pda|psp" [NC]
                RewriteRule ^(.*)$ https://m.%{HTTP_HOST}$1 [R=301,NE,L]

                <Directory "{web application path}">
                        Options none
                        AllowOverride none
                        Order allow,deny
                        Allow from all
                        Require all granted
                </Directory>
				</VirtualHost>
        <VirtualHost *:80>
                ServerName {domain name(DNS) include "www"}
                ServerAlias {domain name(DNS)}
                DocumentRoot {web application path}

                Header always set Access-Control-Allow-Origin "*"
								# Redirect http to https
                RewriteEngine On
                RewriteCond %{HTTPS} !=on [NC]
                RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R,L]

                ProxyPreserveHost On

               <Directory "{web application path}">
                        Options none
                        AllowOverride none
                        Order allow,deny
                        Allow from all
                        Require all granted
                </Directory>
        </VirtualHost>
</IfModule>
```


## **참고**
---
- [Apache2.4 - mod_rewrite Reference](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)
- [mod_rewrite 로 URL 다루기](https://www.lesstif.com/linux-infra-book/mod_rewrite-url-18219493.html)
- [Using multiple rewrite rules?](https://stackoverflow.com/questions/12155991/using-multiple-rewrite-rules/12156027)
- [https://stackoverflow.com/questions/3680463/mobile-redirect-using-htaccess](https://stackoverflow.com/questions/3680463/mobile-redirect-using-htaccess)