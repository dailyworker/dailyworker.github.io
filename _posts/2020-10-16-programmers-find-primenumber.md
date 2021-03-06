---
title: "[PS 프로그래머스] - 소수 찾기"
date: 2020-10-16 17:00:00 +0900
tags:
  - ProblemSolving
  - Java
  - Algorithm
---

## STEP 1. 소수란?

* 1과 자기자신을 제외하고 나누어 지지 않는 수를 말한다. (약수가 1과 자기자신만 존재)

:point_right: **위의 소수 개념으로 아주 단순하게 문제를 해결해보고자 한다.** 

## STEP 2. 문제

### 설명
1부터 입력받은 숫자 n 사이에 있는 소수의 개수를 반환하는 함수, solution을 만들어 보세요.

소수는 1과 자기 자신으로만 나누어지는 수를 의미합니다.
(1은 소수가 아닙니다.)

### 제한조건
n은 2이상 1000000이하의 자연수입니다.

즉, 주어진 n에 대해서 n만큼 돌면서 소수면 answer의 값을 증가시키면 되는 아주 단순한 아이디어로 착안했다.

위의 아이디어를 가지고 아주 단순한 소수 찾기 알고리즘을 짤 수 있을 것이다.

```java
class Solution {
    public int solution(int n) {
        int answer = 0; // 0과 1은 소수가 아니기에 i를 2로 초기화하여 루프를 돌게 설계 
        for(int i = 2; i <= n; i++){ // (1) 외부루프 : n-2 호출 
			// 소수가 맞을 경우에 answer 값이 증가되게끔 설계 
            if(isPrimeNumber(i)){ // (2) n-2회 호출 
                answer++;
            }
        }
        return answer;
    }
    
    public boolean isPrimeNumber(int num) {
        for(int i = 2; i < num; i++){ // (3) n-2회 호출 
             if(num % i == 0) return false; // (4) 외부루프 * 내부루프 (n-2)*(n-2)/2
        } 
        return true;
    }
}
```

하지만, 이렇게 제출할 경우에 **시간 초과**가 발생한다.

아주 단순하게 보면, 2가 n만큼 돈다고 가정하고 isPrimeNumber가 몇 번 호출 될 것인지 예상해보자.

그렇다면 아마 n-2번 호출 됨을 알 수가 있다.

또한, 함수 내부적으로 다시 자기 자신 직전까지 나누어서 소수인지 아닌지 판별할 수 있다.  따라서 등차수열의 합 공식을 활용하여 $$(n-2) * (n-2) / 2$$로 볼 수 있다.

대충 설명하면 $$O(N^2)$$ 의 시간복잡도를 가진다고 볼 수 있다.

그렇다면 이 시간복잡도를 어떻게 줄일 수 있을까?

## STEP 3. 약수의 특징
먼저, 110이라는 숫자를 봐보자 110은 `1, 2, 5, 10, 11, 22, 55, 110`을 약수로 갖는다.

양수는 아래와 같은 성질을 만족한다.

양의 정수 `a, n, b`에 대해서 $${n = a*b(단, a<=b)}$$ 라고 가정하면 a와 b는 n의 약수가 됨을 알 수가 있다.

위의 식 $n = a * b$ 에서 약수 a가 존재하면, 곱해서 n이 되는 짝 b는 항상 존재한다.

모든 약수는 곱해서 n이 되는 쌍이 존재하며, (a, b)꼴 쌍에서 a ≤ b일 경우 a의 최대 값은 $sqrt(n)$ 이 된다.

이러한 이유는 a ≤ b가 성립하는 상태에서 a가 가장 커질 수 있는 경우는 a == b인 경우이고, 이는 $n = a * a$꼴이다.

다시 이제 위의 110의 약수를 봐보자. 110 = 10 * 11  이므로, 10과 11은 110의 약수가 맞다.

그리고 10이 존재하면 곱해서 110이 되는 짝 11은 항상 존재함을 증명할 수 있다.

여기서 ( a, b )꼴을 묶어보면 (1 * 110), (2 * 55), (5 * 22), (10 * 11), (22 * 5), (55 * 2) 이렇게 존재한다. 

여기 서로쌍에서 a가 b보다 커지는 (22 * 5), (55 * 2)는 볼 필요 없는 이유는 중복되는 쌍이기 때문이다.

따라서, a ≤ b라고 할 경우에 a가 가장 커질 수 있는 경우는 a == b일 경우이다.

우리의 110은 그러한 케이스가 아니기 때문에 $sqrt(110)$ = 10.48... 이 나오므로 해당 공식의 증명이 가능하다.

**a가 10이 될때까지만 봐도 된다는 이야기다.**


## STEP 4. 정리

자 정리해보자.

소수는 2 ~ (N-1) 까지의 범위에서 약수를 가지지 않는다.

약수가 존재하는 숫자의 약수들 중 반은 1 ~  $sqrt(n)$ 의 범위 안에 속하게 된다.

해당 숫자의 2 ~  $sqrt(n)$ 까지만 확인해도 된다!!

따라서 코드를 다음과 같이 수정할 수가 있다.

```java
class Solution {
    public int solution(int n) {
        int answer = 0;
        
        for(int i = 2; i <= n; i++){
            if(isPrimeNumber(i)){
                answer++;
            }
        }
        return answer;
    }
    
    public boolean isPrimeNumber(int num) {
        for(int i = 2; i <= Math.sqrt(num); i++){
             if(num % i == 0) return false;
        } 
        return true;
    }
}
```