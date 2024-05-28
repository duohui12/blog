---
layout: post
title: Tree의 종류
tags:
  - 자료구조_알고리즘
---

<br>

### Tree란

- 부모 자식 관계를 가진다 (계층적인 구조)
- 최상위의 노드는 Root / 맨 끝에 더 이상 자식이 없는 노드는 Leaf

<br>

### Binary Tree

- child node가 최대 2개인 노드들로 이루어진 트리

<br>

### Binary Search Tree

- 한 노드를 기준으로 왼쪽 노드와 그 이하 모든 노드들은 현재 노드의 값보다 작은 값을 가지고, 오른쪽 노드와 그 이하 모든 노드들은 현재 노드의 값보다 큰 값을 가진다. 

<br>

### Complete Binary Tree

- 트리의 레벨별로 왼쪽부터 오른쪽으로 중간에 빈 곳 없이 노드가 채워져 있다  

<br>

### Full Binary Tree

- 모든 노드의 child node 수가 0개 아니면 2개

<br>

### Perfect Binary Tree

-  Leaf 노드를 제외하고 모든 노드가 두 개의 자식을 가진다. 레벨의 갯수가 n일때 노드의 갯수가 2^n-1

<br>

![tree](https://github.com/AmyJJung/blog/blob/main/images/DataStructure/tree.png?raw=true)