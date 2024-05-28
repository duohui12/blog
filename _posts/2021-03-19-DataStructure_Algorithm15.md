---
layout: post
title: (Java) Binary Search Tree 구현하기 (재귀)
tags:
  - 자료구조_알고리즘
---

<br>

### 노드 삽입

```java
public void add(int data){
  	root = add(root,data);
}

public Node add(Node root,int data){

    if(root==null) {
      root =  new Node(data);
      return root;
    }

    if(data<root.data) root.left = add(root.left,data);
    else if(data>root.data) root.right = add(root.right,data);

    return root;

}
```

<br>

### 노드 검색

```java
public Node search(Node root,int key){
    if(root==null) return null;

    if(key<root.data) return search(root.left,key);
    else if(key>root.data) return search(root.left,key);
    else return root;
}
```

<br>

### 노드 삭제

```java
public void delete(int data){
 	 root = delete(root,data);
}



public Node delete(Node root, int data) {

    if(root==null) return root;

    if(data<root.data) root.left = delete(root.left,data);
    else if(data>root.data) root.right = delete(root.right,data);
    else{

        if(root.left==null && root.right==null)  return null;
        else if(root.left==null) return root.right;
        else if(root.right==null) return root.left;

        root.data = findMin(root.right);
        root.right = delete(root.right, root.data);  

    }

    return root;

}



public int findMin(Node root){
    int min = root.data;
    while(root.left!=null){
      min = root.left.data;
      root = root.left;
    }
    return min;
}
```

<br>

<br>

### 전체코드

```java
class BinarySearchTree {

    private Node root;

    public Node getRoot() {
        return root;
    }

    public void add(int data){
        root = add(root,data);
    }

    public Node add(Node root,int data){

        if(root==null) {
            root =  new Node(data);
            return root;
        }

        if(data<root.data) root.left = add(root.left,data);
        else if(data>root.data) root.right = add(root.right,data);

        return root;

    }

    public Node search(Node root,int key){
        if(root==null) return null;

        if(key<root.data) return search(root.left,key);
        else if(key>root.data) return search(root.left,key);
        else return root;
    }



    public void delete(int data){
        root = delete(root,data);
    }

    public Node delete(Node root, int data) {

        if(root==null) return root;

        if(data<root.data) root.left = delete(root.left,data);
        else if(data>root.data) root.right = delete(root.right,data);
        else{

            if(root.left==null && root.right==null)  return null;
            else if(root.left==null) return root.right;
            else if(root.right==null) return root.left;

            root.data = findMin(root.right);
            root.right = delete(root.right, root.data);  
          
        }

        return root;

    }

    public int findMin(Node root){
        int min = root.data;
        while(root.left!=null){
            min = root.left.data;
            root = root.left;
        }
        return min;
    }



    public void inorder(Node root){
        if(root==null) return;
        inorder(root.left);
        System.out.print(root.data+" ");
        inorder(root.right);
    }

}



```

<br>

### 테스트 코드

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

class BinarySearchTreeTest {

    private RecursiveBst bst;
  
    @BeforeEach
    void setUp() {
        bst = new RecursiveBst();
        bst.add(100);
        bst.add(50);
        bst.add(40);
        bst.add(80);
        bst.add(20);
        bst.add(90);
        bst.add(10);
        bst.add(110);
        bst.add(150);
        bst.add(130);
        bst.add(60);

        System.out.println("초기 트리 상태");
        bst.inorder(bst.getRoot());
        System.out.println();
        System.out.println("-----------");

    }

    @Test
    void add() {

        assertThat(bst.getRoot().data).isEqualTo(100);

        //중복값 삽입하기-> 그대로
        bst.add(100);
        bst.inorder(bst.getRoot());
        System.out.println();

    }

    @Test
    void search() {

        assertThat(bst.getRoot().data).isEqualTo(100);

        Node root = bst.getRoot();
        assertThat(bst.search(root,1000000)).isNull();
        assertThat(bst.search(root,100).data).isEqualTo(100);
        assertThat(bst.search(root,40).data).isEqualTo(40);
        assertThat(bst.search(root,1)).isNull();

    }

    @Test
    @DisplayName("루트노드 삭제하기")
    void deleteRoot() {

        assertThat(bst.getRoot().data).isEqualTo(100);

        bst.delete(100);
        bst.inorder(bst.getRoot());
        System.out.println();

        assertThat(bst.getRoot()).isNotEqualTo(100);
        System.out.println("새로운 root의 값 : "+bst.getRoot().data);
    }

  
    @Test
    @DisplayName("자식이 없는 노드 삭제하기")
    void deleteNode1(){

        bst.delete(10);
        bst.inorder(bst.getRoot());
        System.out.println();
    }

  
    @Test
    @DisplayName("자식이 한개인 노드 삭제하기")
    void deleteNode2(){
        bst.delete(150);
        bst.inorder(bst.getRoot());
        System.out.println();
    }
  

    @Test
    @DisplayName("자식이 두개인 노드 삭제하기")
    void deleteNode3(){
        bst.delete(50);
        bst.inorder(bst.getRoot());
        System.out.println();

        bst.delete(60);
        bst.inorder(bst.getRoot());
        System.out.println();
    }

}
```

<br>

---

- references
  - [엔지니어 대한민국 -  BST insertion/deletion](https://www.youtube.com/watch?v=xxADG17SveY)

<br>

