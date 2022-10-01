---
title : "credential은 만들었는데, 서버로 그냥 보내면 되는건가..?"
excerpt : "pubKeyCredential에 대해 알아보자"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---
  
> 이런 저런 핑계로, WebAuthn 공부와 포스팅이 자꾸만 밀리고 있다. 반성해야지...   
  
`navigator.credentials.create()`는 서버로부터 응답받은 **publicKeyCredentialOptions**를 매개변수로 받아 `PublicKeyCredential` 객체를 응답해줍니다.  
  
오늘은 `PublicKeyCredential`이 포함하고 있는 정보들의 의미와, 등록절차를 마무리 하기 위한 증명 검증방법에 대해 알아보겠습니다.    

---
  
# **PublicKey Credential**

```javascript
const publicKeyCredentialCreationOptions = await apiStartRegistration();
const publicKeyCredential = await navigator.credentials.create({
    publicKey: publicKeyCredentialCreationOptions
});

console.log(publicKeyCredential);
/*
PublicKeyCredential {
    id: 'ADSUllKQmbqdGtpu4sjseh4cg2TxSvrbcHDTBsv4NSSX9...',
    rawId: ArrayBuffer(59),
    response: AuthenticatorAttestationResponse {
      clientDataJSON: ArrayBuffer(121),
      attestationObject: ArrayBuffer(306),
  },
  type: 'public-key'
}
*/
```
  
## **id**  
새로 생성된 **credential**이 갖는 **base64url**로 인코딩된 식별값입니다. 인증 프로세스에서 **credential**을 식별하는데 사용됩니다.    
  
## **rawId**  
**id**의 binary 값입니다. 
  
## **[clientDataJSON](https://w3c.github.io/webauthn/#dictdef-collectedclientdata)**  
브라우저가 인증장치에게 전달해준 값입니다.  
  
## **attestationObject**  
  
---
    
# Reference

### **[WebAuthn guide](https://webauthn.guide/#webauthn-api)**

  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  