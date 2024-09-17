# Process management system calls (프로세스 관리 시스템 호출)
Processes are the most fundamental abstraction in a Linux system, after files. As object code in execution - active, alive, running programs - processes are more than just assembly language; they consist of data, resources, state, and a virtualized computer.

Linux took an interesting path, one seldom traveled, and separated the act of reating a new process from the act of loading a new binary image. Although the two tasks are performed in tandem most of the time, the division has allowed a great deal of freedom for experimenta tion and evolution for each of the tasks. This road less traveled has survived to this day, and while most operating systems offer a single system call to start up a new program, Linux requires two: a fork and an exec.


프로세스 관리 시스템 호출
프로세스는 파일 다음으로 리눅스 시스템에서 가장 기본적인 추상화입니다. 실행 중인 객체 코드(활성, 살아 있는, 실행 중인 프로그램)로서 프로세스는 어셈블리 언어 그 이상입니다. 데이터, 리소스, 상태 및 가상화된 컴퓨터로 구성됩니다.

리눅스는 거의 가지 않는 흥미로운 길을 택했고, 새로운 프로세스를 생성하는 행위와 새로운 바이너리 이미지를 로드하는 행위를 분리했습니다. 두 가지 작업은 대부분 동시에 수행되지만, 이 구분은 각 작업에 대한 실험과 진화를 위한 많은 자유를 허용했습니다. 덜 다니는 이 길은 오늘날까지 살아남았고, 대부분의 운영 체제가 새로운 프로그램을 시작하기 위해 단일 시스템 호출을 제공하는 반면, 리눅스는 두 가지, 즉 fork와 exec를 요구합니다.

> [!NOTE]
>This document is based on linasm.sourceforge.net[^1] and the Linux manual[^2].
>
> 이 문서는 linasm.sourceforge.net[^1] 및 리눅스 매뉴얼[^2]을 참고 하였음.

## Table of Content
- Creation and termination (생성 및 종료)
- Pocess id (프로세스 ID)
- Session id (세션아이디)
- Process group id (프로세스 그룹 ID)
- Users and groups (사용자 및 그룹)
- Namespaces (네임스페이스)
- Resource limits (리소스 제한)
- Process scheduling (프로세스 스케줄링)
- Virtual memory (가상 메모리)
- Threads (스레드)
- Miscellaneous (기타)

## Creation and termination (생성 및 종료)
### CLONE
> Create a child process

자식 프로세스 생성

**LIBRARY**:

**Arguments**:

**Return Value**: 

- manual page: https://man7.org/linux/man-pages/man2/clone.2.html

### FORK
> Create a child process

자식 프로세스 생성

**LIBRARY**:

**Arguments**:

**Return Value**:
 
- manual page: http://man7.org/linux/man-pages/man2/fork.2.html

### VFORK
> Create a child process and block parent

자식 프로세스를 생성하고 부모 프로세스를 차단합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/vfork.2.html

### EXECVE
> Execute program

프로그램 실행

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/execve.2.html

### EXECVEAT
> Execute program relative to a directory file descriptor

디렉토리 파일 설명자에 상대적으로 프로그램 실행

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/execveat.2.html

### EXIT
> Terminate the calling process

프로세스 종료

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/exit.2.html

### EXIT_GROUP
> Terminate all threads in a process

프로세스의 모든 스레드를 종료합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/exit_group.2.html

### WAIT4
> Wait for process to change state

프로세스가 상태를 변경할 때까지 대기.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/wait4.2.html

### WAITID
> Wait for process to change state

프로세스가 상태를 변경할 때까지 대기.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/waitid.2.html

## Pocess id (프로세스 ID)
### GETPID
> Get process ID

프로세스 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getpid.2.html

### GETPPID
> Get parent process ID

부모 프로세스 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getppid.2.html

### GETTID
> Get thread ID

스레드 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/gettid.2.html

## Session id (세션아이디)
### SETSID
> Set session ID

세션 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setsid.2.html

### GETSID
> Get session ID

세션 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getsid.2.html

## Process group id (프로세스 그룹 ID)
### SETPGID
> Set process group ID

프로세스 그룹 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setpgid.2.html

### GETPGID
> Get process group ID

프로세스 그룹 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getpgid.2.html

### GETPGRP
> Get the process group ID of the calling process

호출 프로세스의 프로세스 그룹 ID를 가져오기.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getpgrp.2.html

## Users and groups (사용자 및 그룹)
### SETUID
> Set real user ID

실제 사용자 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setuid.2.html

### GETUID
> Get real user ID

실제 사용자 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getuid.2.html

