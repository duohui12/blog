---
layout: post
title: (알고리즘) 순열(Permutation) 구하기
tags:
  - 알고리즘
---

<br>

### 순열 (Permutation)

---

- 순열은 n개의 원소를 가진 집합에서 서로 다른 r개의 원소를 순서대로 뽑는 경우를 말한다 

- 예를들어  {1,2,3} 배열에서 2개의 원소를 고르는 경우는 {1,2} {1,3} {2,1} {2,3} {3,1} {3,2}로 6가지 경우가 존재

- 순서가 존재하는 열이기 때문에 {1,2}와 {2,1}을 다른 것으로 취급한다

<br>

### 자바로 구현하기 1 - visited 배열 사용

```java
int[] arr = {1,2,3};
int r = 2; //뽑고자 하는 갯수
int[] selected = new int[r];  //선택된 r개의 원소를 담을 배열
boolean[] visited = new boolean[arr.length];  //이미 사용한 원소인지 체크하기 위해 

permutation(arr, selected, visited, 0);
```

```java
//count => selected 배열에 담긴 원소의 수 
void permutation(int[] arr, int[] selected, boolean[] visited, int count) {

      if(count==selected.length) {
           print(selected);
           return;
      }

      for(int i=0; i<arr.length; i++) {
          if(visited[i]) continue;
          selected[count] = arr[i];
          visited[i] = true;
          permutation(arr,selected,visited,count+1);
          visited[i] = false;
      }

}
```

<br>

### 자바로 구현하기 2 - swap( ) 사용

- depth를 기준 인덱스로 해서 depth보다 인덱스가 작은 값들은 고정하고, depth보다 인덱스가 큰 값들과 swap 

```java
int[] arr = {1,2,3};
int r = 2; //뽑고자 하는 갯수

permutation(arr,0,r);
```

```java
void permutation(int[]arr, int depth, int r) {

      if(depth==r) {
        print(arr,r);
        return;
      }
  
      for(int i=depth; i<arr.length; i++) {
        swap(arr,i,depth);
        permutation(arr,depth+1,r);   
        swap(arr,i,depth);
      }
  
}

void swap(int[] arr, int i, int depth) {
		int temp = arr[depth];
		arr[depth] = arr[i];
		arr[i] = temp;
}

void print(int[] arr, int r){
  	for(int i=0; i<r; i++) System.out.print(arr[i]+" ");
  	System.out.println();
}
```

<br>

### 자바로 구현하기 3

- 원소의 집합이 문자열로 주어지는 경우, substring으로 이미 선택한 원소를 제외시키는 방법을 사용해보았다

```java
String nums = "12345"
int r = 3; //뽑고자 하는 갯수
int[] selected = new int[r]; //선택된 r개의 원소를 담을 배열

permutation(nums, selected, 0);
```

```java
//count => selected 배열에 담긴 원소의 수 
void permutation(String str, int[] selected, int count) {

    if(count==selected.length) {
        ans++;
        print(selected);
        return;
    }

    for(int i=0; i<str.length(); i++) {
        selected[count] = str.charAt(i)-'0';
        permutation( str.substring(0,i)+str.substring(i+1,str.length()), selected, count+1);
    }
}

```

