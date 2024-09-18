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

## 3. 프로세스 상태 및 속성 변경 관련 시스템 호출

### SETUID

> 프로세스의 실제 사용자 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `uid_t uid`: 설정할 사용자 ID

**Use Case**:

- 프로세스가 자신의 실제 사용자 ID를 변경하여 권한을 낮출 때 사용
- SUID 프로그램이 권한을 조정할 때 활용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETGID

> 프로세스의 실제 그룹 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `gid_t gid`: 설정할 그룹 ID

**Use Case**:

- 프로세스가 자신의 실제 그룹 ID를 변경하여 그룹 권한을 조정할 때 사용
- SGID 프로그램이 권한을 조정할 때 활용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETEUID

> 프로세스의 유효 사용자 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `uid_t euid`: 설정할 유효 사용자 ID

**Use Case**:

- 프로세스가 유효 사용자 ID를 변경하여 임시로 권한을 상승 또는 하향할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETEGID

> 프로세스의 유효 그룹 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `gid_t egid`: 설정할 유효 그룹 ID

**Use Case**:

- 프로세스가 유효 그룹 ID를 변경하여 그룹 권한을 조정할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETREUID

> 프로세스의 실제 및 유효 사용자 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `uid_t ruid`: 실제 사용자 ID
- `uid_t euid`: 유효 사용자 ID

**Use Case**:

- 실제 및 유효 사용자 ID를 동시에 변경하여 권한을 조정할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETREGID

> 프로세스의 실제 및 유효 그룹 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `gid_t rgid`: 실제 그룹 ID
- `gid_t egid`: 유효 그룹 ID

**Use Case**:

- 실제 및 유효 그룹 ID를 동시에 변경하여 그룹 권한을 조정할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETRESUID

> 프로세스의 실제, 유효 및 저장된 사용자 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `uid_t ruid`: 실제 사용자 ID
- `uid_t euid`: 유효 사용자 ID
- `uid_t suid`: 저장된 사용자 ID

**Use Case**:

- 세 가지 사용자 ID를 모두 제어하여 복잡한 권한 변경이 필요할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETRESGID

> 프로세스의 실제, 유효 및 저장된 그룹 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `gid_t rgid`: 실제 그룹 ID
- `gid_t egid`: 유효 그룹 ID
- `gid_t sgid`: 저장된 그룹 ID

**Use Case**:

- 세 가지 그룹 ID를 모두 제어하여 복잡한 그룹 권한 변경이 필요할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETFSUID

> 파일 시스템 접근에 사용되는 사용자 ID를 설정합니다.

**LIBRARY**:

- `#include <sys/fsuid.h>`

**Arguments**:

- `uid_t fsuid`: 파일 시스템 사용자 ID

**Use Case**:

- 파일 시스템 접근 권한을 조정하여 특정 파일 작업에 대한 권한을 변경할 때 사용

**Return Value**:

- 이전 `fsuid` 반환

---

### SETFSGID

> 파일 시스템 접근에 사용되는 그룹 ID를 설정합니다.

**LIBRARY**:

- `#include <sys/fsuid.h>`

**Arguments**:

- `gid_t fsgid`: 파일 시스템 그룹 ID

**Use Case**:

- 파일 시스템 접근 시 그룹 권한을 조정할 때 사용

**Return Value**:

- 이전 `fsgid` 반환

---

### CAPSET

> 프로세스의 권한(캡처빌리티)을 설정합니다.

**LIBRARY**:

- `#include <sys/capability.h>`

**Arguments**:

- `cap_user_header_t header`: 캡처빌리티 헤더 정보
- `const cap_user_data_t data`: 캡처빌리티 데이터

**Use Case**:

- 프로세스의 권한을 세밀하게 제어하기 위해 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PERSONALITY

> 프로세스의 실행 환경(퍼스낼리티)을 설정합니다.

**LIBRARY**:

- `#include <sys/personality.h>`

**Arguments**:

- `unsigned long persona`: 설정할 퍼스낼리티 옵션

**Use Case**:

- 이식성이나 호환성을 위해 프로세스의 동작 방식을 변경할 때 사용

**Return Value**:

- 이전 퍼스낼리티 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PRCTL

> 프로세스의 특성 또는 동작을 제어합니다.

**LIBRARY**:

- `#include <sys/prctl.h>`

**Arguments**:

- `int option`: 제어 옵션
- 추가 인자들: 옵션에 따라 다름

**Use Case**:

- 프로세스의 동작을 세밀하게 제어하거나 보안 설정을 변경할 때 사용

