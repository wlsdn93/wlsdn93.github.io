---
title : "오픈소스면 무료니까 그냥 쓰면 되는거 아니야?"
excerpt : "오픈소스 라이브러리를 사용할 때 보이는 라이선스란 뭘까?"
author_profile: true
sidebar:
  nav: "sidebar"
toc: true
toc_sticky: true
category: DevBasic
---

> 당신은 취업준비를 위해 포트폴리오로 사용할 프로젝트를 진행하고 있다.  
>
> 당신이 만들고자 하는 서비스에 필요한 기능들을 생각해보자.  
>
> 예를 들어 **"QR코드 인식", "이미지로 부터 텍스트 추출"** 두 가지 기능이 필요하다면 당신은 어떻게 해야 할 까?    
>
> 당연히 이러한 기능을 제공하는 외부 라이브러리를 찾아볼 것이다.  
>
> 운 좋게도 오픈소스 라이브러리를 찾았고, 무사히 원하는 기능을 추가할 수 있었다.

대부분의 우리는 위와 같이, 오픈소스 라이브러리를 **아무런 고민없이** 바로 자신의 프로젝트에 사용한다.
   
**하지만** 이용한 오픈소스의 라이선스에 따라 **상업적 이용 가능 여부**, **코드 공개 의무** 등의 제약 사항이 발생하기 때문에 결코 가볍게 여겨선 안된다.  

만약 라이선스의 범위를 벗어나는 사용, 수정, 배포를 한 경우 저작권 침해로 인한, **법적 책임과 불이익**을 받을 수 있다는 것을 유념하자    

## **주요 라이선스 요약**

|  라이선스<br  | 상업적 이용 |  소스코드 공개의무   |  동일 라이선스 적용의무   |
|:---------:|:------:|:------------:|:---------------:|
|    GPL    |   O    |      O       |        O        |
|   LGPL    |   O    |      X       |        O        |
|    BSD    |   O    |      X       |        X        |
|    MIT    |   O    |      X       |        X        |
|  Apache   |   O    |      X       |        X        |


## **GPL / LGPL**  
자유소프트웨어재단(FSS)에서 제정하고 관리하는 라이선스  
**리눅스 커널 기본 라이선스로 유명하다.**  
* ### GPL
  GPL 라이선스가 적용된 오픈소스를 이용한 프로그램을 판매/배포 할 경우 아래의 규칙을 따라야 한다.  
1. 상업적 이용이 가능하다.  
2. 소스 코드를 용도에 따라 변경 할 수 있다.   
3. 판매/배포시 소스 코드를 요청하면 제공해야 한다.   
4. 파생 프로그램에도 동일한 GPL 라이선스 가 적용된다.  


* ### LGPL
 LGPL을 따르는 프로그램은 전체 소스코드를 공개하지 않고, 사용된 오픈소스 라이브러리에 대한 소스코드만 공개하면 된다.

  
## **BSD / MIT / Apache**  
허용적 라이선스(permissive license)로 분류되는 라이선스들이다.  
이들 라이선스가 적용된 오픈소스를 활용한 프로그램(파생품)은 공개 의무가 없다.   
***사실 이들 라이선스들 간에 큰 차이가 없어보인다...***

* ### BSD  
버클리 대학에서 제정. BSD 라이선스에는 조항의 갯수에 따라 분류된다.   
공통 의무사항으로는 소프트웨어 배포시 'BSD 라이선스내용', '저작권자 표기'를 함께 배포하는것이 있다.  
또한 BSD 라이선스를 적용받는 소프트웨어에 의해 피해가 발생하더라도, 그 책임을 저작권자에게 물을 수 없다.

* ### MIT
 BSD 라이선스를 기초로 MIT 대학에서 제정. 파생 프로그램을 오픈소스로 해야할 의무가 없다. 
   
* ### Apache 2.0
  아파치 소프트웨어 재단에서 제정한 라이선스 . 소스코드 공개 의무가 없지만, 아파치 라이선스 를 사용한다는 사실(출처표기)을 반드시 명시해야 한다. 
  또한 Apache 라이선스로 출원한 특허에 대해서는 소스코드 사용자에게 특허의 무제한적 사용을 허가한다.



## 참고하면 좋은 글 

**[Open Source Software Licenses 101: The BSD 3-Clause License](https://fossa.com/blog/open-source-software-licenses-101-bsd-3-clause-license/)**  

**[SKTelecom 오픈소스 가이드](https://sktelecom.github.io/guide/use/obligation/)**