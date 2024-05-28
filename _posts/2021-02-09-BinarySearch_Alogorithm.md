---
layout: post
title: (알고리즘) 이진탐색(Binary Searh)으로 upper_bound와 lower_bound 인덱스 구하기
tags:
  - 알고리즘
---

<br>

> [이것이 취업을 위한 코딩 테스트다](http://www.yes24.com/Product/Goods/91433923) 책으로 학습한 내용을 정리한 글입니다

<br>

>이미 `정렬된 수열`에서 `이진탐색`을 이용해 `lower_bound`와 `upper_bound`를 구하면 `O(logN)`의 시간복잡도로 수열에서 특정 숫자(x)가 등장하는 횟수를 구할 수 있다

<br>

### lower_bound

---

- target값보다 같거나 큰 수 중 제일 첫번째 원소의 인덱스를 리턴해준다
- 만약 그런 값이 없다면 맨 마지막 인덱스를 리턴한다
- 예를 들어 정렬된 수열 {1,1,2,2,2,2,3}에서 target(2)의 lower_bound를 구하면 index 2를 리턴한다

<br>

```java
 int lower(int[] arr, int target) {
      int left = 0;
      int right = arr.length-1;
   
      while(left<right) {
        int mid = (left+right)/2;
        if(arr[mid]<target) {
           left = mid+1;
        }else {
           right = mid;
        }
      }	
      return right;
	}
```

<br>

### upper_bound

---

- target값보다 큰 값을 가지는 수 중 제일 첫번째 원소의 인덱스를 리턴해준다
- 만약 그런 값이 없다면 맨 마지막 인덱스를 리턴한다
- 예를 들어 정렬된 수열 {1,1,2,2,2,2,3}에서 target(2)의 upper_bound를 구하면 index 6을 리턴한다

<br>

```java
	public static int upper(int[] arr, int target) {
      int left = 0;
      int right = arr.length-1;
    
      while(left<right) {
        int mid = (left+right)/2;
        if(arr[mid]<=target) {
          left = mid+1;
        }else {
          right = mid;
        }
      }
      return right;
	}
```

<br>

### 주의할 점

---

- target값이 정렬된 array의 맨 마지막에 존재하는 경우
  - ex) 수열{1,2,2,2,3,3}에서 target이 3일 경우
  - 이때 lower_bound => 4, upper_bound => 5 리턴
  - 이때 `upper_bound - lower_bound`의 값은 1로 실제 target number 3의 갯수 2개 보다 적게 나옴
  - 따라서, `if( arr[upper_bound(...)] == target)` 일 경우에는 upper_bound 인덱스 값을 ++ 해준다.

- 정렬된 array 안에 찾고자 하는 값이 아예 없을 경우에는 ?
  - lower_bound와 upper_bound 모두 맨 마지막 인덱스를 리턴한다
  - 따라서, lower_bound 값과, upper_bound값이 같을 경우에는 -1을 리턴하도록 한다.

<br>

- 특정 숫자(x)가 중복해서 등장하는 횟수를 구하는 코드

```java
  int lb = lower_bound(arr,target);
  int ub = upper_bound(arr,target);

  if(arr[ub]==x) ub++;
  System.out.println(ub==lb?-1:ub-lb);
```

<br>