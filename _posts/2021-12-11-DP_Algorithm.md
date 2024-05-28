---
layout: post
title: (알고리즘) 1,2,3 더하기
tags:
  - 알고리즘
---

<br>

### [백준9095-1,2,3 더하기](https://www.acmicpc.net/problem/9095)

문제

---

```java
정수 4를 1, 2, 3의 합으로 나타내는 방법은 총 7가지가 있다. 합을 나타낼 때는 수를 1개 이상 사용해야 한다.
1+1+1+1
1+1+2
1+2+1
2+1+1
2+2
1+3
3+1
  
정수 n이 주어졌을 때, n을 1, 2, 3의 합으로 나타내는 방법의 수를 구하는 프로그램을 작성하시오.
```

<br>

풀이

---

- 1,2,3을 가지고 모든 조합을 다 구해보기

```java
public class Main {

    public static int[] nums = {1,2,3};
    public static int ret = 0;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int tc = Integer.parseInt(br.readLine());
        StringBuilder sb = new StringBuilder();
      
        for(int t = 0; t<tc; t++){
            int n = Integer.parseInt(br.readLine());

            ret = 0;
            go(0,n);
            sb.append(ret+"\n");
        }

        System.out.println(sb.toString());
    }


    public static void go(int sum,int n){

        if(sum==n){
            ret++;
            return;
        }

        if(sum>n){
            return ;
        }

        for(int i=0; i<nums.length; i++){   //1,2,3
            go(sum+nums[i],n);   
        }

    }


}

```

<br>

<br>

### [백준15988-1,2,3 더하기(3)](https://www.acmicpc.net/problem/15988)

풀이

---

- 위 문제와 내용은 동일한데 입력으로 받는 정수의 범위가 훨씬 크다.
- 다이나믹 프로그래밍을 이용해서 dp[n]값 (1,2,3의 합으로 n을 표현할 수 있는 방법)을 미리 저장해놓고, 테스트 케이스로 들어온 입력값마다 dp[n]값을 사용하도록 했다.

```java
public class Main {


    public static void main(String[] args) throws IOException {


        //dp구하기
        long[] dp = new long[1000001];

        dp[0] = 0; dp[1] = 1; dp[2] = 2; dp[3] = 4;

        for(int i=4; i<1000001; i++){
            dp[i] = (dp[i-1] + dp[i-2] + dp[i-3])%1000000009;
        }


        //숫자입력받기
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int tc = Integer.parseInt(br.readLine());
        StringBuilder sb = new StringBuilder();
        for(int t = 0; t<tc; t++){
            int n = Integer.parseInt(br.readLine());
            sb.append(dp[n]+"\n");
        }

        System.out.println(sb.toString());

    }

}

```