### SETGID
> Set real group ID

실제 그룹 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setgid.2.html

### GETGID
> Get real group ID

실제 그룹 ID를 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getgid.2.html

### SETRESUID
> Set real, effective and saved user IDs

실제, 유효 및 저장된 사용자 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setresuid.2.html

### GETRESUID
> Get real, effective and saved user IDs

실제, 유효 및 저장된 사용자 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getresuid.2.html

### SETRESGID
> Set real, effective and saved group IDs

실제, 유효 및 저장된 그룹 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setresgid.2.html

### GETRESGID
> Get real, effective and saved group IDs

실제, 유효 및 저장된 그룹 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getresgid.2.html

### SETREUID
> Set real and/or effective user ID

실제 및/또는 유효 사용자 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setreuid.2.html

### SETREGID
> Set real and/or effective group ID

실제 및/또는 유효 그룹 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setregid.2.html

### SETFSUID
> Set user ID used for file system checks

파일 시스템 검사에 사용되는 사용자 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setfsuid.2.html

### SETFSGID
> Set group ID used for file system checks

파일 시스템 검사에 사용되는 그룹 ID 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setfsgid.2.html

### GETEUID
> Get effective user ID

유효 사용자 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/geteuid.2.html

### GETEGID
> Get effective group ID

유효 그룹 ID 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getegid.2.html

### SETGROUPS
> Set list of supplementary group IDs

보충 그룹 ID 목록 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setgroups.2.html

### GETGROUPS
> Get list of supplementary group IDs

보충 그룹 ID 목록 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getgroups.2.html

## Namespaces (네임스페이스)
### SETNS
> Reassociate thread with a namespace

네임스페이스와 스레드 다시 연결

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setns.2.html

## Resource limits (리소스 제한)
### SETRLIMIT
> Set resource limits

리소스 제한 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setrlimit.2.html

### GETRLIMIT
> Get resource limits

리소스 제한 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getrlimit.2.html

### PRLIMIT
> Set and get the resource limits of an arbitrary process

임의의 프로세스의 리소스 제한을 설정하고 가져옵니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/prlimit.2.html

### GETRUSAGE
> Get resource usage

리소스 사용량 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getrusage.2.html

## Process scheduling (프로세스 스케줄링)
### SCHED_SETATTR
> Set scheduling policy and attributes

스케줄링 정책 및 속성 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_setattr.2.html

### SCHED_GETATTR
> Get scheduling policy and attributes

스케줄링 정책 및 속성 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_getattr.2.html

### SCHED_SETSCHEDULER
> Set scheduling policy/parameters

스케줄링 정책/매개변수 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_setscheduler.2.html

### SCHED_GETSCHEDULER
> Get scheduling policy/parameters

스케줄링 정책/매개변수 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_getscheduler.2.html

### SCHED_SETPARAM
> Set scheduling parameters

스케줄링 매개변수 설정

- manual page: htt*LIBRARY*p://man7.org/linux/man-pages/man2/*sched_setparam*.:
*Arguments*.:
*Return Value*.: 2.html

### SCHED_GETPARAM
> Get scheduling parameters

스케줄링 매개변수 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_getparam.2.html

### SCHED_SETAFFINITY
> Set a process's CPU affinity mask

프로세스의 CPU 친화도 마스크 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_setaffinity.2.html

### SCHED_GETAFFINITY
> Get a process's CPU affinity mask

프로세스의 CPU 친화도 마스크 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_getaffinity.2.html

### SCHED_GET_PRIORITY_MAX
> Get static priority range (max value)

우선 순위 범위(최대값) 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_get_priority_max.2.html

### SCHED_GET_PRIORITY_MIN
> Get static priority range (min value)

우선 순위 범위 가져오기(최소값)

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_get_priority_min.2.html

### SCHED_RR_GET_INTERVAL
> Get the SCHED_RR interval for the named process

프로세스에 대한 SCHED_RR 간격을 가져옵니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_rr_get_interval.2.html

### SCHED_YIELD
> Yield the processor

프로세서를 생산하다

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/sched_yield.2.html

### SETPRIORITY
> Set program scheduling priority

프로그램 스케줄링 우선순위 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/setpriority.2.html

### GETPRIORITY
> Get program scheduling priority

프로그램 스케줄링 우선 순위 얻기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/getpriority.2.html

### IOPRIO_SET
> Set I/O scheduling class and priority

I/O 스케줄링 클래스 및 우선순위 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/ioprio_set.2.html

### IOPRIO_GET
> Get I/O scheduling class and priority

I/O 스케줄링 클래스 및 우선순위 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/ioprio_get.2.html


