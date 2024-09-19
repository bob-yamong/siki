# Finding a List of Traceable Items Using the bpftrace Command
bpftrace 명령을 이용하여 추적 가능 목록 찾기

### 요약

- `bpftrace -l "kprobe:*"`: 커널 함수 목록
- `bpftrace -l "tracepoint:*"`: 트레이스포인트 목록
- `bpftrace -l "syscall:*"`: 시스템 호출 목록
- `bpftrace -l`: 전체 프로브 목록

이 명령들을 통해 시스템에서 추적 가능한 항목을 확인하고 원하는 이벤트를 모니터링할 수 있습니다.

</aside>

### 1. **사용 가능한 시스템 호출 목록 (syscalls)**

시스템 호출을 추적할 수 있는 항목을 확인하려면:

```bash
sudo bpftrace -l "syscall:*"
```

이 명령은 모든 시스템 호출(syscall)에 대한 목록을 출력합니다. 예를 들어, `syscall:clone`, `syscall:fork` 등이 포함될 수 있습니다.

### 2. **사용 가능한 커널 함수 목록 (kprobes)**

커널 함수에 대해 kprobe를 사용할 수 있는 함수 목록을 확인하려면:

```bash
sudo bpftrace -l "kprobe:*"
```

이 명령은 모든 커널 함수 중에서 `kprobe`로 추적 가능한 함수들을 출력합니다.

### 3. **리턴 프로브 (kretprobes)**

함수의 리턴 시점을 추적하는 리턴 프로브 목록을 확인하려면:

```bash
sudo bpftrace -l "kretprobe:*"
```

리턴 프로브는 함수가 리턴되는 시점에 작동하는 프로브입니다.

### 4. **트레이스포인트 목록 (tracepoints)**

커널 트레이스포인트는 특정 이벤트가 발생할 때 실행되는 지점입니다. 사용 가능한 트레이스포인트를 확인하려면:

```bash
sudo bpftrace -l "tracepoint:*"
```

특정 서브시스템의 트레이스포인트만 보고 싶다면:

```bash
sudo bpftrace -l "tracepoint:sched:*"
```

이 명령은 `sched` 서브시스템의 트레이스포인트만 표시합니다. 예를 들어 `tracepoint:sched:sched_process_fork` 같은 항목이 표시됩니다.

### 5. **네트워크 이벤트 (USDT probes)**

사용자 공간에서 동적 추적을 위한 USDT(User-level Statically Defined Tracing) 프로브 목록을 확인하려면:

```bash
sudo bpftrace -l "usdt:*"
```

이 명령은 주로 사용자 애플리케이션에서 제공하는 추적 가능 이벤트를 표시합니다.

### 6. **사용 가능한 함수 호출 스택 (uprobes)**

사용자 공간 프로그램에 대해 추적할 수 있는 함수 호출 목록을 확인하려면:

```bash
sudo bpftrace -l "uprobe:*"
```

이 명령은 사용자 공간에서 함수 호출을 추적할 수 있는 프로브 목록을 출력합니다.

### 7. **추가적으로 지원하는 항목 확인**

추적할 수 있는 다양한 항목을 더 확인하려면 `bpftrace -l` 명령을 통해 지원하는 다양한 프로브 타입을 나열할 수 있습니다:

<aside>
🚫

kretprobe 등 일부 타입은 보이지 않으므로 상기 명령어로 확인하여야 하여야 함.

</aside>

```bash
sudo bpftrace -l
```

이 명령은 `bpftrace`가 지원하는 모든 프로브 타입을 보여줍니다. 여기에는 `syscall`, `kprobe`, `tracepoint`, `uprobe` 등이 포함됩니다.

### 8. 추적이 불가능한 항목 확인

다음과 같은 이유로 특정 함수가 추적 불가능할 수 있습니다:

- **함수 인라인화**: 일부 함수들은 컴파일 시점에 인라인화 되므로 별도의 함수로 존재하지 않아 추적할 수 없습니다.
- **`notrace` 플래그**: 커널이나 사용자 코드에서 함수가 `notrace`로 표시된 경우, 이 함수에 `kprobe`나 `kretprobe`를 설정할 수 없습니다.
- **보안 정책**: 보안상의 이유로 특정 함수는 추적할 수 없도록 커널에서 설정되어 있을 수 있습니다.

만약 특정 항목이 추적 가능한지 확인하고 싶다면, 실제로 `bpftrace` 스크립트를 실행해보고 추적이 가능한지 테스트해야 합니다. 추적이 불가능한 경우, 다음과 같은 에러 메시지가 나타날 수 있습니다:

