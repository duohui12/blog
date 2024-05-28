---

layout: post
title: (Java) LinkedList의 중복값 삭제하기
tags:
  - 자료구조_알고리즘
---

<br>

### 정렬되어있지 않은 LinkedList의 요소 중 중복값 제거하기 (단, 별도의 버퍼 사용x)

<br>

- Hashset을 이용할 경우, LinkedList를 돌면서 요소들을 Hashset에 넣고, 중복된 값이 나올 경우 삭제하면 됨
- 별도의 공간을 사용하지 않을 경우, 두개의 포인터를 사용하여 구현 

<br>

```java
void removeDups() {
  Node n = header.next;

  while(n!=null && n.next!=null) {
    Node runner = n; 

    while(runner.next!=null) {
      if(runner.next.data == n.data) {
        //제거하기
        runner.next = runner.next.next;
      }else {
        //넘어가기
        runner = runner.next;
      }
    }

    n = n.next;
  }

}
```

- 시간복잡도 : O(N^2) / 공간복잡도 : O(1)

<br>

### 전체코드

```java
public class Test {
	public static void main(String[] args) {
		
     LinkedList ll = new LinkedList();
		 ll.append(2);
		 ll.append(3);
		 ll.append(4);
		 ll.append(2); 
		 ll.append(4);
		 ll.retrieve();
		 ll.removeDups();
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
	
	void removeDups() {
		Node n = header.next;
		while(n!=null && n.next!=null) {
			Node runner = n; 
			while(runner.next!=null) {
				if(runner.next.data == n.data) {
					//제거하기
					runner.next = runner.next.next;
				}else {
					//넘어가기
				    runner = runner.next;
				}
			}//while
			n = n.next;
		}//while
	}
	
}
```

---

[엔지니어 대한민국 -  Linked List 중복값 삭제 in Java](https://www.youtube.com/watch?v=Ce4baygLMz0)





