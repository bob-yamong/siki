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

## struct fs_context
- 파일 시스템 마운트 또는 재구성 작업에 대한 정보
- 파일 시스템의 설정과 상태를 나타냄
- 파일 시스템 구현이 초기화 및 구성 단계를 처리하는 데 사용

### x86-64에서의 정의
아래 내용은 일반적인 구조로 버전마다 상이할 수 있음
```
struct fs_context {
    const struct fs_parameter_spec *fs_type;
    struct dentry *root;
    const struct cred *cred;
    struct user_namespace *user_ns;
    struct fc_log *log;
    const struct fs_context_operations *ops;
    const char *source;
    void *security;
    unsigned int sb_flags;
    unsigned int sb_flags_mask;
    loff_t max_size;
    enum fs_context_purpose purpose;
    int need_free:1,
        sb_rdonly:1;
    void *fs_private;
    struct net *net_ns;
    struct fs_parameter *params;
    struct fs_parser *parser;
};
```

## struct fs_context
- 파일 시스템 슈퍼블록을 생성하고 구성하는 데 사용되는 커널 구조체
- 파일 시스템 마운트 작업의 컨텍스트를 캡슐화
- 파일 시스템별 구성 매개변수를 관리하는 데 사용

### x86-64에서의 정의
아래 내용은 일반적인 구조임
```
struct fs_context {
    const struct fs_context_operations *ops;  // 파일 시스템 컨텍스트와 관련된 연산
    struct file_system_type *fs_type;         // 파일 시스템의 타입
    struct dentry *root;                     // 파일 시스템의 루트 디렉터리
    struct super_block *sget_ssb;             // sget() 동안 사용된 슈퍼블록
    struct super_block *sb;                  // 최종 슈퍼블록
    struct user_namespace *user_ns;          // 사용 중인 사용자 네임스페이스
    struct net *net_ns;                      // 연관된 네트워크 네임스페이스
    const struct cred *cred;                 // 생성자의 자격 증명
    void *security;                          // 보안 데이터 포인터
    char *source;                            // 소스 경로 또는 장치
    void *fs_private;                        // 파일 시스템별 개인 데이터
    const char *subtype;                     // 파일 시스템의 하위 타입 문자열
    unsigned long sb_flags;                  // 슈퍼블록 플래그
    unsigned int sb_flags_mask;              // 설정 가능한 슈퍼블록 플래그의 마스크
    loff_t max_size;                         // 파일 시스템의 최대 크기
    enum fs_context_purpose purpose;         // 컨텍스트의 목적(마운트, 재구성 등)
    bool need_free:1;                        // 컨텍스트가 해제되어야 하는지 여부
    bool global:1;                           // 컨텍스트가 전역인지 여부
    unsigned int net_nsok:1;                 // net_ns가 검사된 상태인지 여부
    unsigned int no_open:1;                  // 설정된 경우 소스를 열지 않음
    unsigned int no_fscrypt_key:1;           // 설정된 경우 파일 시스템 암호화 키 거부
    unsigned int legacy_data:1;              // 레거시 데이터를 특정 방식으로 처리
    char *security_xattr;                    // 보안 xattr 버퍼
    size_t security_xattr_size;              // 보안 xattr 버퍼 크기
    struct fc_log *log;                      // 오류 메시지용 로그 구조체 포인터
    struct mutex *uapi_mutex;                // 사용자 공간 API 작업용 뮤텍스
    struct list_head *monitors;              // 컨텍스트 모니터 목록
};
```

## struct fs_parameter
- 파일 시스템 구성 매개변수를 전달하는 데 사용
- 사용자 공간에서 커널로 설정 정보를 전달할 때 사용

### x86-64에서의 정의
아래 내용은 일반적인 구조임
```
struct fs_parameter {
    char *key;                               // 파라미터 키
    union {
        void *value_generic;                 // 일반적인 값 포인터
        int value_int;                       // 정수형 값
        char *value_string;                  // 문자열 값
    };
    enum fs_value_type type;                 // 값의 타입
    struct fs_parameter_spec *spec;          // 파라미터 스펙 포인터
    struct list_head link;                   // 다른 파라미터와의 연결을 위한 리스트 헤더
};
```

## struct super_block
- 리눅스 커널에서 파일 시스템의 상위 구조를 설명하는 중요한 데이터 구조
- 각 마운트된 파일 시스템은 struct super_block 인스턴스를 하나씩 갖음
- 파일 시스템의 타입, 상태, 동작 방식 등을 정의

### x86-64에서의 정의
```
struct super_block {
    struct list_head s_list;        /* 연결 리스트 */
    dev_t s_dev;                    /* 장치 식별자 */
    unsigned long s_blocksize;
    unsigned char s_blocksize_bits;
    unsigned long s_magic;
    struct dentry *s_root;
    struct rw_semaphore s_umount;
    int s_count;
    atomic_t s_active;
    const struct super_operations *s_op;
    struct hlist_bl_head s_mounts;  /* 마운트된 파일 시스템 목록 */
    struct mutex s_lock;
    int s_readonly_remount;
    int s_dirt;
    struct list_head s_inodes;     /* 인덱스 노드 리스트 */
    void *s_fs_info;               /* 파일 시스템 스펙 정보 */
    unsigned int s_maxbytes;
    struct file_system_type *s_type;
    const char *s_id;
    unsigned int s_flags;
    unsigned long s_iflags;
    struct timespec64 s_time_gran;
    struct sb_writers s_writers;
    const struct xattr_handler **s_xattr;
};
```