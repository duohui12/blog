---
layout: post
title: (Java)  단방향 Linked List의 중간노드 삭제하기
tags:
  - 자료구조_알고리즘
---

<br>

### 단방향 Linked List의 중간노드 삭제하기 

(첫번째 노드가 주어지지 않는다. 삭제해야할 해당노드만 주어진다.)

- 삭제할 노드의 이전 노드를 알 수 없기 때문에, 삭제할 노드의 next에 있는 노드를 삭제할 노드 위에 덮어씌우기

<br>

```java
public static void main(String[] args) {

  LinkedList ll = new LinkedList();
  ll.append(1);
  ll.append(2);
  ll.append(3);
  ll.append(4);
  ll.append(5);
  ll.append(6);
  ll.retrieve();

  deleteNode(ll.get(3));
  ll.retrieve();


}

private static boolean deleteNode(Node n) {
  if(n==null || n.next==null) return false;
  Node next = n.next;
  n.data = next.data;
  n.next = next.next;
  return true;
}
```

```markdown
1 -> 2 -> 3 -> 4 -> 5 -> 6
1 -> 2 -> 4 -> 5 -> 6
```

---

[엔지니어 대한민국 -  단방향 Linked List 중간노드 삭제 in Java](https://www.youtube.com/watch?v=xI4iPEmkHlc)

