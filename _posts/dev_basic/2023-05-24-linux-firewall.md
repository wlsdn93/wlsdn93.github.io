---
title : "개발서버에 원격접속을 허용하면서, 안전하게 관리할 수 있을까?"
excerpt : ""
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---
  
최근 회사에서 개발용으로 사용중인 로컬 PC에 원격 접속을 허용해줘야 하는 상황이 생겨 방화벽에 대해 공부 해볼 기회를 얻었습니다.  
  
네트워크 엔지니어 분들이나 다른 시니어 개발자 분들이 보시면 귀여워 하실 내용이겠지만, 누군가에겐 도움이 되길 바라며  
  
공부한 내용을 정리하기 위해 글을 작성합니다.  

## 라우터 포트포워딩
  
> 포트 포워딩에 대해 처음부터 설명하기엔 글이 너무 길어져, [블로그 링크](https://lamanus.kr/59)를 첨부합니다.   
> 본 글에서는 IPTIME 무선 공유기(라우터) 제품을 기준으로 설명을 하고 있습니다.  
  
원격 접속을 허용하려면, 우선 공유기(라우터)에 포트 포워딩 규칙을 추가 해주어야 합니다.  

브라우저를 이용해 기본 게이트웨이 IP주소(일반적으로 192.168.0.1)에 접속하면, IPTIME의 GUI를 확인할 수 있습니다.

![image](/assets/images/web-basic-images/iptime-gui-login.png)

ID/PWD를 입력하고, "관리도구 - 고급설정 - NAT/라우터 관리 - 포트포워드 설정" 으로 접속합니다.

![image](/assets/images/web-basic-images/iptime-gui-port-forward.png)
  
개발서버 PC가 할당받은 '내부IP', 원격 접속시 사용할 '외부 포트번호' 그리고 포워딩 목적지인 '내부 포트번호' 를 입력하고 추가해줍니다.  

현재 위 예시 사진의 포워딩 규칙은 라우터의 공인IP(고정IP를 할당받지 못한 경우, 주기적으로 변경될 수 있음) 10022포트로 들어오는 패킷을 192.168.0.119라는 사설IP주소를 할당받은 호스트의 22포트로 포워딩 시킨다는 의미입니다.

## 방화벽 설정

방화벽은 기본적으로 모든 요청을 차단하고, 일부만을 허용하는 화이트리스트 방식을 사용합니다.

포트 포워딩을 해주더라도, 해당 패킷을 전달받은 PC의 방화벽에 화이트리스트로 등록이 되있지 않다면, 해당 패킷은 버려지게 됩니다. 

클라우드에서 보안그룹 설정을 통해 인바운드/아웃바운드 규칙을 적용하는것과 비슷하다고 생각하시면 됩니다.  

지금부터 설명하는 내용은 `firewall-cmd` 명령어 대신 `/etc/firewalld/zone/` 경로에 위치한 `xml` 파일들을 직접 수정하여도 동일하게 적용할 수 있습니다. 

RHEL7 부터 방화벽을 관리하는 데몬이 `firewalld` 로 변경되었고, 복잡한 `iptabels` 명령어 대신, `firewall-cmd` 명령어를 사용하는 것을 권장하고 있습니다. 

> `Ubuntu` 에는 `firewalld` 가 기본적으로 설치되있지 않기때문에, `firewalld`를 먼저 설치 해주어야 합니다.

우선 현재 방화벽 설정을 확인해보겠습니다.  

```shell
# zone을 명시하지 않는 경우, default zone(일반적으론 public)의 설정을 출력합니다.
sudo firewall-cmd --list-all
```

```text
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp34s0
  sources: 
  services: cockpit dhcpv6-client
  ports: 8080/tcp
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

현재 라우터에서 10022 포트로 들어온 패킷을 22 포트로 포워딩을 시켜주고 있지만  
 
방화벽에서는 22 포트를 공개하지 않고 있기 때문에, 해당 패킷들이 버려지고 있는 상태입니다.  

이 문제를 해결하는 방법은 간단합니다. 포트 또는 서비스를 방화벽의 화이트 리스트에 추가 해주면 됩니다.  

```shell
# SSH 프로토콜의 well-known 포트인 22번 포트를 추가해줍니다. 
sudo firewall-cmd --permanent --add-port=22/tcp

# firewalld 에서 미리 정의해둔 서비스를 등록해도 됩니다. 
# 포트와 서비스 둘다 추가해도, 포트 또는 서비스 둘 중 하나만 추가해도 동일하게 동작합니다. 
sudo firewall-cmd --permanent --add-service=ssh

# --permanent 옵션을 사용하여 추가한 규칙은 리로드후에 적용 됩니다.
sudo firewall-cmd --reload
```

다시 방화벽 규칙을 출력해보면, 아래와 같이 바뀐것을 볼 수 있습니다.

```text
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp34s0
  sources: 
  services: cockpit dhcpv6-client ssh
  ports: 8080/tcp 22/tcp
  protocols: 
  forward: yes
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```

이제 외부에서 호스트 PC로의 원격접속이 가능해졌습니다.  

하지만 현재 방화벽 규칙에서는 SSH 인증에 사용하는 '개인키' 혹은 'ID/PWD'를 사용자 부주의로 외부에 노출시키거나 탈취당하게 되면, 누구나(어디에서나) 호스트로의 원격접속이 가능하다는 문제점이 있습니다.  
  
물론 인증수단을 잘 관리하는게 제일 중요합니다! 하지만 방화벽 규칙을 잘 설정 해둔다면 서버를 조금 더 안전하게 관리할 수 있습니다.   
  
우선 현재의 규칙에 문제가 있다는 것을 알았으니, 먼저 `public zone` 에 추가해두었던 22/tcp 포트와 ssh 서비스를 제거 하겠습니다.  

```shell
# 22/TCP 포트를 제거합니다. 
sudo firewall-cmd --zone=public --permanent --remove-port=22/tcp

# SSH 서비스를 제거합니다. 
sudo firewall-cmd --zone=public --permanent --remove-service=ssh

# 수정된 방화벽 규칙을 다시 로드합니다. 
sudo firewall-cmd --reload
```

이제 우리가 해야하는 것은 **어떤 사용자들에게 원격 접속을 허용할 것인지 인지하고,** 사용자들을 각각의 **그룹으로 묶는 것** 입니다.  

아래와 같은 상황을 가정해보겠습니다. 

> **원격 접속을 허용할 가상의 사용자 목록(IP주소 혹은 네트워크)**
> 1. 동일한 네트워크에 위치한 모든 사용자 (192.168.0.0/24)  
> 2. 회사에서 사용중인 다른 네트워크의 공인IP (Z_IP)  
> 3. 재택 근무중인 직원 A 공유기의 공인IP (A_IP)  
> 4. 재택 근무중인 직원 B 공유기의 공인IP (B_IP)  

이제 이 사용자 목록을 두 개의 그룹으로 나눠 보겠습니다.  

> **그룹 1**  
> 192.168.0.0/24 
>  
> **그룹 2**  
> Z_IP,  A_IP,  B_IP 

사용자를 각각의 그룹으로 분류한 이유는 이들에게 각각 서로 다른 규칙을 적용하기 위해서 입니다.  

그리고 `firewalld` 의 `zone` 을 이용하면, 각각의 그룹에게 서로 다른 규칙을 적용 할 수 있습니다.  

앞으로 그룹 1을 `internal zone`, 그룹 2를 `trusted zone` 에 위치시켜 보겠습니다.  

각각의 그룹을 특정 존에 위치 시킨다는것은 해당 존의 `source`에 그룹원들의 IP주소 혹은 네트워크를 등록하는 것을 의미합니다.  

```shell
# 원격접속을 허용하고자 하는 호스트가 위치한 네트워크를 internal zone의 source에 추가합니다.
sudo firewall-cmd --zone=internal --permanent --add-source=192.168.0.0/24

# 원격 접속을 허용하고자 하는 사용자들의 IP주소를 internal zone의 source에 추가합니다.
# 회사에서 사용중인 다른 네트워크의 공인 IP
sudo firewall-cmd --zone=trusted --permanent --add-source=Z_IP
# 재택직원 A 공인 IP
sudo firewall-cmd --zone=trusted --permanent --add-source=A_IP
# 재택직원 B 공인 IP
sudo firewall-cmd --zone=trusted --permanent --add-source=B_IP
```

각각의 사용자들을 분류하는 작업이 끝났습니다.  

이제 마지막으로 각각의 존에서 허용하고자 하는 서비스와 포트를 추가해줍니다.  

```shell
# internal zone에 위치한 사용자들이 SSH 프로토콜을 이용해 원격으로 호스트에 접속이 가능하도록 허용합니다.
# 앞서 얘기 했듯 둘 중 하나만 추가해도 됩니다.
sudo firewall-cmd --zone=internal --permanent --add-port=22/tcp
sudo firewall-cmd --zone=internal --permanent --add-service=ssh

# trusted zone에 위치한 사용자들이 SSH 프로토콜을 이용해 원격으로 호스트에 접속이 가능하도록 허용합니다.
# 앞서 얘기 했듯 둘 중 하나만 추가해도 됩니다.
sudo firewall-cmd --zone=trusted --permanent --add-port=22/tcp
sudo firewall-cmd --zone=trusted --permanent --add-service=ssh

# trusted zone에 위치한 사용자들이 8080포트와 연결된 서비스에 접근이 가능하도록 허용합니다.
sudo firewall-cmd --zone=trusted --permanent --add-port=8080/tcp

# 수정된 방화벽 규칙을 다시 로드합니다. 
sudo firewall-cmd --reload
```

