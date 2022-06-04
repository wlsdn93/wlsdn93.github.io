---
title : "SSL인증서 갱신하기"
excerpt : "SSL 인증서 갱신을 위해 다시 구글링하며 고민하고 있을 나를 위한 가이드"
author_profile: true
sidebar:
  nav: "sidebar"
category: OnlyForMe
---

미래에 또 다시 구글링을 하고 있을 나를 위해 이 포스트를 작성한다.   

## **프론트 인증서 갱신**

이전에 이미 **`/etc/nginx/site-available/`** 경로에 위치한 **`default`** 파일에 아래와 같이 key 경로를 입력해두었기 때문에  
```
server {
    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/freemath.online/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/freemath.online/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}
```
아래 명령어만 입력하면 인증서를 아주 쉽게 갱신할 수 있다.  

```shell
sudo certbot renew
```
이게 끝이다. 위 명령은 인증서의 유효기간을 확인하고, 기존 인증서 위치에 새로운 인증서를 발급해준다. 
하지만 너무 아쉽게도, 여기까지만 하면 서버와 통신이 되지 않는다... 

## **서버 인증서 갱신**   
스프링부트 서버에 인증서를 등록하기 위해선 **`pkcs#12`** 라는 공개키 암호 표준을 만족하는 **`keystore.p12`** 파일을 업로드하고, 이를 환경설정에 등록해주어야 한다.  
**`certbot`** 을 통해 발급받은 인증서를 이용해 **`keystore.p12`** 파일을 생성하기 위해, **`pem`** 키들이 위치한 디렉토리로 이동해 아래의 명령어를 입력한다.  
e.g. 나는 **`/etc/letsencrypt/live/freemath.online`** 경로로 이동했다.   

**주의! [name], [password]는 직접 입력하는 값이다.**   
```shell
sudo openssl pkcs12 -export -in fullchain.pem -inkey privkey.pem -out keystore.p12 -name [name] -CAfile chain.pem -caname root -password pass: [password]
```
나는 어차피 하나의 프로젝트만 관리하고 있기 때문에 굳이 **`alias`** 를 설정하진 않았다. **`keystore.p12`** 파일을 **`~/[project_path]/src/main/resources/`** 경로로 이동시켜준뒤, 권한을 수정해주자

```shell
sudo chmod a=r ./keystore.p12
```

이제 기존 서버를 죽인 뒤, 다시 빌드 해주고 재 실행 해주면 끝이다.   

참고로 **`applicaiton.yml`** 은 아래와 같이 설정 되있어야 한다. 

```yaml
server:
  port: # [port]
  ssl:
    key-store: classpath:keystore.p12
    key-store-type: PKCS12
    key-store-password: # keystore.p12 파일을 생성할 때 입력한 [password]
```