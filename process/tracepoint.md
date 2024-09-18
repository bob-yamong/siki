### FORK

> Create a child process

자식 프로세스 생성

**LIBRARY**: 

- libc (`unistd.h`)

**Arguments**: 

- 없음 

**Use Case**: 

- Null

**Return Value**:

- **Parent Process**: On success, returns the Process ID (PID) of the child.
- **Child Process**: Returns 0.
- **Failure**: Returns -1 in the parent process; no child is created.

**manual page**:

- Null

---

### VFORK

> Create a child process and suspend the parent until the child exits or executes a new program

자식 프로세스를 생성하고, 자식이 종료하거나 새로운 프로그램을 실행할 때까지 부모를 중단시킴

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- **Parent Process**: On success, returns the PID of the child.
- **Child Process**: Returns 0.
- **Failure**: Returns -1 in the parent process; no child is created.

---

### CLONE

> Create a new process (thread) with shared parts of execution context

실행 컨텍스트의 일부를 공유하는 새로운 프로세스(스레드) 생성

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `int (*fn)(void *)`: Child function to execute.
- `void *child_stack`: Pointer to the bottom of the child's stack.
- `int flags`: Flags to control what is shared between the calling process and the child process.
- `void *arg`: Argument to pass to the child function `fn`.
- Additional arguments depending on flags.

**Return Value**:

- **Parent Process**: On success, returns the PID of the child.
- **Child Process**: Returns 0.
- **Failure**: Returns -1; no child is created.

---

### CLONE3

> Create a new process with extended options

확장된 옵션으로 새로운 프로세스 생성

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `struct clone_args *cl_args`: Pointer to a `clone_args` structure containing extended options.
- `size_t size`: Size of the `clone_args` structure.

**Return Value**:

- **Parent Process**: On success, returns the PID of the child.
- **Child Process**: Returns 0.
- **Failure**: Returns -1; no child is created.

---

### EXECVE

> Execute a program

프로그램 실행

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `const char *pathname`: Path to the executable file.
- `char *const argv[]`: Argument list for the new program.
- `char *const envp[]`: Environment variables for the new program.

**Return Value**:

- On success, does not return (the process image is replaced).
- On failure, returns -1; the process image remains unchanged.

---

### EXECVEAT

> Execute a program relative to a directory file descriptor

디렉토리 파일 디스크립터를 기준으로 프로그램 실행

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int dirfd`: File descriptor of a directory.
- `const char *pathname`: Path to the executable file relative to `dirfd`.
- `char *const argv[]`: Argument list.
- `char *const envp[]`: Environment variables.
- `int flags`: Execution flags.

**Return Value**:

- On success, does not return.
- On failure, returns -1; the process image remains unchanged.

---

### EXIT

> Terminate the calling process

호출한 프로세스를 종료

**LIBRARY**: libc (`stdlib.h`)

**Arguments**:

- `int status`: Exit status code.

**Return Value**:

- This function does not return.

---

### EXIT_GROUP

> Terminate the calling process and all threads in the same thread group

호출한 프로세스와 동일한 스레드 그룹의 모든 스레드를 종료

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int status`: Exit status code.

**Return Value**:

- This function does not return.

---

### WAIT4

> Wait for a process to change state

프로세스의 상태 변경을 대기

**LIBRARY**: libc (`sys/wait.h`)

**Arguments**:

- `pid_t pid`: PID of the child process to wait for.
- `int *wstatus`: Pointer to store the exit status.
- `int options`: Options to modify behavior.
- `struct rusage *rusage`: Resource usage information.

**Return Value**:

- On success, returns the PID of the child.
- On failure, returns -1.

---

### WAITID

> Wait for a specific child process to change state

특정 자식 프로세스의 상태 변경을 대기

**LIBRARY**: libc (`sys/wait.h`)

**Arguments**:

- `idtype_t idtype`: Type of ID (e.g., P_PID, P_PGID).
- `id_t id`: ID of the process or group to wait for.
- `siginfo_t *infop`: Pointer to `siginfo_t` structure.
- `int options`: Options to modify behavior.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### KILL

