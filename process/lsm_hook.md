# Process-Related LSM Hooks in Linux

## 1. 생성
## `task_alloc`
> 새 태스크 구조체 할당 시 호출

**Param** :
- `struct task_struct` *task
- `unsigned long` clone_flags

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

**Return(default_value)** :
- `int` 0

## 2. 종료
## `task_free`
> 태스크 구조체 해제 시 호출

**Param** :
- `struct task_struct` *task

**Return(default_value)** :
- `void` LSM_RET_VOID

## 3. 권한 변경
## `task_fix_setuid`
> setuid 작업 수행 시 호출

**Param** :
- `struct cred` *new
- `const struct cred` *old 
- `int` flags

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

**Return(default_value)** :
- `int` 0

## `task_fix_setgid`
> setgid 작업 수행 시 호출

**Param** :
- `struct cred` *new
- `const struct cred` * old
- `int` flags

**Return(default_value)** :
- `int` 0

## `task_fix_setgroups`
> setgroups 작업 수행 시 호출

**Param** :
- `struct cred` *new 
- `const struct cred` *old

**Return(default_value)** :
- `int` 0

## 4. 스케줄링
## `task_setscheduler`
> 스케줄러 정책 설정 시 호출

**Param** :
- `struct task_struct` *p

**Return(default_value)** :
- `int` 0

## `task_getscheduler`
> 스케줄러 정책 조회 시 호출

**Param** :
- `struct task_struct` *p

**Return(default_value)** :
- `int` 0

## `task_setnice`
> 프로세스 nice 값 설정 시 호출

**Param** :
- `struct task_struct` *p
- `int` nice

**Return(default_value)** :
- `int` 0

## 5. 리소스 관리
## `task_setioprio`
> 프로세스 I/O 우선순위 설정 시 호출

**Param** :
- `struct task_struct` *p
- `int` ioprio

**Return(default_value)** :
- `int` 0

## `task_getioprio`
> 프로세스 I/O 우선순위 조회 시 호출

**Param** :
- `struct task_struct` *p

**Return(default_value)** :
- `int` 0

## `task_prlimit`
> 리소스 제한 설정 시 호출

**Param** :
- `const struct cred` *cred
- `const struct cred` *tcred
- `unsigned int` flags

**Return(default_value)** :
- `int` 0

## `task_setrlimit`
> 리소스 제한 설정 시 호출

**Param** :
- `struct task_struct` *p
- `unsigned int` resource
- `struct rlimit` *new_rlim

```c
struct rlimit {
	__kernel_ulong_t rlim_cur;
	__kernel_ulong_t rlim_max;
};
```

**Return(default_value)** :
- `int` 0

## `task_movememory`
> 메모리 이동 시 호출

**Param** :
- `struct task_struct` *p

**Return(default_value)** :
- `int` 0

## 6. 그 외
## `task_setpgid`
> 프로세스 그룹 ID 설정 시 호출
  LSM_HOOK(int, 0, task_setpgid, struct task_struct *p, pid_t pgid)

**Param** :

**Return(default_value)** :
- `int` 0

## `task_getpgid`
> 프로세스 그룹 ID 조회 시 호출

**Param** :
- `struct task_struct` *p

**Return(default_value)** :
- `int` 0

## `task_getsid`
> 세션 ID 조회 시 호출

**Param** :
- `struct task_struct` *p

**Return(default_value)** :
- `int` 0

## `task_kill`
> 시그널 전송 시 호출

**Param** :
- `struct task_struct` *p
- `struct kernel_siginfo` *info
- `int` sig
- `const struct cred` *cred

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

**Return(default_value)** :
- `int` 0

## `task_prctl`
> prctl 시스템 콜 호출 시 호출

**Param** :
- `int` option
- `unsigned long` arg2
- `unsigned long` arg3
- `unsigned long` arg4
- `unsigned long` arg5

**Return(default_value)** :
- `int` -ENOSYS

## `task_to_inode`
> 태스크를 inode로 변환 시 호출
  
**Param** :
- `struct task_struct` *p 
- `struct inode` *inode

**Return(default_value)** :
- `void` LSM_RET_VOID