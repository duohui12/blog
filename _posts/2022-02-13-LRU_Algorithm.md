---
layout: post
title: (알고리즘) LRU Cache - O(1) 복잡도로 직접 구현하기
tags:
  - 알고리즘
---

<br>

### [LeetCode 146. LRU Cache](https://leetcode.com/problems/lru-cache/)

----

- LRU Cache는 capacity만큼의 크기를 가진다.

- `int get(int key)`  : 값이 없으면 -1리턴, 있으면 value값 리턴

- `void put(key,value)` : 없으면 새로 생성, 이미 있으면 value값 업데이트 / capacity 만큼 데이터가 있는 경우 LRU(최근에 가장 적게 사용된 데이터를) 캐시에서 쫒아낸다.

- get, put 모두 O(1) 의 복잡도로 해결해야 한다

<br>

### 해결 방법

---

- `Doubly LinkedList`를 가지고, head에 데이터를 넣거나 tail에서 데이터를 쫒아내는 작업들을 한다.

- LinkedList에 있는 데이터에 접근하려면 head부터 포인터를 타고 들어가기 때문에 0(N)의 시간이 걸린다.

- 이 문제에서는 O(1)의 시간으로 LinkedList의 노드를 찾아야 하기 때문에 , `HashMap<Key,Node>`의 자료구조를 함께 사용한다.

<br>

### 코드

---

```java
class LRUCache {

    public class CacheItem{
        int key;
        int val;
        CacheItem prev;
        CacheItem next;
        public CacheItem(int key, int val){
            this.key = key;
            this.val = val;
        }
    }
    
    CacheItem head;
    CacheItem tail;
    int capacity;
    
    HashMap<Integer, CacheItem> map;
    
    public LRUCache(int capacity) {
        this.capacity = capacity;
        head = null;
        tail = null;
        map = new HashMap<>();
    }
    
    public int get(int key) {
        if(!map.containsKey(key)){
            return -1;
        }else{
            CacheItem cur = map.get(key);
            
            //최근 사용된 데이터는 head로 옮겨준다.
            if(cur!=head){
                
                if(cur==tail){
                    tail = tail.prev;
                }
                
                if(cur.prev != null) cur.prev.next = cur.next;
                if(cur.next != null) cur.next.prev = cur.prev;
                            
                cur.next = head;
                cur.prev = null;
                head.prev = cur;
                head = cur;
                
            }
            return cur.val;
        }
    }
    
    public void put(int key, int value) {
        if(get(key)==-1){ 
            CacheItem cur = new CacheItem(key,value);
            if(head==null){
                head = cur;
                tail = cur;
            }else{
                head.prev = cur;
                cur.next = head;
                head = cur;
            }
            map.put(key,cur);
            
            if(map.size()> capacity){
                //꼬리 없애기
                map.remove(tail.key);
                tail = tail.prev;
                tail.next = null;
            }
            
        }else{ //get(key) !=-1 일 경우 -> 해당 노드를 head로 옮겨주는 작업까지 해준다.
           map.get(key).val = value; //update
        }
        s
    }
}


```

<br>

[LRU 캐시 O(1) 복잡도로 직접 구현하기 [기술면접 라이브코딩] LC #146 LRU Cache - YouTube](https://www.youtube.com/watch?v=WOaQfWqlV7A&list=PL2mzT_U4XxDl8PP-jMk4rt6BPzBtS__pQ&index=4)
