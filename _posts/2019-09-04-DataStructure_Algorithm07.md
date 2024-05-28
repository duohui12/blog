---
layout: post
title: (Java) 두개의 Stack으로 Queue 만들기
tags:
  - 자료구조_알고리즘
---

<br>

### 두개의 Stack으로 Queue 만들기

```java
class MyQueue<T>{
  
	Stack<T> stackNewest, stackOldest;
  
	MyQueue(){
		stackNewest = new Stack<>();
		stackOldest = new Stack<>();
	}
  
	public int size() {
		return stackNewest.size() + stackOldest.size();
	}
	
	public void add(T value) {
		stackNewest.push(value);
	}
	
	private void shiftStacks() {
		if(stackOldest.isEmpty()) {
			while(!stackNewest.isEmpty()) {
				stackOldest.push(stackNewest.pop());
			} 
		}
	}
	
	public T peek() {
		shiftStacks();
		return stackOldest.peek();
	}
	
	public T remove() {
		shiftStacks();
		return stackOldest.pop();
	}
	
}
```

---

[엔지니어 대한민국 - 두개의 Stack으로 Queue만들기](https://www.youtube.com/watch?v=t45d7CgDaDM)

<br>