- **함수가 존재하지 않거나 인라인화된 경우**:
    
    ```bash
    WARNING: <function> is not traceable (either non-existing, inlined, or marked as "notrace")
    ```
    
- **프로브를 설정할 수 없는 경우**:
    
    ```bash
    ERROR: Error attaching probe: 'kprobe:<function>'
    ```
    

이런 메시지가 나타나면 해당 함수는 추적할 수 없는 상태입니다.

추가로 시스템 안정성, 보안, 성능 문제를 방지하기 위해  `kprobe`와 `kretprobe`를 설정할 수 없는 커널 함수들의 목록은 아래 명령을 통해 확인할 수 있습니다

```bash
 cat /sys/kernel/debug/kprobes/blacklist
```

## 참고

### `bpftrace`에서 사용 가능한 프로브를 eBPF에서 사용하는 방법

1. **kprobe/kretprobe**
    - `bpftrace`에서 `kprobe`나 `kretprobe`를 사용할 수 있다면, eBPF에서도 이를 C 코드로 직접 구현할 수 있습니다.
    
    **bpftrace 예제**:
    
    ```bash
    sudo bpftrace -e 'kprobe:sys_clone { printf("Clone called\n"); }'
    ```
    
    **eBPF (C)**:
    
    ```c
    SEC("kprobe/sys_clone")
    int bpf_prog1(struct pt_regs *ctx) {
        bpf_printk("Clone called\n");
        return 0;
    }
    ```
    
2. **tracepoint**
    - 트레이스포인트 역시 eBPF에서 사용할 수 있습니다. `sched_process_fork` 같은 트레이스포인트는 프로세스 생성 시 실행되며, eBPF 프로그램에서도 쉽게 사용할 수 있습니다.
    
    **bpftrace 예제**:
    
    ```bash
    sudo bpftrace -e 'tracepoint:sched:sched_process_fork { printf("Forked: Parent PID = %d, Child PID = %d\n", args->parent_pid, args->child_pid); }'
    ```
    
    **eBPF (C)**:
    
    ```c
    SEC("tracepoint/sched/sched_process_fork")
    int on_fork(struct trace_event_raw_sched_process_fork *ctx) {
        u32 ppid = ctx->parent_pid;
        u32 cpid = ctx->child_pid;
        bpf_printk("Forked: Parent PID = %d, Child PID = %d\n", ppid, cpid);
        return 0;
    }
    ```
    
3. **syscall**
    - 시스템 호출에 대한 프로브도 eBPF에서 사용할 수 있습니다. `bpftrace`에서 사용하던 `syscall:*` 프로브는 eBPF 프로그램에서 `kprobe/sys_*` 형식으로 사용할 수 있습니다.
    
    **bpftrace 예제**:
    
    ```bash
    sudo bpftrace -e 'kprobe:sys_write { printf("Write called\n"); }'
    ```
    
    **eBPF (C)**:
    
    ```c
    SEC("kprobe/sys_write")
    int trace_sys_write(struct pt_regs *ctx) {
        bpf_printk("Write called\n");
        return 0;
    }
    ```
    
4. **uprobe**
    - 사용자 공간 함수에 대한 프로브는 `uprobe`를 통해 eBPF에서도 사용할 수 있습니다.
    
    **bpftrace 예제**:
    
    ```bash
    sudo bpftrace -e 'uprobe:/bin/bash:main { printf("Bash main called\n"); }'
    ```
    
    **eBPF (C)**:
    
    ```c
    SEC("uprobe/bin/bash:main")
    int trace_bash_main(struct pt_regs *ctx) {
        bpf_printk("Bash main called\n");
        return 0;
    }
    ```
    

### 주의 사항

- **bpftrace와 eBPF의 차이점**: `bpftrace`는 BPF 프로그램을 실행할 때 필요한 많은 부분을 자동으로 처리합니다. 반면, eBPF C 코드로 직접 작성할 경우 더 세부적인 설정이 필요할 수 있습니다. 예를 들어, `bpftrace`는 자동으로 변수 타입을 해석하지만, eBPF C 코드에서는 커널 구조체의 정의와 메모리 오프셋 등을 명시적으로 다루어야 합니다.
- **필수 헤더 및 구조체 정의**: eBPF C 프로그램을 작성할 때는 커널 헤더 파일이나 필요한 구조체를 직접 포함하거나, 커널 버전 및 사용 중인 플랫폼에 맞게 조정해야 합니다. `bpftrace`에서는 이를 대부분 자동으로 처리합니다.

---