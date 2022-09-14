---
title : "WebAuthn & FIDO"
excerpt : "패스워드를 사용하지 않는 인증 프로세스의 표준"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: Spring
---
   
---
  
# 기존 ID/PASSWORD 인증방식의 문제점
누구나 한번쯤 계정을 도용당해 온라인 커뮤니티에 본인도 모르는 홍보글이 도배되 있거나, 게임 계정을 해킹당해 피해를 입어본 경험이 있을것이다.  
  
또한 대부분의 사람들은 각 서비스별로 서로 다른 아이디와 비밀번호를 사용하는 것은 매우 귀찮기 때문에, 여러 서비스에서 동일한 아이디와 비밀번호를 이용한다.    
  
그리고 결국 하나의 서비스에서 계정을 도용 당하게되면, 다른 서비스의 계정 또한 도용 당해 피해가 더욱 커지는 사태가 발생하곤 한다.  

> **[비밀번호 인증방식의 문제](https://fidoalliance.org/what-is-fido/)**
  1. 80%의 데이터 탈취는 비밀번호 인증방식에 의해 발생한다.
  2. 사용자는 평균적으로 90개의 서비스에 등록되어져 있다.
  3. 절반 이상의 비밀번호가 재사용된다.
  4. 비밀번호를 **까먹어서** 33%의 온라인 거래가 결제도중 중단된다.


## Two-Factor Authentication(2FA)
서비스를 제공하는 회사들또한 이러한 문제를 인지하고 있기 때문에, 회사들은 사용자들에게 별도의 인증(SMS OTP, etc)을 한번 더 거치는 **`2FA`** 방식을 권장하고 있다.  
  
게임사의 경우 OTP를 이용하지 않는 사용자의 해킹피해는 복구해주지 않거나, OTP를 등록한 사용자에게 보상을 제공하는 식으로 사용자들이 2FA 인증방식을 사용하도록 유도하고 있다.  
  
하지만 그럼에도 불구하고 여전히 해킹에 의한 피해는 계속해서 발생하고 있으며, 일부 2FA 인증방식은 **[페이스북의 SMS 2FA 인증오류](https://www.okta.com/blog/2019/02/phone-numbers-as-identifiers-the-problem-with-sms-based-authentication/)** 에서 알 수 있듯이 
생각보다 안전하지 않다.
  
---
  
# **PASSWORD-FREE Authentication**
결국 모든 문제의 근원은 **비밀번호** 에 있다. 
  
> **사실 사용자의 부주의로 인한 비밀번호 유출, 예측하기 쉬운 간단한 비밀번호 설정과 같은 사용자 부주의가 주된 원인이지만...** 


## **FIDO** (Fast IDentity Online)  
  
FIDO(파이도 라고 읽는다)는 FIDO Alliance가 정의한 인증 방식의 규격(Specification)이다.

> **FIDO Specification**   
>     
> FIDO 에는 **`UAF`**, **`U2F`**, **`FIDO2`** 라는 세 가지 표준이 공존하고 있다. 
* UAF(Universal Authentication Framework)  
  모바일 앱(Android, iOS)에서 생체정보 또는 PIN 등의 인증 수단을 이용한 인증    
* U2F(Universal 2nd Factor)  
  모바일 앱(Android, iOS)에서 아이디/비밀번호 방식과 함께 별도의 2차 외부 인증장치를 사용하는 이중 인증
* FIDO2  
  기존에 모바일 앱에서만 사용 가능했던 `UAF`와 `U2F`를 웹으로 확장

`FIDO` 규격을 따르는 **인증 장치**와 **인증 서버**는 상호 운용이 가능하기 때문에 사용자는 본인의 필요에 따라 인증장치를 선택 할 수 있다.

## Web Authentication (WebAuthn) 
`WebAuthn`는 `FIDO2 Specification`의 핵심 요소이자, 웹에서 비밀번호에 의존하지 않는 인증 방식의 표준이다.





# Reference
### **[FIDO at LINE: 패스워드 없는 세상으로의 첫 발걸음](https://engineering.linecorp.com/ko/blog/fido-at-line/)**