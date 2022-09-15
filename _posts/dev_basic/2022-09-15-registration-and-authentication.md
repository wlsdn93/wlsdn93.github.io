---
title : "FIDO 등록과 인증"
excerpt : "등록(Registration)과 증명(Attestation), 인증(Authentication)과 승인(Assertion)에 대해 알아봅니다"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---
   
클라이언트 사이드와 서버 사이드에서 FIDO 인증방식을 사용예시 샘플코드들을 처음 봤을 때 느낀점은 **"이게 뭐야???"** 였습니다.

샘플 코드와 레퍼런스를 보다보면 튀어 나오는 **Attestation, relying party, MDS** 와 같은 단어들이 무슨 의미인지 조차 이해하지 못한채로는 샘플 코드의 한 줄 한 줄이 무슨 목적으로 존재하는지 이해할 수 없었습니다.    

이번 포스트는 **FIDO의 인증 프로세스**가 어떻게 작동하는지, 그리고 각각의 용어들이 어떤 의미로 사용되는지 조금 더 자세히 알아보고자 합니다.  

영어 단어를 최대한 보존한 채로 포스팅 하는 이유는 개발 문서는 영어이기 때문입니다.. :/


# **등록(Registration)**  
  
---  
  
최초에 사용자가 서비스 제공자의 서버에 인증 장치를 등록하는 단계입니다. 등록 단계에서는 두 종류의 키쌍이 사용 됩니다.
 
  > ## **1. Credential key-pair**    
  > **인증 장치**가 생성해주는 키쌍으로, 공개키 암호화 방식을 통한 **인증** 프로세스를 수행하는데 사용됩니다.  
  >     
  > 등록 프로세스를 통해 **공개키(credential public key)**는 인증 서버에 등록되고, **개인키(credential private key)**는 인증장치의 (물리적 또는 논리적으로)안전한 공간에 저장됩니다. 
  >    
  > 이후 사용자는 서버에서 전달받은 **Challenge**를 개인키를 이용해 전자서명하여 다시 서버에 전달 함으로써 자신을 증명하게 됩니다.     
  >
  > ## **2. Attestation key-pair**    
  > 인증장치를 생산할 때 제조사가 심어놓은 키쌍입니다.
  >   
  > 이 키쌍은 **private key** 그리고 **public key**를 포함하고 있는 **attestation certificate** 로 이루어져 있습니다.
  >
  > **증명**이란 등록 과정에서 서버에 전달되는 **credential public key**가 **FIDO 규격을 따르는 인증 장치로부터** 생성되었다는 것을 증명하는 과정입니다.  
  >
  > 증명에 사용되는 이 키쌍은 제조사와 모델에 따라 달라지는 값입니다. 예를 들어 모든 **갤럭시 S22 핸드폰은 모두 동일한 증명 키쌍을 갖고있습니다.**  
  >  
  > 따라서 이 키쌍은 개별 인증장치를 구분해 사용자를 추적하는데 사용 될 수 없습니다.
  >   
  > **인증 서버**는 증명에 앞서, **클라이언트(인증장치)**로부터 전달받은 **attestation certificate**를 신뢰할 수 있는지를 먼저 검증합니다.  
  >   
  > 따라서 인증 장치 제조사는 신뢰할 수 있는 공인된 CA 기관으로부터 **attestation certificate**를 발급 받아야 합니다.
  >    
  > 이러한 방식을 **PKI(Public Key Infrastructure)** 라고 부릅니다.  
  >   
  > 공격자가 등록 요청 메세지에 포함된 **credential public key**를 자신의 공개키로 바꿔치기를 시도 하더라도, **attestation certificate에 포함된 공개키 이용한 전자서명 검증을 통과 할 수 없어 안전합니다.**
  > 
  > 사실 **[Web Authentication API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API)**는 암호화된 통신에서만 사용 가능하기 때문에, 공개키가 외부의 공격에 의해 변조될 가능성은 매우 낮습니다.  

 사용자가 최초로 **Relying Party(Service Provider)**에 인증 수단 등록을 요청하면 아래와 같은 등록 프로세스가 동작하게 됩니다.  

  1. 인증장치(ex. 핸드폰)는 사용자를 검증하고(ex. 지문 인식을 통한 잠금해제), **credential key pair**를 생성한 후  
       
     * **credential public key**를 **attestation private key**를 이용해 전자서명한 **signature** 
     * **attestation certificate**   
     * **unique model number**
        
     세 가지를 **relying party**로 전달합니다.  
         
  2. **relying party**는 우선 **FIDO Alliance**에서 **JSON**형식으로 제공하는 **[MDS(Metadata Service)](https://fidoalliance.org/metadata)**로부터 **unique model number**를 이용해 **metadata statement**를 찾습니다.  

     **metadata statement**는 **attestation root certificate** 와 **device metadata**두 가지 정보를 필수로 포함하고 있습니다.  

     **attestation root certificate**는 인증 장치 제조사에 인증서를 발급해준 CA기관의 인증서로 **attestation certificate**를 검증하는데 사용됩니다.  
      
  3. **attestation certificate**에 대한 검증이 완료되면, **FIDO Server**는 **attestation certificate**에 포함되어 있는 **attestation public key**를 이용해 **signature**를 검증합니다.
     
     위의 모든 검증이 완료되면, 서버는 인증 장치로부터 전달받은 **credential public key**를 사용자의 계정에 매핑하고 저장합니다.  

  **2번 과정은 필요에 따라 수행하지 않아도 괜찮습니다.**  
  
# **인증(Authentication)**  
  
---
   
  서비스에 등록이 완료된 사용자는 이후 인증을 요청할 때 아래와 같은 인증 프로세스가 동작하게 됩니다.  

  1. 인증서버는 인증장치에게 **challenge**를 전달합니다.  

  2. 인증장치는 인증 요청자가 **credential private key**의 주인임을 검증(지문, 얼굴, PIN 등)합니다.   
   
  3. 검증에 통과하게 되면 인증 장치는 **credential private key**를 이용해 **challenge**를 전자서명하여 **assertion** 을 생성합니다.  
  
  4. 인증장치는 **assertion**을 인증서버에 전달함으로서 인증에 대한 승인을 요청합니다.    
  
  5. 인증서버는 사용자 계정에 매핑되있는 **credential public key**를 이용해 **assertion**을 복호화하여 서버에서 전달한 **challenge** 확인합니다.  
   
  6. 인증 성공시 인증서버는 요청에 대한 응답으로 **인가(Authorization)**를 위한 토큰 혹은 쿠키를 응답에 포함하여 인증장치에게 전달합니다.   
     

---
    
# Reference  
### **[FIDO 이해하기 : 등록과 인증](https://m.blog.naver.com/aepkoreanet/221510427704)**  
### **[FIDO TechNotes](https://fidoalliance.org/fido-technotes-the-truth-about-attestation/?)**

&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  