**Return Value**:

- 성공 시 `0` 또는 양수 반환 (옵션에 따라 다름)
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SECCOMP

> 프로세스에 대한 시스템 호출 필터를 설정합니다.

**LIBRARY**:

- `#include <linux/seccomp.h>`

**Arguments**:

- `unsigned int operation`: 작업 유형 (예: `SECCOMP_SET_MODE_FILTER`)
- `unsigned int flags`: 플래그
- `const void *args`: 추가 인자

**Use Case**:

- 프로세스의 시스템 호출을 제한하여 보안성을 높일 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETNS

> 프로세스를 다른 네임스페이스로 전환합니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `int fd`: 네임스페이스를 나타내는 파일 디스크립터
- `int nstype`: 네임스페이스 유형 (예: `CLONE_NEWNET`)

**Use Case**:

- 프로세스를 다른 네임스페이스에 연결하여 격리하거나 자원을 공유할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### UNSHARE

> 프로세스가 자원이나 네임스페이스를 공유하지 않도록 설정합니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `int flags`: 공유 해제할 자원의 플래그 (예: `CLONE_FS`)

**Use Case**:

- 프로세스의 자원 격리를 강화하기 위해 공유된 자원을 분리할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_SETAFFINITY

> 프로세스가 실행될 수 있는 CPU 집합을 설정합니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `size_t cpusetsize`: CPU 집합의 크기
- `const cpu_set_t *mask`: 설정할 CPU 집합

**Use Case**:

- 프로세스의 CPU 친화도를 지정하여 성능 최적화를 할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_SETPARAM

> 프로세스의 스케줄링 우선순위를 설정합니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `const struct sched_param *param`: 설정할 우선순위 정보

**Use Case**:

- 실시간 프로세스의 우선순위를 조정할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_SETSCHEDULER

> 프로세스의 스케줄링 정책과 우선순위를 설정합니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `int policy`: 스케줄링 정책 (예: `SCHED_FIFO`)
- `const struct sched_param *param`: 우선순위 정보

**Use Case**:

- 프로세스의 스케줄링 동작을 제어하여 실시간 성능을 조정할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_SETATTR

> 프로세스의 스케줄링 속성을 설정합니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `struct sched_attr *attr`: 설정할 스케줄링 속성 구조체
- `unsigned int flags`: 플래그

**Use Case**:

- 확장된 스케줄링 속성을 설정하여 프로세스의 실행 동작을 제어할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETPGID

> 프로세스의 프로세스 그룹 ID를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `pid_t pgid`: 설정할 프로세스 그룹 ID

**Use Case**:

- 프로세스를 특정 프로세스 그룹에 추가하거나 그룹을 변경할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SETSID

> 새로운 세션을 시작하고 프로세스를 세션 리더로 만듭니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 데몬 프로세스를 생성할 때 터미널과의 연결을 끊고 새로운 세션을 시작하기 위해 사용

**Return Value**:

- 성공 시 새로운 세션 ID 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

## 4. 프로세스 정보 조회 관련 시스템 호출

### GETPID

> 현재 프로세스의 프로세스 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 프로세스가 자신의 PID를 알아야 할 때 사용

**Return Value**:

- 현재 프로세스의 PID 반환

---

### GETPPID

> 부모 프로세스의 프로세스 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 부모 프로세스와의 통신이나 관계 설정에 사용

**Return Value**:

- 부모 프로세스의 PID 반환

---

### GETTID

> 호출한 스레드의 스레드 ID를 반환합니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- 없음

**Use Case**:

- 스레드가 자신의 TID를 알아야 할 때 사용

**Return Value**:

- 현재 스레드의 TID 반환

---

### GETUID

> 프로세스의 실제 사용자 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 프로세스의 권한 확인이나 로깅에 사용

**Return Value**:

- 실제 사용자 ID 반환

---

### GETEUID

> 프로세스의 유효 사용자 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 유효 권한 수준을 확인할 때 사용

**Return Value**:

- 유효 사용자 ID 반환

---

### GETGID

> 프로세스의 실제 그룹 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 그룹 권한 확인이나 로깅에 사용

**Return Value**:

- 실제 그룹 ID 반환

---

### GETEGID

> 프로세스의 유효 그룹 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- 없음

**Use Case**:

- 유효 그룹 권한 수준을 확인할 때 사용

**Return Value**:

- 유효 그룹 ID 반환

---

### GETRESUID

