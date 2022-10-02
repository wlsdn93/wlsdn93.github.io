---
title : "[FIDO2] 비밀번호는 만악의 근원이다"
excerpt : "비밀번호를 사용하지 않는 인증 프로세스의 표준"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---
   
현재 제가 재직중인 회사는 신원정보 기반 암호화를 이용한 인증 솔루션을 개발하고 있습니다.  
  
물론 저는 암호학에 대해서는 전혀 아는바가 없기 때문에, 솔루션 개발이 아닌, 솔루션을 활용한 서비스 개발이 주된 업무입니다.  
  
하지만 회사 특성상 자주 언급되는 주제들이 있는데, 그 중 하나가 **파이도(FIDO)** 입니다.   
  
개인적인 호기심에 검색을 하다보니, 비밀번호에 의존하지 않고 인증을 수행하는 새로운 웹 표준인 **WebAuthn** 이라는 키워드를 알게되었고, 흥미를 느껴 공부를 해보려 합니다.    
  
학습목표 : 웹 서비스에서 지문인식을 통한 인증 프로세스 구현   
  
예상기간 : 2022-09-14 ~ 2022-09-25

---
  
# 기존 ID/PASSWORD 인증방식의 문제점
 
> **[비밀번호 인증방식의 문제](https://fidoalliance.org/what-is-fido/)**
  1. 80%의 데이터 탈취는 비밀번호 인증방식에 의해 발생한다.
  2. 사용자는 평균적으로 90개의 서비스에 등록되어져 있다.
  3. 절반 이상의 비밀번호가 재사용된다.
  4. 비밀번호를 **까먹어서** 33%의 온라인 거래가 결제 도중 중단된다.

비밀번호를 사용한 인증 방식이 안전하지 않다는 사실을 우리는 직접적으로든 혹은 간접적으로든 경험을 통해 알고있습니다.  
  
> 정상(?) 사용자의 계정이 도용 당해 온라인 커뮤니티에 홍보글을 도배하는 모습은 매우 흔히 볼 수 있었다.     
> 필자는 오랜만에 접속한 메이플스토리에서 케릭터의 모든 장비가 벗겨져 있는 참혹한 경험을 직접 겪기도 하였다 :/ 
  

## Two-Factor Authentication(2FA)
서비스 제공자들 또한 비밀번호를 이용한 인증의 취약성 문제를 인지하고 있기 때문에, 별도의 인증(SMS OTP, etc)을 한번 더 거치는 **2FA** 방식을 사용자들에게 권장하고 있습니다.  
  
일례로 대다수의 게임사들은 OTP를 이용하지 않는 사용자의 해킹 피해는 복구해주지 않거나, OTP를 등록한 사용자에게 보상을 제공하는 방식으로 사용자들이 2FA 인증방식을 사용하도록 유도하고 있습니다.  
  
2FA를 적용하고 안하고는 분명 유의미한 차이가 있지만, 그럼에도 불구하고 여전히 해킹에 의한 피해는 계속해서 발생하고 있으며 일부 2FA 인증방식은 **[페이스북의 SMS 2FA 인증오류](https://www.okta.com/blog/2019/02/phone-numbers-as-identifiers-the-problem-with-sms-based-authentication/)** 에서 알 수 있듯이 
생각보다 안전하지 않습니다.
  
---
  
# **PASSWORD-FREE Authentication**
결국 모든 문제의 근원은 **비밀번호를 사용한 인증**에 있다. 
  
> 사실 사용자의 부주의로 인한 비밀번호 유출, 예측하기 쉬운 간단한 비밀번호 설정과 같은 **사용자 부주의**가 주된 원인이지만...  
> Human Error는 어디에서나 발생하기 때문에, 근본적인 해결을 위해서는 클라이언트에서 서버로 비밀번호를 전송하면 안된다는 결론에 이르게 된다.   


## **FIDO** (Fast IDentity Online)  
    
인증시 비밀번호를 이용하지 않으면서, 사용자의 경험을 헤치지 않는 방법에 대한 고민끝에 탄생한 것이 바로 FIDO 인증 방식입니다.  
  
FIDO는 FIDO Alliance에서 정의한 규약(Protocol) 혹은 규격(specification)으로, **인증 수단**과 **인증 방식**이 분리되있다는 특징을 갖고 있습니다.  
    
때문에 사용자는 **본인이 원하는 인증 수단(지문, PIN, 외부장치 등)**을 선택 할 수 있으며, 인증 방식으로는 비대칭키를 이용한 전자서명 검증을 이용합니다.      
  
**FIDO** 규격을 준수하는 **인증 장치(Authenticator)**와 **인증 서버(Server)**는 상호 운용이 가능하기 때문에 사용자는 본인의 필요에 따라 인증장치를 선택 할 수 있습니다.
  
인증 장치에는 브라우저, 모바일 기기, 전용 하드웨어(Yubikey, titan security key) 등이 있습니다. 


> **FIDO Specification**   
>     
> FIDO 에는 **UAF**, **U2F**, **FIDO2** 라는 세 가지 표준이 공존하고 있다. 
* **UAF(Universal Authentication Framework)**  
  모바일 앱(Android, iOS)에서 생체정보 또는 PIN 등의 인증 수단을 이용한 인증    
* **U2F(Universal 2nd Factor)**  
  모바일 앱(Android, iOS)에서 아이디/비밀번호 방식과 함께 별도의 2차 외부 인증장치를 사용하는 이중 인증
* **FIDO2** &#9733;&#9733;   
  기존에 모바일 앱에서만 사용 가능했던 **UAF**와 **U2F**를 웹으로 확장


FIDO 인증방식은 **등록**과 **인증** 이라는 두 가지 단계로 이루어집니다.
* ### **등록(Registration)**  
  최초에 사용자가 서비스 제공자의 서버에 인증 장치를 등록하는 단계입니다.  
    
  등록 단계에서 인증장치는 키쌍을 생성하여 공개키는 인증 서버에 전달하고, 개인키는 인증 장치 내부에 저장합니다.    
  
* ### **인증(Authentication)**  
  등록을 마친 사용자가 인증을 요청하는 단계입니다.  
  
  인증요청이 들어올 경우 인증 서버에서는 챌린지를 인증 장치에 제공합니다.  
   
  인증 장치는 개인키로 암호화된 챌린지를 서버에 다시 전달하여 인증에 대한 승인을 요청하게 됩니다.     
  
> **Web Authentication API (WebAuthn)**   
WebAuthn은 FIDO 인증을 웹 환경으로 확장시킨 **FIDO2 Specification**의 핵심 요소이자, 웹에서 비밀번호에 의존하지 않는 인증 방식의 표준 API 입니다.  
  
이번 학습은 **[WebAuthn MDN Docs](https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API)** 와 **[WebAuthn4j reference](https://webauthn4j.github.io/webauthn4j/en/)** 을 참고해
**Vue.js**와 **SpringBoot**로 구성된 웹서비스에서 핸드폰 지문인식을 이용한 로그인 구현을 목표로 합니다.  
  
---
  
# Reference
### **[FIDO at LINE: 패스워드 없는 세상으로의 첫 발걸음](https://engineering.linecorp.com/ko/blog/fido-at-line/)**  
### **[WebAuthn 이란?](https://odo.jiran.com/jtg/?q=YToxOntzOjEyOiJrZXl3b3JkX3R5cGUiO3M6MzoiYWxsIjt9&bmode=view&idx=9449123&t=board)**  

  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D 