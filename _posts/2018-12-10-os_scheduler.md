---
layout: post
title: 2018-12-10 OS - 스케쥴러
---

## 스케줄러

### 프로세스를 스케줄링하기 위한 Queue에는 3가지 종류가 존재한다.
1. Job Queue : 현재 시스템 내에 있는 모든 프로세스의 집합
2. Ready Queue : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
3. Device Queue : Device I/O 작업을 대기하고 있는 프로세스의 집합

### 장기스케줄러(Long-term scheduler or job scheduler)
- 메모리는 한정되어 있는데 많은 프로세스들이 한꺼번에 메모리에 올라올 경우, 대용량 메모리에 임시로 저장된다. 이 pool에 저장되어 있는 프로세스중 어떤 프로세스에 메모리를 할당하여 ready queue로 보낼지 결정하는 역할을 한다.
- 메모리와 디스크 사이의 스케줄링을 담당, 프로세스에 memory 및 각종리소스를 할당,
- degree of multiprogramming을 제어 : 메모리에 여러 프로그램이 올라갈때 몇개를 올릴지 제어
- 프로세스의 상태 new -> ready(in memeory)

### 단기스케쥴러(shrot-tem scheduler or cpu scheduler)
- cpu와 메모리 사이의 스케쥴링 담당, Ready Queue에 존재하는 프로세스 중 어떤 프로세스를 runningt 시킬지 결정
- 프로세스에 cpu를 할당(scheduler dispatch), 프로세스의 상태 ready -> running -> waiting -> ready

### 중기스케쥴러(Medium-term scheduler or swapper)
- 여유공간 마련을 위해 프로세스를 통째로 디스크에서 쫒아냄(swapping)
- 프로세스에게서 memory를 deallocate, 현 시스템에서 메모리에 너무 많은 프로그램이 동시에 올라가는 것을 조절하는 스케쥴러
- 프로세스의 상태 : ready -> suspended


### 동기와 비동기의 차이 : 일반적으로 동기와 비동기의 차이는 메소드를 실행시킴과 동시에 반환 값이 기대되는 경우를 동기 라고 표현하고 그렇지 않은 경우에 대해서 비동기 라고 표현한다. 동시에라는 말은 실행되었을 때 값이 반환되기 전까지는 blocking되어 있다는 것을 의미한다. 비동기의 경우, blocking되지 않고 이벤트 큐에 넣거나 백그라운드 스레드에게 해당 task 를 위임하고 바로 다음 코드를 실행하기 때문에 기대되는 값이 바로 반환되지 않는다.
