---
title : "[FIDO2] publicKeyCredential 파헤치기"
excerpt : "등록 프로세스의 핵심 ! 증명을 어떻게 해야하는걸까요?"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---
  
서버에 전달할 `credential`을 만들었더니, 이 녀석도 뭔가 정보를 많이 갖고 있습니다.  
  
이번 포스팅에서는 `credential`을 구성하고 있는 정보들에 대해 알아보겠습니다.    

---
  
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
  
앞선 포스팅에서 말씀 드렷듯, **등록 프로세스의 시작**은 서버에게 **인증장치가** `credential`을 **생성할 때 참고할 요구사항**을 알려달라고 요청하는 것으로 시작됩니다.  
  
요청을 받은 **RP서버**는 그들이 생각하기에 `credential`을 생성할 때 **반드시 지켜야 하거나, 가급적 지켜주길 바라는** 내용을 응답해줍니다.  

그렇게 전달 받은 객체를 `publicKeyCredentialCreationOptions`에 담아, 브라우저의 `navigator.credentials.create()` 함수에 인자로 전달하게되면  
  
브라우저는 이 정보를 사용자 기기의 **인증장치**에 전달해주고, **인증장치**에서는 앞으로의 인증 프로세스에서 사용하게 될 키쌍을 생성합니다.  
  
그리고 이 중 **공개키와 증명정보 등**을 브라우저에게 응답해주면, 브라우저는 이 정보들을 예쁘게 다듬어 우리에게 `publicKeyCredential`의 형태로 응답해주는 것입니다.  
  
---  
  
# **Id**  
인증장치에서 각각의 `credential`을 식별하기 위해 사용하는 **credentialId** 입니다. RP서버는 이 **credentialId**를 사용자 계정 정보와 함께 저장하여, 이후 인증 프로세스에서 사용하게 됩니다.     
  
**[이전 포스트](https://wlsdn93.github.io/devbasic/credential-creating-options/#authenticatorselection-optional)**에서 소개한 **residentKey 또는 discoverable credential**은 **credentialId**가 필요하지 않습니다.    
  
**rawId**는 **id**의 binary 형태 입니다.  
    
---
  
# **ClientDataJSON**  

**clientDataJSON**은 `credentail`을 생성할 때 브라우저가 인증장치에게 전달한 정보들로, 인증장치는 이 정보들을 토대로 새롭게 생성한 `crendetial`과 브라우저 그리고 RP서버를 매핑시켜 저장하게 됩니다.  

`UTF-8 byte array`형태입니다.  

```json
{
  "origin" : "http://localhost:8081",
  "challenge" : "YU55NDItQmJ1bXREQkhIdEN1UWVJTkhDQ0VZ",
  "crossOrigin" : false,
  "type" : "webauthn.create"
}
```

## **Origin**
  
**[origin](https://developer.mozilla.org/en-US/docs/Glossary/Origin)**은 인증 또는 등록을 요청한 웹사이트를 의미합니다.  
  
    
## **Challenge**  
  
처음 서버에서 내려준 챌린지 입니다.  
  
## **CrossOrigin** (optional)

**crossOrigin**는 우리가 흔히 아는 **[CORS 정책](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)**에 대한 옵션값 입니다.  
  
이 옵션을 사용하는 경우는 **[제 3자를 통해 인증을 수행해야 하는 경우](https://chromestatus.com/feature/5736091539734528)** 입니다.      
   
## **Type**  
**type**은 해당 요청이 새로운 인증서를 등록하기 위함인지, 아니면 인증을 요청하는 것인지를 서버에 알려주기 위해 사용됩니다.  
  
각각의 경우에 따라 아래와 같이 정해진 문자열을 포함하고 있습니다.  
 - register : "webauthn.create"  
 - authentication : "webauthn.get"  
  
---  
  
# **AttestationObject**  

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
  
## **[Format](https://w3c.github.io/webauthn/#attestation-statement-format)**    
**fmt**는 인증서버에게 **attestation statement**의 형태를 알려주기 위해 사용됩니다.  
  
인증서버에서는 이 정보를 토대로, **attestation statement**을 파싱하고 검증하게 됩니다.
  
이러한 정보가 필요한 이유는, 애석하게도 이미 기존에 여러 인증 장치들이 각자의 방식으로 **attestation data**를 만들어 왔기 때문입니다 :/
  
포맷에 대한 자세한 정보는 아래 링크를 참고하시기 바랍니다.  
  
**[Attestation Statement Format Identifiers](https://w3c.github.io/webauthn/#sctn-attstn-fmt-ids)**  

## **[AttestationStatement](https://w3c.github.io/webauthn/#attestation-statement)**  
- **alg**  
  **attestation signature**를 생성하는데 사용된 알고리즘을 **[COSEAlgorithmIdentifier](https://w3c.github.io/webauthn/#typedefdef-cosealgorithmidentifier)** 형태로 나타낸 값입니다.
  
- **sig**  
  인증 장치에 대한 정보와 **credential public key**를 **attestation private key**로 서명한 전자서명입니다.   
  
- **x5c (optional)**  
  **x5c**는 **attestation certificate**와 **CA certificate**를 **X.509**형태로 인코딩한 값입니다. **이 값의 유무에 따라 검증 방식이 달라집니다.** 


## **[AuthenticatorData](https://w3c.github.io/webauthn/#sctn-attestation)**  
**authData**는 인증 장치에 대한 메타데이터와 공개키를 포함하고 있는 바이트 배열입니다. 인증서버는 **이 바이트 배열을 적절히 잘라** 사용하게 됩니다.    
  
![image.png](/assets/images/web-basic-images/fido-attestation-structures.svg)  

**[Parsing Example](https://webauthn.guide/#registration)**
```javascript
const {authData} = decodedAttestationObject;

// get the length of the credential ID
const dataView = new DataView(new ArrayBuffer(2));
const idLenBytes = authData.slice(53, 55);
idLenBytes.forEach((value, index) => dataView.setUint8(index, value));
const credentialIdLength = dataView.getUint16();

// get the credential ID
const credentialId = authData.slice(55, 55 + credentialIdLength);

// get the public key object
const publicKeyBytes = authData.slice(55 + credentialIdLength);

// the publicKeyBytes are encoded again as CBOR
const publicKeyObject = CBOR.decode(publicKeyBytes.buffer);

console.log(publicKeyObject)
{
  1: 2,
  3: -7,
  -1: 1,
  -2: Uint8Array(32) ...
  -3: Uint8Array(32) ...
}
```       
  
---  
  
  
# Reference
    
### **[Same Origin Policy](https://web.dev/same-origin-policy/)**
  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  