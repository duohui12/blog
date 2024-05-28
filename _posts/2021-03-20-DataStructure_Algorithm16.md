---
layout: post
title: (Java) Queue로 Stack 구현하기 
tags:
  - 자료구조_알고리즘
---

<br>

### [LeetCode 225번. Implement Stack using Queues](https://leetcode.com/problems/implement-stack-using-queues/)

---

- Queue에 데이터를 넣을 때, 최근에 push된 데이터가 가장 먼저 들어온 데이터가 되어야 한다.

- 따라서 데이터를 넣기 전에 큐사이즈를 기억하고, 데이터를 넣은 후에, 사이즈만큼 돌면서 기존 데이터를 poll()해서 다시 큐에 넣는다. 
  
  ```java
  class MyStack {
  
    Queue<Integer> queue = new LinkedList<>();
  
    public MyStack() {
  
    }
  
    public void push(int x) {
        int size = queue.size();
        queue.offer(x);
        for(int i=0; i<size; i++){
            queue.offer(queue.poll());
        }
    }
  
    public int pop() {
        return queue.poll();
    }
  
    public int top() {
        return queue.peek();
    }
  
    public boolean empty() {
        return queue.isEmpty();
    }
  }
  ```
