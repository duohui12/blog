---
layout: post
title: (Java) 퀵정렬(Quicksort) 
tags:
  - 자료구조_알고리즘
---

<br>

- 평균 시간복잡도 : O(nlogn) 
- 최악의 시간복잡도 : O(n^2)

```java
  public static void quickSort(int[] arr, int left, int right) {
      int part2 = partition(arr,left,right);
      if(left+1<part2) {
        quickSort(arr,left,part2-1);
      }
      if(part2<right) {
        quickSort(arr,part2,right);
      }

  }

  public static int partition(int[] arr, int left, int right) {
      int pivot = arr[(left+right)/2]; 
      while(left<=right) {
        while(arr[left]<pivot) left++;
        while(arr[right]>pivot) right--;
        if(left<=right) {
          swap(arr,left,right);
          left++; right--;
        }
      }
      return left;
  }

  public static void swap(int[] arr, int a, int b) {
    int temp = arr[a];
    arr[a] = arr[b];
    arr[b] = temp;
  }
```

---

[엔지니어 대한민국 - 퀵정렬(Quicksort)에 대해 알아보고 자바로 구현하기](https://www.youtube.com/watch?v=7BDzle2n47c)

<br>

