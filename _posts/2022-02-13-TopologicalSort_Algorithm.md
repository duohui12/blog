---
layout: post
title: (알고리즘) 위상정렬
tags:
  - 알고리즘
---

<br>

### 위상정렬이란

----

위상 정렬(topological sorting)은 유향 그래프의 꼭짓점들(vertex)을 변의 방향을 거스르지 않도록 나열하는 것을 의미한다. 위상정렬을 가장 잘 설명해 줄 수 있는 예로 대학의 선수과목(prerequisite) 구조를 예로 들 수 있다. 만약 특정 수강과목에 선수과목이 있다면 그 선수 과목부터 수강해야 하므로, 특정 과목들을 수강해야 할 때 위상 정렬을 통해 올바른 수강 순서를 찾아낼 수 있다. 이와 같이 선후 관계가 정의된 그래프 구조 상에서 선후 관계에 따라 정렬하기 위해 위상 정렬을 이용할 수 있다. 정렬의 순서는 유향 그래프의 구조에 따라 여러 개의 종류가 나올 수 있다. 위상 정렬이 성립하기 위해서는 반드시 그래프의 순환이 존재하지 않아야 한다. 즉, 그래프가 비순환 유향 그래프(directed acyclic graph)여야 한다. [위키](https://ko.wikipedia.org/wiki/%EC%9C%84%EC%83%81%EC%A0%95%EB%A0%AC)

<br>

### 문제풀어보기 - [LeetCode 210번](https://leetcode.com/problems/course-schedule-ii/)

---

- indegree가 0인 노드 탐색
- 관련 edge 제거하고, indegree값 -1
- 루프 종료후 모든 노드를 탐색했는지 확인

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {

        int[] result = new int[numCourses];
        int count = 0;

        int[] indegree = new int[numCourses];
        List<Integer>[] graph = new ArrayList[numCourses];

        //graph만들기
        for(int i=0; i<prerequisites.length; i++){
            int course1 = prerequisites[i][1];
            int course2 = prerequisites[i][0];
            if(graph[course1]==null) graph[course1] = new ArrayList<>();
            graph[course1].add(course2);
            indegree[course2]++;
        }


        Queue<Integer> q = new LinkedList<>();
        //indegree == 0인 노드 queue에 넣기
        for(int i=0; i<numCourses; i++){
            if(indegree[i]==0){
                q.offer(i);
                result[count++] = i;
            }
        }


        //queue에서 하나씩 꺼내면서 연결되어있는 부분 정리
        while(!q.isEmpty()){
            int cur = q.poll();
            if(graph[cur]==null) continue;
            for(int next : graph[cur]){
                indegree[next]--;
                if(indegree[next]==0){
                    q.offer(next);
                    result[count++] = next;
                }
            }
        }


        if(count==numCourses) return result;
        else return new int[0];

    }
}
```

<br>