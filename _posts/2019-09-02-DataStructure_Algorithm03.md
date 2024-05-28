---
layout: post
title: (Java) 단방향 Linked List 뒤에서 k번째 구하기
tags:
  - 자료구조_알고리즘
---

<br>

1. 뒤에서 k번째 노드의 값을 출력하는 경우

   ```java
   //재귀호출
   int kthToLast(Node n,int k) {
     if(n==null) return 0;	
     int count = kthToLast(n.next,k) +1;
     if(count==k) {
       System.out.println(n.data);
     }
     return count;
   }
   ```

   <br>

2. 노드를 반환할 경우 - 시간복잡도 O(N) / 공간복잡도 O(N)

   ```java
   Node kthToLast(Node n,int k,Reference r) {
     if(n==null) return null;
     Node found = kthToLast(n.next,k,r);
     r.count++;
     if(r.count==k) {
       return n;
     }
     return found;
   }
   ```

   ```java
   class Reference{
   	public int count = 0;
   }
   ```

   <br>

3. 두개의 포인터를 사용하는 방법 - 시간복잡도 O(N) / 공간복잡도 O(1)

   ```java
   Node kthToLast4(Node first, int k) {
     Node p1 = first;
     Node p2 = first;
   
     for(int i=1; i<=k; i++) {
       if(p2==null) return null;  //뒤에서 k번째 노드가 없는 경우
       p2=p2.next;
     }
   
     while(p2!=null) {
       p1 = p1.next;
       p2 = p2.next;
     }
   
     return p1;
   }
   ```

   ---

   [엔지니어 대한민국 -  단방향 Linked List 뒤부터 세기 in Java](https://www.youtube.com/watch?v=Vb24scNDAVg)

