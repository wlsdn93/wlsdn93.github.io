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
## Bluetooth

## BLE
수많은 블루투스 스펙중 하나. 

## GAP(Generic Access Profile)  
서로 다른 제조사가 만든 BLE 디바이스들끼리 서로 호환되기 위한 통신 규격. 데이터를 어떻게 보내고, 인지하고, 연결할지에 대해 규격을 정해놓은 `Profile`이다.

## Central(Sever) / Peripheral(Client)  
디바이스는 `Central`, `Peripheral` 두 종류로 구분된다.  
`Peripheral`은 주변에 일정 간격으로 `advertise`를 하고 `Central`이 스캔에 성공함으로서 페어링을 하게 된다.   
두 디바이스가 연결되면 `advertise Mode`는 종료되고 `Connection Mode로` 1대1 통신을 하게 된다.
  
  * Observer(Central): 다른 디바이스의 `Advertise` 신호를 주기적으로 스캔하다가, 연결을 요청한다.
  * Advertiser(Peripheral): 연결하기 위한 Advertise 신호를 주기적으로 보내다가, Central 디바이스가 연결 요청을 보내면, 이를 수락해 연결한다.
  
## GATT (Generic Attribute Profile)  
데이터를 주고받는 규칙은 `Service`, `Characteristic`에 의해 정의 된다. 
저수준에서 데이터를 전송하기 위한 규칙인 `ATT(Attribute Protocol)` 를 기반으로 한다.  
`GATT`는 `Profile`, `Service`, `Characteristic`으로 구성되어있다.   
  * `Service` : 서비스는 데이터를 논리적 단위로 나눠 놓은 그룹이다. 특성(characteristic)이라 불리는 더 작은 데이터 단위를 하나 이상 포함하고 있다. 각 서비스는 `UUID`를 갖고 있다.
  * `Characteristic` : `GATT`에서 가장 작은 데이터 단위. 단 하나의 데이터만 포함한다. 특성도 `UUID`를 가지고 있다.
  * `Profile` : 서비스의 묶음으로, 기본 데이터 표준 집합.
  
> ex) 심박수 측정 데이터를 관리하기 위한 서비스를 정의한다면, 해당 서비스는 실제 심박 수치 뿐만 아니라, 모델 번호, 또는 보안 관련 파라미터 등의 정보를 관리하는 다수의 특성을 포함할 수 있다.  
  
> 정보는 주는쪽이 Server 다. 즉, 주변기기는 GATT Server, 중심기기는 GATT Client 다.
  
## Web Bluetooth API

통신을 위해 필요한 것
1단계 : `Central` 과 `Peripheral` 사이에 연결이 이루어 져야한다. 
```javascript
let characteristic;
const connect = () => async {
  const device = await navigator.bluetooth
                   .requestDevice({
                      filters:[{services:[ 'heart_rate' ]}]
                   });
  const server = await device.gatt.connect();
  const service = await server.getPrimaryService('heart_rate');
  characteristic = await service.getCharacteristic('heart_rate_measurement');
}
async function startNotification() {
  return await characteristic.startNotifications();
}
async function stopNotification() {
  return await characteristic.stopNotifications();
}
```
아래와 같이 filter 를 이용하면, `heart_rate` 서비스를 갖고있는 기기 목록만을 확인할 수 있다.  
filter 를 사용하지 않는다면, 페어링 할 수 있는 수많은 기기목록이 나타나기 때문에 혼동이 생길 수 있다.   
```javascript
const device = await navigator.bluetooth
                   .requestDevice({
                      filters:[{services:[ 'heart_rate' ]}]
                   });
```
사용자가 기기를 선택하고 페어링을 하였을 때 비로소 연결이 생성된다.    
```javascript
const server = await device.gatt.connect();
```
`Characteristic`은 기기의 특정 기능을 의미하며, `unsigned integer array`에 값을 저장한다.  
  
```javascript
const service = await server.getPrimaryService('heart_rate');
characteristic = await service.getCharacteristic('heart_rate_measurement');
```
  
## 기기의 `UUID`는 어떻게 알 수 있는가? 
BLE 디바이스의 연결을 위해 필요한 `UUID`는 두 종류로 분류할 수 있다.
1. 'heart_rate'와 같이 Bluetooth SIG(Special Internet Group)에 의해 관리되고 있는 표준 서비스명 또는 16비트 숫자.   
2. 기존의 표준과 다르게 새롭게 만들어진 services 와 characteristics 를 위한 128비트 숫자  
  
1번의 경우 Bluetooth SIG Website 에서 쉽게 확인 할 수 있다.  
하지만 2번의 경우 해당 기기의 API 혹은 SDK 에서 확인해야 하고, 불가능할 경우, nRF Connect App 을 이용해 해당 기기를 찾아 확인할 수 있다.  