---
layout: post
title: (Java) 병합정렬(Merge sort) 
tags:
  - 자료구조_알고리즘
---

<br>

- 시간복잡도 : O(nlogn) 

```java
//arr 배열 => 정렬될 숫자가 담긴 배결
//temp 배열 => 정렬하면서 임시로 데이터를 담기 위해 필요한 배열, arr.length 길이와 같다

public static void mergeSort(int[] arr, int left, int right, int[] temp) {
  if(left<right){
    int mid = (left+right)/2;
    mergeSort(arr,left,mid,temp);   
    mergeSort(arr,mid+1,right,temp);
    merge(arr,left,mid,right,temp);
  }
}

public static void merge(int[] arr, int left, int mid, int right, int[] temp){

  int index = left;
  int s1 = left;
  int s2 = mid+1;

  while(s1<=mid && s2<=right){
    if(arr[s1]<=arr[s2]){
      temp[index++] = arr[s1++];
    }else{
      temp[index++] = arr[s2++];
    }
  }
  
  while(s1<=mid){
    temp[index++] = arr[s1++];
  }

  while(s2<=right){
    temp[index++] = arr[s2++];
  }

  for(int i=left; i<=right; i++){
    arr[i] = temp[i];
  }
}

```

```java
public static void main(String[] args) {

  int[] arr = {12,11,9,1,2,3,4,8};
  int[] temp = new int[arr.length];
  mergeSort(arr,0,arr.length-1,temp);
  System.out.println(Arrays.toString(arr)); //[1, 2, 3, 4, 8, 9, 11, 12] 출력
}
```

---

[엔지니어 대한민국 - 퀵정렬(Quicksort)에 대해 알아보고 자바로 구현하기](https://www.youtube.com/watch?v=QAyl79dCO_k)

<br>

