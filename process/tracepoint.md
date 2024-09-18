## 1. 프로세스 생성 및 종료 관련 시스템 호출

### FORK

> 자식 프로세스를 생성합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- 없음

**Use Case**:
- 부모 프로세스가 자신의 주소 공간을 복제하여 새로운 프로세스를 생성할 때 사용
- 멀티프로세스 프로그램에서 프로세스 분기를 위해 사용

**Return Value**:
- **부모 프로세스**: 성공 시 자식 프로세스의 PID를 반환
- **자식 프로세스**: 0을 반환
- **실패 시**: -1을 반환하며, 자식 프로세스는 생성되지 않음

---

### VFORK

> 자식 프로세스를 생성하지만, 주소 공간을 공유합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- 없음

**Use Case**:
- 새로운 프로그램을 실행하기 전에 부모 프로세스의 실행을 잠시 중단하고자 할 때 사용
- 메모리 자원 효율화를 위해 주소 공간을 공유

**Return Value**:
- **부모 프로세스**: 성공 시 자식 프로세스의 PID를 반환
- **자식 프로세스**: 0을 반환
- **실패 시**: -1을 반환하며, 자식 프로세스는 생성되지 않음

---

### CLONE

> 새로운 프로세스 또는 스레드를 생성합니다.

**LIBRARY**:
- `#include <sched.h>`

**Arguments**:
- `int (*fn)(void *)`: 자식 프로세스가 실행할 함수의 포인터
- `void *child_stack`: 자식 스택의 시작 주소
- `int flags`: 프로세스 생성 옵션 플래그
- `void *arg`: 함수에 전달할 인자
- 추가 인자들 (`pid_t *ptid`, `void *newtls`, `pid_t *ctid`)은 `flags`에 따라 사용

**Use Case**:
- 스레드 생성 또는 프로세스 생성 시 자원 공유를 세밀하게 제어할 때 사용
- 네임스페이스 분리, 자원 제어 등 고급 기능 활용

**Return Value**:
- **부모 프로세스**: 성공 시 자식의 PID를 반환
- **자식 프로세스**: 0을 반환
- **실패 시**: -1을 반환

---

### CLONE3

> 확장된 옵션으로 새로운 프로세스 또는 스레드를 생성합니다.

**LIBRARY**:
- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:
- `struct clone_args *cl_args`: 확장된 클론 인자 구조체
- `size_t size`: 구조체의 크기

**Use Case**:
- `clone` 시스템 호출의 확장 버전으로, 더 많은 옵션과 기능 제공
- 사용자 네임스페이스 생성, cgroup 설정 등 고급 프로세스 제어

**Return Value**:
- **부모 프로세스**: 성공 시 자식의 PID를 반환
- **자식 프로세스**: 0을 반환
- **실패 시**: -1을 반환

---

### EXECVE

> 현재 프로세스를 새로운 프로그램으로 대체합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 실행할 프로그램의 경로
- `char *const argv[]`: 프로그램에 전달할 인자 배열
- `char *const envp[]`: 프로그램에 전달할 환경 변수 배열

**Use Case**:
- 새로운 프로그램을 실행하여 현재 프로세스를 대체할 때 사용
- 쉘이나 런처 프로그램에서 다른 프로그램을 실행할 때 활용

**Return Value**:
- **성공 시**: 반환하지 않음 (새로운 프로그램으로 대체됨)
- **실패 시**: -1을 반환하며, 기존 프로세스는 그대로 유지됨

---

### EXECVEAT

> 디렉토리 파일 디스크립터를 기준으로 프로그램을 실행합니다.

**LIBRARY**:
- `#include <fcntl.h>` (플래그를 위해)
- `#include <unistd.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리의 파일 디스크립터
- `const char *pathname`: 실행할 프로그램의 경로
- `char *const argv[]`: 인자 배열
- `char *const envp[]`: 환경 변수 배열
- `int flags`: 실행 옵션 플래그 (`AT_EMPTY_PATH` 등)

**Use Case**:
- 열린 디렉토리를 기준으로 상대 경로의 프로그램을 실행할 때 사용
- 파일 시스템 네임스페이스를 변경하거나 특별한 플래그를 사용할 때 활용

**Return Value**:
- **성공 시**: 반환하지 않음 (새로운 프로그램으로 대체됨)
- **실패 시**: -1을 반환하며, 기존 프로세스는 그대로 유지됨

---

### EXIT

> 프로세스를 종료합니다.

**LIBRARY**:
- `#include <stdlib.h>`

