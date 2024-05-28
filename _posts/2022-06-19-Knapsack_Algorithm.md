---
layout: post
title: (알고리즘) 냅색 알고리즘 
tags:
  - 알고리즘
---

<br>

### 냅색 알고리즘 문제 풀어보기 - [백준 12865](https://www.acmicpc.net/problem/12865)

---

### 1. TopDown

- `dp[i][j] : 가방에 담긴 물건의 최대 무게 합이 i일때 j번째 물건까지 담을 수 있는 최대 가치`
  
  - j번째 물건의 무게가 i보다 클 경우 : `dp[i][j] = dp[i][j-1]`
  
  - j번째 물건의 무게가 i보다 작거나 같을 경우 : `Math.max(dp[i][j-1], dp[i-w[j]][j-1] + v[j])`

<br>

```java
public class Main {

    public static Integer[][] dp;
    public static int[] weight;
    public static int[] value;

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int totalW = Integer.parseInt(st.nextToken());

        weight = new int[n+1];
        value = new int[n+1];
        dp = new Integer[totalW+1][n+1];


        for(int i=1; i<=n; i++){
            st = new StringTokenizer(br.readLine());
            weight[i] = Integer.parseInt(st.nextToken());
            value[i] = Integer.parseInt(st.nextToken());
        }

        System.out.println(  knapsack(totalW,n) );


    }


    public static int knapsack( int curWeight, int num ){

        if(num==0 || curWeight==0) return 0;

        if(dp[curWeight][num] != null) return dp[curWeight][num];

        int temp  = knapsack(curWeight,num-1);
        if(weight[num] <= curWeight ) temp = Math.max( temp,  knapsack(curWeight-weight[num],num-1) + value[num]);

        return dp[curWeight][num] = temp;

    }


}

```

<br>

<br>

### 2. BottomUp 1

- `dp[i][j] : 가방에 담긴 물건의 최대 무게 합이 i일때 j번째 물건까지 담을 수 있는 최대 가치`
  
  - j번째 물건의 무게가 i보다 클 경우 : `dp[i][j] = dp[i][j-1]`
  
  - j번째 물건의 무게가 i보다 작거나 같을 경우 : `Math.max(dp[i][j-1], dp[i-w[j]][j-1] + v[j])`

<br>

```java
public class Main {

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int totalW = Integer.parseInt(st.nextToken());

        int[] weight = new int[n];
        int[] value = new int[n];

        for(int i=0; i<n; i++){

            st = new StringTokenizer(br.readLine());
            weight[i] = Integer.parseInt(st.nextToken());
            value[i] = Integer.parseInt(st.nextToken());

        }


        int[][] dp = new int[totalW+1][n+1];

        for(int i=1; i<=totalW; i++){
            for(int j=1; j<=n; j++){

                int w = weight[j-1];
                int v = value[j-1];

                dp[i][j] = dp[i][j-1];

                if(w<=i){   //현재 물건 담을 수 있음
                    dp[i][j] = Math.max(dp[i][j],dp[i-w][j-1] + v);
                }

            }

        }


        System.out.println(dp[totalW][n]);

    }
}

```

<br>

<br>

### 3. BottomUp 2

- dp 배열을 1차원 배열로 구현해볼 수 있다.

- 주의해야할 점은 물건은 가방에 한번 담으며 끝이기 때문에 여러번 중복해서 담을 수 없다.

- 따라서 물건의 무게를 뒤에서부터 넣어보면 한 물건을 중복으로 넣는 경우를 피할 수 있다.

<br>

```java
public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int totalW = Integer.parseInt(st.nextToken());

        int[] weight = new int[n];
        int[] value = new int[n];

        for(int i=0; i<n; i++){

            st = new StringTokenizer(br.readLine());
            weight[i] = Integer.parseInt(st.nextToken());
            value[i] = Integer.parseInt(st.nextToken());

        }


        int[] dp = new int[totalW+1];


        for(int i=0; i<n; i++){

            int w = weight[i];
            int v = value[i];

            for(int j=totalW; j>=w; j--) {
                dp[j] = Math.max(dp[j],dp[j-w] + v);
            }
        }


        System.out.println(dp[totalW]);


    }
}

```
