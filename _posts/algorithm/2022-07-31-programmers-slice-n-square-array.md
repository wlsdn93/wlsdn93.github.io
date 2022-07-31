---
title : "N x N 배열 자르기"
excerpt : "프로그래머스 월간코드챌린지 시즌3 Level2"
author_profile: true
sidebar:
  nav: "sidebar"
category: Algorithm
---
### **[N x N 배열 자르기](https://school.programmers.co.kr/learn/courses/30/lessons/87390)**

처음에는 문제의 설명과 같은 방식으로 풀어보았다.
1. `N`, `left`, `right` 3가지 매개변수가 주어진다.
2. `N x N` 크기의 배열을 만들고
3. `i`번째 행은 `i`번째 열 까지, 숫자 `i` 를 채운 뒤, 이후 마지막 N번째 행까지 행과 같은 값을 채운다.
4. `1 ~ N`번째 행을 모두 이어, **하나의 배열**을 만든다.
5. 새로 만들어진 배열의 left ~ right 번째 인덱스 요소만을 남기고 나머지 값을 지운다.  
  
```javascript
const solution = (n, L, R) => {
    return Array(n).fill(n).map((_, rowIndex) => {
        return Array(n).fill(rowIndex + 1).map((num, colIndex) => {
            return colIndex < rowIndex ? num : colIndex + 1
        })
    }).flatMap(n => n).slice(L, R + 1)
}
``` 
테스트를 잘 통과하길래 당연히 통과할 줄 알았지만, `실패 (signal: aborted (core dumped))` 라는 처음보는 에러를 만났다.  
 
찾아보니 메모리를 너무 많이 사용해서 발생하는 오류라는데.. 시간초과는 자주 당해봤지만 메모리 초과는 처음 당해보았다.  

근데 그럼 그냥 `Out Of Memory` 를 던져주지..?  

**역시 `Level2`부터는 정직하게 풀면 통과할 수 없다 !!**   

메모리 사용을 줄이기 위한, 접근 전략 다시 생각해보았다.

```javascript
/*
 L번째 ~ R번째 배열 바로 만들려면?
 ex)
 [1, 2, 3, 2, 2, 3, 3, 3, 3]
        ^        ^ 
        L        R
 3 x 3 행열이므로
 L 2 ==> 1행 3열 
 R 5 ==> 2행 3열         

 평탄화된 N x N 행렬로부터 k번째 요소의 값을 정하려면, 몫과 나머지를 활용
 row = k / N + 1
 col = k % N + 1 
 value = Math.max(row, col)
*/
```
다행히 간식을 주워먹으며 뒹굴거리다 보니 새로운 아이디어가 금방 떠올랐다.  

```javascript
const solution = (n, L, R) => {
    return Array(R - L  + 1)
            .fill(L)
            .map((_, index) => L + index)
            .map((elem, _) => Math.max(parseInt(elem / n) + 1, elem % n + 1))
}
```