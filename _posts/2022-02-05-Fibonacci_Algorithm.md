---
layout: post
title: (알고리즘) 피보나치 수열의 시간복잡도  
tags:
  - 알고리즘
---

<br>

### 재귀함수로 구현한 피보나치 수열의 시간복잡도 - **O(2^n)**

---

- 결론부터 말하자면 아래 코드처럼 재귀함수로 피보나치 수열을 구현했을 때의 시간 복잡도는 **O(2^n)** 이다

- ```java
   int Fibo(int x){
       if(x<=1) return x;
       return Fibo(x-1) + Fibo(x-2);
   }
  ```

- ![피보나치](https://github.com/AmyJJung/blog/blob/main/images/Fibo_img.png?raw=true)

- 맨 위 노드부터 쭉 타고 내려가면  `Fibo(n) -> Fibo(n-1) -> Fibo(n-2) ..... -> Fibo(1)` 이기 때문에, 높이가 n인 Binary Tree의 모습을 확인할 수 있다.

- 높이가 n인 Perfect Binary Tree의 노드 수는 2^n - 1이다

- 따라서, Fibo(n)의 시간복잡도는 **O( 2^n -1 ) = O( 2^n )** 이다

<br>

<br>

### 메모이제이션(Memoization)을 사용한 피보나치 수열의 시간복잡도 - O(n)

---

- ```java
  int Memo_Fibo(x){
      if(x<=1) return x;
      if(memo[x]!=0) return memo[x];
      return memo[x] = Memo_Fibo(x-1) + Memo_Fibo(x-2);
  }
  ```

- 위 코드처럼 각각의 단계에서 값들을 기록해두면 중복해서 호출할 필요가 없기 때문에 시간복잡도는 O(n)이 된다.
