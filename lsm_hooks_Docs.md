# LSM hooks Document
LSM hook의 용도와 eBPF에서의 사용 방법을 정리한 문서

## kprobe__sys_execve
execve가 실행된 시점과 관련 메타데이터를 context로 가져올 수 있다.<br>
`struct pt_regs`는 프로세서의 레지스터 상태를 저장하는 구조체

- e.g.
pid 당 execve 호출 횟수 모니터링
```
#include <uapi/linux/bpf.h>
#include <uapi/linux/ptrace.h>
#include <linux/binfmts.h>

// eBPF 맵을 정의하여 execve 호출 횟수를 추적
BPF_HASH(exec_count, u32, u64);

// execve 시스템 호출 감시를 위한 kprobe
int kprobe__sys_execve(struct pt_regs *ctx) {
    u32 pid = bpf_get_current_pid_tgid() >> 32;
    u64 counter = 0, *val;

    val = exec_count.lookup(&pid);
    if (val) {
        counter = *val;
    }
    counter++;
    exec_count.update(&pid, &counter);

    bpf_trace_printk("Execve called by PID %d, count %d\\n", pid, counter);
    return 0;
}
```

@bprm_creds_for_exec