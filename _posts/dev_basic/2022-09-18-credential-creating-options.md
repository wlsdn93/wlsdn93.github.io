---
title : "서버한테 받아야할 게 챌린지만 있는게 아니었네..?"
excerpt : "등록 프로세스를 시작할 때, 서버가 클라이언트에게 응답하는 값에는 챌린지만 있는게 아닙니다"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---

클라이언트가 서비스에 등록을 요청하게 되면, 사실 서버는 **챌린지**이외에도 **몇 가지 정보들을** 함께 클라이언트에게 응답해줍니다.  
 
---
  
# **Credential Creation Options**

등록 프로세스는 요청을 받은 **RP서버**가 클라이언트에게 전달할 **몇 가지 정보들**을 생성하는 것으로 시작됩니다.  

이 **몇 가지 정보들**은 인증장치에서 **`credential`**을 **RP서버의 요구에 맞게** 생성하기 위한 정보로 사용됩니다.  

**[응답 정보 예시](https://webauthn.guide/#webauthn-api)**

```javascript
const publicKeyCredentialCreationOptions = {
  challenge: Uint8Array.from(
          randomStringFromServer, c => c.charCodeAt(0)),
  rp: {
    name: "Duo Security",
    id: "duosecurity.com",
  },
  user: {
    id: Uint8Array.from(
            "UZSL85T9AFC", c => c.charCodeAt(0)),
    name: "lee@webauthn.guide",
    displayName: "Lee",
  },
  pubKeyCredParams: [{alg: -7, type: "public-key"}],
  authenticatorSelection: {
    authenticatorAttachment: "cross-platform",
  },
  timeout: 60000,
  attestation: "direct"
};
```  
  
네, 뭔가 많습니다. 왠지 어지럽습니다. 

어지러움을 해소하기 위해 오늘은 지난 포스트에서 다룬 **챌린지**를 제외한 정보들에 대해 차근차근 알아가 보겠습니다.      
  
## **rp (required)**

```javascript
rp: {
    name: "Duo Security",
    id: "duosecurity.com"
    }
```

**`rp(Relying Party)`**는 인증 서버가 아닌 **사용자가 로그인 하고자 하는 서비스의 서버**를 의미합니다.
  
`name`에는 특별한 제약사항이 없지만, `id`의 경우 **반드시** 도메인명과 같거나, **[도메인명의 부분집한을 사용해야 합니다.](https://w3c.github.io/webauthn/#relying-party)**  

> 예를 들어 RP서버의 **origin**이 https://login.example.com:1337 이라면  
>   
> **login.example.com** 혹은 **example.com** 은 **rpId**로 사용할 수 있습니다.    
>   
> **n.example.com** 혹은 **com** 은 **rpId**로 사용 할 수 없습니다.    

## **user (required)**
  
```javascript
user: {
    id: Uint8Array.from(
        "UZSL85T9AFC", c => c.charCodeAt(0)),
    name: "lee@webauthn.guide",
    displayName: "Lee"
    }
```
**`user`**는 인증 등록을 요청한 사용자정보를 나타냅니다. 특히 `id`는 인증장치가 **`credential private key`**와 함께 저장하는 값이기 때문에, 사용자를 특정지을 수 없는 값을 사용하는것이 **[권장](https://w3c.github.io/webauthn/#dom-publickeycredentialcreationoptions-user)**됩니다.  
  
## **pubKeyCredParams (required)**
```javascript
  pubKeyCredParams: [
    {
      type: "public-key",
      alg: -7 
    },
    {
      type: "public-key",
      alg: -257
    }
  ]
```

**`pubKeyCredParams`**은 인증장치가 전자서명을 수행할 때, **사용할 수 있는(RP서버에서 지원하는) 알고리즘** 정보를 의미합니다. 객체의 순서는 서버의 선호도를 나타냅니다.    

각각의 객체는 `type` 과 `alg`를 멤버변수로 갖습니다. 이 중 `type`은 **[현재는 "public-key"를 고정 값으로 사용합니다.](https://w3c.github.io/webauthn/#enumdef-publickeycredentialtype)**

`alg`는 **[COSE Algorithm Identifier](https://w3c.github.io/webauthn/#typedefdef-cosealgorithmidentifier)**를 나타냅니다. 
   
위의 예시는 서버에서 **ES256**와 **RS256** 두 알고리즘을 지원하지만, **ES256**를 더 선호한다는 의미입니다.  

## **timeout (optional)**
```javascript
 timeout: 60000
```
**`timeout`**은 클라이언트가 서버가 등록요청을 위해 **기다려줄 시간**을 의미합니다. **milliseconds**단위를 사용하므로, **위 예시는 1분을 의미**합니다.

## **excludeCredentials (optional)**  

```javascript
  excludeCredentials: [
      {
        "id": Uint8Array.from(window.atob("ufJWp8YGlibm1Kd9XQBWN1WAw2jy5In2Xhon9HAqcXE="), c=>c.charCodeAt(0)),
        "type": "public-key"
      },
      {
        "id": Uint8Array.from(window.atob("E/e1dhZc++mIsz4f9hb6NifAzJpF1V4mEtRlIPBiWdY="), c=>c.charCodeAt(0)),
        "type": "public-key"
      }
  ]
```
**`excludeCredentials`**는 이미 사용자가 등록한 **credential 목록**을 나타냅니다. 사용자에게 이미 등록된 인증정보가 있음을 알려주기 위해 사용됩니다.  

## authenticatorSelection (optional)

```javascript
  authenticatorSelection: {
    authenticatorAttachment: {"cross-platform", "platform"},
    residentKey: "required",
    requiredResidentKey: ture,
    userVerification: "preferred",
  }
```

**`authenticatorSelection`**은 **사용자가 등록할 수 있는 인증장치의 타입과 생성해야 하는 credential의 특징**을 제한하는데 사용됩니다.

- **[authenticatorAttachment](https://w3c.github.io/webauthn/#dom-authenticatorselectioncriteria-authenticatorattachment)**
  - **`cross-platform`**  
    사용자의 기기에 종속되지 않은, 외부 인증장치를 의미합니다.  
  - **`platform`**
    사용자 기기에 종속된 인증 장치를 의미합니다.
  
  e.g) `authenticatorAttachment: "cross-platform"` 옵션을 전달할 경우, 사용자는 반드시 외부 인증장치를 사용해야 합니다.
  
- **[userVerification](https://w3c.github.io/webauthn/#dom-authenticatorselectioncriteria-userverification)**
  - **`required`**  
    사용자 검증이 반드시 필요합니다. 인증과정에서 [`UV flag`](https://w3c.github.io/webauthn/#authdata-flags)를 검사합니다.  
  - **`preferred`**  
    가능하다면 사용자 검증을 수행합니다. 인증과정에서 `UV flag`를 검사하지 않습니다.  
  - **`discouraged`**  
    사용자 검증을 수행하지 않습니다.  

- **[residentKey](https://w3c.github.io/webauthn/#enumdef-residentkeyrequirement)**   
      
  권장되는 용어는 **`discoverable credential`**이지만, **[여러 이유](https://w3c.github.io/webauthn/#client-side-discoverable-credential)**로 **`resident`**라는 용어가 그대로 사용되고 있습니다.  
     
  **`residentKey`** 옵션이 사용 되는 이유는, **`credentialId`**가 아닌 **RP서버에 대한 정보만을** 이용해 연관된 **`credential(private key)`**를 찾도록 하기 위함입니다.   
   
  이와 반대대는 개념의 단어는 **`server-side credential`**입니다. **`server-side credential`**을 사용하기 위해서 RP서버는 **`credentialId`**을 응답해야 합니다.  

  보다 자세한 내용은 **[Resident Keys And The Future Of WebAuthn](https://duo.com/labs/tech-notes/resident-keys-and-the-future-of-webauthn-fido2)**을 읽어보시길 권합니다.  

  - **`required`**  
    인증장치가 반드시 **`discoverable credential`**를 생성 해야 합니다. 불가능 할 경우 에러가 발생합니다.   
  - **`preferred`**  
    가능한경우 **`discoverable credential`**를 생성 해야 합니다. **`server-side credential`**를 생성해도 괜찮습니다.  
  - **`discouraged`**  
    가능한경우 인증장치에서 **`server-side credential`**을 우선적으로 생성해야 합니다. **`discoverable credential`**를 생성해도 괜찮습니다.  

- **[requireResidentKey](https://w3c.github.io/webauthn/#dom-authenticatorselectioncriteria-requireresidentkey)**

  - **`true`**  
    residentKey 옵션 값이 **`required`**인 경우, 반드시 true 값을 사용합니다.
    
## **[attestation](https://w3c.github.io/webauthn/#attestation-conveyance)** (optional)
   
```javascript
  attestation: "direct"
```

  **`attestation`**옵션은 클라이언트가 등록 요청시 RP서버에 전달해야 하는 증명정보(attestation statement)에 대한 제약조건을 결정하는데 사용됩니다.     
  - **`none`**    
    클라이언트는 인증장치에 대한 증명정보를 요청에 포함하지 않아도 괜찮습니다.  
  - **`indirect`**    
    클라이언트는 인증장치에 대한 증명정보를 요청에 포함시켜야 합니다. 하지만 클라이언트가 원한다면 **[`self-attestation`](https://w3c.github.io/webauthn/#self-attestation)**을 이용해 RP서버에서 검증할 수 없는 **`attestation statement`**를 전달 할 수 있습니다.    
  - **`direct`**  
    클라이언트는 검증가능한(인증장치에 의해 생성된) 증명정보를 반드시 요청에 포함시켜야 합니다.  
  - **`enterprise`**  
    클라이언트는 특수한 정보가 포함된 증명정보를 요청에 포함시켜야 합니다. 해당 옵션은 인증수단으로서 특정 인증장치만을 이용하도록 제한하기 위해 사용됩니다.     

## **[extensions](https://w3c.github.io/webauthn/#dom-publickeycredentialcreationoptions-extensions)** (optional)
  
  **`extensions`**옵션은 클라이언트와 인증장치에서 추가적인 데이터 처리가 필요한 경우 사용됩니다. **JSON 형식**으로 제공되어야 합니다.  
  
  
# **Create Credential**  
  
클라이언트(브라우저)는 서버로부터 응답받은 이 값들을 `navigator.credentials.create()`에 전달해 **`publicKeyCredential`**객체를 생성하게 됩니다.  

```javascript
const publicKeyCredentialCreationOptions = await apiStartRegistration();
const publicKeyCredential = await navigator.credentials.create({
    publicKey: publicKeyCredentialCreationOptions
});

console.log(credential);
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
**`publicKeyCredential`**객체에 대한 이야기는 다음 포스팅에서 계속하도록 하겠습니다.  

---
    
# Reference

### **[W3C WebAuthn Recommendation](https://www.w3.org/TR/webauthn-2/)**
### **[WebAuthn guide](https://webauthn.guide/#webauthn-api)**
### **[Yubico WebAuthn Developers Guide](https://developers.yubico.com/WebAuthn/)**

  
&nbsp;  
&nbsp;  
  
잘못된 정보에 대한 피드백을 언제나 환영합니다. 많은 지적 바랍니다 :D  