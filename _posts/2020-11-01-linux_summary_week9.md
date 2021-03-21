---
layout: post
title: "Linux 정리 #7 - Process and Thread"
description: ""
modified: 2020-11-01
categories: study
tags: linux
comments: true
---

> 본 포스트는 2020-2학기 Linux System and its Applications 9주차 수업을 정리한 글입니다.

## Process in Linux
- 프로세스는 *'Running Program'(실행중인 프로그램)*
- 프로그램을 실행시키면 메인메모리에 올라오고 새로운 프로세스가 생성/시작
- 리눅스는 프로세스를 unique한 5자리 숫자의 프로세스 ID(pid)로 관리
    - 동시에 같은 Pid를 가지는 프로세스 존재 X


### Creating process in user-level
- fork system call
    - *`fork()`* 시스템 콜을 통해 프로세스 생성
        - *`fork()`* 실패시, -1 리턴
        - *`fork()`* 성공시, *child* 에게 0 리턴하고 *parent*에게 *child*의 pid 리턴
    - *Parent Process (Process 자기자신)*
    - *Child Process (복제된 process)*


### Process descriptor : task_struct (in Linux)
- 리눅스 커널은 프로세스를 *'task list'*라고 불리는 `Circular Doubly linked list`로 관리
- 각 프로세스는 `task_struct`라는 구조체에 Context 저장

![/assets/img/linux/summary7/Untitled.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled.png)

- 리눅스에서는 하나의 프로세스를 하나의 태스크로 mapping 시켜서 관리


### Process States (in Linux)
![/assets/img/linux/summary7/Untitled%201.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%201.png)

- **TASK_RUNNING**
    - 수행가능한 프로세스
    - 수행 중이거나 실행큐에서 대기중인 2가지 상태
    - 태스크는 user- 또는 kernel-space에 존재
- **TASK_INTERRUPTIBLE**
    - *sleeping (Blocked)*
    - 특정 조건을 기다리는 중 → 조건을 만족하면 다시 *runnable* 상태로(***TASK_RUNNING***)
- **TASK_UNINTERRUPTIBLE**
    - ***TASK_INTERRUPTIBLE***와 유사한 상태지만, runnable로 wake up 되지 않는 상태
    - 인터럽트 신호가 오더라도 *wait*해야될 때 사용
- **TASK_STOPPED**
    - 프로세스 실행이 끝남
    - *running*도 *waiting*도 아님

- *Linux 프로세스 state를 변경하는 방법*
    - `set_current_state(task, state)`
    - `set_task_state(task, state)`

### Process states Example (생산자-소비자문제)

- Producer(생산자)
    - Event 생성 및 소비자 *wake up*
- Consumer(소비자)
    - Event가 존재하면 리스트에 존재하는 모든 이벤트 처리
    - 그렇지 않으면, *wake up*할 때 까지 *Sleep*

![/assets/img/linux/summary7/Untitled%202.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%202.png)

### Procedure of Task Creation in Linux

- `fork()`
    - *caller(parent)* 태스크의 모든 상태를 그대로 복사해서 *task*(*child)* 생성

    ![/assets/img/linux/summary7/Untitled%203.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%203.png)

- `Clone()`

    ![/assets/img/linux/summary7/Untitled%204.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%204.png)

    - *caller* 태스크의 일부를 공유하는 새로운 태스크 생성
    - `fork()` 와 다르게 *child process*가 *caller(parent)*의 context일부를 공유
    - *Clone Flags*
        - *Clone Flags*를 통해서 context 공유범위를 정해줌

        ![/assets/img/linux/summary7/Untitled%205.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%205.png)

        - ***CLONE_VM***
            - CLONE_VM is set, *calling* 프로세스와 *child* 프로세스는 같은 메모리 공간에서 실행
            - CLONE_VM is *not* set, *child* 프로세스는 복제된 다른 메모리 공간에서 실행

### `do_fork()`

- **Execution flow**

![/assets/img/linux/summary7/Untitled%206.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%206.png)