> 프로세스의 실제, 유효 및 저장된 사용자 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `uid_t *ruid`: 실제 UID를 저장할 포인터
- `uid_t *euid`: 유효 UID를 저장할 포인터
- `uid_t *suid`: 저장된 UID를 저장할 포인터

**Use Case**:

- 세 가지 사용자 ID를 모두 확인해야 할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### GETRESGID

> 프로세스의 실제, 유효 및 저장된 그룹 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `gid_t *rgid`: 실제 GID를 저장할 포인터
- `gid_t *egid`: 유효 GID를 저장할 포인터
- `gid_t *sgid`: 저장된 GID를 저장할 포인터

**Use Case**:

- 세 가지 그룹 ID를 모두 확인해야 할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### GETPGID

> 프로세스의 프로세스 그룹 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID

**Use Case**:

- 프로세스 그룹 정보를 확인할 때 사용

**Return Value**:

- 성공 시 `PGID` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### GETSID

> 프로세스의 세션 ID를 반환합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID

**Use Case**:

- 세션 정보를 확인할 때 사용

**Return Value**:

- 성공 시 세션 ID 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### GETPRIORITY

> 프로세스, 프로세스 그룹 또는 사용자의 스케줄링 우선순위를 반환합니다.

**LIBRARY**:

- `#include <sys/resource.h>`

**Arguments**:

- `int which`: 식별자 유형 (`PRIO_PROCESS` 등)
- `id_t who`: 대상의 식별자

**Use Case**:

- 현재 스케줄링 우선순위를 확인할 때 사용

**Return Value**:

- 성공 시 우선순위 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_GETAFFINITY

> 프로세스의 CPU 친화도 마스크를 가져옵니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `size_t cpusetsize`: CPU 집합의 크기
- `cpu_set_t *mask`: CPU 집합을 저장할 포인터

**Use Case**:

- 프로세스가 실행될 수 있는 CPU를 확인할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_GETPARAM

> 프로세스의 스케줄링 우선순위를 가져옵니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `struct sched_param *param`: 우선순위를 저장할 구조체

**Use Case**:

- 프로세스의 현재 우선순위를 확인할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_GETSCHEDULER

> 프로세스의 스케줄링 정책을 가져옵니다.

**LIBRARY**:

- `#include <sched.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID

**Use Case**:

- 프로세스의 스케줄링 정책(SCHED_OTHER, SCHED_FIFO 등)을 확인할 때 사용

**Return Value**:

- 성공 시 스케줄링 정책 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### SCHED_GETATTR

> 프로세스의 스케줄링 속성을 가져옵니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `struct sched_attr *attr`: 속성을 저장할 구조체
- `unsigned int size`: 구조체의 크기
- `unsigned int flags`: 플래그

**Use Case**:

- 확장된 스케줄링 정보를 확인할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PRLIMIT64

> 프로세스의 자원 제한을 가져오거나 설정합니다.

**LIBRARY**:

- `#include <sys/resource.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `int resource`: 자원 유형 (예: `RLIMIT_CPU`)
- `const struct rlimit64 *new_limit`: 새로운 제한값
- `struct rlimit64 *old_limit`: 이전 제한값을 저장할 포인터

**Use Case**:

- 프로세스의 자원 사용 제한을 확인하거나 변경할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

## 5. 프로세스 메모리 및 자원 관리 관련 시스템 호출

### MMAP

> 프로세스의 가상 메모리 공간에 파일이나 디바이스를 매핑합니다.

**LIBRARY**:

- `#include <sys/mman.h>`

**Arguments**:

- `void *addr`: 매핑할 시작 주소 (NULL일 경우 시스템이 결정)
- `size_t length`: 매핑할 메모리 영역의 크기
- `int prot`: 메모리 보호 프로토콜 (`PROT_READ`, `PROT_WRITE` 등)
- `int flags`: 매핑 옵션 (`MAP_SHARED`, `MAP_PRIVATE` 등)
- `int fd`: 매핑할 파일의 파일 디스크립터
- `off_t offset`: 파일 내에서 매핑을 시작할 오프셋

**Use Case**:

- 파일의 내용을 메모리에 매핑하여 효율적으로 입출력을 수행할 때 사용
- 공유 메모리 영역을 생성하거나 접근할 때 활용

**Return Value**:

- 성공 시 매핑된 메모리 영역의 시작 주소 반환
- 실패 시 `MAP_FAILED` 반환하고 `errno` 설정

---

### MUNMAP

> 이전에 매핑된 메모리 영역을 해제합니다.

**LIBRARY**:

- `#include <sys/mman.h>`

**Arguments**:

