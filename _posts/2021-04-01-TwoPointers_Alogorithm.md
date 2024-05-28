---
layout: post
title: (알고리즘) 투 포인터 알고리즘(Two Pointer ) 문제풀이
tags:
  - 알고리즘
---

<br>

### [백준2003번_수들의 합2](https://www.acmicpc.net/problem/2003)

---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int m = Integer.parseInt(st.nextToken());

        st = new StringTokenizer(br.readLine());
        int[] arr = new int[n];
        for(int i=0; i<n; i++) arr[i] = Integer.parseInt(st.nextToken());

        int count = 0;
        int sum = 0;
        int lt = 0;

        for(int rt = 0; rt<n; rt++){

            sum += arr[rt];

            while(sum > m){
                sum -= arr[lt++];

            }

            if(sum==m) count++;
        }

        System.out.println(count);


    }
}

```

<br>

### [백준1806번_부분합](https://www.acmicpc.net/problem/1806)

---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int s = Integer.parseInt(st.nextToken());
        int[] arr = new int[n];

        st = new StringTokenizer(br.readLine());
        for(int i=0; i<n; i++) arr[i] = Integer.parseInt(st.nextToken());

        int minLen = arr.length+1;
        int lt = 0;
        int sum = 0;

        for(int rt=0; rt<n; rt++){
            sum += arr[rt];

            while(sum>=s) {
                minLen = Math.min(minLen, (rt - lt + 1));
                sum -= arr[lt++];
            }

        }


        System.out.println(minLen==arr.length+1?0:minLen);


    }
}

```

<br>

### [백준1644번_소수의 연속합](https://www.acmicpc.net/problem/1644)

---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;

public class Main {


    public static void main(String[] args) throws IOException {



        //에라토스테네스의 체 구하기 N까지
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());

        boolean[] isPrime = new boolean[n+1];
        Arrays.fill(isPrime,true);

        isPrime[0] = false;
        isPrime[1] = false;

        for(int i=2; i*i<=n; i++){
            if(isPrime[i]){
                for(int j=i*i; j<=n; j+=i){
                    isPrime[j] = false;
                }
            }
        }


        ArrayList<Integer> primeList = new ArrayList<>();
        for(int i=2; i<=n; i++){
            if(isPrime[i]) primeList.add(i);
        }


        int lt = 0;
        int sum = 0;
        int count = 0;


        //N까 투포인터로 합이 N이 되는 경우의 수 구하기
        for(int rt=0; rt<primeList.size(); rt++){

            sum += primeList.get(rt);

            while(sum>n){
                sum -= primeList.get(lt++);
            }

            if(sum==n){
                count++;
            }

        }


        System.out.println(count);

    }
}

```

<br/>

### [백준2230번_수 고르기](https://www.acmicpc.net/problem/2230)

---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {


    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int m = Integer.parseInt(st.nextToken());
        int[] arr = new int[n];

        for(int i=0; i<n; i++) arr[i] = Integer.parseInt(br.readLine());
        Arrays.sort(arr);

        int lt = 0;
        int minDiff = Integer.MAX_VALUE;


        int rt = 0;

        while(lt<n && rt<n){

            int tempDiff = arr[rt] - arr[lt];

            while(tempDiff >= m){
                minDiff = Math.min(minDiff, tempDiff);
                if(lt==n-1) break;
                tempDiff = arr[rt] - arr[++lt];
            }

            rt++;

        }



        System.out.println(minDiff);

    }
}

```

<br>

### [백준1484번_다이어트](https://www.acmicpc.net/problem/1484)

---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());

        List<Integer> list = new ArrayList<>();


        for(int i=3; i<=n; i=i+2){
            list.add(i);
        }



        StringBuilder sb = new StringBuilder();

        int lt = 0;
        int sum = 0;
        for(int rt = 0; rt<list.size(); rt++){

            sum += list.get(rt);

            while(sum>n){
                sum -= list.get(lt++);
            }

            if(sum==n){
                sb.append(rt+2);
                sb.append("\n");
            }
        }


        if(sb.length()==0) System.out.println(-1);
        else System.out.println(sb.toString());
    }
}

```

<br>

### [백준2531번_회전초밥](https://www.acmicpc.net/problem/2531)

---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.StringTokenizer;

public class Main {


    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int d = Integer.parseInt(st.nextToken());  //초밥의 가지수 1~d 까지
        int k = Integer.parseInt(st.nextToken());  //연속해서 먹는 접시수
        int c = Integer.parseInt(st.nextToken());  //쿠폰번호 C

        int[] arr = new int[n+k-1];
        for(int i=0; i<n; i++) arr[i] = Integer.parseInt(br.readLine());
        for(int i=0; i<k-1; i++) arr[n+i] = arr[i];


        //arr을 돌면서 k접시를 먹는데
        //C를 포함한 경우 -> 해당 범위내 종류만 카운트
        //C를 포함하지 않은 경우 -> 해당 범위내 종류 + 1


        int maxType = -1;
        int lt = 0;
        int len = 0;
        HashMap<Integer,Integer> map = new HashMap<>();


        for(int rt = 0; rt<n+k-1; rt++){

            ++len;
            map.put(arr[rt],map.getOrDefault(arr[rt],0)+1);


            if(len==k){ //길이를 다 채운 경우
                int typeCnt = map.size();
                if(!map.containsKey(c)) typeCnt++;
                maxType = Math.max(maxType,typeCnt);


                if(map.get(arr[lt])==1){
                    map.remove(arr[lt]);
                }else{
                    map.put(arr[lt],map.get(arr[lt])-1);
                }
                lt++; len--;

            }



        }//for


        System.out.println(maxType);

    }
}


```