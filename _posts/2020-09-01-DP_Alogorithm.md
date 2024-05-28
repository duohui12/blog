---
layout: post
title: (알고리즘) 다이나믹 프로그래밍(DP) 문제풀이
tags:
  - 알고리즘
---

<br>

> [이것이 취업을 위한 코딩 테스트다](http://www.yes24.com/Product/Goods/91433923) 책으로 학습한 내용을 정리한 글입니다

<br> 

### 1. 1로 만들기

---

- 정수 X가 주어질 때 사용할 수 있는 연산은 4가지이다.
  - X가 5로 나누어 떨어지면 5로 나눈다
  - X가 3로 나누어 떨어지면 3로 나눈다
  - X가 2로 나누어 떨어지면 2로 나눈다
  - X에서 1을 뺀다

- 정수 X가 주어졌을 때 위 연산을 사용해서 1을 만들려고 한다. 사용하는 연산의 최소 횟수 구하기

<br>

- 점화식
  - <b>Ai = min(Ai/5 , A i/3 , Ai/2 , Ai-1 ) +1</b>

<br>

```java
//bottom-up

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int n = Integer.parseInt(br.readLine());
int[] dp = new int[n+1];

for(int i=2; i<=n; i++) {
  dp[i] = dp[i-1] +1;
  if(i%2==0) dp[i] = Math.min(dp[i],dp[i/2]+1);
  if(i%3==0) dp[i] = Math.min(dp[i],dp[i/3]+1);
  if(i%5==0) dp[i] = Math.min(dp[i],dp[i/5]+1);
}

System.out.println(dp[n]);
```

<br>

### 2. 개미전사

---

- 개미전사가 정찰병에서 들키지 않고 식량창고를 약탈하기 이해서는 최소한 한 칸 이상 떨어진 식량창고를 약탈해야 한다. 

- 예를들어 식량창고 4개가 {1,3,1,5}와 같이 존재할 때, 연속한 식량창고는 약탈할 수 없지만, 두번째 식량창고와 네번째 식량창고를 선택하면 최대 3+5=8만큼의 식량을 얻을 수 있다.

<br>

- 점화식
  - <b>Ai = max(Ai-1,Ai-2+Ki) </b>
  - Ai : 맨 앞 창고에서부터 i번째 창고까지 순서대로 털 때 얻을 수 있는 최대 식량 / Ki : i번째 식량창고에 있는 식량의 양
  - i번째 식량창고는 연속한 i-1번째 식량창고와 함께 털 수 없다. 따라서 i번째 식량창고를 털지 안 털지를 결정함에 따라 최댓값을 구할 수 있다.
    - i번째 식량창고를 털 경우 : i-2번째 식량창고까지 얻을 수 있는 최대 식량 + i번째 식량 창고에 있는 식량의 양
    - i번째 식량창고를 털지 않을 경우 : i-1번째 식량창고까지 얻을 수 있는 최대 식량 (연속되는 i번째 창고의 식량을 더하지 않음)

<br>

```java
//bottom-up

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int n = Integer.parseInt(br.readLine());
int[] arr = new int[n];
int[] dp = new int[n];
StringTokenizer st = new StringTokenizer(br.readLine());
for(int i=0; i<n; i++) {
  arr[i] = Integer.parseInt(st.nextToken());
}

dp[0]=arr[0];
dp[1]=Math.max(arr[0], arr[1]);
for(int i=2; i<n; i++) {
  dp[i] = Math.max(dp[i-2]+arr[i],dp[i-1]);
}
System.out.println(dp[n-1]);
```

<br>

### 3. 바닥공사

---

- 가로의 길이가 N, 세로의 길이가 2인 직사각형 형태의 바닥이 있다. 이 바닥을 1X2, 2X1, 2X2 크기의 덮개로 채우고자 한다.
- 바닥을 채우는 모든 경우의 수를 구하기
- 2XN 크기의 바닥을 채우는 방법의 수를 796,796으로 나눈 나머지값을 출력

<br>

- 점화식
  - <b>Ai = Ai-1 + Ai-2 *2</b>
  - Ai : i번째 길이까지 덮개로 채울 수 있는 경우의 수
    - i-1번째 길이까지 이미 채워져 있는 경우에는 2X1덮개 하나를 채우는 방법만 존재한다.
    - i-2번째 길이까지 채운 후, i-1번째와 i번째를 채울 수 있는 방법은 2X2덮개를 채우거나, 1X2덮개를 채우는 방법이 존재한다. 

<br>

```java
//bottom-up

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int n = Integer.parseInt(br.readLine());
int[] dp = new int[n+1];

dp[1] = 1;
dp[2] = 3;
for(int i=3; i<=n; i++) {
  dp[i] = (dp[i-1] + dp[i-2]*2) % 796796;
}

System.out.println(dp[n]);
```

<br>

### 4. 효율적인 화폐구성

---

- N가지 종류의 화폐가 있다. 이 화폐를 최소한으로 이용해서 가치의 합이 M원이 되도록 하려고한다
- 각 화폐는 몇 개라도 사용할 수 있다. 
- (그리디 거스름돈 문제와 비슷하지만 큰 단위 화폐가 작은 단위의 배수가 아니라는 점이 다르다)

<br>

- 점화식

  - Ai : 금액 i를 만들 수 있는 최소한의 화폐 갯수 / 화폐의 단위 k
    - Ai-k를 만드는 방법이 존재하는 경우 : <b>Ai = min(Ai,Ai-k+1)</b>
    - Ai-k를 만드는 방법이 존재하지 않는 경우 : <b>Ai = 큰수</b>
  - 먼저 dp배열의 모든 값을 큰 수(특정 금액을 만들 수 있는 화폐 구성이 가능하지 않다는 의미)로 설정한다. 
  - dp[0] = 0 (화폐를 하나도 사용하지 않을 때 만들 수 있다)
  - 화폐의 단위마다 적은 금액부터 큰 금액까지 만들 수 있는 최소한의 화폐 개수를 찾아나간다

  <br>

  ```java
  //bottom-up
  
  BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  StringTokenizer st = new StringTokenizer(br.readLine());
  int n = Integer.parseInt(st.nextToken());  //N종류의 화폐, 1<=N<=100
  int m = Integer.parseInt(st.nextToken());  //1<=M<=10000
  int[] n_arr = new int[n];
  for(int i=0; i<n; i++) {
    n_arr[i] = Integer.parseInt(br.readLine());
  }
  
  int[] dp = new int[m+1];
  Arrays.fill(dp,10001);  
  dp[0] = 0;
  
  for(int i=0; i<n; i++) {
    int money = n_arr[i];  //먼저 화폐의 단위를 하나 잡고,
    for(int j=money; j<=m; j++) {
      dp[j] = Math.min(dp[j],dp[j-money]+1);  
    }
  }
  
  if(dp[m]==10001) System.out.println(-1);
  else System.out.println(dp[m]);
  ```

  <br>

### 3. 금광

---

- NXM 크기의 금광이 있다. 금광은 1X1 크기의 칸으로 나누어져 있으며, 각 칸은 특정한 크기의 금이 들어있다. 채굴자는 첫번째 열부터 출발해서 금을 캐기 시작한다. 맨 처음에는 첫번째 열의 어느 행에서든 출발할 수 있다. 이후 m번에 동안 매번 오른쪽 위, 오른쪽, 오른쪽 아래 3가지 중 하나의 위치로 이동해야 한다. 결과적으로 채굴자가 얻을 수 있는 금의 최대 크기를 구하자

<br>

- 점화식
  - <b>dp[i] [j] = array[i] [j] + max(dp[i-1] [j-1], dp[i] [j-1], dp[i+1] [j-1]) </b>

<br>

```java
//bottom-up

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
int testCase = Integer.parseInt(br.readLine());

for(int t=0; t<testCase; t++) {
  StringTokenizer st = new StringTokenizer(br.readLine());
  int n = Integer.parseInt(st.nextToken());
  int m = Integer.parseInt(st.nextToken());
  st = new StringTokenizer(br.readLine());
  int[][] map = new int[n][m];
  int[][] dp = new int[n][m];
  
  for(int i=0; i<n; i++) {
    for(int j=0; j<m; j++) {
      map[i][j] = Integer.parseInt(st.nextToken());
      dp[i][j] = map[i][j];
    }
  }

  //1번째 열부터 시작
  for(int c=1; c<m; c++) {  //열
    for(int r=0; r<n; r++) {  //행
      dp[r][c] = dp[r][c-1];
      if(r-1>=0) dp[r][c] = Math.max(dp[r][c], dp[r-1][c-1]);
      if(r+1<n) dp[r][c] = Math.max(dp[r][c], dp[r+1][c-1]);
      dp[r][c] += map[r][c];
    }
  }


  int result = 0;
  for(int i=0; i<n; i++) {
    result = Math.max(result,dp[i][m-1]);
  }
  
  System.out.println(result);
  
}
```

