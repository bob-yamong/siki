# Process-Related kprobe in Linux

> [!WARNING]
> This part of the docs is incomplete, contributions are very welcome
> 
> 이 문서는 불완전합니다. 기여를 환영합니다.


### do_fork (또는 _do_fork)

> 새 프로세스를 생성할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- (커널 내부 함수이므로 정확한 인자는 커널 버전에 따라 다를 수 있습니다)

**Use Case**: 

- 프로세스 생성 이벤트 모니터링
- 새 프로세스의 특성 분석

**Return Value**:

- 성공 시 새로 생성된 프로세스의 PID
- 실패 시 오류 코드

**manual page**:

- Null

---

### do_exit

> 프로세스가 종료될 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- long code (종료 코드)

**Use Case**: 

- 프로세스 종료 이유 분석
- 리소스 해제 추적

**Return Value**:

- 이 함수는 반환하지 않습니다 (프로세스 종료)

**manual page**:

- Null

---

### wake_up_new_task

> 새로 생성된 태스크를 깨울 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- struct task_struct *p (새로 생성된 태스크의 구조체 포인터)

**Use Case**: 

- 새 태스크의 초기 스케줄링 분석

**Return Value**:

- void (반환 값 없음)

**manual page**:

- Null

---

### sched_switch

> 프로세스 간 컨텍스트 스위치가 발생할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- struct task_struct *prev (이전 태스크)
- struct task_struct *next (다음 태스크)

**Use Case**: 

- CPU 사용 패턴 분석
- 스케줄링 결정 추적

**Return Value**:

- void (반환 값 없음)

**manual page**:

- Null

---

### do_execve (또는 do_execveat)

> 새로운 프로그램을 실행할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- const char *filename
- const char __user *const __user *argv
- const char __user *const __user *envp

**Use Case**: 

- 프로그램 실행 추적
- 보안 감사

**Return Value**:

- 성공 시 0
- 실패 시 음수 오류 코드

**manual page**:

- Null

---

### sys_clone

> 새 프로세스나 스레드를 생성하는 시스템 콜입니다.

**LIBRARY**: 

- libc

**Arguments**: 

- unsigned long flags
- void *child_stack
- int *ptid
- int *ctid
- unsigned long newtls

**Use Case**: 

- 프로세스/스레드 생성 패턴 분석

**Return Value**:

- 성공 시 자식 프로세스의 PID
- 실패 시 -1

**manual page**:

- man 2 clone

---

### do_sigaction

> 프로세스의 시그널 핸들러를 설정할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- int sig (시그널 번호)
- struct k_sigaction *act (새 시그널 핸들러)
- struct k_sigaction *oact (이전 시그널 핸들러)

**Use Case**: 

- 시그널 처리 동작 분석
- 보안 모니터링

**Return Value**:

- 성공 시 0
- 실패 시 오류 코드

**manual page**:

- Null

---

### do_wait

> 프로세스가 자식 프로세스의 종료를 기다릴 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- (커널 내부 함수이므로 정확한 인자는 커널 버전에 따라 다를 수 있습니다)

**Use Case**: 

- 프로세스 간 동기화 패턴 분석

**Return Value**:

- 종료된 자식 프로세스의 PID 또는 오류 코드

**manual page**:

- Null

---

### do_set_tid_address

> 스레드 ID를 설정할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- int __user *tidptr (스레드 ID를 저장할 사용자 공간 주소)

**Use Case**: 

- 멀티스레딩 동작 분석

**Return Value**:

- 현재 스레드의 PID

**manual page**:

- Null

---

### set_task_comm

> 프로세스 이름을 변경할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- struct task_struct *tsk (대상 태스크 구조체)
- const char *comm (새 이름)

**Use Case**: 

- 프로세스 이름 변경 추적
- 동적 행동 분석

**Return Value**:

- void (반환 값 없음)

**manual page**:

- Null

---

### sched_setscheduler

> 프로세스의 스케줄링 정책이나 우선순위를 변경할 때 호출됩니다.

**LIBRARY**: 

- libc

**Arguments**: 

- pid_t pid
- int policy
- const struct sched_param *param

**Use Case**: 

- 스케줄링 변경 추적
- 성능 최적화 분석

**Return Value**:

- 성공 시 0
- 실패 시 -1

**manual page**:

- man 2 sched_setscheduler

---

### do_sysinfo

> 시스템 정보를 조회할 때 호출됩니다.

**LIBRARY**: 

- kernel

**Arguments**: 

- struct sysinfo *info

**Use Case**: 

- 시스템 정보 요청 패턴 분석

**Return Value**:

- 성공 시 0
- 실패 시 오류 코드

**manual page**:

- Null

---