> Send a signal to a process or a group of processes

프로세스 또는 프로세스 그룹에 신호 보내기

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `pid_t pid`: PID of the target process or group.
- `int sig`: Signal number to send.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### TKILL

> Send a signal to a specific thread

특정 스레드에 신호 보내기

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `pid_t tid`: Thread ID of the target thread.
- `int sig`: Signal number to send.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### TGKILL

> Send a signal to a specific thread within a specific process

특정 프로세스 내의 특정 스레드에 신호 보내기

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `pid_t tgid`: Thread group ID (Process ID).
- `pid_t tid`: Thread ID.
- `int sig`: Signal number to send.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### RT_SIGQUEUEINFO

> Queue a signal and data to a process

프로세스에 신호와 데이터를 큐잉

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `pid_t tgid`: Target process ID.
- `int sig`: Signal number.
- `siginfo_t *uinfo`: Signal information.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### RT_TGSIGQUEUEINFO

> Send a signal and data to a specific thread within a process

프로세스 내의 특정 스레드에 신호와 데이터를 전송

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `pid_t tgid`: Thread group ID (Process ID).
- `pid_t tid`: Thread ID.
- `int sig`: Signal number.
- `siginfo_t *uinfo`: Signal information.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### RT_SIGPROCMASK

> Examine and change blocked signals

차단된 신호를 검사하고 변경

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `int how`: How to modify the signal mask.
- `const sigset_t *set`: Signals to block/unblock.
- `sigset_t *oldset`: Previous signal mask.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### RT_SIGPENDING

> Examine pending signals

대기 중인 신호를 검사

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `sigset_t *set`: Pointer to store pending signals.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### RT_SIGTIMEDWAIT

> Synchronously wait for queued signals

큐에 대기 중인 신호를 동기적으로 대기

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `const sigset_t *set`: Signals to wait for.
- `siginfo_t *info`: Signal information.
- `const struct timespec *timeout`: Maximum wait time.

**Return Value**:

- On success, returns signal number.
- On failure, returns -1.

---

### RT_SIGSUSPEND

> Temporarily replace the signal mask and suspend the process

신호 마스크를 임시로 교체하고 프로세스를 중단

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `const sigset_t *mask`: New signal mask.

**Return Value**:

- Always returns -1 with `errno` set to `EINTR`.

---

### SIGALTSTACK

> Set or get signal stack context

신호 스택 컨텍스트 설정 또는 가져오기

**LIBRARY**: libc (`signal.h`)

**Arguments**:

- `const stack_t *ss`: New signal stack.
- `stack_t *old_ss`: Previous signal stack.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETUID

> Set user ID of the calling process

호출한 프로세스의 사용자 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `uid_t uid`: New user ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETGID

> Set group ID of the calling process

호출한 프로세스의 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `gid_t gid`: New group ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETEUID

> Set effective user ID of the calling process

호출한 프로세스의 유효 사용자 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `uid_t euid`: New effective user ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETEGID

> Set effective group ID of the calling process

호출한 프로세스의 유효 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `gid_t egid`: New effective group ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETREUID

> Set real and effective user IDs

실제 및 유효한 사용자 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `uid_t ruid`: Real user ID.
- `uid_t euid`: Effective user ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETREGID

> Set real and effective group IDs

실제 및 유효한 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `gid_t rgid`: Real group ID.
- `gid_t egid`: Effective group ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETRESUID

> Set real, effective, and saved user IDs

실제, 유효 및 저장된 사용자 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `uid_t ruid`: Real user ID.
- `uid_t euid`: Effective user ID.
- `uid_t suid`: Saved user ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETRESGID

> Set real, effective, and saved group IDs

실제, 유효 및 저장된 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `gid_t rgid`: Real group ID.
- `gid_t egid`: Effective group ID.
- `gid_t sgid`: Saved group ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETFSUID

> Set user ID for file system checks

파일 시스템 검사에 사용할 사용자 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `uid_t fsuid`: New file system user ID.

**Return Value**:

- Returns previous file system user ID.

---

### SETFSGID

> Set group ID for file system checks

