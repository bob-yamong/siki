# Document - Data Struct
ebpf에서 사용하는 구조체에 대한 설명을 정리한 문서<br>
아키텍쳐마다 구조가 조금씩 다름 

## struct pt_regs
커널 내부에서 사용하는 구조체로, 프로세스가 실행될 때 CPU의 레지스터 상태를 저장하는 데 사용<br>
주로 컨텍스트 스위칭, ebpf 및 트레이싱, 인터럽트 처리에서 사용한다.<br>
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