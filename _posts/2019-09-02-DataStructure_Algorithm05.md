---
layout: post
title: (Java) Linked List Digit합산 알고리즘
tags:
  - 자료구조_알고리즘
---

<br>

1. 어떤 수의 각 자리의 숫자를 LinkedList에 순서대로 담았다. 이러한 두 LinkedList의 합을 구하기

   ```markdown
   예시)
   123+27 = 150
   
   List1  : 1->2->3
   List2  : 2->7
   result : 1->5->0
   ```

   <br>

   코드

   ```java
   private static Node sumLists(Node l1, Node l2) {
     //1.각 리스트의 길이를 구한 후, 짧은 리스트의 앞부분을 0으로 채워서 긴 리스트와 길이가 똑같이 맞추기
     int len1 = getListLength(l1);
     int len2 = getListLength(l2);
     if(len1<len2) {
       l1 = LPadList(l1,len2-len1);
     }else {
       l2 = LPadList(l2,len1-len2);
     }
   
     Storage storage = addLists(l1,l2);
     //첫째자리에서 carry가 발생할 경우를 대비해 다시한번 확인하기
     if(storage.carry!=0) {
       storage.result = insertBefore(storage.result,storage.carry);
     }
     return storage.result;
   }
   
   private static Storage addLists(Node l1, Node l2) {
     if(l1==null && l2 == null) {
       return new Storage();
     }
     Storage storage = addLists(l1.next,l2.next);
     int value = storage.carry + l1.data + l2.data;
     storage.result = insertBefore(storage.result,value%10);
     storage.carry = value/10;
     return storage;
   }
   
   //len만큼 node의 앞을 0으로 채우기
   private static Node LPadList(Node node,int len) {
     Node head = node; 
     for(int i=0; i<len; i++) {
       head = insertBefore(head,0);
     }
     return head;
   }
   
   //data를 값으로 가지는 노드를 생성하고, node앞에 연결하기
   private static Node insertBefore(Node node, int data) {
     Node before = new Node();
     before.data = data;
     before.next = node ;
     return before;
   }
   
   private static int getListLength(Node node) {
     int total = 0;
     while(node!=null) {
       total++;
       node = node.next;
     }
     return total;
   }	
   ```

   ```java
   class Storage{
   	int carry = 0;
   	Node result = null;
   }
   ```

   <br>

   실행결과

   ```java
   public static void main(String[] args) {
       LinkedList l1 = new LinkedList();
       l1.append(1);
       l1.append(2);
       l1.append(3);
       l1.retrieve();
   
       LinkedList l2 = new LinkedList();
       l2.append(2);
       l2.append(7);
       l2.retrieve();
   
       ret = sumLists(l1.getFirst(),l2.getFirst());
       while(ret.next!=null) {
         System.out.print(ret.data+" -> ");
         ret = ret.next;
       }
       System.out.print(ret.data);
   }
   ```

   ```markdown
   1 -> 2 -> 3
   2 -> 7
   1 -> 5 -> 0
   ```

   <br>

2. 어떤 수의 각 자리의 숫자를 LinkedList에 1의 자리부터 거꾸로 담았다. 이러한 두 LinkedList의 합을 구하기

   ```markdown
   예시)
   123+27 = 150
   
   List1  : 3->2->1
   List2  : 7->2
   result : 0->5->1
   ```

   <br>

   코드

   ```java
   private static Node sumLists(Node l1, Node l2, int carry) {
     if(l1 == null && l2 == null & carry == 0) return null;
   
     Node result = new Node();
     int value = carry;
     if(l1 != null) {
       value += l1.data;
     }
     if(l2 != null) {
       value += l2.data;
     }
     result.data = value % 10;
   
     if(l1!=null || l2!=null) {
       Node next = sumLists(l1==null?null:l1.next,
                            l2==null?null:l2.next,
                            value/10);
       result.next = next;
     }
   
     return result;
   
   }
   ```

   <br>

   실행결과

   ```java
   public static void main(String[] args) {
        
        LinkedList l1 = new LinkedList();
   		 l1.append(3);
   		 l1.append(2);
   		 l1.append(1);
   		 l1.retrieve();
   
   		 LinkedList l2 = new LinkedList();
   		 l2.append(7);
   		 l2.append(2);
   		 l2.retrieve();
     
        Node ret = sumLists(l1.getFirst(),l2.getFirst(),0);
   		 while(ret.next!=null) {
   			 System.out.print(ret.data+" -> ");
   			 ret = ret.next;
   		 }
   		 System.out.print(ret.data);
     
   }
   ```

   ```markdown
   3 -> 2 -> 1
   7 -> 2
   0 -> 5 -> 1
   ```

---

[엔지니어 대한민국 - Linked List Digit합산 알고리즘 in Java](https://www.youtube.com/watch?v=vuJk2JZd3fI)

<br>