파일 시스템 검사에 사용할 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `gid_t fsgid`: New file system group ID.

**Return Value**:

- Returns previous file system group ID.

---

### CAPSET

> Set process capabilities

프로세스의 권한 설정

**LIBRARY**: libc (`sys/capability.h`)

**Arguments**:

- `cap_user_header_t hdrp`: Pointer to capability header.
- `const cap_user_data_t datap`: Pointer to capability data.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### PERSONALITY

> Set the process execution domain

프로세스 실행 도메인 설정

**LIBRARY**: libc (`sys/personality.h`)

**Arguments**:

- `unsigned long persona`: New execution domain.

**Return Value**:

- On success, returns previous persona.
- On failure, returns -1.

---

### PRCTL

> Operations on a process

프로세스에 대한 작업 수행

**LIBRARY**: libc (`sys/prctl.h`)

**Arguments**:

- `int option`: Operation to perform.
- `unsigned long arg2`, `arg3`, `arg4`, `arg5`: Additional arguments depending on the operation.

**Return Value**:

- On success, returns 0 or operation-specific value.
- On failure, returns -1.

---

### SECCOMP

> Set secure computing mode

보안 컴퓨팅 모드 설정

**LIBRARY**: libc (`seccomp.h`)

**Arguments**:

- `unsigned int operation`: Seccomp operation.
- `unsigned int flags`: Flags modifying behavior.
- `void *args`: Operation-specific arguments.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETNS

> Join a namespace

네임스페이스에 참여

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `int fd`: File descriptor referring to a namespace.
- `int nstype`: Namespace type.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### UNSHARE

> Disassociate parts of the process execution context

프로세스 실행 컨텍스트의 일부를 분리

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `int flags`: Flags indicating which parts to unshare.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SCHED_SETAFFINITY

> Set the CPU affinity of a process

프로세스의 CPU 친화도 설정

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `size_t cpusetsize`: Size of the CPU set.
- `cpu_set_t *mask`: CPU set to assign.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SCHED_SETPARAM

> Set scheduling parameters of a process

프로세스의 스케줄링 매개변수 설정

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `const struct sched_param *param`: Scheduling parameters.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SCHED_SETSCHEDULER

> Set scheduling policy and parameters of a process

프로세스의 스케줄링 정책 및 매개변수 설정

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `int policy`: Scheduling policy.
- `const struct sched_param *param`: Scheduling parameters.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SCHED_SETATTR

> Set extended scheduling attributes

확장된 스케줄링 속성 설정

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `struct sched_attr *attr`: Scheduling attributes.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETPGID

> Set process group ID of a process

프로세스의 프로세스 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `pid_t pgid`: New process group ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SETSID

> Create a new session and set the process group ID

새로운 세션 생성 및 프로세스 그룹 ID 설정

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- On success, returns the new session ID.
- On failure, returns -1.

---

### GETPID

> Get the process ID of the calling process

호출한 프로세스의 PID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- Returns the PID of the calling process.

---

### GETPPID

> Get the parent process ID of the calling process

호출한 프로세스의 부모 PID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- Returns the PID of the parent process.

---

### GETTID

> Get the thread ID of the calling thread

호출한 스레드의 TID 가져오기

**LIBRARY**: System call interface (direct syscall)

**Arguments**: None

**Return Value**:

- Returns the TID of the calling thread.

---

### GETUID

> Get the real user ID of the calling process

호출한 프로세스의 실제 사용자 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- Returns the real user ID.

---

### GETEUID

> Get the effective user ID of the calling process

호출한 프로세스의 유효 사용자 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- Returns the effective user ID.

---

### GETGID

> Get the real group ID of the calling process

호출한 프로세스의 실제 그룹 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- Returns the real group ID.

---

### GETEGID

> Get the effective group ID of the calling process

호출한 프로세스의 유효 그룹 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**: None

**Return Value**:

- Returns the effective group ID.

---

### GETRESUID

> Get real, effective, and saved user IDs

실제, 유효 및 저장된 사용자 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `uid_t *ruid`: Pointer to store real user ID.
- `uid_t *euid`: Pointer to store effective user ID.
- `uid_t *suid`: Pointer to store saved user ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### GETRESGID

