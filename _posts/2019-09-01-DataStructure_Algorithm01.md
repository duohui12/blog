---
layout: post
title: (Java) LinkedList 구현하기
tags:
  - 자료구조_알고리즘
---

<br>

```java
public class Test {
	public static void main(String[] args) {
		
		LinkedList ll = new LinkedList();
		ll.append(1);
		ll.append(2);
		ll.append(3);
		ll.append(4);
		ll.retrieve();
		
		ll.delete(3);
		ll.retrieve();
    
	}
}


class LinkedList{
	Node header;
  
	static class Node{
		int data;
		Node next = null;
	}
  
	LinkedList(){
		header = new Node();
	}
	
	void append(int data) {
	   Node end = new Node();
	   end.data = data;
	   Node n = header;
	   while(n.next!=null) {
		   n = n.next;
	   }
	   n.next = end;
	}
	
	void delete(int data) {
		Node n = header;
		while(n.next!=null) {
			if(n.next.data==data) {
				n.next = n.next.next;
			}else {
				n = n.next;
			}
		}
	}
	
	void retrieve() {
		Node n = header.next;
		while(n.next!=null) {
			System.out.print(n.data+" -> ");
			n = n.next;
		}
		System.out.print(n.data);
		System.out.println();
	}
	
}
```

---

[엔지니어 대한민국 - LinkedList의 구현 in Java](https://www.youtube.com/watch?v=IrXYr7T8u_s)