**Arguments**:
- `int status`: 종료 상태 코드

**Use Case**:
- 프로그램이 실행을 완료하고 종료할 때 사용
- 종료 전에 필요한 정리 작업을 수행한 후 호출

**Return Value**:
- 반환하지 않음 (프로세스가 종료됨)

---

### EXIT_GROUP

> 프로세스 그룹 전체를 종료합니다.

**LIBRARY**:
- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:
- `int status`: 종료 상태 코드

**Use Case**:
- 멀티스레드 프로세스에서 모든 스레드를 종료할 때 사용
- 프로세스 그룹 단위의 종료가 필요할 때 활용

**Return Value**:
- 반환하지 않음 (프로세스 그룹이 종료됨)

---

### WAIT4

> 자식 프로세스의 종료를 대기하고 리소스 사용 정보를 얻습니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/wait.h>`

**Arguments**:
- `pid_t pid`: 대기할 자식 프로세스의 PID
- `int *wstatus`: 종료 상태를 저장할 포인터
- `int options`: 동작을 수정하는 옵션들 (`WNOHANG` 등)
- `struct rusage *rusage`: 리소스 사용 정보를 저장할 구조체

**Use Case**:
- 자식 프로세스의 종료를 감지하고, 해당 프로세스의 리소스 사용량을 확인할 때 사용
- 멀티프로세스 관리에서 자식 프로세스의 상태를 추적

**Return Value**:
- **성공 시**: 종료된 자식 프로세스의 PID 반환
- **실패 시**: -1을 반환

---

### WAITID

> 특정 자식 프로세스나 프로세스 그룹의 상태 변화를 대기합니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/wait.h>`

**Arguments**:
- `idtype_t idtype`: 대기할 대상의 유형 (`P_PID`, `P_PGID` 등)
- `id_t id`: 대상의 ID
- `siginfo_t *infop`: 프로세스 정보를 저장할 구조체 포인터
- `int options`: 동작을 수정하는 옵션들
- `struct rusage *rusage`: 리소스 사용 정보를 저장할 구조체

**Use Case**:
- 비동기적으로 자식 프로세스의 상태 변화를 감시할 때 사용
- 세밀한 제어가 필요한 프로세스 관리에서 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### WAITPID

> 자식 프로세스의 종료를 대기합니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/wait.h>`

**Arguments**:
- `pid_t pid`: 대기할 자식 프로세스의 PID
- `int *wstatus`: 종료 상태를 저장할 포인터
- `int options`: 동작을 수정하는 옵션들

**Use Case**:
- 특정 자식 프로세스의 종료를 기다릴 때 사용
- 간단한 프로세스 제어 흐름에서 자주 활용

**Return Value**:
- **성공 시**: 종료된 자식 프로세스의 PID 반환
- **실패 시**: -1을 반환

---

## 2. 프로세스 신호 처리 관련 시스템 호출

### KILL

> 프로세스나 프로세스 그룹에 신호를 보냅니다.

**LIBRARY**:
- `#include <signal.h>`

**Arguments**:
- `pid_t pid`: 신호를 보낼 대상 프로세스 또는 프로세스 그룹의 PID
- `int sig`: 보낼 신호 번호

**Use Case**:
- 프로세스 종료나 특정 동작을 유발하기 위해 신호를 보낼 때 사용
- 프로세스 제어 및 관리에서 필수적

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### TKILL

> 특정 스레드에 신호를 보냅니다.

**LIBRARY**:
- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:
- `pid_t tid`: 신호를 보낼 대상 스레드의 TID
- `int sig`: 보낼 신호 번호

**Use Case**:
- 멀티스레드 프로세스에서 특정 스레드에 신호를 보낼 때 사용
- 세밀한 스레드 제어가 필요한 경우 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### TGKILL

> 특정 프로세스 내의 특정 스레드에 신호를 보냅니다.

**LIBRARY**:
- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:
- `pid_t tgid`: 프로세스 그룹 ID (PID)
- `pid_t tid`: 스레드 ID
- `int sig`: 보낼 신호 번호

