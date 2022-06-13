---
title : "20대 후반 비전공자, 백엔드 개발자로 취업하기까지"
excerpt : "대학원에서 배터리를 연구하다 자퇴하고, 학원강사로 근근히 먹고 살다 개발자가 된 내 이야기"
author_profile: true
sidebar:
  nav: "sidebar"
category: Life
---

## **이 글을 쓰게된 이유**

약 7개월의 독학끝에 만 28살의 나이로 현재 회사에 합격해 개발자가 되었다.  

솔직히 아직 개발자라고 하기에는 많이 민망하다. 모르는 것이 너무 많고, 제대로 아는것도 별로 없다.  
  
내가 해온 방법이 무조건 정답도 아니고, "이렇게 하면 너도 개발자가 될 수 있어" 라는 말도 안되는 희망고문을 하고 싶지도 않다.   

그저 내 글이 새로울 출발을 하기 위해 애쓰고 있는 당신의 불안과 "다른 사람들은 어떻게 공부하는걸까?" 와 같은 궁금증을 조금이나마 해소 시켜 줄 수 있기를 바란다.    
  
## **백엔드 개발자 로드맵**

백엔드 개발자가 되려면 어떤 공부를 해야하는지 찾아본 사람이라면 누구나 한번쯤 봤을 로드맵이 있다.  
  
부끄럽지만 나는 아직도 로드맵의 내용 중 절반 이상은 이름만 들어본 수준이다.  

**즉 저 로드맵에 적혀있는 모든 내용을 공부해야만 취업을 할 수 있는건 아니다. 너무 걱정말자**  

![image.png](/assets/images/life/loadmap.png)  

내 기준에서, '백엔드 개발자'가 되기 위해 공부를 시작한 사람에게 필요한 지식은 `API` 까지다.  

물론 아직도 배워야 할 내용이 너무 많다고 생각할 수 있다. 그래도 절반은 덜어냈다.  

그 아래 지식은 자신만의 프로젝트를 만들고 나서 조금씩 보완해나갈 부분이다.  

사실 ***필요하다고 느낄 때 공부해야 한다고 믿는*** 내 기준에서는 로드맵의 그림도 마음에 들지는 않는다.       

예를 들어 `인터넷`, `OS에 대한 전반적인 지식`에 포함된 지식은 개발 공부를 막 시작하는 사람에겐 잘 와닿지도 않는다.   

나는 프로젝트를 진행하면서 `HTTP` 에 대한 이해의 필요성을 느꼈고, 프로젝트를 배포할 때 처음으로 `터미널 명령어`의 필요성을 느꼈다.  

## **독학으로 공부한 과정**  
간단하게 순서와 기간으로 얘기를 하자면 아래와 같다.  
### **1. Youtube 생활코딩 강의 - 약 2주**
'egoing'님은 천사다. 무한 칭찬으로 자신감을 갖게 해주고, 정말 기초적인 내용을 아주 쉽게 설명해준다. `WEB1`, `WEB2` 영상들을 봤다.

### **2. Java - 약 2개월** 
흔히들 한국은 '자바공화국' 이라는 말을 한다. 수많은 프로그래밍 언어들이 있지만, 단연코 한국에서 제일 수요가 많은 언어는 `Java`라는 사실을 부정할 순 없다.  
  
많은 사람들이 추천했던 '자바의 정석'책을 사서 공부했다. 변수선언, 조건문, 반복문, 클래스와 같이 책의 앞부분에 있는 내용조차 내가 맞게 이해한건지 확신을 갖을 수 없었다.  
  
돌이켜 보면 "연봉, 개발문화, 기술스택" 등의 조건이, 주관적으로 보았을 때, 좋다고 느껴지는 회사에서 사용하는 언어의 비율은 `Java`, `Python`, `JavaScript` 모두 비슷해보인다.    

### **3. SQL - 약 1주**
`MariaDB`를 기준으로 유튜브와 인터넷 블로그에 있는 글을 보며 공부했다. 

### **4. Spring Framework - 약 1개월**
인프런에 있는 김영한님의 강의들을 아래 순서대로 공부했다.
강의를 한번 듣고 완전히 이해하진 못했다. 당장 깊은 이해보다는 프로젝트를 진행하기 위해 프레임워크를 사용하는 방법을 익히는데 초점을 맞췄다.  
  * 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 (무료)
  * 스프링의 핵심 원리 - 기본편  
  * 모든 개발자를 위한 HTTP 웹 기본지식
  * 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술   
  * 스프링 MVC 2편 - 백엔드 웹 개발 활용 기술