## Virtual memory (가상 메모리)
### BRK
> Change data segment size

데이터 세그먼트 크기 변경

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/brk.2.html

### MMAP
> Map files or devices into memory

파일이나 장치를 메모리에 매핑합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mmap.2.html

### MUNMAP
> Unmap files or devices into memory

파일 또는 장치를 메모리에 매핑 해제합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mremap.2.html

### MREMAP
> Remap a virtual memory address

가상 메모리 주소 다시 매핑

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mremap.2.html

### MPROTECT
> Set protection on a region of memory

메모리 영역에 보호 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mprotect.2.html

### MADVISE
> Give advice about use of memory

메모리 사용에 대한 조언을 해주세요

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/madvise.2.html

### MLOCK
> Lock part of the calling process's virtual address space into RAM

호출 프로세스의 가상 주소 공간의 일부를 RAM에 잠급니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mlock.2.html

### MLOCK2
> Lock part of the calling process's virtual address space into RAM

호출 프로세스의 가상 주소 공간의 일부를 RAM에 잠급니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mlock2.2.html

### MLOCKALL
> Lock all of the calling process's virtual address space into RAM

호출 프로세스의 모든 가상 주소 공간을 RAM에 잠급니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mlockall.2.html

### MUNLOCK
> Unlock part of the calling process's virtual address space into RAM

호출 프로세스의 가상 주소 공간 일부를 RAM으로 잠금 해제합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/munlock.2.html

### MUNLOCKALL
> Unlock all of the calling process's virtual address space into RAM

호출 프로세스의 모든 가상 주소 공간을 RAM으로 잠금 해제합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/munlockall.2.html

### MINCORE
> Determine whether pages are resident in memory

페이지가 메모리에 상주하는지 확인

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/mincore.2.html

### MEMBARRIER
> Issue memory barriers on a set of threads

스레드 집합에 메모리 장벽을 발행합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/membarrier.2.html

### MODIFY_LDT
> Reads or writes the local descriptor table (ldt) for a process

프로세스에 대한 로컬 설명자 테이블(ldt)을 읽거나 씁니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/modify_ldt.2.html

## Threads (스레드)
### CAPSET
> Set capabilities of thread(s)

스레드의 기능 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/capset.2.html

### CAPGET
> Get capabilities of thread(s)

스레드의 기능 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/capget.2.html

### SET_THREAD_AREA
> Set a thread local storage (TLS) area

TLS(스레드 로컬 스토리지) 영역 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/set_thread_area.2.html

### GET_THREAD_AREA
> Get a thread-local storage (TLS) area

스레드 로컬 스토리지(TLS) 영역 가져오기

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/get_thread_area.2.html

### SET_TID_ADDRESS
> Set pointer to thread ID

스레드 ID에 대한 포인터 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/set_tid_address.2.html

### ARCH_PRCTL
> Set architecture-specific thread state

아키텍처별 스레드 상태 설정

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/arch_prctl.2.html

## Miscellaneous (기타)
### USELIB
> Load shared library

공유 라이브러리 로드

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/uselib.2.html

### PRCTL
> Operations on a process

프로세스에 대한 작업

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/prctl.2.html

### SECCOMP
> Operate on Secure Computing state of the process

프로세스의 보안 컴퓨팅 상태에서 작동

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/seccomp.2.html

### PTRACE
> Process trace

프로세스 추적

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/ptrace.2.html

### PROCESS_VM_READV
> Transfers data from the remote process to the local process

원격 프로세스에서 로컬 프로세스로 데이터를 전송합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/process_vm_readv.2.html

### PROCESS_VM_WRITEV
> Transfers data from the local process to the remote process

로컬 프로세스에서 원격 프로세스로 데이터를 전송합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/process_vm_writev.2.html

### KCMP
> Compare two processes to determine if they share a kernel resource

두 프로세스를 비교하여 커널 리소스를 공유하는지 확인합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/kcmp.2.html

### UNSHARE
> Disassociate parts of the process execution context

프로세스 실행 컨텍스트의 일부를 분리합니다.

**LIBRARY**:

**Arguments**:

**Return Value**: 
- manual page: http://man7.org/linux/man-pages/man2/unshare.2.html


> [!WARNING]
> This part of the docs is incomplete, contributions are very welcome
> 
> 이 문서는 불완전합니다. 기여를 환영합니다.

[^1]: [linasm.sourceforge.net](https://linasm.sourceforge.net/docs/syscalls/index.php)
[^2]: [Linux man pages](https://man7.org/linux/man-pages/dir_section_2.html)