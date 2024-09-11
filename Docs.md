# Document
LSM hook, syscall, Kprobe의 용도와 eBPF에서의 사용 방법을 정리한 문서

## kprobe__sys_execve
execve가 실행된 시점과 관련 메타데이터를 context로 가져올 수 있다.<br>
`struct pt_regs`는 프로세서의 레지스터 상태를 저장하는 구조체

- e.g.<br>
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

## bprm_creds_from_file
파일 실행 시 해당 파일이 특수 권한(setuid, setgid 등)을 가지고 있는 경우<br> 
권한을 변경을 반영하여 실행 중인 프로세스의 자격증명을 업데이트한다.<br>
인터프리터 없이 직접 실행되었을 때 호출되는 특징이 있다.<br>

- e.g.<br>
setuid/setgid가 어느 파일에서 실행되었는지 감지
```
#include <uapi/linux/bpf.h>
#include <uapi/linux/ptrace.h>
#include <linux/binfmts.h>

SEC("lsm/bprm_creds_from_file")
int BPF_PROG(trace_exec, struct linux_binprm *bprm)
{
    // 파일 정보에 접근
    struct file *file = bprm->file;
    if (file->f_path.dentry->d_inode->i_mode & (S_ISUID | S_ISGID)) {
        bpf_trace_printk("setuid/setgid 실행 감지: %s\n", file->f_path.dentry->d_name.name);
    }

    return 0;
}

char _license[] SEC("license") = "GPL";
```
`trace_exec`: bpf 프로그램의 이름<br>
`struct linux_binprm`: <br>
    &emsp;bpf 프로그램에 전달되는 파라미터<br>
    &emsp;Linux 커널이 실행 파일을 로드할 때 사용하는 정보를 담고있음<br>
    &emsp;실행 파일의 경로, 실행 파일을 로드하기 위한 메모리 포인터, 실행에 필요한 자격 증명 등이 포함<br>

## bprm_check_security
시스템 호출 과정에서 바이너리 핸들러에 대한 검색이 시작되기 직전에 보안 검사를 수행할 수 있도록 설계된 훅<br>
실행 파일의 보안 속성을 확인하고, 해당 실행 파일이 적절한 권한을 가지고 있는지 검사<br>
argv 및 envp 리스트에 접근할 수 있어, 실행될 파일의 인자와 환경 변수를 기반으로 추가적인 보안 검사를 수행할 수 있음<br>

- e.g. <br>
실행 중인 혹은 실행하려는 바이너리에 대한 파라미터를 검사하여 차단

```
#include <linux/bpf.h>
#include <linux/binfmts.h>
#include <bpf/bpf_helpers.h>
#include <linux/string.h>

SEC("lsm/bprm_check_security")
int BPF_PROG(check_exec_security, struct linux_binprm *bprm)
{
    // 실행 파일 경로 추출
    struct file *file = bprm->file;
    if (file) {
        struct dentry *dentry = file->f_path.dentry;
        if (dentry && dentry->d_name.name) {
            // 실행 파일 이름 확인
            const char *filename = dentry->d_name.name;
            bpf_trace_printk("Attempting to execute binary: %s\n", filename);

            // 특정 바이너리 이름("blocked_binary")일 경우 실행 차단
            if (bpf_strncmp(filename, "blocked_binary", sizeof("blocked_binary") - 1) == 0) {
                bpf_trace_printk("Execution blocked for binary: %s\n", filename);
                return -1; // -1을 반환하면 실행이 차단됨
            }
        }
    }

    return 0; // 0을 반환하면 실행 허용
}

char _license[] SEC("license") = "GPL";
```