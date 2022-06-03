---
title : "SSL인증서 갱신하기"
excerpt : "Nginx + Vue + Springboot 프로젝트 SSL인증서 갱신을 위한 삽질이야기"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: LifeStory
---

```shell
certbot renew
```

위 명령을 입력 해주면 너무나 간편하게 기존 pem 키들을 갱신해준다.
하지만 이 pem 키들은 프론트의 nginx에서 사용하기 위한 키들이고...
우리의 스프링부트 서버에는 pkcs#12 파일을 업로드하고, 등록해주어야 한다.

pem 키들이 위치한 디렉토리 /etc/letsencrypt/live/freemath/online 으로 이동한 뒤 

```shell
sudo openssl pkcs12 -export \
	-in fullchain.pem \
	-inkey privkey.pem \
	-out keystore.p12 \
	-name [name] \
	-CAfile chain.pem \
	-caname root \
	-password pass: [password]
```
를 쳐주면 `keystore.p12` 파일이 현재 위치에 생성된다.

이 파일을 

~/math_web/src/main/resources/ 경로로 이동시켜준뒤 

```shell
sudo chmod a=r ./keystore.p12
```

를 통해 읽을 수 있도록 변경...

`applicaiton.yml` 에도 keystore.p12 파일을 생성할 때 만든 비밀번호 넣어줘야함

```yaml

```