### **5. 개인 프로젝트 - 약 4.5개월**
**프로젝트를 어디서부터 시작해야 할지, 전혀 감조차 오지 않았다.** 약 2주에 가까운 고민과 검색 끝에 아래와 같이 프로젝트를 시작 할 수 있었다. 많은 사람들이 이야기하듯 강의나 책을 보며 공부하는 것 보다는 프로젝트를 진행하는 과정에서 배우는게 훨씬 많았다.   

  * **주제 정하기**  
    당시에 강사로 일하던 학원에서 사용하는 웹서비스들이 눈에 들어왔다. 학원 선생님들이 문제를 출제하고, 학생들의 오답을 관리할 수 있도록 돕는 서비스였다. 
    고3 학생들을 가르치다 보니 기출문제들을 풀어주곤 했는데, 문득 무료로 공개되어져 있는 기출문제들로 **학생들이 스스로 오답 관리를 할 수 있는 웹서비스**를 만들고 싶다는 생각이 들었다.
  * **DB 설계**  
    우선 내가 만들고자 하는 서비스에 필요한 최소한의 정보들을 생각해보았다. 최소한 문제들의 정보, 학생들의 정보를 저장해야 겠다는 생각을 했다. 프로젝트를 진행하며 처음 생각한 구조에 계속 수정을 해 나갔다.    
  * **기능 설계**  
    `Leetcode` 와 같은 알고리즘 문제 풀이 사이트를 참고했다. 예를 들면   
    문제를 과목, 풀이 여부 등의 조건으로 필터링 하는 기능이 있으면 좋겠다는 생각을 했다.  
    문제를 풀었을 때, 오답 또는 정답에 따라 표시를 해주는 기능이 있으면 좋겠다는 생각을 했다.  
    내 주요 고객(?) 들은 학생들 이기 때문에 소셜 로그인 기능이 필요하다고 생각했다.  
  * **구현 및 배포**  
    처음에는 '스프링 부트와 AWS로 혼자 구현하는 웹 서비스' 책을 많이 참고했다. 그 외에도 대학원에 시절에 단련된 구글링 실력이 큰 도움이 됐다. 
    프론트와 백을 분리하고 싶다는 욕심에 약 1개월을 투자해 `Vue.js`를 공부했다. 아래와 같이 틀이 잡히고 난 뒤에는 배포후 실제 사용자들의 피드백을 얻고 싶다는 생각을 하게되었다.  
    ![image.png](/assets/images/life/web-service-architecture.png)  
    `Docker`, `CI/CD` 와 같은 단어들을 이 때 처음 접했다. 하지만 계속 새로운 공부만 하다가는 취업이 너무 늦어질거라는 생각에 우선 쌩(?) 배포를 했다.  

### **6. 포트폴리오 만들기**  
`Notion` 을 이용해 포트폴리오를 만들었다. 역시나 '개발자 포트폴리오' 와 같은 키워드로 구글링을 해가며, 남들이 만든 포트폴리오를 참고했다.
나는 [이렇게](https://palm-cheque-a4f.notion.site/JinWoo-An-e4b3bd044f3245a487497c26e6883a9c) 만들었다.  

### **7. 입사지원**
나는 `프로그래머스`, `원티드`, `로켓펀치` 등의 사이트에서 제공하는 간편 지원 서비스를 통해 지원했었다. 솔직히 `잡코리아` 같은 사이트에 올라오는 구인 공고들은 
흔히 얘기하는 별로 좋지 않은 SI 회사들 같아 보였다.   
  
  [SI회사에 입사하기 전 읽으면 좋은 글 - SI의 단점](https://preamtree.tistory.com/133) 을 읽어보자.  

내 기준에서 직업과 일이라는게 단순히 돈을 벌기 위한 수단이 아니기 때문에, 발전하지 못한다는건 가장 큰 단점으로 보였다.  
내가 눈이 너무 높았었는지, 지원한 모든 서류전형에서 빛의 속도로 탈락하거나, 응답을 못받았었다.       
포기하지 말고, 열심히 지원을 해보자. 취업은 운칠기삼 이다.  