- `void *addr`: 해제할 메모리 영역의 시작 주소
- `size_t length`: 해제할 메모리 영역의 크기

**Use Case**:

- 사용이 끝난 메모리 매핑을 해제하여 메모리 리소스를 반환할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### BRK

> 프로세스의 데이터 영역 끝 주소를 설정합니다.

**LIBRARY**:

- `#include <unistd.h>`

**Arguments**:

- `void *addr`: 새로운 데이터 영역의 끝 주소

**Use Case**:

- 힙 영역의 크기를 조절하여 동적 메모리 할당을 관리할 때 사용
- 일반적으로 `sbrk`와 함께 사용되며, 직접 호출보다는 메모리 할당 라이브러리를 통해 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### MPROTECT

> 메모리 영역의 보호 속성을 변경합니다.

**LIBRARY**:

- `#include <sys/mman.h>`

**Arguments**:

- `void *addr`: 변경할 메모리 영역의 시작 주소
- `size_t len`: 변경할 메모리 영역의 크기
- `int prot`: 새로운 보호 속성 (`PROT_READ`, `PROT_WRITE`, `PROT_EXEC` 등)

**Use Case**:

- 메모리 영역의 접근 권한을 동적으로 변경할 때 사용
- 실행 권한을 설정하여 JIT 컴파일된 코드를 실행할 때 활용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### MADVISE

> 메모리 영역에 대한 힌트를 커널에 제공합니다.

**LIBRARY**:

- `#include <sys/mman.h>`

**Arguments**:

- `void *addr`: 대상 메모리 영역의 시작 주소
- `size_t length`: 메모리 영역의 크기
- `int advice`: 조언 플래그 (`MADV_NORMAL`, `MADV_SEQUENTIAL`, `MADV_RANDOM` 등)

**Use Case**:

- 메모리 사용 패턴을 커널에 알려 성능을 최적화할 때 사용
- 캐시 관리나 프리페칭 등 메모리 관리 효율성을 높이기 위해 활용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PROCESS_VM_READV

> 다른 프로세스의 메모리에서 데이터를 읽어옵니다.

**LIBRARY**:

- `#include <sys/uio.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `const struct iovec *local_iov`: 로컬 메모리 버퍼 배열
- `unsigned long liovcnt`: 로컬 버퍼 배열의 개수
- `const struct iovec *remote_iov`: 원격 프로세스의 메모리 버퍼 배열
- `unsigned long riovcnt`: 원격 버퍼 배열의 개수
- `unsigned long flags`: 플래그 (현재 0으로 설정)

**Use Case**:

- 프로세스 간 통신에서 직접 메모리 접근이 필요할 때 사용
- 디버깅이나 메모리 검사 도구에서 활용

**Return Value**:

- 성공 시 전송된 바이트 수 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PROCESS_VM_WRITEV

> 다른 프로세스의 메모리에 데이터를 씁니다.

**LIBRARY**:

- `#include <sys/uio.h>`

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `const struct iovec *local_iov`: 로컬 메모리 버퍼 배열
- `unsigned long liovcnt`: 로컬 버퍼 배열의 개수
- `const struct iovec *remote_iov`: 원격 프로세스의 메모리 버퍼 배열
- `unsigned long riovcnt`: 원격 버퍼 배열의 개수
- `unsigned long flags`: 플래그 (현재 0으로 설정)

**Use Case**:

- 프로세스 간 통신에서 직접 메모리에 데이터를 쓸 때 사용
- 디버깅이나 메모리 조작 도구에서 활용

**Return Value**:

- 성공 시 전송된 바이트 수 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### MEMBARRIER

> 프로세스 내 또는 전체 시스템에 메모리 배리어를 설정합니다.

**LIBRARY**:

- `#include <linux/membarrier.h>`

**Arguments**:

- `int cmd`: 명령어 (`MEMBARRIER_CMD_SHARED`, `MEMBARRIER_CMD_PRIVATE` 등)
- `int flags`: 플래그 (현재 0으로 설정)

**Use Case**:

- 멀티스레드 프로그래밍에서 메모리 동기화를 보장할 때 사용
- 메모리 순서 보장을 통해 데이터 일관성 유지

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

## 6. 기타 프로세스 관련 시스템 호출

### PTRACE

> 다른 프로세스의 실행을 제어하고 검사합니다.

**LIBRARY**:

- `#include <sys/ptrace.h>`

**Arguments**:

