---
layout: post
title: (알고리즘) 최소 신장 트리(MST, Minimum Spanning Tree)
tags:
  - 알고리즘
---

<br>

### Spanning Tree (신장트리)

---

- 그래프 내의 모든 노드를 포함하는 부분 그래프
- 하나의 그래프의 여러 개의 신장트리가 존재할 수 있다
- 모든 노드를 포함하고, 사이클이 형성되지 않는 그래프이다
- N개의 정점을 가질 때 N-1개의 간선으로 연결된다

<br>

### Minimun Spanning Tree (최소신장트리)

---

- 트리를 구성하는 간선(edge)의 가중치 합이 최소인 신장트리

<br>

### Kruskal Algorithm (크루스칼 알고리즘)

---

- 모든 정점을 최소 비용으로 연결하는 최적 해답을 구한다. 즉, 최소신장트리를 찾아주는 알고리즘
- 간선을 가중치 오름차순으로 정렬해서 비용이 작은 순서대로 신장 트리를 만들어가기 때문에 그리디 알고리즘의 일종이다
- 동작방식
  - 그래프의 간선들을 가중치 오름차순으로 정렬
  - 정렬된 간선 순서대로, 사이클을 형성하지 않는 간선을 선택한다.
    - 이때 사이클을 형성하는지 확인하기 위해 `Union&Find 알고리즘` 을 사용한다

<br>

### 백준 1197_최소 스패닝 트리 문제풀이

---

```java
public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int v = Integer.parseInt(st.nextToken());  //정점의 갯수
        int e = Integer.parseInt(st.nextToken());  //간선의 갯수
      
        List<Edge> edgeList = new ArrayList<>();
        for(int i=0; i<e; i++){
            st = new StringTokenizer(br.readLine());
            int n1 = Integer.parseInt(st.nextToken());
            int n2 = Integer.parseInt(st.nextToken());
            int cost = Integer.parseInt(st.nextToken());
            edgeList.add(new Edge(n1,n2,cost));
        }
      
        //간선을 가중치 오름차순으로 정렬
        Collections.sort(edgeList);

        //부모 노드를 기록하는 parent배열 초기화
        int[] parent = new int[v+1];
        for(int i=0; i<v+1; i++) parent[i] = i; 

        int ans = 0;
      
        for(int i=0; i<e; i++){
            Edge edge = edgeList.get(i);
            if(!find(parent,edge.n1,edge.n2)) {  //n1과 n2의 부모가 다름(사이클x) -> 가중치 더하고, 연결
                ans += edge.cost;
                union(parent,edge.n1,edge.n2);
            }
        }

        System.out.println(ans);
    }

    public static int getParent(int[] parent, int x){
        if(x!=parent[x]) parent[x] = getParent(parent,parent[x]);
        return parent[x];
    }

    public static void union(int[] parent, int a, int b){
        a = getParent(parent,a);
        b = getParent(parent,b);

        if(a<b) parent[b] = a;
        else parent[a] = b;
    }

    public static boolean find(int[] parent, int a, int b){
        a = getParent(parent,a);
        b = getParent(parent,b);
        return a==b;
    }

}


```