**Use Case**:
- 멀티스레드 환경에서 정확한 스레드에 신호를 전달할 때 사용
- 보안 및 안정성을 위해 정확한 대상 지정 필요 시 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### RT_SIGQUEUEINFO

> 프로세스에 실시간 신호와 데이터를 전송합니다.

**LIBRARY**:
- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:
- `pid_t pid`: 대상 프로세스의 PID
- `int sig`: 신호 번호
- `siginfo_t *uinfo`: 추가 신호 정보를 담은 구조체 포인터

**Use Case**:
- 추가적인 데이터를 포함한 실시간 신호를 보낼 때 사용
- 프로세스 간의 세밀한 통신이 필요할 때 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### RT_TGSIGQUEUEINFO

> 특정 스레드에 실시간 신호와 데이터를 전송합니다.

**LIBRARY**:
- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:
- `pid_t tgid`: 프로세스 그룹 ID (PID)
- `pid_t tid`: 스레드 ID
- `int sig`: 신호 번호
- `siginfo_t *uinfo`: 추가 신호 정보를 담은 구조체 포인터

**Use Case**:
- 멀티스레드 프로세스 내에서 특정 스레드에 데이터와 함께 신호를 보낼 때 사용
- 정확한 스레드 통신이 필요한 경우 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### RT_SIGPROCMASK

> 프로세스의 신호 마스크를 변경하거나 조회합니다.

**LIBRARY**:
- `#include <signal.h>`

**Arguments**:
- `int how`: 신호 마스크를 변경하는 방법 (`SIG_BLOCK`, `SIG_UNBLOCK`, `SIG_SETMASK`)
- `const sigset_t *set`: 새로운 신호 집합
- `sigset_t *oldset`: 이전 신호 집합을 저장할 포인터
- `size_t sigsetsize`: `sigset_t`의 크기

**Use Case**:
- 프로세스가 수신할 신호를 제어하기 위해 사용
- 중요한 코드 섹션에서 특정 신호를 블록하거나 해제할 때 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### RT_SIGPENDING

> 프로세스에 대기 중인 신호를 조회합니다.

**LIBRARY**:
- `#include <signal.h>`

**Arguments**:
- `sigset_t *set`: 대기 중인 신호 집합을 저장할 포인터
- `size_t sigsetsize`: `sigset_t`의 크기

**Use Case**:
- 프로세스가 아직 처리하지 않은 신호를 확인할 때 사용
- 신호 처리 로직을 디버깅하거나 모니터링할 때 활용

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### RT_SIGTIMEDWAIT

> 지정한 신호 집합 중 하나를 대기하고, 타임아웃을 설정합니다.

**LIBRARY**:
- `#include <signal.h>`

**Arguments**:
- `const sigset_t *set`: 대기할 신호 집합
- `siginfo_t *info`: 수신한 신호 정보를 저장할 구조체 포인터
- `const struct timespec *timeout`: 최대 대기 시간
- `size_t sigsetsize`: `sigset_t`의 크기

**Use Case**:
- 특정 신호를 일정 시간 동안 대기하고자 할 때 사용
- 동기식 신호 처리가 필요한 상황에서 활용

**Return Value**:
- **성공 시**: 수신한 신호 번호를 반환
- **실패 시**: -1을 반환하고 `errno` 설정

---

### RT_SIGSUSPEND

> 신호 마스크를 변경하고 신호를 대기합니다.

**LIBRARY**:
- `#include <signal.h>`

**Arguments**:
- `const sigset_t *mask`: 새로운 신호 마스크
- `size_t sigsetsize`: `sigset_t`의 크기

**Use Case**:
- 프로세스를 특정 신호를 받을 때까지 중단시키고자 할 때 사용
- 원자적으로 신호 마스크를 변경하여 데드락을 방지

**Return Value**:
- 항상 -1을 반환하며, `errno`는 `EINTR`로 설정

---

### SIGALTSTACK

> 대체 신호 스택을 설정하거나 조회합니다.

**LIBRARY**:
- `#include <signal.h>`

**Arguments**:
- `const stack_t *ss`: 새로운 신호 스택 설정
- `stack_t *old_ss`: 이전 신호 스택 정보를 저장할 포인터

**Use Case**:
- 신호 처리 시 스택 오버플로우를 방지하기 위해 별도의 스택을 사용할 때 사용
- 신호 핸들러 내에서 안전한 스택 공간 확보

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

