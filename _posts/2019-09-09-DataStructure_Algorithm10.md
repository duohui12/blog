---
layout: post
title: (Java) Binary Tree의 순회(traversal)
tags:
  - 자료구조_알고리즘
---

<br>

#### Binary Tree의 3가지 순회방법 

1. inorder :     Left -> Root -> Right
2. preorder :   Root -> Left -> Right
3. postorder : Left -> Right -> Root

<br>

```java
public class Test {

	public static void main(String[] args) {

		//       (1)
		//     /     \
		//   (2)      (3)
		//   / \
		// (4) (5)
 		//inorder => 4 2 5 1 3
		//preorder =>1 2 4 5 3
		//postorder => 4 5 2 3 1 
		
		
		Tree t = new Tree();
		Node n4 = t.makeNode(null, 4, null);
		Node n5 = t.makeNode(null, 5, null);
		Node n2 = t.makeNode(n4, 2, n5);
		Node n3 = t.makeNode(null, 3, null);
		Node n1 = t.makeNode(n2, 1, n3);
		t.setRoot(n1);
		t.inorder(t.getRoot());
		System.out.println();
		t.preorder(t.getRoot());
		System.out.println();
		t.postorder(t.getRoot());
		
	}

	
}


class Node{
	int data;
	Node left;
	Node right;
}

class Tree{
	public Node root;
	public void setRoot(Node root) {
		this.root = root;
	}
	public Node getRoot() {
		return root;
	}
	public Node makeNode(Node left,int data, Node right) {
		Node node = new Node();
		node.data = data;
		node.left = left;
		node.right = right;
		return node;
	}
	
	public void inorder(Node node) {
		if(node==null) return;
		inorder(node.left);
		System.out.print(node.data+" ");
		inorder(node.right);
	}
	
	public void preorder(Node node) {
		if(node==null) return;
		System.out.print(node.data+" ");
		preorder(node.left);
		preorder(node.right);
	}
	
	public void postorder(Node node) {
		if(node==null) return;
		postorder(node.left);
		postorder(node.right);
		System.out.print(node.data+" ");

	}
}
```

```java
4 2 5 1 3 
1 2 4 5 3 
4 5 2 3 1 
```

---

[엔지니어 대한민국 - Binary Tree의 3가지 순회방법 구현하기](https://www.youtube.com/watch?v=QN1rZYX6QaA)

<br>

