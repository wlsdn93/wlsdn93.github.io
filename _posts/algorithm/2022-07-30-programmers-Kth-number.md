---
title : "JS Array.prototype.sort()는 생각처럼 동작하지 않는다"
excerpt : "feat. 프로그래머스 K번째 수 찾기"
author_profile: true
sidebar:
  nav: "sidebar"
category: Algorithm
---

**[프로그래머스 Level 1 : K번째 수](https://school.programmers.co.kr/learn/courses/30/lessons/42748)**  

평화로운 주말, 기분 전환을 위해 간단한 코딩 테스트 문제를 풀다 중요한 걸 배웠다 !

```javascript
const array = [1, 30, 4, 21, 100000];
array.sort();
console.log(array);
```

위 코드를 실행하면 어처구니 없게도, **`[1, 100000, 21, 30, 4]`** 이 출력 된다..

그 이유는 `sort()` 함수에 `compareFunction`을 전달하지 않을 경우, **배열속 요소를 문자열로 변환하고, 유니 코드 포인트를 기준으로 대소 비교를 수행하기 때문**이다.  

이걸 몰라서... 나머지 케이스는 모두 통과하는데, 왜 2번 케이스만 통과가 안되는건지 당황했었다.  

`sort()` 에 숫자 대소비교를 위한 `(a, b) => { return a - b }` 함수를 전달하여 해결 하였다. 

```javascript
const solution = (array, commands) => {
    return commands.reduce((acc, cur) => {
        return [...acc, array.slice(cur[0] - 1, cur[1]).sort((a, b) => { 
            return a - b })[cur[2] - 1]]
    }, [])
}
```


### 참고자료  
**[MDN Array.prototype.sort() docs](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)**