> Get real, effective, and saved group IDs

실제, 유효 및 저장된 그룹 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `gid_t *rgid`: Pointer to store real group ID.
- `gid_t *egid`: Pointer to store effective group ID.
- `gid_t *sgid`: Pointer to store saved group ID.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### GETPGID

> Get the process group ID of a process

프로세스의 프로세스 그룹 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.

**Return Value**:

- On success, returns the PGID.
- On failure, returns -1.

---

### GETSID

> Get the session ID of a process

프로세스의 세션 ID 가져오기

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.

**Return Value**:

- On success, returns the SID.
- On failure, returns -1.

---

### GETPRIORITY

> Get the scheduling priority of a process, process group, or user

프로세스, 프로세스 그룹 또는 사용자의 스케줄링 우선순위 가져오기

**LIBRARY**: libc (`sys/resource.h`)

**Arguments**:

- `int which`: Type of identifier (e.g., PRIO_PROCESS).
- `id_t who`: Identifier of the target.

**Return Value**:

- On success, returns the priority.
- On failure, returns -1.

---

### SCHED_GETAFFINITY

> Get the CPU affinity mask of a process

프로세스의 CPU 친화도 마스크 가져오기

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `size_t cpusetsize`: Size of the CPU set.
- `cpu_set_t *mask`: Pointer to store the CPU set.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SCHED_GETPARAM

> Get scheduling parameters of a process

프로세스의 스케줄링 매개변수 가져오기

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `struct sched_param *param`: Pointer to store scheduling parameters.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### SCHED_GETSCHEDULER

> Get scheduling policy of a process

프로세스의 스케줄링 정책 가져오기

