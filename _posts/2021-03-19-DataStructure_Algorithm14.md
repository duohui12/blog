---
layout: post
title: (Java) Binary Search Tree 구현하기 (반복문 사용)
tags:
  - 자료구조_알고리즘
---

<br>

### 노드 삽입

```java
public void add(int data){

    Node newNode = new Node(data);

    if(root==null){
      root = newNode;
      return;
    }

    Node curNode = root;
    while(curNode!=null){
      
      if(data<curNode.data){
       
          if(curNode.left==null){
            curNode.left = newNode;
            break;
          }
          curNode = curNode.left;
        
      }else if(data>curNode.data){
        
          if(curNode.right==null){
            curNode.right = newNode;
            break;
          }
          curNode = curNode.right;
        
      }else{
          break; 
      }

    }//while

}
```

<br>

### 노드 검색

```java
public Node search(int key){

    Node curNode = root;

    while(curNode!=null){
        if(key<curNode.data){
          curNode = curNode.left;
        }else if(key>curNode.data){
          curNode = curNode.right;
        }else{
          return curNode;
        }
    }

  return null;

}
```

<br>

### 노드 삭제

노드 삭제는 3가지 경우를 고려해야한다

1. 삭제할 노드의 자식 노드가 없는 경우
   - 삭제할 노드의 부모노드에 삭제할 노드가 아닌 null을 연결한다
2. 삭제할 노드의 자식 노드가 한개인 경우
   - 삭제할 노드의 부모노드에 삭제할 노드의 자식 노드를 연결한다
3. 삭제할 노드의 자식 노드가 두개인 경우
   - 삭제할 노드의 값을 다른 값으로 매꿔야한다.
   - bst를 유지하기 위해 트리의 값을 오름차순으로 정렬했을 때 바로 뒤에 오는 값이나 바로 앞에 오는 값으로 삭제할 노드의 값을 바꿔야함
   - 삭제할 노드 데이터의 이전 값은 왼쪽 서브트리 중에서 가장 큰 값 (왼쪽 서브트리에서 오른쪽 끝까지 내려가면 찾을 수 있는 값)
   - 삭제할 노드 데이터의 이후 값은 오른쪽 서브트리 중에서 가장 작은 값(오른쪽 서브트리에서 왼쪽 끝까지 내려가면 찾을 수 있는 값)
   - 삭제할 노드의 값을 변경한 후에, 위에서 찾은 노드는 삭제시켜야 한다

