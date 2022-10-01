---
title : "등록하기"
excerpt : "navigator.credential.creat() 함수에 전달할 실제 값을 만들어보겠습니다."
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---


이번 포스트에서는 앞서 공부한 내용들을 바탕으로, 등록요청시 서버에서 응답해줄 값(PublicKeyCredentialCreationOptions)을 구현해보록 하겠습니다.  
    
---
  
# Parsing & Validating

```javascript
console.log(publicKeyCredential)
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

등록의 마지막 단계는 `PublicKeyCredential`객체를 인증서버에 전달하여, 증명을 검증하고, 검증에 통과한 credential을 사용자 계정에 매핑하여 저장하는 것입니다.  

* clientDataJSON: 
인증장치가 브라우저와 rp서버를 새롭게 생성하는 credential과 연관짓기 위해, 브라우저로부터 전달받은 정보. utf-8로 인코딩된 바이트 배열

```javascript
// decode the clientDataJSON into a utf-8 string
const utf8Decoder = new TextDecoder('utf-8');
const decodedClientData = utf8Decoder.decode(credential.response.clientDataJSON)
// parse the string as an object
const clientDataObj = JSON.parse(decodedClientData);

console.log(clientDataObj)

/*
{
  challenge: "p5aV2uHXr0AOqUk7HQitvi-Ny1....",
  origin: "https://webauthn.guide",
  type: "webauthn.create"
}
*/
```

parsed clientDataJSON 
```json
{
  "origin":"http://localhost:8081",
  "challenge":"YU55NDItQmJ1bXREQkhIdEN1UWVJTkhDQ0VZ",
  "crossOrigin":false,
  "type":"webauthn.create"
}
```
challenge 랑 origin 검사하고 



* attestationObject:
public key / attestation certificate / matadata 가 들어있는 CBOR data
CBOR 이란 객체를 바이너리 데이터 형태로 나타낸 것.

```javascript
// note: a CBOR decoder library is needed here.
const decodedAttestationObj = CBOR.decode(
    credential.response.attestationObject);

console.log(decodedAttestationObject);
/*
{
  authData: Uint8Array(196),
  fmt: "fido-u2f",
  attStmt: {
      sig: Uint8Array(70),
      x5c: Array(1),
  },
}
*/
```

## attestationObject
base64 decoded data
```text
id : N3AyWrUpc5AY6oTBCz3uF4DeBbaY8PE0_xpnWT4PakdqoACNT23bZxzATUaz
attestationObject : o2NmbXRmcGFja2VkZ2F0dFN0bXSiY2FsZyZjc2lnWEcwRQI...+7KxZNCJYIKA2IY78vH5BIsTqYL9DyUHVxOb0XbepHqSddOMdHrlX
clientDataJSON : eyJ0eXBlIjoid2ViYXV0aG4uY3JlYXRlIiwiY2hhbGx...iaHR0cDovL2xvY2FsaG9zdDo4MDgxIiwiY3Jvc3NPcmlnaW4iOmZhbHNlfQ==
type : public-key
```


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
after parsing 
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
  - authData : 공개키
  - fmt : attestation이 작성된 format 정보, 서버는 이 값에 따라 검증 방식을 달리해야함
  - 
 
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


### **[What is replay attack?](https://academy.binance.com/en/articles/what-is-a-replay-attack)**

### **[W3C WebAuthn Reference](https://w3c.github.io/webauthn/#sctn-cryptographic-challenges)**  
  
### **[Moving away from uuid](https://neilmadden.blog/2018/08/30/moving-away-from-uuids/)**  
  
### **[Constraints on the length of the challenge in FIDO2](https://groups.google.com/a/fidoalliance.org/g/fido-dev/c/la5eGs484n8)**  
  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  