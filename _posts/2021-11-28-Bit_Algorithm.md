---
layout: post
title: (알고리즘) 비트와 부분집합
tags:
  - 알고리즘
---

<br>

### 트랜잭션

---

-  <b><<</b> (Left Shift)
  - x        = 00000001 = 1
  - x<<2  = 00000100 = 4
  - 비트를 왼쪽으로 하나 이동할 때마다 곱하기 2

- <b>>></b> (Right Shift)
  - x         = 00000100 = 4
  - x>>2  = 00000001 = 1
  - 비트를 오른쪽으로 하나 이동할 때마다 나누기 2

<br>

### 비트를 이용한 부분 집합

---

- 원소가 n개인 집합

  - 부분집합의 총 개수 : 1<<n

- 원소가 4개인 집합 <b>{A,B,C,D}</b>

  - 2 x 2 x 2 x 2 = 16가지의 경우의 수 
  - 부분집합의 총 갯수 : <b>1<<4</b> 

  <table>
    <tr>
    	<th>십진수</th>
      <th>이진수</th>
      <th>집합</th>
    </tr>
    <tr>
    	<td>0</td>
      <td>0000</td>
      <td>{}</td>
    </tr>
    <tr>
    	<td>1</td>
      <td>0001</td>
      <td>{A}</td>
    </tr>
    <tr>
    	<td>2</td>
      <td>0010</td>
      <td>{B}</td>
    </tr>
    <tr>
    	<td>3</td>
      <td>0011</td>
      <td>{A,B}</td>
    </tr>
      <tr>
    	<td>4</td>
      <td>0100</td>
      <td>{C}</td>
    </tr>
      <tr>
    	<td>5</td>
      <td>0101</td>
      <td>{A,C}</td>
    </tr>
      <tr>
    	<td>6</td>
      <td>0110</td>
      <td>{B,C}</td>
    </tr>
      <tr>
    	<td>7</td>
      <td>0111</td>
      <td>{A,B,C}</td>
    </tr>
      <tr>
    	<td>8</td>
      <td>1000</td>
      <td>{D}</td>
    </tr>
      <tr>
    	<td>9</td>
      <td>1001</td>
      <td>{A,D}</td>
    </tr>
      <tr>
    	<td>10</td>
      <td>1010</td>
      <td>{B,D}</td>
    </tr>
      <tr>
    	<td>11</td>
      <td>1011</td>
      <td>{A,B,D}</td>
    </tr>
      <tr>
    	<td>12</td>
      <td>1100</td>
      <td>{C,D}</td>
    </tr>
      <tr>
    	<td>13</td>
      <td>1101</td>
      <td>{A,C,D}</td>
    </tr>
      <tr>
    	<td>14</td>
      <td>1110</td>
      <td>{B,C,D}</td>
    </tr>
      <tr>
    	<td>15</td>
      <td>1111</td>
      <td>{A,B,C,D}</td>
    </tr>
  </table>

- ```java
  public class Main
  {
  	static void printSubsets(char[] arr, int n){
      for(int i=0; i<(1<<n); i++){
        System.out.print("{");
        for(int j=0; j<n; j++){
          	if(i&(1<<j)!=0)
              System.out.println(arr[j]+" ");
        }
        System.out.println("}");
      }  
    } 
    
    public static void main(String[] args){
      char[] data = {'A','B','C','D'};
      printSubsets(data,4);
    }
    
  }
  ```

<br>

### 집합에 비트 연산 활용

---

- <b>i번째 원소가 있는지 확인</b>
  - `(비트로 표현된 집합) & (1<<i)` 결과가 0이 아니면 존재
- ex) 0101에서 2번째 원소가 있는지 확인
  - `0101 & (1<<2) = 0101 & 0100 = 0100` (0이아님, 2번째 원소 있음)

<br>

- <b>i번째 원소를 추가</b>
  - `(비트로 표현된 집합) | (1<<i)`

- ex) 0101에서 1번째 원소를 추가
  - `0101 | (1<<1) = 0101 | 0010 = 0111`

<br>

- <b>i번째 원소를 삭제</b>
  - `(비트로 표현된 집합)  & ~(1<<i)`
- ex) 0101에서 2번째 원소를 삭제
  - `0101 & ~(1<<2) = 0101 & 1011 = 0001`

<br>

### 집합의 원소 갯수 구하기

---

- `Integer.bitCount(int i) ` 메서드 사용

- ```java
  int countBits(int n){
    int ret = 0;
    while(n!=0){
      if( (n&1) != 0 ) ++ret;
      n = n>>1;
    }
    return ret;
  }
  ```

<br>

### 연습문제 - 두 수의 합이 7인 경우의 수

---

- 입력

```java
6
1 2 3 4 5 6
```

- 출력

```java
3     //(1,6) (2,5) (3,4)
```

<br>

```java
public static void main(String[] args){
  Scanner sc = new Scanner(System.in);
  N = sc.nextInt();
  for(int i=0; i<N; i++){
    Arr[i] = sc.nextInt();
  }
  
  System.out.println(solve());
}

static int solve(){
  int ret = 0;
  
  for(int i=0; i<(1<<N); i++){
    if(Integer.bitCount(i)!=2) continue;  
    
    int sum = 0;
    for(int j=0; j<N; j++){
      if(i&(1<<j)!=0){
        sum += Arr[j];
      }
    }
    
    if(sum==7) ret++;
  }
  return ret;
}
```