```java
  public void delete(int key){

        Node curNode = root;
        Node prevNode = root;
        boolean isLeft = false;

        while(curNode!=null){
            if(key<curNode.data){
                isLeft = true;
                prevNode = curNode;
                curNode = prevNode.left;
            }else if(key>curNode.data){
                prevNode = curNode;
                isLeft = false;
                curNode = prevNode.right;
            }else{
                //found
                break;
            }
        }//while

        if(curNode==null) throw new NoSuchElementException();

        if(curNode.left==null && curNode.right==null){ //child node가 없을 때
          
            if(curNode==root) root = null;
            if(isLeft) prevNode.left = null;
            else prevNode.right = null;

        }else if(curNode.left==null){ //right child만 존재

            if(curNode==root) root = curNode.right;
            if(isLeft) prevNode.left = curNode.right;
            else prevNode.right = curNode.right;

        }else if(curNode.right==null){ //left child만 존재

            if(curNode==root) root = curNode.left;
            if(isLeft) prevNode.left = curNode.left;
            else prevNode.right = curNode.left;

        }else{  //두개의 child node

            //오른쪽 subtree중 가장 작은 값을 찾아서 그 값으로 바꾸기
            curNode.data = findMin(curNode.right);
            //오른쪽 subtree중 가장 작은 값을 찾아서 삭제하기
            curNode.right = deleteMin(curNode.right);
        }
    }




    public int findMin(Node root){
        int min = root.data;
        if(root.left!=null){
            min = root.left.data;
            root = root.left;
        }
        return min;
    }



    public Node deleteMin(Node root){
        //가장 왼쪽까지 내려가서 그 값을 삭제하기
        //가장 왼쪽값의 오른쪽 자식이 존재하거나 존재하지 않거나 두가지 경우가 있다 (왼쪽값은 무조건 없음)
        Node prevNode = root;
        Node curNode = root;

        while(curNode.left!=null){
            prevNode = curNode;
            curNode = prevNode.left;
        }


        if(prevNode==curNode){
            if(curNode.right==null) return null;
            else return curNode.right;
        }


        if(curNode.right==null) prevNode.left = null;
        else prevNode.left = curNode.right;
        return root;
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

        Node newNode = new Node(data);

        if(root==null){
            root = newNode;
            return;
        }

        Node curNode = root;
        while(curNode!=null){

            if(data<curNode.data){

                if(curNode.left==null){
                    curNode.left = newNode;
                    break;
                }
                curNode = curNode.left;

            }else if(data>curNode.data){

                if(curNode.right==null){
                    curNode.right = newNode;
                    break;
                }
                curNode = curNode.right;

            }else{

                break;  //이미 그 값이 존재한다면 그냥 종료하기

            }

        }//while

    }


    public Node search(int key){

        Node curNode = root;

        while(curNode!=null){
            if(key<curNode.data){
                curNode = curNode.left;
            }else if(key>curNode.data){
                curNode = curNode.right;
            }else{
                return curNode;
            }
        }

        return null;

    }



    //1.no child
    //2.one child
    //3.two childeren
    public void delete(int key){

        Node curNode = root;
        Node prevNode = root;
        boolean isLeft = false;

        while(curNode!=null){
            if(key<curNode.data){
                isLeft = true;
                prevNode = curNode;
                curNode = prevNode.left;
            }else if(key>curNode.data){
                prevNode = curNode;
                isLeft = false;
                curNode = prevNode.right;
            }else{
                //found
                break;
            }
        }//while

        if(curNode==null) throw new NoSuchElementException();

        if(curNode.left==null && curNode.right==null){ //child node가 없을 때
            System.out.println("no child");
            System.out.println(prevNode.data+"   " +curNode.data);
            if(curNode==root) root = null;
            if(isLeft) prevNode.left = null;
            else prevNode.right = null;

        }else if(curNode.left==null){ //right child만 존재

            if(curNode==root) root = curNode.right;
            if(isLeft) prevNode.left = curNode.right;
            else prevNode.right = curNode.right;

        }else if(curNode.right==null){ //left child만 존재

            if(curNode==root) root = curNode.left;
            if(isLeft) prevNode.left = curNode.left;
            else prevNode.right = curNode.left;

        }else{  //두개의 child node

            //오른쪽 subtree중 가장 작은 값을 찾아서 그 값으로 바꾸기
            curNode.data = findMin(curNode.right);
            //오른쪽 subtree중 가장 작은 값을 찾아서 삭제하기
            curNode.right = deleteMin(curNode.right);
        }


    }


    public int findMin(Node root){
        int min = root.data;
        if(root.left!=null){
            min = root.left.data;
            root = root.left;
        }
        return min;
    }

    public Node deleteMin(Node root){
        //가장 왼쪽까지 내려가서 그 값을 삭제하기
        //가장 왼쪽값의 오른쪽 자식이 존재하거나 존재하지 않거나 두가지 경우가 있다
        Node prevNode = root;
        Node curNode = root;

        while(curNode.left!=null){
            prevNode = curNode;
            curNode = prevNode.left;
        }


        if(prevNode==curNode){
            if(curNode.right==null) return null;
            else return curNode.right;
        }


        if(curNode.right==null) prevNode.left = null;
        else prevNode.left = curNode.right;
        return root;
    }



    public void inorder(Node node){  //left-root-right ->오름차순 정렬
        if(node==null) return;
        inorder(node.left);
        System.out.print(node.data+" ");
        inorder(node.right);
    }


}
```

<br>

### 테스트 코드

```java
class BinarySearchTreeTest {

    private BinarySearchTree bst;

    @BeforeEach
    void setUp() {
        bst = new BinarySearchTree();
        bst.add(100);
        bst.add(10);
        bst.add(20);
        bst.add(50);
        bst.add(15);
        bst.add(150);
        bst.add(130);
        bst.add(40);
        bst.add(45);
      
        System.out.println("초기 트리 상태");
        bst.inorder(bst.getRoot());
        System.out.println();
        System.out.println("-----------");

    }

    @Test
    void add() {
        assertThat(bst.getRoot().data).isEqualTo(100);
      
        bst.add(100); //이미 존재하는 값 넣기 -> 변화 없음
        bst.inorder(bst.getRoot());
        System.out.println();
    }

    @Test
    void search() {
        assertThat(bst.getRoot().data).isEqualTo(100);
        assertThat(bst.search(100000)).isNull();
        assertThat(bst.search(100).data).isEqualTo(100);
        assertThat(bst.search(40).data).isEqualTo(40);
    }

    @Test
    void delete() {
        assertThat(bst.getRoot().data).isEqualTo(100);
        assertThrows(NoSuchElementException.class, () ->bst.delete(10000000));

        System.out.println("10삭제");
        bst.delete(10);
        bst.inorder(bst.getRoot());
        System.out.println();
        bst.add(10);
        System.out.println("-------");

        System.out.println("100삭제");
        bst.delete(100);
        assertThat(bst.getRoot().data).isEqualTo(130);
        bst.inorder(bst.getRoot());
        System.out.println();
        bst.add(100);
        System.out.println("-------");

        System.out.println("130삭제");
        bst.delete(130);
        bst.inorder(bst.getRoot());
        System.out.println();
        bst.add(130);
        System.out.println("-------");

        System.out.println("20삭제");
        bst.delete(20);
        bst.inorder(bst.getRoot());
        System.out.println();
        bst.add(20);
        System.out.println("-------");


    }


}
```

<br>

---

- references
  - [엔지니어 대한민국 -  BST insertion/deletion](https://www.youtube.com/watch?v=xxADG17SveY)

<br>

