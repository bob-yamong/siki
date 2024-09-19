# Process-Related LSM Hooks in Linux
프로세스 조작과 관련된 tracepoint 정리 문서

> [!NOTE]
> This document is based on linux repository(v.6.8)[^1].
>
> 이 문서는 리눅스 레포(v.6.8)[^1]를 기준으로 작성 하였음.

## Table of Content
1. 생성
	- [task_alloc](#task_alloc)
2. 종료
	- [task_free](#task_free)
3. 권한 변경
	- [task_fix_setuid](#task_fix_setuid)
	- [task_fix_setgid](#task_fix_setgid)
	- [task_fix_setgroups](#task_fix_setgroups)
4. 스케줄링
	- [task_setscheduler](#task_setscheduler)
	- [task_getscheduler](#task_getscheduler)
	- [task_setnice](#task_setnice)
5. 리소스 관리
	- [task_setioprio](#task_setioprio)
	- [task_getioprio](#task_getioprio)
	- [task_prlimit](#task_prlimit)
	- [task_setrlimit](#task_setrlimit)
	- [task_movememory](#task_movememory)
6. 그 외
	- [task_setpgid](#task_setpgid)
	- [task_getpgid](#task_getpgid)
	- [task_getsid](#task_getsid)
	- [task_kill](#task_kill)
	- [task_prctl](#task_prctl)
	- [task_to_inode](#task_to_inode)

## 1. 생성
### task_alloc
> 새 태스크 구조체 할당 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *task: 새로 할당된 태스크(프로세스 또는 스레드) 구조체를 가리키는 포인터. 이 구조체는 새로운 프로세스나 스레드의 모든 정보를 포함
- `unsigned long` clone_flags: 프로세스 또는 스레드 생성 시 사용된 플래그. 예를 들어, CLONE_THREAD 플래그는 새 스레드를 생성

```c
struct task_struct {
	struct thread_info thread_info;
	unsigned int __state;
	void *stack;
	refcount_t usage;
	unsigned int flags;
	unsigned int ptrace;
	int on_cpu;
	struct __call_single_node wake_entry;
	unsigned int wakee_flips;
	long unsigned int wakee_flip_decay_ts;
	struct task_struct *last_wakee;
	int recent_used_cpu;
	int wake_cpu;
	int on_rq;
	int prio;
	int static_prio;
	int normal_prio;
	unsigned int rt_priority;
	struct sched_entity se;
	struct sched_rt_entity rt;
	struct sched_dl_entity dl;
	const struct sched_class *sched_class;
	struct task_group *sched_task_group;
	long: 64;
	long: 64;
	long: 64;
	long: 64;
	struct sched_statistics stats;
	unsigned int btrace_seq;
	unsigned int policy;
	int nr_cpus_allowed;
	const cpumask_t *cpus_ptr;
	cpumask_t *user_cpus_ptr;
	cpumask_t cpus_mask;
	void *migration_pending;
	short unsigned int migration_disabled;
	short unsigned int migration_flags;
	int rcu_read_lock_nesting;
	union rcu_special rcu_read_unlock_special;
	struct list_head rcu_node_entry;
	struct rcu_node *rcu_blocked_node;
	long unsigned int rcu_tasks_nvcsw;
	u8 rcu_tasks_holdout;
	u8 rcu_tasks_idx;
	int rcu_tasks_idle_cpu;
	struct list_head rcu_tasks_holdout_list;
	int trc_reader_nesting;
	int trc_ipi_to_cpu;
	union rcu_special trc_reader_special;
	struct list_head trc_holdout_list;
	struct list_head trc_blkd_node;
	int trc_blkd_cpu;
	struct sched_info sched_info;
	struct list_head tasks;
	struct plist_node pushable_tasks;
	struct rb_node pushable_dl_tasks;
	struct mm_struct *mm;
	struct mm_struct *active_mm;
	struct task_rss_stat rss_stat;
	int exit_state;
	int exit_code;
	int exit_signal;
	int pdeath_signal;
	long unsigned int jobctl;
	unsigned int personality;
	unsigned int sched_reset_on_fork: 1;
	unsigned int sched_contributes_to_load: 1;
	unsigned int sched_migrated: 1;
	long: 29;
	unsigned int sched_remote_wakeup: 1;
	unsigned int in_execve: 1;
	unsigned int in_iowait: 1;
	unsigned int restore_sigmask: 1;
	unsigned int in_user_fault: 1;
	unsigned int brk_randomized: 1;
	unsigned int no_cgroup_migration: 1;
	unsigned int frozen: 1;
	unsigned int use_memdelay: 1;
	unsigned int in_eventfd: 1;
	unsigned int reported_split_lock: 1;
	unsigned int in_thrashing: 1;
	long unsigned int atomic_flags;
	struct restart_block restart_block;
	pid_t pid;
	pid_t tgid;
	long unsigned int stack_canary;
	struct task_struct *real_parent;
	struct task_struct *parent;
	struct list_head children;
	struct list_head sibling;
	struct task_struct *group_leader;
	struct list_head ptraced;
	struct list_head ptrace_entry;
	struct pid *thread_pid;
	struct hlist_node pid_links[4];
	struct list_head thread_group;
	struct list_head thread_node;
	struct completion *vfork_done;
	int *set_child_tid;
	int *clear_child_tid;
	void *worker_private;
	u64 utime;
	u64 stime;
	u64 gtime;
	struct prev_cputime prev_cputime;
	long unsigned int nvcsw;
	long unsigned int nivcsw;
	u64 start_time;
	u64 start_boottime;
	long unsigned int min_flt;
	long unsigned int maj_flt;
	struct posix_cputimers posix_cputimers;
	struct posix_cputimers_work posix_cputimers_work;
	const struct cred *ptracer_cred;
	const struct cred *real_cred;
	const struct cred *cred;
	struct key *cached_requested_key;
	char comm[16];
	struct nameidata *nameidata;
	struct sysv_sem sysvsem;
	struct sysv_shm sysvshm;
	long unsigned int last_switch_count;
	long unsigned int last_switch_time;
	struct fs_struct *fs;
	struct files_struct *files;
	struct io_uring_task *io_uring;
	struct nsproxy *nsproxy;
	struct signal_struct *signal;
	struct sighand_struct *sighand;
	sigset_t blocked;
	sigset_t real_blocked;
	sigset_t saved_sigmask;
	struct sigpending pending;
	long unsigned int sas_ss_sp;
	size_t sas_ss_size;
	unsigned int sas_ss_flags;
	struct callback_head *task_works;
	struct audit_context *audit_context;
	kuid_t loginuid;
	unsigned int sessionid;
	struct seccomp seccomp;
	struct syscall_user_dispatch syscall_dispatch;
	u64 parent_exec_id;
	u64 self_exec_id;
	spinlock_t alloc_lock;
	raw_spinlock_t pi_lock;
	struct wake_q_node wake_q;
	struct rb_root_cached pi_waiters;
	struct task_struct *pi_top_task;
	struct rt_mutex_waiter *pi_blocked_on;
	struct mutex_waiter *blocked_on;
	int non_block_count;
	struct irqtrace_events irqtrace;
	unsigned int hardirq_threaded;
	u64 hardirq_chain_key;
	int softirqs_enabled;
	int softirq_context;
	int irq_config;
	u64 curr_chain_key;
	int lockdep_depth;
	unsigned int lockdep_recursion;
	struct held_lock held_locks[48];
	void *journal_info;
	struct bio_list *bio_list;
	struct blk_plug *plug;
	struct reclaim_state *reclaim_state;
	struct backing_dev_info *backing_dev_info;
	struct io_context *io_context;
	struct capture_control *capture_control;
	long unsigned int ptrace_message;
	kernel_siginfo_t *last_siginfo;
	struct task_io_accounting ioac;
	u64 acct_rss_mem1;
	u64 acct_vm_mem1;
	u64 acct_timexpd;
	nodemask_t mems_allowed;
	seqcount_spinlock_t mems_allowed_seq;
	int cpuset_mem_spread_rotor;
	int cpuset_slab_spread_rotor;
	struct css_set *cgroups;
	struct list_head cg_list;
	struct robust_list_head *robust_list;
	struct list_head pi_state_list;
	struct futex_pi_state *pi_state_cache;
	struct mutex futex_exit_mutex;
	unsigned int futex_state;
	struct perf_event_context *perf_event_ctxp[2];
	struct mutex perf_event_mutex;
	struct list_head perf_event_list;
	long unsigned int preempt_disable_ip;
	struct mempolicy *mempolicy;
	short int il_prev;
	short int pref_node_fork;
	int numa_scan_seq;
	unsigned int numa_scan_period;
	unsigned int numa_scan_period_max;
	int numa_preferred_nid;
	long unsigned int numa_migrate_retry;
	u64 node_stamp;
	u64 last_task_numa_placement;
	u64 last_sum_exec_runtime;
	struct callback_head numa_work;
	struct numa_group *numa_group;
	long unsigned int *numa_faults;
	long unsigned int total_numa_faults;
	long unsigned int numa_faults_locality[3];
	long unsigned int numa_pages_migrated;
	struct rseq *rseq;
	u32 rseq_sig;
	long unsigned int rseq_event_mask;
	struct tlbflush_unmap_batch tlb_ubc;
	union {
		refcount_t rcu_users;
		struct callback_head rcu;
	};
	struct pipe_inode_info *splice_pipe;
	struct page_frag task_frag;
	struct task_delay_info *delays;
	int make_it_fail;
	unsigned int fail_nth;
	int nr_dirtied;
	int nr_dirtied_pause;
	long unsigned int dirty_paused_when;
	u64 timer_slack_ns;
	u64 default_timer_slack_ns;
	int curr_ret_stack;
	int curr_ret_depth;
	struct ftrace_ret_stack *ret_stack;
	long long unsigned int ftrace_timestamp;
	atomic_t trace_overrun;
	atomic_t tracing_graph_pause;
	long unsigned int trace_recursion;
	struct mem_cgroup *memcg_in_oom;
	gfp_t memcg_oom_gfp_mask;
	int memcg_oom_order;
	unsigned int memcg_nr_pages_over_high;
	struct mem_cgroup *active_memcg;
	struct request_queue *throttle_queue;
	struct uprobe_task *utask;
	struct kmap_ctrl kmap_ctrl;
	long unsigned int task_state_change;
	int pagefault_disabled;
	struct task_struct *oom_reaper_list;
	struct timer_list oom_reaper_timer;
	struct vm_struct *stack_vm_area;
	refcount_t stack_refcount;
	void *security;
	struct bpf_local_storage *bpf_storage;
	struct bpf_run_ctx *bpf_ctx;
	void *mce_vaddr;
	__u64 mce_kflags;
	u64 mce_addr;
	__u64 mce_ripv: 1;
	__u64 mce_whole_page: 1;
	__u64 __mce_reserved: 62;
	struct callback_head mce_kill_me;
	int mce_count;
	struct llist_head kretprobe_instances;
	struct llist_head rethooks;
	struct callback_head l1d_flush_kill;
	long: 64;
	long: 64;
	struct thread_struct thread;
};
```

**Use Case** : 
- 새로운 프로세스나 스레드 생성 시 보안 컨텍스트 초기화
- 프로세스 생성 제한 또는 모니터링

**Return** :
- `int` 0

## 2. 종료
### task_free
> 태스크 구조체 해제 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *task: 해제될 태스크 구조체를 가리키는 포인터. 이 구조체는 종료되는 프로세스나 스레드의 정보를 포함.

**Use Case** : 
- 프로세스 종료 시 보안 리소스 정리
- 프로세스 종료 이벤트 로깅

**Return** :
- `void` LSM_RET_VOID

## 3. 권한 변경
### task_fix_setuid
> setuid 작업 수행 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct cred` *new: 새로운 자격 증명(credentials) 정보를 포함하는 구조체 포인터
- `const struct cred` *old: 이전 자격 증명 정보를 포함하는 구조체 포인터 
- `int` flags: setuid 작업과 관련된 플래그

```c
struct cred {
	atomic_t usage;
	atomic_t subscribers;
	void *put_addr;
	unsigned int magic;
	kuid_t uid;
	kgid_t gid;
	kuid_t suid;
	kgid_t sgid;
	kuid_t euid;
	kgid_t egid;
	kuid_t fsuid;
	kgid_t fsgid;
	unsigned int securebits;
	kernel_cap_t cap_inheritable;
	kernel_cap_t cap_permitted;
	kernel_cap_t cap_effective;
	kernel_cap_t cap_bset;
	kernel_cap_t cap_ambient;
	unsigned char jit_keyring;
	struct key *session_keyring;
	struct key *process_keyring;
	struct key *thread_keyring;
	struct key *request_key_auth;
	void *security;
	struct user_struct *user;
	struct user_namespace *user_ns;
	struct ucounts *ucounts;
	struct group_info *group_info;
	union {
		int non_rcu;
		struct callback_head rcu;
	};
};
```

**Use Case** : 
- setuid 작업 시 권한 변경 검증
- 권한 상승 시도 감지 및 제어

**Return** :
- `int` 0

### task_fix_setgid
> setgid 작업 수행 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct cred` *new: 새로운 자격 증명(credentials) 정보를 포함하는 구조체 포인터
- `const struct cred` *old: 이전 자격 증명 정보를 포함하는 구조체 포인터 
- `int` flags: setuid 작업과 관련된 플래그

**Use Case** : 
- setgid 작업 시 그룹 권한 변경 검증
- 그룹 권한 상승 시도 감지 및 제어

**Return** :
- `int` 0

### task_fix_setgroups
> setgroups 작업 수행 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct cred` *new: 새로운 보조 그룹 목록을 포함하는 자격 증명 구조체 포인터
- `const struct cred` *old: 이전 보조 그룹 목록을 포함하는 자격 증명 구조체 포인터

**Use Case** : 
- 프로세스의 보조 그룹 목록 변경 검증
- 그룹 멤버십 변경 모니터링

**Return** :
- `int` 0

## 4. 스케줄링
### task_setscheduler
> 스케줄러 정책 설정 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 스케줄링 정책이 변경될 태스크의 구조체 포인터

**Use Case** : 
- 프로세스 스케줄링 정책 변경 제어
- 특정 프로세스의 스케줄링 우선순위 제한

**Return** :
- `int` 0

### task_getscheduler
> 스케줄러 정책 조회 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 스케줄링 정책을 조회할 태스크의 구조체 포인터

**Use Case** : 
- 프로세스 스케줄링 정책 조회 권한 검증
- 스케줄링 정보 접근 감사

**Return** :
- `int` 0

### task_setnice
> 프로세스 nice 값 설정 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: nice 값이 변경될 태스크의 구조체 포인터
- `int` nice: 설정하려는 새로운 nice 값

**Use Case** : 
- 프로세스 우선순위 변경 제어
- 리소스 사용 조절을 위한 nice 값 설정 제한

**Return** :
- `int` 0

## 5. 리소스 관리
### task_setioprio
> 프로세스 I/O 우선순위 설정 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: I/O 우선순위가 변경될 태스크의 구조체 포인터
- `int` ioprio: 설정하려는 새로운 I/O 우선순위 값

**Use Case** : 
- I/O 우선순위 변경 권한 검증
- 특정 프로세스의 I/O 사용량 제어

**Return** :
- `int` 0

### task_getioprio
> 프로세스 I/O 우선순위 조회 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: I/O 우선순위를 조회할 태스크의 구조체 포인터

**Use Case** : 
- I/O 우선순위 정보 접근 권한 검증
- I/O 우선순위 조회 감사

**Return** :
- `int` 0

### task_prlimit
> 리소스 제한 설정 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `const struct cred` *cred: 작업을 수행하는 프로세스의 자격 증명 구조체 포인터
- `const struct cred` *tcred: 대상 프로세스의 자격 증명 구조체 포인터
- `unsigned int` flags: prlimit 작업과 관련된 플래그

**Use Case** : 
- 프로세스 리소스 제한 변경 권한 검증
- 리소스 사용량 제어 정책 적용

**Return** :
- `int` 0

### task_setrlimit
> 리소스 제한 설정 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 리소스 제한이 변경될 태스크의 구조체 포인터
- `unsigned int` resource: 제한을 설정할 리소스의 식별자
- `struct rlimit` *new_rlim: 새로운 리소스 제한 값을 포함하는 구조체 포인터

```c
struct rlimit {
	__kernel_ulong_t rlim_cur;
	__kernel_ulong_t rlim_max;
};
```

**Use Case** : 
- 특정 리소스에 대한 제한 설정 제어
- 리소스 제한 변경 감사

**Return** :
- `int` 0

### task_movememory
> 메모리 이동 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 메모리 이동 작업이 수행될 태스크의 구조체 포인터

**Use Case** : 
- 프로세스 메모리 이동 작업 검증
- 메모리 조작 시도 탐지

**Return** :
- `int` 0

## 6. 그 외
### task_setpgid
> 프로세스 그룹 ID 설정 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 프로세스 그룹 ID가 변경될 태스크의 구조체 포인터
- `pid_t` pgid: 설정하려는 새로운 프로세스 그룹 ID

**Use Case** : 
- 프로세스 그룹 변경 권한 검증
- 프로세스 그룹 관리 감사

**Return** :
- `int` 0

### task_getpgid
> 프로세스 그룹 ID 조회 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 프로세스 그룹 ID를 조회할 태스크의 구조체 포인터

**Use Case** : 
- 프로세스 그룹 정보 접근 권한 검증
- 프로세스 그룹 조회 로깅

**Return** :
- `int` 0

### task_getsid
> 세션 ID 조회 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 세션 ID를 조회할 태스크의 구조체 포인터

**Use Case** : 
- 세션 ID 접근 권한 검증
- 세션 관리 감사

**Return** :
- `int` 0

### task_kill
> 시그널 전송 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: 시그널을 받을 대상 태스크의 구조체 포인터
- `struct kernel_siginfo` *info: 전송될 시그널에 대한 상세 정보를 포함하는 구조체 포인터
- `int` sig: 전송될 시그널 번호
- `const struct cred` *cred: 시그널을 보내는 프로세스의 자격 증명 구조체 포인터

```c
struct kernel_siginfo {
	struct {
		int si_signo;
		int si_errno;
		int si_code;
		union __sifields _sifields;
	};
};
```

**Use Case** : 
- 시그널 전송 권한 검증
- 악의적인 프로세스 종료 시도 탐지

**Return** :
- `int` 0

### task_prctl
> prctl 시스템 콜 호출 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `int` option: 수행할 prctl 작업의 옵션
- `unsigned long` arg2: prctl 작업에 필요한 첫 번째 인자
- `unsigned long` arg3: prctl 작업에 필요한 두 번째 인자
- `unsigned long` arg4: prctl 작업에 필요한 세 번째 인자
- `unsigned long` arg5: prctl 작업에 필요한 네 번째 인자

**Use Case** : 
- prctl 작업 수행 권한 검증
- 특정 prctl 작업 제한 또는 감사

**Return** :
- `int` -ENOSYS

### task_to_inode
> 태스크를 inode로 변환 시 호출

**LIBRARY**: 
- #include <vmlinux.h>

**Arguments** :
- `struct task_struct` *p: inode로 변환될 태스크의 구조체 포인터 
- `struct inode` *inode: 태스크 정보를 저장할 inode 구조체 포인터

**Use Case** : 
- 프로세스 정보를 파일 시스템 inode로 변환 시 보안 속성 설정
- 프로세스 정보 노출 제어

**Return** :
- `void` LSM_RET_VOID


[^1]: [linux 레포 lsm_hook](https://github.com/torvalds/linux/blob/v6.8/include/linux/lsm_hook_defs.h)
