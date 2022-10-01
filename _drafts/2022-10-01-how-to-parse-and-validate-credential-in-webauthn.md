---
title : "이제 publicKeyCredential 널 어떻게 해야하니...?"
excerpt : "등록 프로세스의 핵심 ! 증명을 어떻게 해야하는걸까요?"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---
  
`credential` 도 만들었는데, 이제 이거로 뭘 어떻게 해야 할 까요? 서버에 전달해주는 것만으로 마법처럼 인증 과정이 끝나면 참 좋겠지만    
  
애석하게도 개발자인 우리가 마법을 부려야 합니다. 이번 포스팅에서는 `credential`을 구성하고 있는 정보들의 의미와 이를 검증하는 방법에 대해 알아보겠습니다.    

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
  authenticatorAttachment: `platform`
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
  
앞선 포스팅에서 말씀 드렸듯, **등록 프로세스의 시작**은 서버에게 **인증장치가** `credential`을 **생성할 때 참고할 요구사항**을 알려달라고 요청하는 것으로 시작됩니다.  
  
요청을 받은 **RP서버**는 그들이 생각하기에 `credential`을 생성할 때 반드시 지켜야 하거나, 가급적 지켜주길 바라는 내용을 응답해줍니다.  

그렇게 전달 받은 객체를 `publicKeyCredentialCreationOptions`에 담아, 브라우저의 `navigator.credentials.create()` 함수에 인자로 전달하게되면  
  
브라우저는 이 정보를 사용자 기기의 **인증장치**에 전달해주고, **인증장치**에서는 앞으로의 인증 프로세스에서 사용하게 될 키쌍을 생성합니다.  
  
그리고 이중 **공개키와 증명정보 등**을 브라우저에게 응답해주면, 브라우저는 이 정보들을 예쁘게 다듬어 우리에게 `publicKeyCredential`으로 응답해주는 것입니다.  

## **id**  
인증장치에서 각각의 `credential`을 식별하기 위해 사용하는 **credentialId** 입니다. 사용자 계정 정보와 함께 저장하여 이후 인증 프로세스에서 사용될 정보 입니다.   
  
**[이전 포스트](https://wlsdn93.github.io/devbasic/credential-creating-options/#authenticatorselection-optional)**에서 소개한 **residentKey 또는 discoverable credential**은 **credentialId**가 필요하지 않습니다.    
  
**rawId**는 **id**의 binary 형태 데이터 입니다. 
  
## **clientDataJSON**  

**clientDataJSON**은 `credentail`을 생성할 때 브라우저가 인증장치에게 전달한 정보들로, 인증 장치는 이 정보들을 `UTF-8 byte array`형태로 돌려줍니다.  

인증장치는 이 정보들을 토대로 새롭게 생성한 `crendetial`과 브라우저 그리고 RP서버를 매핑시켜 저장하게 됩니다.  

```json
{
  "origin" : "http://localhost:8081",
  "challenge" : "YU55NDItQmJ1bXREQkhIdEN1UWVJTkhDQ0VZ",
  "crossOrigin" : false,
  "type" : "webauthn.create"
}
```

### **origin**
  
**[origin](https://developer.mozilla.org/en-US/docs/Glossary/Origin)**은 인증 또는 등록을 요청한 웹사이트의 출처를 의미합니다.  
    
### **challenge**  
  
처음 서버에서 내려준 챌린지 입니다.  
  
### **crossOrigin**  
  

  
### **type**  
  


## **attestationObject**  

**attestationObject**은 인증장치가 생성한 **공개키**, 비밀키를 이용해 **전자서명**한 챌린지 그리고 **증명에 사용될 기타 정보**들을 **[CBOR](https://cbor.io/)**이라는 데이터 형태로 인코딩한 바이너리 데이터 입니다.  

```json
{
    "fmt":"packed",
    "attStmt": {
      "alg":-7,
      "sig":"MEUCIQCb7Pz+nR1pAZv+be/ckG7ru...usWu2tFrt9jbUNX6sAMqHwE9zukGVOmSv5fUJ2P9A="
    },
    "authData":"SZYN5YgOjGh0NBcPZHZgW4/krrmihjLHmVzzuoMdl...8vH5BIsTqYL9DyUHVxOb0XbepHqSddOMdHrlX"
}
``` 
### **[fmt](https://w3c.github.io/webauthn/#attestation-statement-format)**  
증명 정보의 형태를 의미합니다. 
### **[attStmt]()**  
  
### **[authData]()**  
  

before parsing
```text
[
  -93, 99, 102, 109, 116, 102, 112, 97, 99, 107, 101, 100, 103, 97, 116, 116, 83, 116, 109, 116,  108, 103,
  38, 99, 115, 105, 103, 88, 71, 48, 69, 2, 33, 0, -101, -20, -4, -2, -99, 29, 105, 1, -101,  -36, -112,
  ...
  32, -22, 2, -67, 8, -21, -122, -104, 101, 53, -63, -76, 101, -122, 102, 23, -22, -81, -2,  52, 34, 88, 32,
  -96, 54, 33, -114, -4, -68, 126, 65, 34, -60, -22, 96, -65, 67, -55, 65, -43, -60, -26,  -87, 30, -92, -99,
  116, -29, 29, 30, -71, 87
]
```  
 
# Reference

> Advice to sites regarding clientDataJSON
When performing a registration or authentication with webauthn, it's critical that sites confirm that the returned clientDataJSON contains the challenge originally provided. 
> Otherwise old messages can be replayed. Likewise, sites must check the origin member to confirm that the action is not being proxied by another site.
>  
> In order to implement this, sites should parse the JSON as a CollectedClientData structure and confirm that the type, challenge, and origin members (at least) are as expected.
>  
> Sites should not implement this by comparing the unparsed value of clientDataJSON against a template with the challenge value filled in. 
> This would fail when new members are added to CollectedClientData in the future as the template would no longer be correct.
>  
> In order to guide sites away from doing this, Chromium will sometimes, randomly insert an extra member into clientDataJSON which references this documentation.

base64 decoded data
```text
id : N3AyWrUpc5AY6oTBCz3uF4DeBbaY8PE0_xpnWT4PakdqoACNT23bZxzATUaz
attestationObject : o2NmbXRmcGFja2VkZ2F0dFN0bXSiY2FsZyZjc2lnWEcwRQI...+7KxZNCJYIKA2IY78vH5BIsTqYL9DyUHVxOb0XbepHqSddOMdHrlX
clientDataJSON : eyJ0eXBlIjoid2ViYXV0aG4uY3JlYXRlIiwiY2hhbGx...iaHR0cDovL2xvY2FsaG9zdDo4MDgxIiwiY3Jvc3NPcmlnaW4iOmZhbHNlfQ==
type : public-key
```
  
### **[Constraints on the length of the challenge in FIDO2](https://groups.google.com/a/fidoalliance.org/g/fido-dev/c/la5eGs484n8)**  
  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  