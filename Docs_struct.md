# Document - Data Struct
ebpf에서 사용하는 구조체에 대한 설명을 정리한 문서<br>
아키텍쳐마다 구조가 조금씩 다름 

## struct pt_regs
- 커널 내부에서 사용하는 구조체로, 프로세스가 실행될 때 CPU의 레지스터 상태를 저장하는 데 사용
- 주로 컨텍스트 스위칭, ebpf 및 트레이싱, 인터럽트 처리에서 사용한다.
### x86-64에서의 정의
```
struct pt_regs {
    unsigned long r15;   // r15 레지스터
    unsigned long r14;   // r14 레지스터
    unsigned long r13;   // r13 레지스터
    unsigned long r12;   // r12 레지스터
    unsigned long rbp;   // 베이스 포인터 (RBP)
    unsigned long rbx;   // 베이스 레지스터 (RBX)

    unsigned long r11;   // r11 레지스터 (임시 레지스터)
    unsigned long r10;   // r10 레지스터 (임시 레지스터)
    unsigned long r9;    // r9 레지스터
    unsigned long r8;    // r8 레지스터
    unsigned long rax;   // 누산기 레지스터 (RAX)
    unsigned long rcx;   // 카운터 레지스터 (RCX)
    unsigned long rdx;   // 데이터 레지스터 (RDX)
    unsigned long rsi;   // 소스 인덱스 레지스터 (RSI)
    unsigned long rdi;   // 목적지 인덱스 레지스터 (RDI)

    unsigned long orig_rax;  // 시스템 콜 원본 RAX 값 (시스템 콜을 호출할 때 사용)

    unsigned long rip;   // 명령 포인터 (RIP)
    unsigned long cs;    // 코드 세그먼트 레지스터 (CS)
    unsigned long eflags; // 플래그 레지스터 (EFLAGS)
    unsigned long rsp;   // 스택 포인터 (RSP)
    unsigned long ss;    // 스택 세그먼트 레지스터 (SS)
};
```

## struct linux_binprm
- 프로세스의 실행 파일과 관련된 정보를 관리하는 구조체
- 주로 새로운 프로세스를 실행하기 위해 `execve()` 이 호출될 때 사용
- 실행 파일과 메타데이터를 저장
- 모든 아키텍쳐가 거의 동일한 구조

### x86-64에서의 정의
```
struct linux_binprm {
    char buf[BINPRM_BUF_SIZE];        // 실행 파일의 헤더(파일 앞부분)를 저장하는 버퍼
    struct mm_struct *mm;             // 새로운 프로세스의 메모리 구조체
    struct file *file;                // 실행할 파일에 대한 포인터 (struct file)
    struct cred *cred;                // 프로세스가 사용할 자격 증명 (권한 및 사용자 정보)
    int argc;                         // 실행 파일의 인수 개수
    int envc;                         // 환경 변수 개수
    char *filename;                   // 실행할 파일의 이름
    char *interp;                     // 해석기(interpreter)가 필요한 경우 해당 경로
    unsigned interp_flags;            // 인터프리터 플래그
    unsigned interp_data;             // 인터프리터를 위한 데이터
    int execfd;                       // 실행 파일 디스크립터 (fd)
    struct page *page[MAX_ARG_PAGES]; // 명령 인수 및 환경 변수를 위한 메모리 페이지
    struct vm_area_struct *vma;       // 가상 메모리 영역 구조체
    int pid;                          // 실행 중인 프로세스의 PID
    unsigned long p;                  // 현재 명령 줄 인수의 위치
    int per_clear;                    // 퍼스낼리티를 초기화해야 하는지 여부
    unsigned long stack_base;         // 스택이 시작되는 주소
    unsigned long pgrp;               // 프로세스 그룹 ID
    unsigned long brk;                // 현재 brk 포인터(메모리 확장용)
    int secureexec;                   // 보안 실행 여부(보안 모드로 실행될 때 설정)
    struct rlimit rlim_stack;         // 스택 크기 제한 정보
};
```