**LIBRARY**: libc (`sched.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.

**Return Value**:

- On success, returns the scheduling policy.
- On failure, returns -1.

---

### SCHED_GETATTR

> Get extended scheduling attributes

확장된 스케줄링 속성 가져오기

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `struct sched_attr *attr`: Pointer to store scheduling attributes.
- `unsigned int size`: Size of the `sched_attr` structure.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### PRLIMIT64

> Get/set resource limits

자원 제한 가져오기/설정하기

**LIBRARY**: libc (`sys/resource.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `int resource`: Resource to limit (e.g., RLIMIT_CPU).
- `const struct rlimit *new_limit`: New limits.
- `struct rlimit *old_limit`: Pointer to store old limits.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### MMAP

> Map or unmap files or devices into memory

파일 또는 디바이스를 메모리에 매핑 또는 해제

**LIBRARY**: libc (`sys/mman.h`)

**Arguments**:

- `void *addr`: Desired address.
- `size_t length`: Length of mapping.
- `int prot`: Memory protection flags.
- `int flags`: Mapping flags.
- `int fd`: File descriptor.
- `off_t offset`: Offset in the file.

**Return Value**:

- On success, returns the mapped address.
- On failure, returns MAP_FAILED.

---

### MUNMAP

> Unmap files or devices from memory

파일 또는 디바이스를 메모리에서 해제

**LIBRARY**: libc (`sys/mman.h`)

**Arguments**:

- `void *addr`: Address of the mapping.
- `size_t length`: Length of the mapping.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### BRK

> Change data segment size

데이터 세그먼트 크기 변경

**LIBRARY**: libc (`unistd.h`)

**Arguments**:

- `void *addr`: New end of data segment.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### MPROTECT

> Set protection on a region of memory

메모리 영역의 보호 설정

**LIBRARY**: libc (`sys/mman.h`)

**Arguments**:

- `void *addr`: Address to start.
- `size_t len`: Length of the area.
- `int prot`: Desired memory protection.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### MADVISE

> Give advice about use of memory

메모리 사용에 대한 조언 제공

**LIBRARY**: libc (`sys/mman.h`)

**Arguments**:

- `void *addr`: Address to start.
- `size_t length`: Length of the area.
- `int advice`: Advice to give.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### PROCESS_VM_READV

> Read from another process's memory

다른 프로세스의 메모리에서 읽기

**LIBRARY**: libc (`sys/uio.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `const struct iovec *local_iov`: Local buffers.
- `unsigned long liovcnt`: Number of local buffers.
- `const struct iovec *remote_iov`: Remote buffers.
- `unsigned long riovcnt`: Number of remote buffers.
- `unsigned long flags`: Flags (must be 0).

**Return Value**:

- On success, returns number of bytes read.
- On failure, returns -1.

---

### PROCESS_VM_WRITEV

> Write to another process's memory

다른 프로세스의 메모리에 쓰기

**LIBRARY**: libc (`sys/uio.h`)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `const struct iovec *local_iov`: Local buffers.
- `unsigned long liovcnt`: Number of local buffers.
- `const struct iovec *remote_iov`: Remote buffers.
- `unsigned long riovcnt`: Number of remote buffers.
- `unsigned long flags`: Flags (must be 0).

**Return Value**:

- On success, returns number of bytes written.
- On failure, returns -1.

---

### MEMBARRIER

> Memory barrier for threads

스레드를 위한 메모리 배리어

**LIBRARY**: libc (`linux/membarrier.h`)

**Arguments**:

- `int cmd`: Command to execute.
- `int flags`: Flags (must be 0).

**Return Value**:

- On success, returns 0 or a positive value depending on `cmd`.
- On failure, returns -1.

---

### PTRACE

> Process trace

프로세스 추적

**LIBRARY**: libc (`sys/ptrace.h`)

**Arguments**:

- `enum __ptrace_request request`: Request type.
- `pid_t pid`: PID of the target process.
- `void *addr`: Address for the operation.
- `void *data`: Data for the operation.

**Return Value**:

- On success, returns dependent on request.
- On failure, returns -1.

---

### PIDFD_OPEN

> Obtain a file descriptor that refers to a process

프로세스를 참조하는 파일 디스크립터 얻기

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `pid_t pid`: PID of the target process.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns a file descriptor.
- On failure, returns -1.

---

### PIDFD_GETFD

> Duplicate a file descriptor of another process

다른 프로세스의 파일 디스크립터 복제

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int pidfd`: File descriptor referring to the process.
- `int targetfd`: Target file descriptor in the process.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns a new file descriptor.
- On failure, returns -1.

---

### PIDFD_SEND_SIGNAL

> Send a signal to a process via a pidfd

pidfd를 통해 프로세스에 신호 보내기

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int pidfd`: File descriptor referring to the process.
- `int sig`: Signal number.
- `siginfo_t *info`: Signal information.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### PROCESS_MADVISE

> Give advice about use of memory in another process

다른 프로세스의 메모리 사용에 대한 조언 제공

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int pidfd`: File descriptor referring to the process.
- `const struct iovec *iovec`: Memory regions.
- `size_t vlen`: Number of memory regions.
- `int advice`: Advice to give.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### PROCESS_MRELEASE

> Release the memory of a process

프로세스의 메모리 해제

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int pidfd`: File descriptor referring to the process.
- `unsigned int flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### LANDLOCK_CREATE_RULESET

> Create a new Landlock ruleset

새로운 Landlock 규칙 집합 생성

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `const struct landlock_ruleset_attr *attr`: Ruleset attributes.
- `size_t size`: Size of the attributes.
- `__u32 flags`: Flags.

**Return Value**:

- On success, returns a file descriptor.
- On failure, returns -1.

---

### LANDLOCK_ADD_RULE

> Add a rule to a Landlock ruleset

Landlock 규칙 집합에 규칙 추가

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int ruleset_fd`: File descriptor of the ruleset.
- `enum landlock_rule_type rule_type`: Type of rule.
- `const void *rule_attr`: Rule attributes.
- `__u32 flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

---

### LANDLOCK_RESTRICT_SELF

> Apply a Landlock ruleset to the current process

현재 프로세스에 Landlock 규칙 집합 적용

**LIBRARY**: System call interface (direct syscall)

**Arguments**:

- `int ruleset_fd`: File descriptor of the ruleset.
- `__u32 flags`: Flags.

**Return Value**:

- On success, returns 0.
- On failure, returns -1.