- `enum __ptrace_request request`: 요청 유형 (`PTRACE_ATTACH`, `PTRACE_DETACH` 등)
- `pid_t pid`: 대상 프로세스의 PID
- `void *addr`: 주소 또는 기타 데이터 (요청에 따라 다름)
- `void *data`: 추가 데이터

**Use Case**:

- 디버깅을 위해 프로세스의 실행을 제어할 때 사용
- 시스템 호출 추적이나 메모리 검사에 활용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PIDFD_OPEN

> 프로세스 ID에 대한 파일 디스크립터를 생성합니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `pid_t pid`: 대상 프로세스의 PID
- `unsigned int flags`: 플래그 (현재 0 또는 `PIDFD_NONBLOCK`)

**Use Case**:

- 프로세스를 식별하기 위한 안전한 파일 디스크립터를 얻을 때 사용
- PID 재사용 문제를 방지하고자 할 때 활용

**Return Value**:

- 성공 시 파일 디스크립터 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PIDFD_GETFD

> 다른 프로세스의 열린 파일 디스크립터를 복사합니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `int pidfd`: 대상 프로세스의 PID 파일 디스크립터
- `int target_fd`: 복사할 대상의 파일 디스크립터 번호
- `unsigned int flags`: 플래그 (현재 0)

**Use Case**:

- 다른 프로세스의 열린 파일을 현재 프로세스로 가져올 때 사용
- 디버깅이나 파일 디스크립터 전달에 활용

**Return Value**:

- 성공 시 새로운 파일 디스크립터 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PIDFD_SEND_SIGNAL

> 프로세스 파일 디스크립터를 사용하여 신호를 보냅니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `int pidfd`: 대상 프로세스의 PID 파일 디스크립터
- `int sig`: 보낼 신호 번호
- `siginfo_t *info`: 추가 신호 정보
- `unsigned int flags`: 플래그 (현재 0)

**Use Case**:

- PID 재사용 없이 안전하게 신호를 보낼 때 사용
- 프로세스 제어 및 관리에서 활용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### PROCESS_MADVISE

> 다른 프로세스의 메모리 영역에 대해 조언을 제공합니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `int pidfd`: 대상 프로세스의 PID 파일 디스크립터
- `const struct iovec *iovec`: 메모리 영역 배열
- `size_t vlen`: 배열의 길이
- `int advice`: 조언 플래그 (`MADV_DONTNEED` 등)
- `unsigned int flags`: 플래그 (현재 0)

**Use Case**:

- 다른 프로세스의 메모리 사용을 최적화하거나 관리할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수 반환하고 `errno` 설정

---

### PROCESS_MRELEASE

> 프로세스의 메모리 리소스를 해제합니다.

**LIBRARY**:

- 직접 시스템 호출 인터페이스 (`syscall`)

**Arguments**:

- `int pidfd`: 대상 프로세스의 PID 파일 디스크립터

**Use Case**:

- 프로세스가 종료되기 전에 메모리 리소스를 회수할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수 반환하고 `errno` 설정

---

### LANDLOCK_CREATE_RULESET

> Landlock 보안 모듈을 위한 규칙 세트를 생성합니다.

**LIBRARY**:

- `#include <linux/landlock.h>`

**Arguments**:

- `struct landlock_ruleset_attr *attr`: 규칙 세트 속성 구조체
- `size_t size`: 구조체의 크기
- `__u32 flags`: 플래그 (현재 0)

**Use Case**:

- 프로세스의 파일 시스템 접근을 제한하는 보안 정책을 설정할 때 사용

**Return Value**:

- 성공 시 파일 디스크립터 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### LANDLOCK_ADD_RULE

> 규칙 세트에 새로운 규칙을 추가합니다.

**LIBRARY**:

- `#include <linux/landlock.h>`

**Arguments**:

- `int ruleset_fd`: 규칙 세트의 파일 디스크립터
- `enum landlock_rule_type rule_type`: 규칙 유형
- `void *rule_attr`: 규칙 속성
- `__u32 flags`: 플래그 (현재 0)

**Use Case**:

- 보안 정책에 세부 규칙을 추가하여 접근 제어를 세밀하게 설정할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정

---

### LANDLOCK_RESTRICT_SELF

> 현재 프로세스에 Landlock 규칙 세트를 적용합니다.

**LIBRARY**:

- `#include <linux/landlock.h>`

**Arguments**:

- `int ruleset_fd`: 적용할 규칙 세트의 파일 디스크립터
- `__u32 flags`: 플래그 (현재 0)

**Use Case**:

- 프로세스 스스로의 파일 시스템 접근 권한을 제한하여 보안성을 높일 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 `-1` 반환하고 `errno` 설정