## Disk Structure

### Sector

- 디스크를 관리하는 최소 단위
- 디스크의 섹터는 내부에서 관리하는 단위이고, 섹터에 데이터를 읽고 쓰는 요청은 디스크 컨트롤러가 직접 관리
- Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터 (어떤 파일 시스템을 쓰던 부팅과 관련된 정보 저장)
- **Logical block이 물리적인 디스크에 매핑된 위치**

### logical block

- 디스크의 **외부에서 보는** **디스크의 단위 정보 저장 공간들**
- 주소를 가진 1차원 배열처럼 취급
- 정보를 전송하는 최소 단위

## Disk Management

### 1. physical formatting(Low-Level formatting)

- 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
- 각 섹터는 logical block을 저장하기도 하지만, 부가적으로 섹터의 앞 뒤 부분에 붙는 header와 trailer로 구성된다. ⇒ header + 실제 data + trailer
- header와 trailer는 sector number, ECC (Error-Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영

### 2. Partitioning

- 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
- OS는 이것을 독립적 disk로 취급 (logical disk - c 드라이브와 d 드라이브 같은 것)
- file system 용도로 쓸 수도 있고, swapping의 용도로 사용할 수도 있음

### 3. Logical formatting

- 파일 시스템을 만드는 것
- FAT, inode, free space등의 구조 포함

### 4. Booting

- ROM(전원이 나가더라도 소량의 메모리 유지 - 부팅을 위한 소량의 loader 저장)에 있는 “small bootstrap loader” 실행
- sector 0 (boot block)을 load 하여 실행
- sector 0은 “full Bootstrap loader program”
- OS를 디스크에서 load하여 실행

## Disk Scheduling

### Access time의 구성

1. Seek time
    
    헤드를 해당 실린더로 움직이는데 걸리는 시간, 가장 시간이 오래 걸리는 부분
    
2. Rotational latency
    
    헤드가 원하는 섹터에 도달하기까지 걸리는 회전 지연 시간, seek time의 10분의 1정도
    
3. Transfer time
    
    실제 데이터의 전송 시간, 굉장히 적은 시간
    

### Disk bandwidth

단위 시간 당 전송된 바이트의 수

### Disk Scheduling

seek time을 최소화하는 것이 목표

seek time = seek distance

## Disk Scheduling Algorithm

### FCFS(First come First Service)

들어온 순서대로 처리

서로의 거리가 멀면 멀수록 head의 이동거리가 길어져 비효율적

### SSTF(Shortest Seek Time First)

현재 head위치에서 가장 가까운 요청을 처리

starvation 문제 발생 가능성 있음 ⇒ 가장 먼 요청으로 가야하는데 가장 가까운 요청이 다시 들어오게 될 경우

### SCAN

큐에 어떤 위치에 요청이 들어왔는지와 상관 없이 동작한다.

disk arm이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리

다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동

starvation 문제가 발생하지 않음

문제점: 실린더 위치에 따라 대기 시간이 다르다.

### C-SCAN

헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리

다른쪽 끝에 도달했으면 곧바로 출발점으로 다시 이동

이동 거리는 조금 길어질 수 있지만 SCAN보다 균일한 대기 시간을 제공

### N-SCAN

SCAN의 변형 알고리즘

일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service

### LOOK and C-LOOK

SCAN이나 C-SCAN은 요청이 있든 없든 헤드가 디스크 끝에서 끝으로 이동

LOOK가 C-LOOK은 헤드가 진행 중이다가 그 방향에 더이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동한다.

## Disk-Scheduling Algorithm의 결정

SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음

File의 할당 방법에 따라 디스크 요청이 영향을 받음

디스크 스케줄링 알고리즘은 필요한 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 만드는 것이 바람직

## Swap-Space Management

### Disk를 사용하는 이유

1. memory는 휘발성이 있기 때문에 비휘발성의 disk file system을 사용
2. 프로그램 실행을 위한 memory 공간 부족 → swap space (swap area) ⇒ 메모리의 연장

### Swap-space

물리적인 디스크를 파티셔닝해서 logical disk를 만들며, 운영체제가 독립적인 디스크로 간주하며 파일 시스템으로 사용하거나 swap area로 사용할 수 있다.

- Virtual memory system에서는 디스크를 memory 연장 공간으로 사용
- 파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
    - 공간 효율성보다는 속도 효율성이 우선
    - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
    - 따라서 block의 크기 및 저장 방식이 일반 파일 시스템과 다름

## RAID(Redundant Array of Independent Disks)

여러 개의 디스크를 묶어서 사용

여러 개의 디스크에 데이터를 어느 정도 중복 저장할 것인가 ⇒ 분산 저장 

### 목적

1. 디스크 처리 속도 향상
    1. 여러 디스크에 block의 내용을 **분산 저장**
    2. 병렬적으로 읽어 옴 (interleaving, striping)
2. 신뢰성 향상
    1. 동일 정보를 여러 디스크에 중복 저장
    2. 하나의 디스크가 고장시 다른 디스크에서 읽어옴 (Mirroring, shadowing)
    3. 단순히 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있음