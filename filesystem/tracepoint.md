# Filesystem-Related Tracepoint in Linux

## Table of Content
1. [File operations (파일 작업)](#file-operations)
2. [Directory operations (디렉토리 작업)](#directory-operations)
3. [Link operations (링크 작업)](#link-operations)
4. [Basic file attributes (기본 파일 속성)](#basic-file-attributes)
5. [Extended file attributes (확장 파일 속성)](#extended-file-attributes)
6. [File descriptor manipulations (파일 디스크립터 조작)](#file-descriptor-manipulations)
7. [Read/Write (읽기/쓰기)](#readwrite)
8. [Synchronized I/O (동기화된 I/O)](#synchronized-io)
9. [Asynchronous I/O (비동기 I/O)](#asynchronous-io)
10. [Multiplexed I/O (다중화된 I/O)](#multiplexed-io)
11. [Monitoring file events (파일 이벤트 모니터링)](#monitoring-file-events)
12. [Miscellaneous (기타)](#miscellaneous)

## File operations

### Close
> 파일 디스크립터를 닫습니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 닫을 파일 디스크립터.

**Use Case**
- 파일을 다 사용한 후, 리소스 해제를 위해 파일 디스크립터를 닫는 데 사용됩니다. 특히 파일을 읽기 또는 쓰기 작업이 완료된 후 시스템 리소스를 절약하기 위해 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/close.2.html

---

### Creat
> 파일을 열거나 존재하지 않을 시 생성합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `const char *pathname`: 열거나 생성할 파일 경로.
- `mode_t mode`: 새로 생성될 파일의 접근 권한을 설정.

**Use Case**
- 파일이 존재하지 않을 때, 새 파일을 생성하며, 로그 파일이나 데이터를 저장할 파일을 동적으로 생성하는 경우에 주로 사용됩니다.

**Return Value**
- 성공 시: 새 파일에 대한 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/creat.2.html

---

### Open
> 파일을 열거나, 필요 시 생성합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `const char *pathname`: 열 파일의 경로.
- `int flags`: 파일 열기 플래그 (예: `O_RDONLY`, `O_WRONLY`, `O_RDWR`).
- `mode_t mode` (옵션): 파일을 새로 생성할 때 권한을 설정하는 값.

**Use Case**
- 파일을 읽거나 쓰기 위해 열 때 사용되며, 존재하지 않는 경우에는 선택적으로 파일을 생성할 수 있습니다.

**Return Value**
- 성공 시: 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/open.2.html

---

### Openat
> 디렉터리 파일 디스크립터를 기준으로 파일을 열거나 생성합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 열거나 생성할 파일 경로.
- `int flags`: 파일 열기 플래그.
- `mode_t mode` (옵션): 파일을 새로 생성할 때 권한을 설정하는 값.

**Use Case**
- 특정 디렉터리를 기준으로 파일을 열거나 생성할 때 사용되며, 보안이 중요한 환경에서 안전한 파일 접근을 보장할 때 유용합니다.

**Return Value**
- 성공 시: 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/openat.2.html

---

### Name_to_handle_at
> 경로명을 기반으로 파일 핸들을 얻습니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 핸들을 얻고자 하는 파일의 경로.
- `struct file_handle *handle`: 파일 핸들을 저장할 구조체.
- `int *mount_id`: 마운트 ID를 저장할 포인터.
- `int flags`: 추가 동작을 정의하는 플래그.

**Use Case**
- 핸들을 사용해 파일 시스템 내에서 파일을 고유하게 식별할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/name_to_handle_at.2.html

---

### Open_by_handle_at
> 핸들을 사용해 파일을 엽니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int mount_fd`: 마운트 포인트의 파일 디스크립터.
- `struct file_handle *handle`: 열 파일에 대한 핸들.
- `int flags`: 파일 열기 플래그.

**Use Case**
- 기존에 얻은 파일 핸들을 통해 파일을 열 때 사용됩니다. 주로 핸들 기반 파일 접근이 필요한 시스템에서 사용됩니다.

**Return Value**
- 성공 시: 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/open_by_handle_at.2.html

---

### Memfd_create
> 익명 파일을 생성합니다.

**LIBRARY**
- `#include <sys/mman.h>`

**Arguments**
- `const char *name`: 익명 파일의 이름.
- `unsigned int flags`: 익명 파일 생성 플래그 (예: `MFD_CLOEXEC`).

**Use Case**
- 메모리 내에서 익명 파일을 생성하여, 파일 시스템에 저장하지 않고 프로세스 간 메모리 공유나 IPC(Inter-Process Communication)에 사용됩니다.

**Return Value**
- 성공 시: 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/memfd_create.2.html

---

### Mknod
> 특수 파일 또는 일반 파일을 생성합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `const char *pathname`: 생성할 파일의 경로.
- `mode_t mode`: 파일 유형 및 접근 권한을 지정합니다 (예: `S_IFREG`, `S_IFCHR`).
- `dev_t dev`: 특수 장치 파일일 경우, 장치 번호를 설정합니다.

**Use Case**
- 장치 파일, 파이프 등 특수 파일을 생성할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/mknod.2.html

---

### Mknodat
> 디렉터리 파일 디스크립터를 기준으로 특수 파일 또는 일반 파일을 생성합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 생성할 파일의 경로.
- `mode_t mode`: 파일 유형 및 접근 권한 (예: `S_IFREG`, `S_IFCHR`).
- `dev_t dev`: 장치 파일일 경우 장치 번호를 설정합니다.

**Use Case**
- 특정 디렉터리를 기준으로 특수 파일 또는 일반 파일을 생성할 때 사용됩니다. 보안이 중요한 환경에서 주로 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/mknodat.2.html

---

### Rename
> 파일의 이름을 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *oldpath`: 기존 파일 경로.
- `const char *newpath`: 새 파일 경로.

**Use Case**
- 파일이나 디렉터리의 이름을 변경하거나, 파일을 다른 디렉터리로 이동할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/rename.2.html

---

### Renameat
> 디렉터리 파일 디스크립터를 기준으로 파일 이름을 변경합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int olddirfd`: 기존 파일이 위치한 디렉터리의 파일 디스크립터
- `const char *oldpath`: 기존 파일 경로
- `int newdirfd`: 새 파일이 위치할 디렉터리의 파일 디스크립터
- `const char *newpath`: 새 파일 경로

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 파일이나 디렉터리의 이름을 변경할 때 사용됩니다. 보안이 중요한 상황에서 사용되기도 합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/renameat.2.html

---

### Renameat2
> 디렉터리 파일 디스크립터를 기준으로 파일 이름을 플래그와 함께 변경합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int olddirfd`: 기존 파일이 위치한 디렉터리의 파일 디스크립터.
- `const char *oldpath`: 기존 파일 경로.
- `int newdirfd`: 새 파일이 위치할 디렉터리의 파일 디스크립터.
- `const char *newpath`: 새 파일 경로.
- `unsigned int flags`: 이름 변경 동작을 제어하는 플래그 (예: `RENAME_NOREPLACE`, `RENAME_EXCHANGE`).

**Use Case**
- 파일을 이동하거나 이름을 변경할 때, 추가적인 제어가 필요할 때 사용됩니다. 예를 들어, 이름 충돌 방지나 파일 교환에 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/renameat2.2.html

---

### Truncate
> 파일을 지정된 길이로 잘라냅니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *path`: 길이를 조정할 파일의 경로.
- `off_t length`: 파일을 잘라낼 길이.

**Use Case**
- 파일의 크기를 줄이거나 확장할 때 사용됩니다. 주로 로그 파일과 같은 큰 파일의 크기를 관리할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/truncate.2.html

---

### Ftruncate
> 열린 파일을 지정된 길이로 잘라냅니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 열린 파일의 파일 디스크립터.
- `off_t length`: 파일을 잘라낼 길이.

**Use Case**
- 열린 파일의 크기를 줄이거나 확장할 때 사용됩니다. 특히 파일이 이미 열려 있는 상태에서 크기를 조정할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/ftruncate.2.html

---

### Fallocate
> 파일 공간을 조작합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int fd`: 파일 디스크립터.
- `int mode`: 파일 공간 할당 모드 플래그 (예: `FALLOC_FL_KEEP_SIZE`).
- `off_t offset`: 할당할 시작 위치.
- `off_t len`: 할당할 파일 크기.

**Use Case**
- 파일에 필요한 공간을 미리 할당하여 디스크 공간을 예약하거나 파일의 크기를 조정하는 데 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fallocate.2.html

## Directory operations

---

### Mkdir
> 디렉터리를 생성합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `const char *pathname`: 생성할 디렉터리의 경로.
- `mode_t mode`: 새로 생성할 디렉터리의 접근 권한을 설정합니다.

**Use Case**
- 새로운 디렉터리를 생성할 때 사용됩니다. 프로젝트 구조를 만들거나 파일을 저장할 디렉터리를 생성할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/mkdir.2.html

---

### Mkdirat
> 디렉터리 파일 디스크립터를 기준으로 디렉터리를 생성합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 생성할 디렉터리의 경로.
- `mode_t mode`: 새로 생성할 디렉터리의 접근 권한을 설정합니다.

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 디렉터리를 생성할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/mkdirat.2.html

---

### Rmdir
> 디렉터리를 삭제합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *pathname`: 삭제할 디렉터리의 경로.

**Use Case**
- 빈 디렉터리를 삭제할 때 사용됩니다. 디렉터리가 비어 있어야만 삭제할 수 있으며, 비어 있지 않으면 실패합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/rmdir.2.html

---

### Getcwd
> 현재 작업 중인 디렉터리의 경로를 반환합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `char *buf`: 현재 작업 중인 디렉터리 경로를 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 프로그램의 현재 작업 디렉터리의 절대 경로를 얻을 때 사용됩니다. 디렉터리 변경 전후에 경로를 확인할 때 유용합니다.

**Return Value**
- 성공 시: 경로 문자열을 반환
- 실패 시: `NULL` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/getcwd.2.html

---

### Chdir
> 현재 작업 중인 디렉터리를 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *path`: 새로 설정할 디렉터리의 경로.

**Use Case**
- 프로세스가 작업 중인 디렉터리를 변경할 때 사용됩니다. 특정 디렉터리 내에서 작업을 수행하기 전에 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/chdir.2.html

---

### Fchdir
> 파일 디스크립터를 사용해 현재 작업 중인 디렉터리를 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 새로 설정할 디렉터리의 파일 디스크립터.

**Use Case**
- 열린 디렉터리의 파일 디스크립터를 통해 작업 중인 디렉터리를 변경할 때 사용됩니다. 디렉터리를 미리 열어 작업할 경우 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fchdir.2.html

---

### Chroot
> 루트 디렉터리를 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *path`: 새로 설정할 루트 디렉터리 경로.

**Use Case**
- 보안상 특정 프로세스의 루트 디렉터리를 격리하기 위해 사용됩니다. 주로 컨테이너 및 샌드박스 환경에서 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/chroot.2.html

---

### Getdents
> 디렉터리의 엔트리를 가져옵니다.

**LIBRARY**
- `#include <dirent.h>`

**Arguments**
- `unsigned int fd`: 디렉터리의 파일 디스크립터.
- `struct dirent *dirp`: 디렉터리 엔트리 정보를 저장할 버퍼.
- `unsigned int count`: 읽을 바이트 수.

**Use Case**
- 디렉터리의 엔트리를 읽어 파일 목록을 가져올 때 사용됩니다. 파일 시스템을 탐색하거나 디렉터리 내부를 확인할 때 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/getdents.2.html

---

### Getdents64
> 64비트 시스템에서 디렉터리의 엔트리를 가져옵니다.

**LIBRARY**
- `#include <dirent.h>`

**Arguments**
- `unsigned int fd`: 디렉터리의 파일 디스크립터.
- `struct dirent64 *dirp`: 디렉터리 엔트리 정보를 저장할 버퍼.
- `unsigned int count`: 읽을 바이트 수.

**Use Case**
- 64비트 시스템에서 디렉터리 엔트리를 읽어 파일 목록을 가져오는 데 사용됩니다. 대용량 파일 시스템을 탐색할 때 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/getdents64.2.html

---

### Lookup_dcookie
> 디렉터리 엔트리의 경로를 반환합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `u64 cookie`: 특정 디렉터리 엔트리를 식별하는 쿠키 값.
- `char *buffer`: 반환된 경로를 저장할 버퍼.
- `size_t len`: 버퍼의 크기.

**Use Case**
- 디렉터리 엔트리의 쿠키 값을 통해 해당 경로 정보를 얻는 데 사용됩니다. 주로 디버깅 및 파일 시스템 관련 작업에서 사용됩니다.

**Return Value**
- 성공 시: 경로 문자열의 길이 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lookup_dcookie.2.html

## Link operations

---

### Link
> 파일에 하드 링크를 만듭니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *oldpath`: 하드 링크를 생성할 기존 파일의 경로.
- `const char *newpath`: 새로 생성할 하드 링크의 경로.

**Use Case**
- 동일한 파일을 참조하는 여러 파일 이름을 만들 때 사용됩니다. 이는 파일 데이터는 동일하게 유지하면서 새로운 이름으로 참조할 수 있게 합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/link.2.html

---

### Linkat
> 디렉터리 파일 디스크립터를 기준으로 파일에 하드 링크를 만듭니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int olddirfd`: 기존 파일이 위치한 디렉터리의 파일 디스크립터.
- `const char *oldpath`: 하드 링크를 생성할 기존 파일의 경로.
- `int newdirfd`: 새로 생성될 하드 링크가 위치할 디렉터리의 파일 디스크립터.
- `const char *newpath`: 새로 생성할 하드 링크의 경로.
- `int flags`: 추가 동작을 정의하는 플래그 (옵션).

**Use Case**
- 특정 디렉터리를 기준으로 하드 링크를 만들 때 사용되며, 보안이 중요한 환경에서 안전한 파일 접근을 보장할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/linkat.2.html

---

### Symlink
> 파일에 심볼릭 링크를 만듭니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *target`: 심볼릭 링크가 참조할 대상 파일 경로.
- `const char *linkpath`: 생성할 심볼릭 링크의 경로.

**Use Case**
- 다른 파일을 참조하는 심볼릭 링크(소프트 링크)를 생성할 때 사용됩니다. 심볼릭 링크는 대상 파일과 독립적으로 존재하며, 파일의 경로가 변경되더라도 링크는 유지됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/symlink.2.html

---

### Symlinkat
> 디렉터리 파일 디스크립터를 기준으로 파일에 심볼릭 링크를 만듭니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *target`: 심볼릭 링크가 참조할 대상 파일 경로.
- `int newdirfd`: 심볼릭 링크가 생성될 디렉터리의 파일 디스크립터.
- `const char *linkpath`: 생성할 심볼릭 링크의 경로.

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 심볼릭 링크를 생성할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/symlinkat.2.html

---

### Unlink
> 파일 이름을 삭제하고, 필요 시 파일 자체도 삭제합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *pathname`: 삭제할 파일의 경로.

**Use Case**
- 파일 시스템에서 파일 이름을 제거할 때 사용됩니다. 파일이 더 이상 참조되지 않으면 해당 파일 데이터도 삭제됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/unlink.2.html

---

### Unlinkat
> 디렉터리 파일 디스크립터를 기준으로 파일 이름을 삭제하고, 필요 시 파일 자체도 삭제합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int dirfd`: 파일이 위치한 디렉터리의 파일 디스크립터.
- `const char *pathname`: 삭제할 파일의 경로.
- `int flags`: 파일 삭제 동작을 제어하는 플래그 (옵션).

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 파일 이름을 제거할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/unlinkat.2.html

---

### Readlink
> 심볼릭 링크의 값을 읽습니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *pathname`: 읽을 심볼릭 링크의 경로.
- `char *buf`: 심볼릭 링크의 값을 저장할 버퍼.
- `size_t bufsiz`: 버퍼의 크기.

**Use Case**
- 심볼릭 링크가 참조하는 파일 경로를 확인할 때 사용됩니다. 링크 자체가 가리키는 실제 파일의 경로를 얻을 수 있습니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/readlink.2.html

---

### Readlinkat
> 디렉터리 파일 디스크립터를 기준으로 심볼릭 링크의 값을 읽습니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int dirfd`: 링크가 위치한 디렉터리의 파일 디스크립터.
- `const char *pathname`: 읽을 심볼릭 링크의 경로.
- `char *buf`: 심볼릭 링크의 값을 저장할 버퍼.
- `size_t bufsiz`: 버퍼의 크기.

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 심볼릭 링크의 경로를 확인할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/readlinkat.2.html




## Basic file attributes

---

### Umask
> 파일 생성 시 기본 권한을 제한하는 마스크를 설정합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `mode_t mask`: 새로 생성되는 파일의 권한에서 제외할 비트 값을 설정.

**Use Case**
- 파일이나 디렉터리 생성 시 기본 권한을 제한하고 싶을 때 사용됩니다. 예를 들어, 파일을 생성할 때 그룹이나 다른 사용자가 접근하지 못하도록 설정할 수 있습니다.

**Return Value**
- 성공 시: 이전의 `umask` 값 반환
- 실패 시: 해당 없음

**manual page**
- http://man7.org/linux/man-pages/man2/umask.2.html

---

### Stat
> 파일의 메타데이터를 가져옵니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `const char *pathname`: 메타데이터를 조회할 파일의 경로.
- `struct stat *statbuf`: 파일의 메타데이터를 저장할 버퍼.

**Use Case**
- 파일의 크기, 권한, 생성 및 수정 시간 등의 정보를 얻을 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/stat.2.html

---

### Lstat
> 심볼릭 링크의 메타데이터를 가져옵니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `const char *pathname`: 심볼릭 링크의 경로.
- `struct stat *statbuf`: 심볼릭 링크의 메타데이터를 저장할 버퍼.

**Use Case**
- 심볼릭 링크의 정보를 조회하고자 할 때 사용됩니다. 일반 `stat`과 달리 링크 자체의 메타데이터를 반환합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lstat.2.html

---

### Fstat
> 열린 파일의 메타데이터를 가져옵니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int fd`: 메타데이터를 조회할 파일의 파일 디스크립터.
- `struct stat *statbuf`: 파일의 메타데이터를 저장할 버퍼.

**Use Case**
- 이미 열린 파일의 크기, 권한, 수정 시간 등을 조회할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fstat.2.html

---

### Fstatat
> 디렉터리 파일 디스크립터를 기준으로 파일의 메타데이터를 가져옵니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 메타데이터를 조회할 파일의 경로.
- `struct stat *statbuf`: 파일의 메타데이터를 저장할 버퍼.
- `int flags`: 플래그 (예: `AT_SYMLINK_NOFOLLOW`).

**Use Case**
- 보안이 중요한 환경에서 특정 디렉터리 파일 디스크립터를 기준으로 파일의 메타데이터를 조회할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fstatat.2.html

---

### Chmod
> 파일의 권한을 변경합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `const char *pathname`: 권한을 변경할 파일의 경로.
- `mode_t mode`: 새로 설정할 파일 권한.

**Use Case**
- 파일 또는 디렉터리의 읽기, 쓰기, 실행 권한을 변경할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/chmod.2.html

---

### Fchmod
> 열린 파일의 권한을 변경합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int fd`: 권한을 변경할 파일의 파일 디스크립터.
- `mode_t mode`: 새로 설정할 파일 권한.

**Use Case**
- 이미 열린 파일의 권한을 변경해야 할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fchmod.2.html

---

### Fchmodat
> 디렉터리 파일 디스크립터를 기준으로 파일의 권한을 변경합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 권한을 변경할 파일의 경로.
- `mode_t mode`: 새로 설정할 파일 권한.
- `int flags`: 플래그 (예: `AT_SYMLINK_NOFOLLOW`).

**Use Case**
- 보안이 중요한 환경에서 특정 디렉터리 파일 디스크립터를 기준으로 파일의 권한을 변경할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fchmodat.2.html

---

### Chown
> 파일의 소유자와 그룹을 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *pathname`: 소유자와 그룹을 변경할 파일의 경로.
- `uid_t owner`: 새로 설정할 파일 소유자 ID.
- `gid_t group`: 새로 설정할 파일 그룹 ID.

**Use Case**
- 파일 또는 디렉터리의 소유자와 그룹을 변경할 때 사용됩니다. 주로 파일 권한을 관리할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/chown.2.html

---

### Lchown
> 심볼릭 링크의 소유자와 그룹을 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *pathname`: 소유자와 그룹을 변경할 심볼릭 링크의 경로.
- `uid_t owner`: 새로 설정할 소유자 ID.
- `gid_t group`: 새로 설정할 그룹 ID.

**Use Case**
- 심볼릭 링크 자체의 소유자와 그룹을 변경할 때 사용됩니다. 심볼릭 링크가 참조하는 파일이 아닌, 링크 자체의 속성을 수정할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lchown.2.html

---

### Fchown
> 열린 파일의 소유자와 그룹을 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 소유자와 그룹을 변경할 파일의 파일 디스크립터.
- `uid_t owner`: 새로 설정할 소유자 ID.
- `gid_t group`: 새로 설정할 그룹 ID.

**Use Case**
- 열린 파일의 소유자와 그룹을 변경할 때 사용됩니다. 파일을 작업 중인 동안 소유자나 그룹을 변경할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fchown.2.html

---

### Fchownat
> 디렉터리 파일 디스크립터를 기준으로 파일의 소유자와 그룹을 변경합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 소유자와 그룹을 변경할 파일의 경로.
- `uid_t owner`: 새로 설정할 소유자 ID.
- `gid_t group`: 새로 설정할 그룹 ID.
- `int flags`: 심볼릭 링크를 처리할 방법 (예: `AT_SYMLINK_NOFOLLOW`).

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 파일의 소유자와 그룹을 변경할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fchownat.2.html

---

### Utime
> 파일의 마지막 접근 시간과 수정 시간을 변경합니다.

**LIBRARY**
- `#include <utime.h>`

**Arguments**
- `const char *filename`: 시간을 변경할 파일의 경로.
- `const struct utimbuf *times`: 접근 시간과 수정 시간을 저장하는 구조체.

**Use Case**
- 파일의 마지막 접근 시간과 수정 시간을 변경할 때 사용됩니다. 주로 파일 백업 및 동기화에서 파일 타임스탬프를 수정하는 경우에 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/utime.2.html

---

### Utimes
> 파일의 마지막 접근 시간과 수정 시간을 마이크로초 단위로 변경합니다.

**LIBRARY**
- `#include <sys/time.h>`

**Arguments**
- `const char *filename`: 시간을 변경할 파일의 경로.
- `const struct timeval times[2]`: 접근 시간과 수정 시간을 저장하는 배열.

**Use Case**
- 파일의 마지막 접근 시간과 수정 시간을 더 정밀한 마이크로초 단위로 변경할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/utimes.2.html

---

### Futimesat
> 디렉터리 파일 디스크립터를 기준으로 파일의 마지막 접근 시간과 수정 시간을 변경합니다.

**LIBRARY**
- `#include <sys/time.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 시간을 변경할 파일의 경로.
- `const struct timeval times[2]`: 접근 시간과 수정 시간을 저장하는 배열.

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 파일의 타임스탬프를 변경할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/futimesat.2.html

---

### Utimensat
> 파일의 마지막 접근 시간과 수정 시간을 나노초 단위로 변경합니다.

**LIBRARY**
- `#include <sys/stat.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 시간을 변경할 파일의 경로.
- `const struct timespec times[2]`: 접근 시간과 수정 시간을 저장하는 배열.
- `int flags`: 플래그 (예: `AT_SYMLINK_NOFOLLOW`).

**Use Case**
- 나노초 단위로 파일의 마지막 접근 시간과 수정 시간을 변경해야 할 때 사용됩니다. 정밀한 타임스탬프가 필요한 작업에 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/utimensat.2.html

---

### Access
> 실제 사용자의 파일 접근 권한을 확인합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `const char *pathname`: 접근 권한을 확인할 파일의 경로.
- `int mode`: 접근 권한을 확인할 방법 (예: `R_OK`, `W_OK`, `X_OK`).

**Use Case**
- 실제 사용자가 특정 파일에 대해 읽기, 쓰기 또는 실행 권한을 가지고 있는지 확인할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/access.2.html

---

### Faccessat
> 디렉터리 파일 디스크립터를 기준으로 실제 사용자의 파일 접근 권한을 확인합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 접근 권한을 확인할 파일의 경로.
- `int mode`: 접근 권한을 확인할 방법 (예: `R_OK`, `W_OK`, `X_OK`).
- `int flags`: 심볼릭 링크 처리 방식을 제어하는 플래그 (옵션).

**Use Case**
- 특정 디렉터리 파일 디스크립터를 기준으로 실제 사용자의 파일 접근 권한을 확인할 때 사용됩니다. 보안이 중요한 환경에서 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/faccessat.2.html

## Extended file attributes

---

### Setxattr
> 확장 속성 값을 설정합니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 속성을 설정할 파일의 경로.
- `const char *name`: 설정할 확장 속성의 이름.
- `const void *value`: 설정할 속성 값.
- `size_t size`: 속성 값의 크기.
- `int flags`: 동작을 제어하는 플래그 (예: `XATTR_CREATE`, `XATTR_REPLACE`).

**Use Case**
- 파일에 확장 속성(메타데이터)를 설정할 때 사용됩니다. 예를 들어, 파일에 사용자 정의 메타데이터를 저장할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/setxattr.2.html

---

### Lsetxattr
> 심볼릭 링크의 확장 속성 값을 설정합니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 속성을 설정할 심볼릭 링크의 경로.
- `const char *name`: 설정할 확장 속성의 이름.
- `const void *value`: 설정할 속성 값.
- `size_t size`: 속성 값의 크기.
- `int flags`: 동작을 제어하는 플래그.

**Use Case**
- 심볼릭 링크 자체의 확장 속성을 설정할 때 사용됩니다. 심볼릭 링크가 참조하는 파일이 아닌, 링크 자체의 속성을 변경하는 데 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lsetxattr.2.html

---

### Fsetxattr
> 열린 파일의 확장 속성 값을 설정합니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `int fd`: 속성을 설정할 파일의 파일 디스크립터.
- `const char *name`: 설정할 확장 속성의 이름.
- `const void *value`: 설정할 속성 값.
- `size_t size`: 속성 값의 크기.
- `int flags`: 동작을 제어하는 플래그.

**Use Case**
- 열린 파일의 확장 속성을 설정할 때 사용됩니다. 파일을 열고 작업하는 도중 메타데이터를 추가하거나 변경할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fsetxattr.2.html

---

### Getxattr
> 확장 속성 값을 가져옵니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 속성을 조회할 파일의 경로.
- `const char *name`: 조회할 확장 속성의 이름.
- `void *value`: 속성 값을 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 파일에 설정된 확장 속성 값을 읽을 때 사용됩니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/getxattr.2.html

---

### Lgetxattr
> 심볼릭 링크의 확장 속성 값을 가져옵니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 속성을 조회할 심볼릭 링크의 경로.
- `const char *name`: 조회할 확장 속성의 이름.
- `void *value`: 속성 값을 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 심볼릭 링크 자체의 확장 속성 값을 읽을 때 사용됩니다. 심볼릭 링크가 참조하는 파일이 아닌, 링크 자체의 속성을 조회할 때 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lgetxattr.2.html

---

### Fgetxattr
> 열린 파일의 확장 속성 값을 가져옵니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `int fd`: 확장 속성을 조회할 파일의 파일 디스크립터.
- `const char *name`: 조회할 확장 속성의 이름.
- `void *value`: 속성 값을 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 열린 파일의 확장 속성을 조회할 때 사용됩니다. 열린 상태에서 파일의 메타데이터를 확인할 때 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fgetxattr.2.html

---

### Listxattr
> 확장 속성 이름들의 목록을 가져옵니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 확장 속성 목록을 조회할 파일의 경로.
- `char *list`: 속성 이름 목록을 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 파일에 설정된 모든 확장 속성의 이름 목록을 가져올 때 사용됩니다.

**Return Value**
- 성공 시: 속성 이름 목록의 크기 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/listxattr.2.html

---

### Llistxattr
> 심볼릭 링크의 확장 속성 이름들의 목록을 가져옵니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 확장 속성 목록을 조회할 심볼릭 링크의 경로.
- `char *list`: 속성 이름 목록을 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 심볼릭 링크의 확장 속성 목록을 가져올 때 사용됩니다.

**Return Value**
- 성공 시: 속성 이름 목록의 크기 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/llistxattr.2.html

---

### Flistxattr
> 열린 파일의 확장 속성 이름들의 목록을 가져옵니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `int fd`: 확장 속성 목록을 조회할 파일의 파일 디스크립터.
- `char *list`: 속성 이름 목록을 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**
- 열린 파일의 확장 속성 목록을 가져올 때 사용됩니다.

**Return Value**
- 성공 시: 속성 이름 목록의 크기 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/flistxattr.2.html

---

### Removexattr
> 파일의 확장 속성을 제거합니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 확장 속성을 제거할 파일의 경로.
- `const char *name`: 제거할 확장 속성의 이름.

**Use Case**
- 파일에 설정된 확장 속성을 제거할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/removexattr.2.html

---

### Lremovexattr
> 심볼릭 링크의 확장 속성을 제거합니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `const char *path`: 확장 속성을 제거할 심볼릭 링크의 경로.
- `const char *name`: 제거할 확장 속성의 이름.

**Use Case**
- 심볼릭 링크 자체에 설정된 확장 속성을 제거할 때 사용됩니다. 링크가 참조하는 파일의 속성이 아닌, 링크 자체의 메타데이터를 삭제하는 데 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lremovexattr.2.html

---

### Fremovexattr
> 열린 파일의 확장 속성을 제거합니다.

**LIBRARY**
- `#include <sys/xattr.h>`

**Arguments**
- `int fd`: 확장 속성을 제거할 파일의 파일 디스크립터.
- `const char *name`: 제거할 확장 속성의 이름.

**Use Case**
- 열린 파일의 확장 속성을 제거할 때 사용됩니다. 파일이 열려 있는 상태에서 메타데이터를 동적으로 수정하거나 삭제할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fremovexattr.2.html

## File descriptor manipulations

---

### Ioctl
> 장치 제어 명령을 수행합니다.

**LIBRARY**
- `#include <sys/ioctl.h>`

**Arguments**
- `int fd`: 제어할 장치의 파일 디스크립터.
- `unsigned long request`: 수행할 제어 명령.
- `...`: 명령에 필요한 추가 인자들.

**Use Case**
- 장치 파일이나 소켓 같은 특수 파일에 대해 제어 명령을 수행할 때 사용됩니다. 예를 들어, 터미널 설정을 변경하거나 네트워크 인터페이스에 대한 정보를 얻을 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/ioctl.2.html

---

### Fcntl
> 파일 디스크립터를 조작합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int fd`: 조작할 파일 디스크립터.
- `int cmd`: 수행할 제어 명령 (예: `F_DUPFD`, `F_GETFL`, `F_SETFL`).
- `...`: 명령에 필요한 추가 인자들.

**Use Case**
- 파일 디스크립터의 속성을 변경하거나 잠금을 설정할 때 사용됩니다. 예를 들어, 파일에 대한 비차단 모드를 설정하거나 잠금을 걸 때 유용합니다.

**Return Value**
- 성공 시: `0` 또는 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fcntl.2.html

---

### Dup
> 파일 디스크립터를 복제합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int oldfd`: 복제할 기존 파일 디스크립터.

**Use Case**
- 열린 파일의 파일 디스크립터를 복제하여 동일한 파일을 가리키는 새로운 파일 디스크립터를 생성할 때 사용됩니다.

**Return Value**
- 성공 시: 새 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/dup.2.html

---

### Dup2
> 기존 파일 디스크립터를 지정된 파일 디스크립터로 복제합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int oldfd`: 복제할 기존 파일 디스크립터.
- `int newfd`: 복제될 파일 디스크립터의 값.

**Use Case**
- 기존 파일 디스크립터를 새로운 파일 디스크립터 값으로 복제할 때 사용됩니다. 새로운 파일 디스크립터가 이미 열려 있는 경우, 이를 먼저 닫고 복제합니다.

**Return Value**
- 성공 시: `newfd` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/dup2.2.html

---

### Dup3
> 기존 파일 디스크립터를 지정된 파일 디스크립터로 복제하며, 플래그를 설정할 수 있습니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int oldfd`: 복제할 기존 파일 디스크립터.
- `int newfd`: 복제될 파일 디스크립터의 값.
- `int flags`: 복제할 때 적용할 플래그 (예: `O_CLOEXEC`).

**Use Case**
- `dup2`와 유사하지만, 추가 플래그를 지정할 수 있어 더 세밀한 제어가 가능합니다. 예를 들어, 파일 디스크립터 상속을 막기 위해 `O_CLOEXEC` 플래그를 사용할 수 있습니다.

**Return Value**
- 성공 시: `newfd` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/dup3.2.html

---

### Flock
> 열린 파일에 대한 조언적 잠금을 설정하거나 해제합니다.

**LIBRARY**
- `#include <sys/file.h>`

**Arguments**
- `int fd`: 잠금을 설정할 파일의 파일 디스크립터.
- `int operation`: 잠금 동작 (예: `LOCK_SH`, `LOCK_EX`, `LOCK_UN`).

**Use Case**
- 파일에 대해 공유 잠금 또는 배타적 잠금을 설정하거나 해제할 때 사용됩니다. 파일 시스템에서 여러 프로세스 간의 동기화를 보장할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/flock.2.html

## Read/Write

---

### Read
> 파일 디스크립터에서 데이터를 읽어옵니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 데이터를 읽어올 파일 디스크립터.
- `void *buf`: 읽은 데이터를 저장할 버퍼.
- `size_t count`: 읽을 바이트 수.

**Use Case**
- 파일이나 소켓에서 데이터를 읽어올 때 사용됩니다. 파일의 내용이나 네트워크 데이터를 읽는 기본적인 작업에 활용됩니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/read.2.html

---

### Readv
> 데이터를 여러 버퍼에 나누어 읽어옵니다.

**LIBRARY**
- `#include <sys/uio.h>`

**Arguments**
- `int fd`: 데이터를 읽어올 파일 디스크립터.
- `const struct iovec *iov`: 데이터를 저장할 버퍼 배열.
- `int iovcnt`: 배열의 크기 (버퍼의 개수).

**Use Case**
- 데이터를 여러 메모리 버퍼에 나누어 읽을 때 사용됩니다. 네트워크 데이터나 파일에서 여러 부분으로 나뉜 데이터를 읽어올 때 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/readv.2.html

---

### Pread
> 파일 디스크립터의 지정된 오프셋에서 데이터를 읽어옵니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 데이터를 읽어올 파일 디스크립터.
- `void *buf`: 읽은 데이터를 저장할 버퍼.
- `size_t count`: 읽을 바이트 수.
- `off_t offset`: 읽기를 시작할 파일 내 오프셋.

**Use Case**
- 파일에서 현재 위치와 상관없이 특정 오프셋에서 데이터를 읽어야 할 때 사용됩니다. 랜덤 접근이 필요한 경우에 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/pread.2.html

---

### Preadv
> 여러 버퍼에 나누어 데이터를 지정된 오프셋에서 읽어옵니다.

**LIBRARY**
- `#include <sys/uio.h>`

**Arguments**
- `int fd`: 데이터를 읽어올 파일 디스크립터.
- `const struct iovec *iov`: 데이터를 저장할 버퍼 배열.
- `int iovcnt`: 배열의 크기 (버퍼의 개수).
- `off_t offset`: 읽기를 시작할 파일 내 오프셋.

**Use Case**
- 여러 버퍼에 나누어 데이터를 특정 위치에서 읽어야 할 때 사용됩니다. 파일이나 네트워크 데이터에서 랜덤 읽기 작업을 수행할 때 유용합니다.

**Return Value**
- 성공 시: 읽은 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/preadv.2.html

---

### Write
> 파일 디스크립터에 데이터를 씁니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 데이터를 쓸 파일 디스크립터.
- `const void *buf`: 쓸 데이터를 저장한 버퍼.
- `size_t count`: 쓸 바이트 수.

**Use Case**
- 파일이나 소켓에 데이터를 쓸 때 사용됩니다. 파일을 수정하거나 네트워크 데이터를 전송할 때 기본적으로 사용됩니다.

**Return Value**
- 성공 시: 쓴 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/write.2.html

---

### Writev
> 여러 버퍼에 있는 데이터를 파일 디스크립터에 씁니다.

**LIBRARY**
- `#include <sys/uio.h>`

**Arguments**
- `int fd`: 데이터를 쓸 파일 디스크립터.
- `const struct iovec *iov`: 데이터를 저장한 버퍼 배열.
- `int iovcnt`: 배열의 크기 (버퍼의 개수).

**Use Case**
- 여러 버퍼에 나누어 저장된 데이터를 한 번에 파일 디스크립터에 쓸 때 사용됩니다. 네트워크 데이터 전송 또는 파일 시스템 작업에서 효율성을 높일 수 있습니다.

**Return Value**
- 성공 시: 쓴 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/writev.2.html

---

### Pwrite
> 파일 디스크립터의 지정된 오프셋에 데이터를 씁니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 데이터를 쓸 파일 디스크립터.
- `const void *buf`: 쓸 데이터를 저장한 버퍼.
- `size_t count`: 쓸 바이트 수.
- `off_t offset`: 쓰기를 시작할 파일 내 오프셋.

**Use Case**
- 파일의 특정 위치에 데이터를 쓸 때 사용됩니다. 현재 파일 포인터 위치에 상관없이 데이터를 쓸 수 있어 랜덤 쓰기가 필요한 경우에 유용합니다.

**Return Value**
- 성공 시: 쓴 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/pwrite.2.html

---

### Pwritev
> 여러 버퍼에 있는 데이터를 지정된 오프셋에 씁니다.

**LIBRARY**
- `#include <sys/uio.h>`

**Arguments**
- `int fd`: 데이터를 쓸 파일 디스크립터.
- `const struct iovec *iov`: 데이터를 저장한 버퍼 배열.
- `int iovcnt`: 배열의 크기 (버퍼의 개수).
- `off_t offset`: 쓰기를 시작할 파일 내 오프셋.

**Use Case**
- 여러 버퍼에 있는 데이터를 파일의 특정 위치에 쓸 때 사용됩니다. 랜덤 쓰기와 네트워크 작업에서 효율성을 높이는 데 유용합니다.

**Return Value**
- 성공 시: 쓴 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/pwritev.2.html

---

### Lseek
> 파일 오프셋을 재조정합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 오프셋을 변경할 파일 디스크립터.
- `off_t offset`: 파일 내 새로운 오프셋.
- `int whence`: 오프셋을 기준으로 삼을 위치 (예: `SEEK_SET`, `SEEK_CUR`, `SEEK_END`).

**Use Case**
- 파일 내 읽기/쓰기 위치를 변경할 때 사용됩니다. 파일을 순차적으로 읽거나 쓰는 것 외에 랜덤 접근이 필요할 때 유용합니다.

**Return Value**
- 성공 시: 새로운 오프셋 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/lseek.2.html

---

### Sendfile
> 두 파일 디스크립터 간 데이터를 전송합니다.

**LIBRARY**
- `#include <sys/sendfile.h>`

**Arguments**
- `int out_fd`: 데이터를 전송할 파일 디스크립터.
- `int in_fd`: 데이터를 읽어올 파일 디스크립터.
- `off_t *offset`: 읽기를 시작할 파일 내 오프셋.
- `size_t count`: 전송할 바이트 수.

**Use Case**
- 두 파일 디스크립터 간의 데이터를 직접 전송할 때 사용됩니다. 네트워크 소켓 간 데이터 전송에서 성능을 향상시킬 수 있습니다.

**Return Value**
- 성공 시: 전송된 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/sendfile.2.html

## Synchronized I/O

---

### Fdatasync
> 파일의 메모리 상의 데이터를 스토리지 장치에 동기화합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 동기화할 파일의 파일 디스크립터.

**Use Case**
- 파일의 데이터를 디스크에 동기화할 때 사용됩니다. 메타데이터는 동기화되지 않고, 데이터만 디스크에 기록됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fdatasync.2.html

---

### Fsync
> 파일의 메모리 상의 데이터를 스토리지 장치에 동기화합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 동기화할 파일의 파일 디스크립터.

**Use Case**
- 파일의 데이터를 디스크에 동기화할 때 사용됩니다. 데이터뿐만 아니라 메타데이터도 함께 동기화됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fsync.2.html

---

### Msync
> 메모리 맵핑된 파일의 내용을 디스크와 동기화합니다.

**LIBRARY**
- `#include <sys/mman.h>`

**Arguments**
- `void *addr`: 동기화할 메모리 맵핑된 영역의 시작 주소.
- `size_t length`: 동기화할 메모리 영역의 길이.
- `int flags`: 동기화 방식 (예: `MS_ASYNC`, `MS_SYNC`, `MS_INVALIDATE`).

**Use Case**
- 메모리 맵핑된 파일과 디스크 간의 동기화를 보장할 때 사용됩니다. 메모리 맵핑된 파일이 수정된 후 이를 디스크에 저장하기 위해 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/msync.2.html

---

### Sync_file_range
> 파일의 특정 구간을 디스크와 동기화합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int fd`: 동기화할 파일의 파일 디스크립터.
- `off64_t offset`: 동기화할 파일 내 시작 오프셋.
- `off64_t nbytes`: 동기화할 바이트 수.
- `unsigned int flags`: 동기화 방식 (예: `SYNC_FILE_RANGE_WAIT_BEFORE`, `SYNC_FILE_RANGE_WRITE`, `SYNC_FILE_RANGE_WAIT_AFTER`).

**Use Case**
- 파일의 특정 구간을 선택적으로 디스크에 동기화할 때 사용됩니다. 대용량 파일에서 효율적으로 동기화를 관리할 수 있습니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/sync_file_range.2.html

---

### Sync
> 버퍼 캐시의 내용을 디스크에 커밋합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- 없음

**Use Case**
- 모든 파일 시스템의 버퍼 캐시를 디스크에 강제로 기록할 때 사용됩니다. 시스템 충돌 전이나 중요한 작업 후 데이터를 안전하게 저장하기 위해 유용합니다.

**Return Value**
- 없음

**manual page**
- http://man7.org/linux/man-pages/man2/sync.2.html

---

### Syncfs
> 파일 시스템의 버퍼 캐시를 디스크에 커밋합니다.

**LIBRARY**
- `#include <unistd.h>`

**Arguments**
- `int fd`: 동기화할 파일 시스템이 포함된 파일의 파일 디스크립터.

**Use Case**
- 특정 파일 시스템의 버퍼 캐시를 디스크에 강제로 기록할 때 사용됩니다. 여러 파일 시스템이 있을 때 특정 파일 시스템만 동기화할 수 있어 효율적입니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/syncfs.2.html

## Asynchronous I/O

---

### Io_setup
> 비동기 I/O 컨텍스트를 생성합니다.

**LIBRARY**
- `#include <linux/aio.h>`

**Arguments**
- `unsigned int nr_events`: 처리할 수 있는 최대 I/O 이벤트 수.
- `aio_context_t *ctxp`: 생성된 비동기 I/O 컨텍스트를 저장할 포인터.

**Use Case**
- 비동기 I/O 작업을 관리하기 위한 컨텍스트를 생성할 때 사용됩니다. 다중 I/O 요청을 비동기적으로 처리할 수 있는 환경을 설정할 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/io_setup.2.html

---

### Io_destroy
> 비동기 I/O 컨텍스트를 제거합니다.

**LIBRARY**
- `#include <linux/aio.h>`

**Arguments**
- `aio_context_t ctx`: 제거할 비동기 I/O 컨텍스트.

**Use Case**
- 비동기 I/O 작업이 완료되거나 더 이상 필요하지 않을 때 생성된 비동기 I/O 컨텍스트를 제거할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/io_destroy.2.html

---

### Io_submit
> 비동기 I/O 블록을 제출합니다.

**LIBRARY**
- `#include <linux/aio.h>`

**Arguments**
- `aio_context_t ctx`: 비동기 I/O 작업을 처리할 컨텍스트.
- `long nr`: 제출할 I/O 블록의 수.
- `struct iocb **iocbpp`: 제출할 I/O 블록의 배열.

**Use Case**
- 비동기 I/O 작업을 제출하여 처리할 때 사용됩니다. 여러 I/O 작업을 동시에 처리할 때 매우 효율적입니다.

**Return Value**
- 성공 시: 제출된 I/O 블록 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/io_submit.2.html

---

### Io_cancel
> 처리 중인 비동기 I/O 작업을 취소합니다.

**LIBRARY**
- `#include <linux/aio.h>`

**Arguments**
- `aio_context_t ctx`: 취소할 비동기 I/O 작업이 있는 컨텍스트.
- `struct iocb *iocb`: 취소할 비동기 I/O 작업을 나타내는 블록.
- `struct io_event *result`: 취소된 작업의 결과를 저장할 구조체.

**Use Case**
- 비동기 I/O 작업 중 특정 작업을 취소할 때 사용됩니다. 이미 제출된 I/O 요청을 중단하고 싶을 때 유용합니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/io_cancel.2.html

---

### Io_getevents
> 완료된 비동기 I/O 작업 이벤트를 읽어옵니다.

**LIBRARY**
- `#include <linux/aio.h>`

**Arguments**
- `aio_context_t ctx`: 비동기 I/O 작업을 처리하는 컨텍스트.
- `long min_nr`: 반환할 최소 이벤트 수.
- `long max_nr`: 반환할 최대 이벤트 수.
- `struct io_event *events`: 완료된 I/O 이벤트를 저장할 배열.
- `struct timespec *timeout`: 대기할 최대 시간.

**Use Case**
- 비동기 I/O 작업의 완료 상태를 확인하고 결과를 읽을 때 사용됩니다. 제출된 비동기 I/O 작업이 완료되었는지 확인하는데 유용합니다.

**Return Value**
- 성공 시: 읽은 이벤트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/io_getevents.2.html

## Multiplexed I/O

---

### Select
> 동기식 I/O 멀티플렉싱을 수행합니다.

**LIBRARY**
- `#include <sys/select.h>`

**Arguments**
- `int nfds`: 감시할 파일 디스크립터의 수.
- `fd_set *readfds`: 읽기 가능 여부를 확인할 파일 디스크립터 집합.
- `fd_set *writefds`: 쓰기 가능 여부를 확인할 파일 디스크립터 집합.
- `fd_set *exceptfds`: 예외 발생 여부를 확인할 파일 디스크립터 집합.
- `struct timeval *timeout`: 대기할 최대 시간.

**Use Case**
- 여러 파일 디스크립터에 대한 I/O 이벤트를 동기적으로 감시할 때 사용됩니다. 주로 소켓이나 파일을 동시에 감시하면서 데이터를 비동기적으로 처리할 때 유용합니다.

**Return Value**
- 성공 시: 준비된 파일 디스크립터의 개수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/select.2.html

---

### Pselect6
> 동기식 I/O 멀티플렉싱을 수행하며, 신호 마스크를 설정합니다.

**LIBRARY**
- `#include <sys/select.h>`

**Arguments**
- `int nfds`: 감시할 파일 디스크립터의 수.
- `fd_set *readfds`: 읽기 가능 여부를 확인할 파일 디스크립터 집합.
- `fd_set *writefds`: 쓰기 가능 여부를 확인할 파일 디스크립터 집합.
- `fd_set *exceptfds`: 예외 발생 여부를 확인할 파일 디스크립터 집합.
- `const struct timespec *timeout`: 대기할 최대 시간.
- `const sigset_t *sigmask`: 블록할 신호 집합.

**Use Case**
- `select`와 유사하지만, 신호 마스크를 설정할 수 있어 더 세밀한 제어가 가능합니다. 신호가 처리되지 않도록 블로킹하고 I/O를 기다릴 때 유용합니다.

**Return Value**
- 성공 시: 준비된 파일 디스크립터의 개수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/pselect6.2.html

---

### Poll
> 파일 디스크립터의 이벤트를 기다립니다.

**LIBRARY**
- `#include <poll.h>`

**Arguments**
- `struct pollfd *fds`: 감시할 파일 디스크립터와 이벤트 목록.
- `nfds_t nfds`: 감시할 파일 디스크립터 목록의 크기.
- `int timeout`: 대기할 최대 시간(밀리초 단위).

**Use Case**
- 여러 파일 디스크립터에 대해 이벤트를 감시할 때 사용됩니다. 소켓이나 파일 디스크립터에서 발생하는 읽기, 쓰기 또는 오류 이벤트를 감지할 수 있습니다.

**Return Value**
- 성공 시: 발생한 이벤트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/poll.2.html

---

### Ppoll
> 파일 디스크립터의 이벤트를 기다리며, 신호 마스크를 설정합니다.

**LIBRARY**
- `#include <poll.h>`

**Arguments**
- `struct pollfd *fds`: 감시할 파일 디스크립터와 이벤트 목록.
- `nfds_t nfds`: 감시할 파일 디스크립터 목록의 크기.
- `const struct timespec *timeout`: 대기할 최대 시간(밀리초 단위).
- `const sigset_t *sigmask`: 블록할 신호 집합.

**Use Case**
- `poll`과 유사하지만, 신호 마스크를 설정할 수 있어 신호 처리 중에도 안정적으로 이벤트를 감시할 수 있습니다.

**Return Value**
- 성공 시: 발생한 이벤트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/ppoll.2.html

---

### Epoll_create
> epoll 파일 디스크립터를 생성합니다.

**LIBRARY**
- `#include <sys/epoll.h>`

**Arguments**
- `int size`: 초기 이벤트 목록의 크기.

**Use Case**
- epoll 인터페이스를 사용하여 파일 디스크립터의 이벤트를 비동기적으로 감시할 때 사용됩니다. 이벤트 기반 프로그래밍에 유용합니다.

**Return Value**
- 성공 시: epoll 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/epoll_create.2.html

---

### Epoll_create1
> epoll 파일 디스크립터를 생성하며, 플래그를 설정합니다.

**LIBRARY**
- `#include <sys/epoll.h>`

**Arguments**
- `int flags`: epoll 동작을 제어하는 플래그 (예: `EPOLL_CLOEXEC`).

**Use Case**
- `epoll_create`와 유사하지만, 파일 디스크립터 상속 금지 등의 플래그를 설정할 수 있어 더 세밀한 제어가 가능합니다.

**Return Value**
- 성공 시: epoll 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/epoll_create1.2.html

---

### Epoll_ctl
> epoll 파일 디스크립터에 대한 제어 인터페이스를 제공합니다.

**LIBRARY**
- `#include <sys/epoll.h>`

**Arguments**
- `int epfd`: epoll 파일 디스크립터.
- `int op`: 수행할 제어 작업 (`EPOLL_CTL_ADD`, `EPOLL_CTL_MOD`, `EPOLL_CTL_DEL`).
- `int fd`: 제어할 대상 파일 디스크립터.
- `struct epoll_event *event`: epoll 이벤트에 대한 설정 정보.

**Use Case**
- epoll 파일 디스크립터에 파일 디스크립터를 추가, 수정, 또는 삭제할 때 사용됩니다. I/O 멀티플렉싱에서 효율적인 이벤트 기반 작업을 처리하는 데 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/epoll_ctl.2.html

---

### Epoll_wait
> epoll 파일 디스크립터에서 I/O 이벤트를 기다립니다.

**LIBRARY**
- `#include <sys/epoll.h>`

**Arguments**
- `int epfd`: epoll 파일 디스크립터.
- `struct epoll_event *events`: 발생한 이벤트 정보를 저장할 배열.
- `int maxevents`: 반환할 수 있는 최대 이벤트 수.
- `int timeout`: 대기할 최대 시간 (밀리초, `-1`은 무한 대기).

**Use Case**
- epoll을 통해 여러 파일 디스크립터에서 발생한 I/O 이벤트를 감지할 때 사용됩니다. 효율적인 비동기 I/O 이벤트 처리를 위해 주로 사용됩니다.

**Return Value**
- 성공 시: 발생한 이벤트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/epoll_wait.2.html

---

### Epoll_pwait
> epoll 파일 디스크립터에서 I/O 이벤트를 기다리며, 시그널 마스크를 설정합니다.

**LIBRARY**
- `#include <sys/epoll.h>`

**Arguments**
- `int epfd`: epoll 파일 디스크립터.
- `struct epoll_event *events`: 발생한 이벤트 정보를 저장할 배열.
- `int maxevents`: 반환할 수 있는 최대 이벤트 수.
- `int timeout`: 대기할 최대 시간 (밀리초, `-1`은 무한 대기).
- `const sigset_t *sigmask`: 블록할 시그널을 설정하는 시그널 마스크.

**Use Case**
- epoll을 통해 여러 파일 디스크립터에서 발생한 I/O 이벤트를 감지하는 동시에, 시그널 처리를 제어하고 싶을 때 사용됩니다.

**Return Value**
- 성공 시: 발생한 이벤트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/epoll_pwait.2.html

## Monitoring file events

---

### Inotify_init
> Inotify 인스턴스를 초기화합니다.

**LIBRARY**
- `#include <sys/inotify.h>`

**Arguments**
- 없음

**Use Case**
- 파일 시스템 이벤트를 모니터링하기 위해 inotify 인스턴스를 초기화할 때 사용됩니다. 이 인스턴스를 통해 파일이나 디렉터리에서 발생하는 변경 사항을 감지할 수 있습니다.

**Return Value**
- 성공 시: 새로운 inotify 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/inotify_init.2.html

---

### Inotify_init1
> Inotify 인스턴스를 초기화하며 플래그를 설정합니다.

**LIBRARY**
- `#include <sys/inotify.h>`

**Arguments**
- `int flags`: 플래그 값 (예: `IN_NONBLOCK`, `IN_CLOEXEC`).

**Use Case**
- inotify 인스턴스를 초기화할 때 추가적인 플래그를 설정하여, 비차단 모드로 설정하거나 파일 디스크립터를 상속하지 않도록 할 때 사용됩니다.

**Return Value**
- 성공 시: 새로운 inotify 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/inotify_init1.2.html

---

### Inotify_add_watch
> Inotify 인스턴스에 감시 항목을 추가합니다.

**LIBRARY**
- `#include <sys/inotify.h>`

**Arguments**
- `int fd`: inotify 파일 디스크립터.
- `const char *pathname`: 감시할 파일이나 디렉터리 경로.
- `uint32_t mask`: 감시할 이벤트의 마스크 (예: `IN_MODIFY`, `IN_CREATE`).

**Use Case**
- 특정 파일이나 디렉터리에서 발생하는 이벤트를 감지할 때 사용됩니다. 파일 시스템에서 파일 생성, 삭제, 수정 등의 이벤트를 모니터링할 수 있습니다.

**Return Value**
- 성공 시: 감시 항목의 ID 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/inotify_add_watch.2.html

---

### Inotify_rm_watch
> Inotify 인스턴스에서 감시 항목을 제거합니다.

**LIBRARY**
- `#include <sys/inotify.h>`

**Arguments**
- `int fd`: inotify 파일 디스크립터.
- `int wd`: 제거할 감시 항목의 ID.

**Use Case**
- 더 이상 필요하지 않은 파일이나 디렉터리에 대한 감시를 중지할 때 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/inotify_rm_watch.2.html

---

### Fanotify_init
> Fanotify 그룹을 생성하고 초기화합니다.

**LIBRARY**
- `#include <sys/fanotify.h>`

**Arguments**
- `unsigned int flags`: Fanotify 인스턴스 동작을 제어하는 플래그 (예: `FAN_CLASS_NOTIF`, `FAN_CLASS_CONTENT`).
- `unsigned int event_f_flags`: Fanotify 이벤트 파일 플래그 (예: `O_RDONLY`, `O_RDWR`).

**Use Case**
- Fanotify를 사용하여 파일 시스템 객체에 대한 이벤트를 모니터링하는 그룹을 생성할 때 사용됩니다. 예를 들어, 시스템 보안 모니터링에 사용됩니다.

**Return Value**
- 성공 시: 새로운 fanotify 파일 디스크립터 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fanotify_init.2.html

---

### Fanotify_mark
> Fanotify 마크를 파일 시스템 객체에 추가, 수정 또는 제거합니다.

**LIBRARY**
- `#include <sys/fanotify.h>`

**Arguments**
- `int fanotify_fd`: fanotify 파일 디스크립터.
- `unsigned int flags`: 동작을 제어하는 플래그 (예: `FAN_MARK_ADD`, `FAN_MARK_REMOVE`).
- `uint64_t mask`: 감시할 이벤트 마스크 (예: `FAN_ACCESS`, `FAN_MODIFY`).
- `int dfd`: 파일 디스크립터 또는 `AT_FDCWD`.
- `const char *pathname`: 파일 시스템 객체의 경로.

**Use Case**
- 특정 파일 또는 디렉터리에서 발생하는 이벤트를 감지하기 위해 fanotify 마크를 설정할 때 사용됩니다. 마크를 설정함으로써 지정된 이벤트가 발생할 경우 알림을 받을 수 있습니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/fanotify_mark.2.html

## Miscellaneous

---

### Fadvise64
> 파일 데이터에 대한 접근 패턴을 미리 선언합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int fd`: 파일 디스크립터.
- `off_t offset`: 선언할 접근 패턴의 시작 위치.
- `off_t len`: 적용할 데이터의 길이.
- `int advice`: 접근 패턴에 대한 조언 (예: `POSIX_FADV_NORMAL`, `POSIX_FADV_SEQUENTIAL`).

**Use Case**
- 커널에 파일 데이터에 대한 예상 접근 패턴을 알려주는 데 사용됩니다. 성능 최적화를 위해 데이터 접근 방식을 미리 지정할 수 있습니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/posix_fadvise.2.html

---

### Readahead
> 파일 데이터를 페이지 캐시로 미리 로드합니다.

**LIBRARY**
- `#include <fcntl.h>`

**Arguments**
- `int fd`: 파일 디스크립터.
- `off64_t offset`: 미리 읽기를 시작할 오프셋.
- `size_t count`: 미리 읽을 바이트 수.

**Use Case**
- 파일 시스템 성능을 높이기 위해 파일 데이터를 미리 캐시하여 읽기 성능을 향상시킬 때 사용됩니다. 순차적 파일 접근에서 주로 사용됩니다.

**Return Value**
- 성공 시: `0` 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/readahead.2.html

---

### Getrandom
> 난수 바이트 시퀀스를 얻습니다.

**LIBRARY**
- `#include <sys/random.h>`

**Arguments**
- `void *buf`: 랜덤 데이터를 저장할 버퍼.
- `size_t buflen`: 랜덤 데이터를 읽을 바이트 수.
- `unsigned int flags`: 랜덤 데이터 생성을 제어하는 플래그 (예: `GRND_NONBLOCK`, `GRND_RANDOM`).

**Use Case**
- 암호학적 보안이 필요한 랜덤 데이터를 얻을 때 사용됩니다. 난수 생성기에서 안전한 랜덤 바이트 시퀀스를 얻을 때 유용합니다.

**Return Value**
- 성공 시: 생성된 바이트 수 반환
- 실패 시: `-1` 반환하며, `errno`에 오류 코드가 설정

**manual page**
- http://man7.org/linux/man-pages/man2/getrandom.2.html

---

# END
> [!WARNING]
> This part of the docs is incomplete, contributions are very welcome
> 
> 이 문서는 불완전합니다. 기여를 환영합니다.

[^1]: [linasm.sourceforge.net](https://linasm.sourceforge.net/docs/syscalls/index.php)
[^2]: [Linux man pages](https://man7.org/linux/man-pages/dir_section_2.html)