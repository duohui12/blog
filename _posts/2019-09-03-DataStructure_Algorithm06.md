---
layout: post
title: (Java) Stack, Queue 구현하기
tags:
  - 자료구조_알고리즘
---

<br>

### 스택 구현하기

```java
class Stack<T>{
  
	class Node<T>{
		private T data;
		private Node<T> next;
		public Node(T data) {
			this.data = data;
		}
	}
  
	private Node<T> top;
  
	public T pop() {
		if(top==null) {
			throw new EmptyStackException();
		}
		T item = top.data;
		top = top.next;
		return item;
	}
  
	public void push(T item) {
		Node<T> node = new Node<>(item);
		node.next = top;
		top = node;
	}
  
	public T peek() {
		if(top==null) {
			throw new EmptyStackException();
		}
		return top.data;
	}
	
	public boolean isEmpty() {
		return top==null;
	}
}
```

<br>

### 큐 구현하기

```java
class Queue<T>{
  
	class Node<T>{
		private T data;
		private Node<T> next;
		public Node(T data) {
			this.data = data;
		}
	}
  
	private Node<T> first;
	private Node<T> last;
	
	public void add(T item) {
		Node<T> node = new Node<>(item);
		if(last!=null) {
			last.next = node;
		}
		last = node;
		if(first ==null) {
			first = last;
		}
	}
	
	public T remove() {
		if(first==null) {
			throw new NoSuchElementException();
		}
		T data = first.data;
		first = first.next;
		if(first==null) {
			last = null;
		}
		return data;
	}
	
	public T peek() {
		if(first==null) {
			throw new NoSuchElementException();
		}
		return first.data;
	}
  
	public boolean isEmpty() {
		return first==null;
	}
	
	
}
```

---

[엔지니어 대한민국 - Stack 구현하기 in Java](https://www.youtube.com/watch?v=whVUYv0Leg0)

<br>

