---
layout: post
title: (알고리즘) Union-Find
tags:
  - 알고리즘
---

<br>

### Union-Find (합집합 찾기)

- `int getParent(int[] parent, int x)`
  - 재귀적으로 x노드의 부모를 찾아주고 부모값을 갱신해준다

- `void union(int[] parent, int a, int b)`
  - a노드와 b노드를 연결
  - 두 노드의 부모 중 더 작은 값으로 부모값을 통일 시켜준다
- `boolean find(int[] parent, int a, int b)`
  - a노드와 b노드가 같은 그래프에 속해있는지 true/false값을 반환해준다

<br>

### 구현코드

---

```java
  public int getParent(int[] parent, int x) {
		
    if(x!=parent[x]) parent[x] = getParent(parent,parent[x]);
    return parent[x];
    
    //if(parent[x] == x) return x;
		//return parent[x] = getParent(parent, parent[x]);
	}
	
	public void union(int[] parent, int a, int b) {
		a = getParent(parent,a);
		b = getParent(parent,b);
		if(a<b) parent[b] = a;
		else parent[a] = b; 
	}
	
	public boolean find(int[] parent, int a, int b) {
	    a = getParent(parent,a);
	    b = getParent(parent,b);
	    return a==b;  //부모가 같으면 -> 같은 그래프에 속해 있으면 -> true 반환
	}
```

<br>

<br>

#### 헷갈렸던 부분!!

---

- 노드 1,2,3,4,5가 있고 아래와 같이 union을 수행했다고 가정해보았다

```java
union(parent,1,2)
union(parent,2,4)
union(parent,3,5)
```

<br>

- 그 결과 1,2,4 / 3,5 두 그룹으로 나뉘고, parent 배열은 아래와 같다

| 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- |
| 1    | 1    | 3    | 1    | 3    |

<br>

- 이때 아래와 같이 union을 수행하면, parent 배열은 아래 표와 같이 바뀐다

```java
union(parent,3,4)
```

| 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- |
| 1    | 1    | 1    | 1    | 3    |

<br>

- 즉 1,2,3,4,5모든 노드가 하나의 그래프로 연결되었음에도, 노드 5의 부모값은 갱신되지 않는 문제가 발생한다고 생각함
- 하지만, 어느 두 노드가 서로 연결되어있는지 (같은 그래프에 속하는지) 확인하기 위해서는 `find()` 함수를 수행하게 되고
- `find( )` 함수는 각 노드의 부모 노드를 찾기 위해 `getParent()` 함수를 호출하면서 부모값을 갱신해준다

- `find(parent,2,5)` 를 수행하면, parent 배열은 아래와 같이 바뀌어 있고, true를 리턴해준다

| 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- |
| 1    | 1    | 1    | 1    | 1    |

<br>

---

[합집합 찾기(Union-Find)](https://www.youtube.com/watch?v=AMByrd53PHM)

<br>