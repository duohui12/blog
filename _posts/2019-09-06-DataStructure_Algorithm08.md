---
layout: post
title: (Java) Stack 정렬하기
tags:
  - 자료구조_알고리즘
---

<br>

### 주어진 스택 외에 별도의 스택 1개만 사용해서 주어진 스택 정렬하기

```java
public class Test {
  
	public static void main(String[] args) {

		Stack<Integer> s1 = new Stack<>();
		s1.push(6);
		s1.push(2);
		s1.push(4);
		s1.push(5);
		s1.push(3);
		s1.push(4);
		sort(s1);  //오름차순
	}
	
	private static void sort(Stack<Integer> s1) {
		Stack<Integer> s2 = new Stack<>();
    
		while(!s1.isEmpty()) {
			int tmp = s1.pop();
			while(!s2.isEmpty() && s2.peek()<tmp) {
				s1.push(s2.pop());
			}
			s2.push(tmp);
		}
		
		while(!s2.isEmpty()) {
			System.out.println(s2.pop());
		}
    
	}
}
```

---

[엔지니어 대한민국 - Stack 정렬하기](https://www.youtube.com/watch?v=6-tsS9aBfzY)

<br>

