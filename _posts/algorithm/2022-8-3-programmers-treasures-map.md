---
title : "\"비트연산\", \"정규식(global flag)\", \"replaceAll()의 배신\"의 콜라보가 레벨 1 ?"
excerpt : "string.prototype.replaceAll() is not a function ???"
author_profile: true
sidebar:
  nav: "sidebar"
category: Algorithm
---

## [보물지도](https://school.programmers.co.kr/learn/courses/30/lessons/17681)  

프로그래머스 레벨 1 문제의 난이도는 정말 다양하지만.. 위 문제는 레벨 1 문제중 역대급으로 다양한 아이디어를 활용했다.

나의 최종 풀이는 아래와 같다.  
```javascript
function solution(n, arr1, arr2) {
    return arr1.map((elem, index) => elem | arr2[index])
    .map((elem, _) => elem.toString(2).padStart(n, "0"))
    .map((elem, _) => elem.replace(/1/g, "#").replace(/0/g, " "))
}
```

## `STEP 1` &nbsp; 비트연산  
**지도 1 또는 지도 2 중 어느 하나라도 벽인 부분은 전체 지도에서도 벽이다**  
즉 0 과 1 을 비교했을 때 1 이 되어야 하므로, `OR 연산(|)`을 수행했다.  

## `STEP 2` &nbsp; 2진수 변환 및 자릿수 맞추기
비트연산 수행 결과를 2진수로 변환하기 위해 `Object.prototye.toString(2)` 를 사용했다.     
2진수(문자열)의 자릿수를 맞추기 위해 `string.prototype.padStart(targetLength, padString)` 함수를 이용했다.
  
`padStart` 함수를 모르던 나는 처음 시도때 아래와 같이 풀었었다. 
```javascript
    /* ~ */
    .map((elem, _) => {
        const bi = elem.toString(2);
        return bi.length !== n ? "0".repeat(n - bi.length) + bi : bi;
    })
    /* ~ */
```

## `STEP 3` &nbsp; 숫자 1은 #으로, 0은 공백으로 치환하기
모든 1과 0을 각각 #, 공백으로 치환해야 했기 때문에, 당연히(?) `replaceAll` 함수를 사용하려 했다.  
하지만 배열의 모든 요소가 분명히 `string` 인데도, **replaceAll is not a function** 라는 오류를 뿜어댔다.  
대안책으로 `global flag` 를 사용한 정규식과 `replace` 함수를 이용했다.   

## 참고자료
### [replaceAll is not a function Error in JavaScript](https://bobbyhadz.com/blog/javascript-typeerror-replaceall-is-not-a-function)