- `copy_process()` **Execution flow**

    ![/assets/img/linux/summary7/Untitled%207.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%207.png)

    ![/assets/img/linux/summary7/Untitled%208.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%208.png)

    ![/assets/img/linux/summary7/Untitled%209.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%209.png)

- **Sharing vs. copying state "ABC"**

    ![/assets/img/linux/summary7/Untitled%2010.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2010.png)

    - *'ABC'*라는 State가 있고, Child가 ABC_struct의 m값에 1을 더한다고 가정
    - *CLONE_ABC is set, ABC_struct는 공유되고, 공유된 count의 값은  m+1*
    - *CLONE_ABC is not set, 각각 ABC_struct를 가지고,  Child의 ABC_struct의 count값 1*

- **Copying Address space**
    - `copy_mm()`에 의해 수행
    - 페이지 테이블을 가리키고있는 메모리 관련 *mm_struct*

    ![/assets/img/linux/summary7/Untitled%2011.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2011.png)

    - *Child*가 모든 page table을 복사하는 것은 너무 오래걸림 → *Copy-on-write(COW)사용*
    - ***Copy-on-write(COW)***

        ![/assets/img/linux/summary7/Untitled%2012.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2012.png)

        - 실제 리소스를 수정할 때까지 복사 작업을 지연 (수정이 있으면 복사 후 수정)
        - 커널의 여러 곳에서 사용(`fork()`, 파일 시스템, 가상 메모리 등)

    - ***Copy-on-write(COW)** in `do_fork()`*
        - 페이지 테이블만 복사하고 *page Frame*은 공유
        - *Page Frame*에 수정이 일어나면 *Page Frame*을 *Copy* 후 수정 *(Copy-on-write)*

    ![/assets/img/linux/summary7/Untitled%2013.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2013.png)

## Thread

- Thread는 Process안의 실행흐름의 단위 *(lightweight process)*
- PC(프로그램 카운터), 스택, 쓰레드 ID로 구성
- 하나의 프로세스에 여러 개의 Thread 가능 → CPU Utilization을 높임
- *Multi-Threading*(멀티쓰레딩)
    - 프로세스를 여러개의 쓰레드로 나누어 병렬처리의 극대화
    - *Mulit-Processing*대비 *Context Switching*의 오버헤드 적고, 메모리 사용도 줄일 수 있음
- 쓰레드는 동일한 주소 공간을 공유하고 동일한 프로그램에 속하는 실행 흐름 → 독립적으로 CPU에 할당

![/assets/img/linux/summary7/Untitled%2014.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2014.png)

### Creating Thread in User-level

- `pthread_create(..., void *(*start_routine)(void*), void *arg)`
    - *calling* 프로세스 내의 새로운 쓰레드 생성
    - 쓰레드는 항상 생성 후 `start_routine(arg)`을 수행
- `pthread_join(pthread_thread, void **retval)`
    - 생성된 쓰레드를 terminate할 때 사용
    - join 실패 시 *'zombie thread'* 상태로 남아서 system 리소스를 소비

- **Example**
    - `pthread_create`를 사용해 쓰레드를 생성하는 예제
        - pthread_t : type that represents a thread ID
        - pthread_self(): function that returns thread ID

    ![/assets/img/linux/summary7/Untitled%2015.png](/https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2015.png)

    ![/assets/img/linux/summary7/Untitled%2016.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2016.png)

### Kernel Thread

- 커널에서 background operation을 수행할 때 주로 사용 (Demon Thread)
- User-level 스레드와 유사
- 커널 스레드 생성시, `kthread_create()` 사용
- `kthread_run()`, `kthread_stop()`
- `kthread_should_stop()` : 커널 쓰레드를 계속 실행 혹은 stop을 결정할 때 사용
- **Example in EXT4 file system**
    - EXT4 파일 시스템은 커널 스레드를 사용하여 백그라운드에서 파일 시스템 초기화를 완료

        ![/assets/img/linux/summary7/Untitled%2017.png](https://cdn.jsdelivr.net/gh/ddamddi/ddamddi.github.io/assets/img/linux/summary7/Untitled%2017.png)