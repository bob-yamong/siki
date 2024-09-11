# Document
LSM hook, syscall, Kprobe의 용도와 eBPF에서의 사용 방법을 정리한 문서

## kprobe__sys_execve
- execve가 실행된 시점과 관련 메타데이터를 context로 가져올 수 있다.
- `struct pt_regs`는 프로세서의 레지스터 상태를 저장하는 구조체

### e.g.
- pid 당 execve 호출 횟수 모니터링
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
- 파일 실행 시 해당 파일이 특수 권한(setuid, setgid 등)을 가지고 있는 경우
- 권한을 변경을 반영하여 실행 중인 프로세스의 자격증명을 업데이트한다.
- 인터프리터 없이 직접 실행되었을 때 호출되는 특징이 있다.

### e.g.
- setuid/setgid가 어느 파일에서 실행되었는지 감지
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
- 시스템 호출 과정에서 바이너리 핸들러에 대한 검색이 시작되기 직전에 보안 검사를 수행할 수 있도록 설계된 훅
- 실행 파일의 보안 속성을 확인하고, 해당 실행 파일이 적절한 권한을 가지고 있는지 검사
- argv 및 envp 리스트에 접근할 수 있어, 실행될 파일의 인자와 환경 변수를 기반으로 추가적인 보안 검사를 수행할 수 있음

### e.g.
- 실행 중인 혹은 실행하려는 바이너리에 대한 파라미터를 검사하여 차단

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
## bprm_committed_creds
- 새로운 자격 증명이 `current->cred`에 설정된 후에 실행
- `execve`가 실행되는 도중에 프로세스의 보안 속성을 새롭게 설치한 직후에 호출

### e.g.
- 실행 파일의 이름을 로깅
```
#include <linux/bpf.h>
#include <linux/binfmts.h>
#include <bpf/bpf_helpers.h>

// 프로그램 라이선스 명시
char LICENSE[] SEC("license") = "GPL";

// LSM Hook: bprm_committed_creds
SEC("lsm/bprm_committed_creds")
int BPF_PROG(handle_committed_creds, struct linux_binprm *bprm)
{
    // 실행 파일의 dentry 구조체 접근
    if (bprm->file) {
        struct dentry *dentry = bprm->file->f_path.dentry;
        
        // 실행 파일 이름 로깅
        if (dentry && dentry->d_name.name) {
            bpf_trace_printk("Executed binary: %s\n", dentry->d_name.name);
        }
    }

    // 필요한 경우 여기에서 추가 조치 수행
    // 예: 특정 실행 파일에 대한 추가 로깅, 메모리 상태 감사 등

    return 0;
}
```

## fs_context_dup
- 파일 시스템 컨텍스트를 복제할 때 호출
- 새로운 파일 시스템 컨텍스트(fc)가 생성되고 원본 파일 시스템 컨텍스트(src_fc)로부터 복제되는 과정에서 보안 구조를 할당하고 초기화하는 역할
- fs_context_dup은 보안 모듈이 필요한 보안 속성을 새로운 컨텍스트에 적절히 복제할 수 있도록함

### e.g.
- 파일 시스템 컨텍스트의 복제 과정에서 발생하는 보안 관련 이벤트를 로깅
```
#include <linux/bpf.h>
#include <linux/fs_context.h>
#include <bpf/bpf_helpers.h>

// 프로그램 라이선스 명시
char LICENSE[] SEC("license") = "GPL";

// LSM Hook: fs_context_dup
SEC("lsm/fs_context_dup")
int BPF_PROG(handle_fs_context_dup, struct fs_context *fc, struct fs_context *src_fc)
{
    // 원본 fs_context 정보 로깅
    if (src_fc) {
        bpf_trace_printk("Original fs_context: %p\n", src_fc);
    }

    // 새로운 fs_context 정보 로깅
    if (fc) {
        bpf_trace_printk("New fs_context: %p\n", fc);
    }

    // 보안 모듈에서 요구하는 추가적인 검사나 조치 수행 가능
    // 예: 특정 타입의 파일 시스템에 대한 접근 제한 등

    return 0;
}
```
`struct fs_context`: 파일 시스템 마운트 또는 재구성 작업에 대한 정보