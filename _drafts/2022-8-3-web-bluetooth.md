---
title : "웹으로 블루투스 기기 컨트롤하기"
excerpt : "오픈소스 라이브러리를 사용할 때 보이는 라이선스란 뭘까?"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: 
---

## BLE
수많은 블루투스 스펙중 하나. `BLE`는 Bluetooth Smart 로도 불리며 classic bluetooth 의 경량화 버전을 목표로 블루투스 4.0의 일부로 발표되었다.  
Classic bluetooth 와 겹치는 부분이 존재하지만 BLE 는 완전히 다른 표준이다.  

## Central(Sever) / Peripheral(Client)  
디바이스는 `Central`, `Peripheral` 두 종류로 구분된다.  
`Peripheral`은 주변에 일정 간격으로 `advertise`를 하고 `Central`이 스캔에 성공함으로서 페어링을 하게 된다.   
두 디바이스가 연결되면 `advertise Mode`는 종료되고 `Connection Mode로` 1대1 통신을 하게 된다.
  
  * Observer(Central): 다른 디바이스의 `Advertise` 신호를 주기적으로 스캔하다가, 연결을 요청한다.
  * Advertiser(Peripheral): 연결하기 위한 Advertise 신호를 주기적으로 보내다가, Central 디바이스가 연결 요청을 보내면, 이를 수락해 연결한다.
  
## GATT (Generic Attribute Profile)  
`GATT`란 `Service`, `Characteristic` 을 이용해 데이터를 주고받는 규칙을 방법을 정의한 것이다.       
`GATT`는 `Profile`, `Service`, `Characteristic`으로 구성되어있다.   
  * `Service` : 특성(characteristic) 데이터 단위의 집합. 각 서비스는 `UUID`를 갖고 있다.
  * `Characteristic` : `GATT`에서 가장 작은 데이터 단위. 단 하나의 데이터만 포함한다. 특성도 `UUID`를 가지고 있다.
  * `Profile` : 서비스의 묶음으로, 기본 데이터 표준 집합.
  
## Web Bluetooth API  
아직은 실험단계 이긴 하나 `JavaScript`에서는 **[Web Bluetooth API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API)** 를 제공하고 있다.  
  
BLE 기기간 통신을 위해서는 우선 `Central` 과 `Peripheral` 사이에 연결이 이루어 져야한다. 
```javascript
const connect = () => async {
  const device = await navigator.bluetooth.requestDevice({
                      filters:[{services:[ 'heart_rate' ]}]
                   });
   /* ~ */
}
```
위와 같이 `filters` 키를 이용하면, BLE 기기와의 연결을 위해 기기를 탐색하는 과정에서 `heart_rate` 서비스를 갖고있는 기기 목록만을 확인할 수 있다.  
  
아래와 같이 `filters`키를 사용하지 않고 `acceptAllDevices`키를 이용하면 근처의 모든 BLE 장치를 표시할 수도 있다.  
**하지만 이때는, `optionalServices` 키를 이용해야만, 기기와의 연결을 획득한 이후 서비스에 접근할 수 있다.**  
```javascript
const device = navigator.bluetooth.requestDevice({
  acceptAllDevices: true,
  optionalServices: ['heart_rate'] // Required to access service later.
})
```  
  
사용자가 기기를 선택하고 페어링할 기기를 선택하면, 두 BLE 기기 사이에 연결이 생성된다.    
```javascript
const server = await device.gatt.connect();
```  

연결을 획득 한 이후 특정 기능으로부터 정보를 얻기 위해선 서비스를 통해 `Characteristic` 에 접근해야 한다.  
이 때, `Characteristic`은 기기의 특정 기능을 의미하며, `unsigned integer array`에 값을 저장한다.  
`Characteristic` 을 획득하면 값을 읽고`(readValue)`, 쓸 수`(writeValue)` 있다. 
```javascript
const service = await server.getPrimaryService('heart_rate');
const characteristic = await service.getCharacteristic('heart_rate_measurement');
const value = await characteristic.readValue();
await console.log(value.getUint8(0)
```
  
```javascript
const value = Uint8Array.of(1);
const service = await server.getPrimaryService('heart_rate');
const characteristic = await service.getCharacteristic('heart_rate_control_point');
await characteristic.wirteValue(value);
```

**[Send files over Web-Bluetooth](https://stackoverflow.com/questions/56469188/can-i-send-files-over-web-bluetooth)**


  
## 기기의 `UUID`는 어떻게 알 수 있는가? 
BLE 기기 사이에 연결을 만들기 위해서는 서비스의 `UUID`가 필요하다. 이 때 `UUID`는 두 종류로 분류할 수 있다.
1. 'heart_rate'와 같이 Bluetooth SIG(Special Internet Group)에 의해 관리되고 있는 표준 서비스명 또는 16비트 숫자.   
2. 기존의 표준과 다르게 새롭게 만들어진 services 와 characteristics 를 위한 128비트 숫자  
  
1번의 경우 Bluetooth SIG Website 에서 쉽게 확인 할 수 있다.  
하지만 2번의 경우 해당 기기의 API 혹은 SDK 에서 확인해야 하며, 이러한 정보가 없는 경우, nRF Connect App 등을 이용해 해당 기기를 찾아 확인할 수 있다.  