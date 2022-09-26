---
title : "PublicKeyCredentialCreationOptions 응답해주기"
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

 
# Reference

### **[What is replay attack?](https://academy.binance.com/en/articles/what-is-a-replay-attack)**

### **[W3C WebAuthn Reference](https://w3c.github.io/webauthn/#sctn-cryptographic-challenges)**  
  
### **[Moving away from uuid](https://neilmadden.blog/2018/08/30/moving-away-from-uuids/)**  
  
### **[Constraints on the length of the challenge in FIDO2](https://groups.google.com/a/fidoalliance.org/g/fido-dev/c/la5eGs484n8)**  
  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  