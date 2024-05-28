---
layout: post
title: (운영체제) System Structure & Program Execution
tags:
  - 운영체제
---

<br>

>[운영체제](http://www.kocw.net/home/search/kemView.do?kemId=1046323) 강의를 보면서 헷갈렸던 부분을 정리한 내용

<br>

<img src="https://github.com/AmyJJung/blog/blob/main/images/computer_structure.png?raw=true" alt="스크린샷 2021-02-09 오전 11.13.55" style="zoom:60%;"/>

<br>

- `CPU`
  - cpu는 매 clock마다 memory에서 instruction을 읽어서 수행
  - cpu는 instruction을 수행한 후 매번 interrupt line을 체크한다

 - `Mode bit`

    - 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호장치
    - Mode bit을 통해 하드웨어적으로 2가지의 operation 모드를 지원
       - 0 모니터모드 => OS코드 수행
       - 1 사용자모드 => 사용자 프로그램 수행

   - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 '특권명령'으로 규정
   - Interrupt나 Exception 발생시 하드웨어가 mode bit을 0으로 바꾼다
   - 사용자 프로그램에게 cpu를 넘길 때 mode bit을 1로 바꾼다

- `Timer`
  - 특정 프로그램이 cpu를 독점하는 것을 막는다
  - 예를들어 사용자 프로그램에서 무한루프가 돌고 있을 경우, timer가 터지면 Interrupt가 걸려서 cpu제어권이 운영체제에게 넘어간다

- `Device Controller`

  - 해당 I/O장치를 관리하는 일종의 작은 cpu (하드웨어)
  - I/O 작업이 끝났을 경우 interrupt로 그 사실을 cpu에게 알려준다

  - cf) Device Driver : os코드 중 각 장치별 처리루틴 (소프트웨어)

- `DMA (Direct Memory Access)`
  - I/O장치에서 작업이 끝나면 local buffer에 저장된 값들을 DMA Controller가 직접 메모리에 카피해주고, 인터럽트를 발생시킨다
  - DMA 사용으로 프로그램 수행 중 인터럽트 발생 횟수를 최소화시켜 cpu 효율을 늘린다
  - memory controller가 DMA와 CPU의 동시 접근을 통제한다

- `Interrupt`

  - 현대의 운영체제는 인터럽트에 의해 구동된다
  - `Interrupt(하드웨어 인터럽트)` => 타이머, I/O controller가 발생시키는 인터럽트 등..

  - `Trap(소프트웨어 인터럽트)`
    - `System Call` => 프로그램이  OS서비스를 요청하기 위해 직접 인터럽트 라인을 세팅한다
    - `Exception` => 프로그램이 오류를 범한 경우 발생
  - 인터럽트 관련 용어
    - 인터럽트 벡터 => 해당 인터럽트 처리 루틴 주소를 가지고 있다
    - 인터럽트 처리 루틴 (ISR) => 해당 인터럽트를 처리하는 커널 함수

- `동기식 입출력 (syschronous I/O)` => I/O가 끝나야지만 다음 명령어를 수행할 수 있는 경우

  - I/O끝날 때까지 CPU 낭비시킴
  - I/O가 완료될 때까지 다른 프로세스로 CPU제어권이 넘어간다. 

- `비동기식 입출력 (asynchronous I/O)`

  - I/O요청만 하고 다시 CPU 제어권을 얻어서 다음 작업을 수행
  - I/O가 끝나면 I/O device가 인터럽트 걸어서 알려줌

<br>

### Q) 사용자 프로그램은 어떻게 I/O를 하는걸까?

- 먼저 사용자 프로그램은 운영체제에게 I/O를 요청한다 `시스템 콜(소프트웨어 인터럽트)`
- I/O Controller가 작업이 끝나면 `하드웨어 인터럽트`를 건다. 작업이 끝났음을 cpu에 알려줌

<br>