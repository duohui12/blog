---
layout: post
title: (운영체제) Paging System과 LRU,LFU
tags:
  - 운영체제
---

<br>

>[운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323) 강의를 보면서 헷갈렸던 부분을 정리한 내용

<br>

- LRU (Least Recently Used) 
  - 최근에 가장 적게 참조된 페이지, 즉 가장 오래 전에 참조된 페이지를 메모리에서 몰아낸다
  - 메모리에 올라와있는 페이지들에 대해 이들이 참조된 `시간` 을 기준으로 교체 페이지를 선정하는 알고리즘
- LFU(Least Frequently Used) 
  - 가장 적게 참조된 페이지를 메모리에서 몰아낸다
  - 메모리에 올라와있는 페이지들에 대해 이들이 참조된 `횟수` 를 기준으로 교체 페이지를 선정하는 알고리즘
- buffer cache
  - 버퍼 캐시(buffer cache)란 디스크 입출력 효율을 높이기 위해 최근 사용된 디스크 블록을 메모리에 캐시하는 것을 말한다.

<br>

paging system에서 접근하려는 페이지가 메모리에 없고(invalid), 메모리가 꽉찬 경우 어떤 페이지를 교체해야 할까?

이때 LRU(Least Recently Used) 또는 LFU(Least Frequently Used) 알고리즘을 사용하면 될 것 같다.

그런데 위 두 알고리즘은 paging system에서 사용할 수 없다. 

왜냐하면 logical address에서 physical address로 주소 변환 시에 해당 페이지가 메모리에 올라와 있다면 (valid)  

MMU 하드웨어가 주소를 변환해주기 때문에 운영체제는 이 사실을 모른다.

즉, 어떤 시점에 어떤 페이지가 참조되었는지에 대한 정보들을 운영체제는 모른다. 

반면 page fault가 발생하면 제어권이 운영체제에게 넘어가게 된다. 그리고 운영체제는 필요한 페이지를 찾아 메모리에 올려준다.

즉, page fault가 발생한 경우에만 운영체제가 페이지 참조에 관한 정보를 알게 된다. 

따라서, `clock algorithm` 이라고 하는 근사 LRU 알고리즘을 사용한다.

<br>

버퍼 캐시 (buffer cache) 의 경우

디스크에 있는 어떤 파일에 접근하기 위해 무조건 `system call` 을 호출한다. (항상 운영체제로 제어권이 넘어간다)

따라서 페이징 시스템과는 다르게 모든 접근 정보를 운영체제가 알기 때문에 LRU, LFU 알고리즘을 사용할 수 있다.

