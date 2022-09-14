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

# 기존 ID/PASSWORD 인증방식의 문제점
누구나 한번쯤 계정을 도용당해 온라인 커뮤니티에 본인도 모르는 홍보글이 도배되 있거나, 게임 계정을 해킹당해 피해를 입어본 경험이 있을것이다.  
또한 대부분의 사람들은 각 서비스별로 서로 다른 아이디와 비밀번호를 사용하는 것은 매우 귀찮기 때문에, 여러 서비스에서 동일한 아이디와 비밀번호를 이용한다.    
그리고 결국 하나의 서비스에서 계정을 도용 당하게되면, 다른 서비스의 계정 또한 도용 당해 피해가 더욱 커지는 사태가 발생하곤 한다.  


## Two-Factor Authentication(2FA)
서비스를 제공하는 회사들또한 이러한 문제를 인지하고 있기 때문에, 회사들은 사용자들에게 별도의 인증(SMS OTP, etc)을 한번 더 거치는 **`2FA`** 방식을 권장하고 있다.  
게임사의 경우 OTP를 이용하지 않는 사용자의 해킹피해는 복구해주지 않거나, OTP를 등록한 사용자에게 보상을 제공하는 식으로 사용자들이 2FA 인증방식을 사용하도록 유도하고 있다.  
하지만 그럼에도 불구하고 여전히 해킹에 의한 피해는 계속해서 발생하고 있으며, 일부 2FA 인증방식은 **[페이스북의 SMS 2FA 인증오류](https://www.okta.com/blog/2019/02/phone-numbers-as-identifiers-the-problem-with-sms-based-authentication/)** 에서 알 수 있듯이 
생각보다 안전하지 않다.

# **PASSWORD-FREE Authentication**
결국 모든 문제의 근원은 **`비밀번호`** 에 있다. 
  
> ***사실 사용자의 부주의로 인한 비밀번호 유출, 예측하기 쉬운 간단한 비밀번호 설정과 같은 사용자 부주의가 주된 원인이지만...*** 


## **FIDO?**  
> **[FIDO Alliance](https://fidoalliance.org/what-is-fido/)**
  1. 80%의 데이터 탈취는 비밀번호 인증방식에 의해 발생한다.
  2. 사용자는 평균적으로 90개의 서비스에 등록되어져 있다.
  3. 절반 이상의 비밀번호가 재사용된다.
  4. 비밀번호를 **까먹어서** 33%의 온라인 거래가 결제도중 중단된다.

때문에 이런 만악의 근원인 **비밀번호**를 사용하지 않으면서, 사용자들이 간편하게 사용할 수 있는 인증 프로세스가 필요했고 **`FIDO`** 는 이런 시장의 요구에 대한 대답이다.

**FIDO** (**F**ast **ID**entity **O**nline)는 공개키 암호화를 이용한 인증 체계를 이용한다. FIDO Authenticator 와 FIDO Server 로 구성되며 


### **FIDO 1.0 & FIDO 2.0**
FIDO 에는 UAF, U2F, FIDO2 라는 세 가지 표준이 공존하고 있다. 
* UAF  
  모바일 앱(Android, iOS)에서 패스워드 없이 생체정보 또는 PIN 같은 인증 수단으로 사용자가 인증할 수 있도록 만든 표준. 유스 케이스가 모바일 앱에 한정되어 있다.  
* U2F  
  
* FIDO2  
  

### Web Authentication (WebAuthn) 
2019년 3월 `W3C`는 `WebAuthn`을 `password-free` 인증방식의 표준으로 발표했습니다.     




## Reference  
### **[WebAuthn4j-spring-security Reference](https://webauthn4j.github.io/webauthn4j-spring-security/en/)**
### **[FIDO at LINE: 패스워드 없는 세상으로의 첫 발걸음](https://engineering.linecorp.com/ko/blog/fido-at-line/)**
### **[SMS를 이용한 2FA 인증방식의 실패사례](https://www.okta.com/blog/2019/02/phone-numbers-as-identifiers-the-problem-with-sms-based-authentication/)**