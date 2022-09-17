---
title : "Challenge 그렇게 만드는거 아닌데..."
excerpt : "등록과 인증과정에 반드시 필요한 챌린지, 왜 사용하고, 어떻게 만들어야 할 까?"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---

드디어 본격적인 구현을 위한 준비과정입니다 !!

이제 조금은 이해했다고, 용어들과도 친숙해졌다고 생각했지만... 막상 구현을 시작하려고 하니 다시 막막함이 벽처럼 느껴집니다.  
  
오늘은 그 막막한 벽을 오르기 위한, 첫 관문인 **challenge**에 대한 이야기 입니다.

> 여담으로 이번 WebAuthn 학습을 진행 하면서 선배 개발자분들에게 무한한 존경심이 생기기 시작했다.  
>  
> 내가 아직은 소화하기 쉽게 **한 단계, 한 단계 알려주지 않으면 안되는 응애개발자라는 사실**이 분하다..    
>    
> 이번 학습으로 조금은 응애 티를 벗을 수 있기를..
    
---
  
# Challenge  

아마도 **challenge**라는 이름은, 공격자가 넘어서야하는 하나의 **도전과제** 라는 의미가 아닐까 싶습니다.      
  
FIDO 인증방식에서는 **[replay attack](https://academy.binance.com/en/articles/what-is-a-replay-attack)**을 방지하기 위해 등록과 인증 과정 모두, 서버로부터 **challenge**를 전달 받습니다.  

서버는 **challenge**를 DB에 보관하였다가, 클라이언트가 인증 요청을 위해 전달한 전자서명을 확인하는 과정에서 **challenge**가 서버에서 전달한 값과 같은지 확인하는 용도로 사용됩니다.  

**[공식문서](https://w3c.github.io/webauthn/#sctn-cryptographic-challenges)**에서는 이 **challenge**에 대한 스펙을 규정하고 있습니다.   
  
근데 왜 하필 최소 16Byte 인걸까요? 그냥 **UUID**같은 값을 사용하면 안되는 걸까요?  
  
## **UUID는 비효율적이고 안전하지 않다.**  
  
네 uuid로는 충분하지 안답니다..  왜 안되는지는 아래 글을 참고해주시기 바랍니다.  

**[Moving away from uuid](https://neilmadden.blog/2018/08/30/moving-away-from-uuids/)**   
  
> 영어로 된 자료를 읽는데 거부감이 있는 분들을 위한 짧은 요약  
>   
> 극단적인 상황에서는 특정 uuid를 추측하는데 약 35분이면 충분하다.
>   
> uuid를 이용해 16byte 크기의 데이터를 표현하려면 36개의 문자가 필요하지만, base64 encoding을 이용한다면 훨씬 적은 문자로 표현이 가능하다.  
>   
> 임의의 20byte 값을 URL-safe base64 encoding해서 사용하는 것을 권장한다.

## **더 크게 만들면 더 안전해지는거 아닌가?**  
    
단순한 생각이지만 **더더더더 안정해지기 위해 더 큰 값을 사용하면 안되는걸까요?**  
  
**네 그러지 말랍니다...**  
  
**[constraints on the length of the challenge in FIDO2](https://groups.google.com/a/fidoalliance.org/g/fido-dev/c/la5eGs484n8)**에서 발췌한 내용입니다.  

> 첫 번째로 우선 더 큰 값을 만들기 위해선, 더 많은 서버 자원을 소모해야 합니다.  
>  
> 개별 요청으로 본다면 큰 차이가 아닐 수 있지만, 한번에 많은 요청을 수행해야 하는 경우 부담이 될 수 있습니다.    
>    
> 두 번째로 인증장치의 물리적 한계 때문입니다. 특정한 인증 장치들은 매우 제한적인 환경에서 작동하도록 생산되었기 때문에, 오류가 발생할 수 있습니다.  
> 
> 마지막으로 **20Byte**는 매우 안전한 크기 입니다. 

    
# Reference

### **[What is replay attack?](https://academy.binance.com/en/articles/what-is-a-replay-attack)**

### **[W3C WebAuthn Reference](https://w3c.github.io/webauthn/#sctn-cryptographic-challenges)**  
  
### **[Moving away from uuid](https://neilmadden.blog/2018/08/30/moving-away-from-uuids/)**  
  
### **[Constraints on the length of the challenge in FIDO2](https://groups.google.com/a/fidoalliance.org/g/fido-dev/c/la5eGs484n8)**  
  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  