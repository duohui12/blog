---
layout: post
title: (알고리즘) Heap(힙) 배열로 구현해보기 
tags:
  - 알고리즘
---

<br>

### Heap

----

> heap이란..

<br>

### 구현해보기 - 1

---

> 먼저, 내가 이해한대로 배열로 Max-Heap을 구현해본 코드이다. (틀린부분 있을 수 있음..)

```java
public class HeapSortTest {
    public static void main(String[] args) {

        Heap heapSort = new Heap(10);
        int[] arr = {10,80,100,1,0,5,60};

        for(int i=0; i<arr.length; i++){
            heapSort.push(arr[i]);
        }

        for(int i=0; i<arr.length; i++){
            System.out.print(heapSort.pop()+" ");
        }
        System.out.println();



//        //min-heap으로 사용하기
//        for(int i=0; i<arr.length; i++){
//            heapSort.push(arr[i]*-1);
//        }
//
//        for(int i=0; i<arr.length; i++){
//            System.out.print(heapSort.pop()*-1+" ");
//        }
//        System.out.println();

    }

}



//maxHeap
class Heap{

    int[] heap;
    int index = 0;

    public Heap(int size){
        heap = new int[size];
    }

    public void push(int val) {
        if(index==heap.length) throw new IndexOutOfBoundsException();

        //complete binary tree -> 맨마지막에 삽입
        heap[index] = val;

        heapifyBottomUp(index++);

    }

    public int pop(){

        if(index==0) throw new NoSuchElementException();

        //0번째 인덱스에 있는 값을 꺼내서 리턴
        int ret = heap[0];

        //맨마지막 인덱스에 있는 값을 0번째 인덱스에 넣기
        --index;
        heap[0] = heap[index];

        //heapify -> 루트노트부터 리프 노드까지 heapify수행
        heapifyTopDown();

        return ret;

    }

    public void heapifyBottomUp(int index){  //O(logN)

        //현재노드가 0이면 멈춤 = root 노드 
        //현재노드가 0이 아니면 -> 부모노드값 찾기 -> 부모노드 값 < 현재노드 이면 swap -> 현재 노드 인덱스 = 부모노드 인덱스
        //                                -> 부모노드 값 >= 현재노드 이면 -> 메서드 종료

        while(index!=0){
            int parentIndex = index%2==0 ? index/2 - 1 : index/2;
            if(heap[parentIndex] < heap[index]) {

                swap(parentIndex,index);
                index = parentIndex;

            }else{
                break;
            }
        }

    }


    public void heapifyTopDown(){


        int parentIndex = 0;


        //왼쪽 자식 노드와 오른쪽 자식노드중에 더 큰 값을 찾기 =>max
        //현재 부모노드의 값 < max => swap => 부모노드 = maxIndex
        //현재 부모노드의 값 >= max => break;


        while(parentIndex*2+1 < index){  //적어도 하나의 자식 노드라도 있을 때 -> leaf노드가 아닐때

            int maxIndex = parentIndex*2 + 1;

            if(maxIndex+1 < index && heap[maxIndex+1] > heap[maxIndex]){  //오른쪽 자식도 존재하고 왼쪽자식값보다 더 크면??
                maxIndex = maxIndex+1;
            }

            if(heap[parentIndex] < heap[maxIndex]){
                swap(parentIndex,maxIndex);
                parentIndex = maxIndex;
            }else{
                break;
            }

        }


    }


    public void swap(int a, int b){
        int temp = heap[a];
        heap[a] = heap[b];
        heap[b] = temp;
    }



}

```

<br>

### 구현해보기 - 2

---

```java

```
