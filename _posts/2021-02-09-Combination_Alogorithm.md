---
layout: post
title: (알고리즘) 조합(Combination) 구하기
tags:
  - 알고리즘
---

<br>

### 조합 (Combination)

---

- 조합은 n개의 원소를 가진 집합에서 서로 다른 r개의 원소를 순서없이 선택하는 것이다. 

- 예를들어  {1,2,3} 배열에서 2개의 원소를 고르는 조합은 {1,2} {1,3} {2,3} 과 같다.

- 순열과 달리 순서에 상관없이 원소를 선택하기 때문에 {1,2}와 {2,1}을 같은 것으로 취급한다

<br>

- 조합 구하는 방법 

  - 배열의 처음부터 마지막까지 돌면서, 

    - 현재 인덱스를 선택하고 넘어가는 경우

    - 현재 인덱스를 선택하지 않고 넘어가는 경우

  - 이 두가지로 만들 수 있는 모든 경우를 완전 탐색한다

<br>

### 자바로 구현하기 1

```java
int[] arr = {1,2,3,4,5};
int r = 3; //뽑고자 하는 갯수
int[] selected = new int[r];  //선택된 r개의 원소를 담을 배열
```

```java
//count => selected 배열의 담긴 원소의 수 
//index => arr 배열의 처음부터 마지막까지 탐색하기 위해 필요한 index
void getCombination(int[] arr, int[] selected, int count, int index) {

    if(count==selected.length) {   //r개만큼의 원소를 모두 골랐음을 의미한다 -> 종료
      //출력
      return;
    }

    if(index==arr.length) {   //r개만큼의 원소를 모두 고르지 못했지만, arr배열을 끝까지 모두 탐색한 경우 -> 종료
      return; 
    }

    selected[count] = arr[index];
    getCombination(arr,selected,count+1,index+1);  //arr[index] 원소를 고르고 넘어가는 경우
  
    getCombination(arr,selected,count,index+1);  //arr[index]원소를 고르지 않고 넘어가는 경우

}
```

<br>

### 자바로 구현하기 2

```java
int[] arr = {1,2,3,4,5};
int r = 3; //뽑고자 하는 갯수
int[] selected = new int[r];  //선택된 r개의 원소를 담을 배열
```

```java
//count => selected 배열의 담긴 원소의 수 
//start => arr 배열의 처음부터 마지막까지 탐색하기 위해 필요한 index
void getCombination(int[] arr, int[] selected, int start, int count) {
		
		if(count==selected.length) { //r개만큼의 원소를 모두 골랐음을 의미한다 -> 종료
			//출력
			return;
		}
		
  //for문으로 arr배열의 범위를 정해주기 때문에 위의 방법처럼 따로 arr 인덱스를 벗어나는지 체크하지 않아도 된다.
		for(int i=start; i<arr.length; i++) {  
			selected[count] = arr[i];  //현재 인덱스의 원소를 선택한후
			getCombination(arr, selected, i+1, count+1); //그 다음부터 탐색하도록 재귀호출
		}
		
}
```