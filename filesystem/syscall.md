# File system calls
The file is the most basic and fundamental abstraction in Linux. Linux follows the everything-is-a-file philosophy. Consequently, much interaction transpires via filesystem system calls such as reading of and writing to files, even when the object in question is not what you would consider your everyday file.

In order to be accessed, a file must first be opened. Files can be opened for reading, writing, or both. An open file is referenced via a unique descriptor, a mapping from the metadata associated with the open file back to the specific file itself. Inside the Linux kernel, this descriptor is handled by an integer (of the C type int) called the file descriptor, abbreviated fd. File descriptors are shared with user space, and are used directly by user programs to access files. A large part of Linux system programming consists of opening, manipulating, closing, and otherwise using file descriptors.

파일 시스템 호출
파일은 리눅스에서 가장 기본적이고 근본적인 추상화입니다. 리눅스는 '모든 것은 파일이다'라는 철학을 따릅니다. 따라서 많은 상호작용이 파일 시스템 호출을 통해 이루어지며, 여기에는 파일을 읽고 쓰는 작업도 포함됩니다. 심지어 다루고 있는 객체가 우리가 흔히 생각하는 파일이 아닐 때조차도 이러한 방식으로 상호작용이 이루어집니다.

파일에 접근하기 위해서는 먼저 파일을 열어야 합니다. 파일은 읽기, 쓰기, 또는 둘 다를 위해 열 수 있습니다. 열린 파일은 고유한 디스크립터를 통해 참조되며, 이는 열린 파일과 관련된 메타데이터에서 해당 파일 자체로 다시 매핑되는 방식입니다. 리눅스 커널 내부에서 이 디스크립터는 파일 디스크립터(fd)라고 하는 C 언어의 int 타입으로 된 정수로 처리됩니다. 파일 디스크립터는 사용자 공간과 공유되며, 사용자 프로그램이 파일에 접근하는 데 직접적으로 사용됩니다. 리눅스 시스템 프로그래밍의 많은 부분은 파일 디스크립터를 열고, 조작하고, 닫거나 그 외의 방식으로 사용하는 작업으로 이루어집니다.

> [!NOTE]
>This document is based on linasm.sourceforge.net[^1] and the Linux manual[^2].
>
> 이 문서는 linasm.sourceforge.net[^1] 및 리눅스 매뉴얼[^2]을 참고 하였음.

## Table of Content
- File operations (파일 작업)
- Directory operations (디렉토리 작업)
- Link operations (링크 작업)
- Basic file attributes (기본 파일 속성)
- Extended file attributes (확장 파일 속성)
- File descriptor manipulations (파일 디스크립터 조작)
- Read/Write (읽기/쓰기)
- Synchronized I/O (동기화된 I/O)
- Asynchronous I/O (비동기 I/O)
- Multiplexed I/O (다중화된 I/O)
- Monitoring file events (파일 이벤트 모니터링)
- Miscellaneous (기타)

## File operations

### Close
> 파일 디스크립터를 닫습니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `int fd`: 닫을 파일 디스크립터.

**Use Case**:
- 파일을 다 사용한 후, 리소스 해제를 위해 파일 디스크립터를 닫는 데 사용됩니다. 특히 파일을 읽기 또는 쓰기 작업이 완료된 후 시스템 리소스를 절약하기 위해 사용됩니다.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/close.2.html

### Creat
> 파일을 열거나 존재하지 않을 시 생성합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `const char *pathname`: 열거나 생성할 파일 경로.
- `mode_t mode`: 파일 권한 설정 (예: `S_IRUSR | S_IWUSR`).

**Use Case**:
- 파일이 존재하지 않을 때, 새 파일을 생성하며, 주로 로그 파일이나 데이터를 저장할 파일을 동적으로 생성하는 경우에 사용됩니다.

**Return Value**:
- 성공 시 새 파일에 대한 파일 디스크립터를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/creat.2.html

### Open
> 파일을 열거나, 필요 시 생성합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `const char *pathname`: 열 파일의 경로.
- `int flags`: 파일 열기 동작을 정의하는 플래그들 (예: `O_RDONLY`, `O_WRONLY`, `O_RDWR`).
- `mode_t mode` (선택 사항): 파일을 새로 생성할 때 권한을 설정하는 값.

**Use Case**:
- 파일을 읽거나 쓰기 위해 열 때 사용됩니다. 파일이 존재하지 않는 경우에는, 선택적으로 새로운 파일을 생성할 수 있습니다.

**Return Value**:
- 성공 시 파일 디스크립터를 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/open.2.html

### Openat
> 파일 경로를 기준으로 특정 디렉터리 내의 파일을 열거나 생성합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 디렉터리 내에서 열거나 생성할 파일 경로.
- `int flags`: 파일 열기 동작을 정의하는 플래그들.
- `mode_t mode` (선택 사항): 파일을 새로 생성할 때 권한을 설정하는 값.

**Use Case**:
- 특정 디렉터리 내에서 파일을 상대적으로 열거나 생성할 때 사용되며, 특히 보안 상의 이유로 안전한 파일 접근이 필요한 경우에 사용됩니다.

**Return Value**:
- 성공 시 파일 디스크립터를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/openat.2.html

### Name_to_handle_at
> 경로명에 대한 파일 핸들을 가져옵니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 핸들을 얻고자 하는 파일의 경로.
- `struct file_handle *handle`: 파일 핸들에 대한 정보를 저장하는 구조체.
- `int *mount_id`: 마운트 ID를 저장하는 포인터.
- `int flags`: 동작을 정의하는 플래그.

**Use Case**:
- 파일 핸들을 사용하여 파일 시스템 내에서 파일을 고유하게 식별하고, 파일에 대한 핸들 기반 접근이 필요한 경우에 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/name_to_handle_at.2.html

### Open_by_handle_at
> 핸들을 통해 파일을 엽니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int mount_fd`: 마운트 포인트의 파일 디스크립터.
- `struct file_handle *handle`: 열 파일에 대한 핸들.
- `int flags`: 파일 열기 동작을 정의하는 플래그들.

**Use Case**:
- 기존에 얻은 파일 핸들을 통해 파일을 여는 데 사용됩니다. 특히, 핸들 기반 접근 방식을 사용하는 시스템에서 파일에 접근할 때 유용합니다.

**Return Value**:
- 성공 시 파일 디스크립터를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/open_by_handle_at.2.html

### Memfd_create
> 익명 파일을 생성합니다.

**LIBRARY**:
- `#include <sys/mman.h>`

**Arguments**:
- `const char *name`: 익명 파일의 이름.
- `unsigned int flags`: 생성 동작을 정의하는 플래그들 (예: `MFD_CLOEXEC`).

**Use Case**:
- 메모리 내에서 익명 파일을 생성하여, 파일 시스템에 저장하지 않고 메모리 공유 또는 IPC(Inter-Process Communication) 용도로 사용됩니다.

**Return Value**:
- 성공 시 파일 디스크립터를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/memfd_create.2.html

### Mknod
> 특수 파일 또는 일반 파일을 생성합니다.

**LIBRARY**:
- `#include <sys/stat.h>`

**Arguments**:
- `const char *pathname`: 생성할 파일의 경로.
- `mode_t mode`: 파일 유형 및 접근 권한을 지정합니다 (예: `S_IFREG`, `S_IFCHR`).
- `dev_t dev`: 파일이 특수 장치 파일일 경우, 장치 번호를 설정합니다.

**Use Case**:
- 주로 장치 파일 또는 파이프와 같은 특수 파일을 생성하는 데 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/mknod.2.html

### Mknodat
> 디렉터리 파일 디스크립터를 기준으로 특수 파일 또는 일반 파일을 생성합니다.

**LIBRARY**:
- `#include <sys/stat.h>`

**Arguments**:
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 생성할 파일의 경로.
- `mode_t mode`: 파일 유형 및 접근 권한을 지정합니다.
- `dev_t dev`: 장치 파일일 경우 장치 번호를 설정합니다.

**Use Case**:
- 특정 디렉터리를 기준으로 장치 파일, 파이프 또는 일반 파일을 생성할 때 사용되며, 보안이 중요한 환경에서 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/mknodat.2.html

### Rename
> 파일 이름을 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *oldpath`: 기존 파일 경로.
- `const char *newpath`: 새 파일 경로.

**Use Case**:
- 파일 또는 디렉터리의 이름을 변경하거나 파일을 다른 디렉터리로 이동할 때 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/rename.2.html

### Renameat
> 디렉터리 파일 디스크립터를 기준으로 파일 이름을 변경합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int olddirfd`: 기존 파일의 디렉터리 파일 디스크립터.
- `const char *oldpath`: 기존 파일 경로.
- `int newdirfd`: 새 파일의 디렉터리 파일 디스크립터.
- `const char *newpath`: 새 파일 경로.

**Use Case**:
- 보안이 중요한 상황에서 파일 또는 디렉터리의 이름을 변경하거나 파일을 다른 디렉터리로 이동할 때 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/renameat.2.html

### Renameat2
> 디렉터리 파일 디스크립터를 기준으로 파일 이름을 플래그와 함께 변경합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int olddirfd`: 기존 파일의 디렉터리 파일 디스크립터.
- `const char *oldpath`: 기존 파일 경로.
- `int newdirfd`: 새 파일의 디렉터리 파일 디스크립터.
- `const char *newpath`: 새 파일 경로.
- `unsigned int flags`: 이름 변경 동작을 제어하는 플래그들 (예: `RENAME_NOREPLACE`, `RENAME_EXCHANGE`).

**Use Case**:
- 파일 이름을 변경할 때 추가적인 제어가 필요할 때 사용됩니다. 예를 들어, 파일 교환이나 중복 이름 방지를 위해 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/renameat2.2.html

### Truncate
> 파일을 지정된 길이로 자릅니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *path`: 길이를 조정할 파일의 경로.
- `off_t length`: 파일을 자를 길이.

**Use Case**:
- 파일의 내용을 일부 잘라내거나 확장할 때 사용됩니다. 주로 로그 파일이나 임시 파일을 관리할 때 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/truncate.2.html

### Ftruncate
> 열린 파일을 지정된 길이로 자릅니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `int fd`: 길이를 조정할 파일의 디스크립터.
- `off_t length`: 파일을 자를 길이.

**Use Case**:
- 열린 파일의 내용을 일부 잘라내거나 확장할 때 사용됩니다. 주로 파일의 크기를 조절하거나 내용을 덮어쓸 때 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/ftruncate.2.html

### Fallocate
> 파일 공간을 조작합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int fd`: 조작할 파일의 디스크립터.
- `int mode`: 파일 할당 모드를 설정하는 플래그 (예: `FALLOC_FL_KEEP_SIZE`).
- `off_t offset`: 할당할 시작 위치.
- `off_t len`: 할당할 파일 크기.

**Use Case**:
- 파일에 물리적인 공간을 미리 할당하여, 디스크 공간을 예약하거나 파일 확장을 준비할 때 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fallocate.2.html


## Directory Operations

### Mkdir
> 디렉터리를 생성합니다.

**LIBRARY**:
- `#include <sys/stat.h>`

**Arguments**:
- `const char *pathname`: 생성할 디렉터리의 경로.
- `mode_t mode`: 새 디렉터리의 접근 권한을 설정합니다 (예: `S_IRWXU`).

**Use Case**:
- 새로운 디렉터리를 생성할 때 사용됩니다. 주로 데이터 파일을 저장할 폴더나 프로젝트 구조를 구성할 때 활용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/mkdir.2.html

### Mkdirat
> 디렉터리 파일 디스크립터를 기준으로 디렉터리를 생성합니다.

**LIBRARY**:
- `#include <sys/stat.h>`

**Arguments**:
- `int dirfd`: 기준이 되는 디렉터리의 파일 디스크립터.
- `const char *pathname`: 생성할 디렉터리의 경로.
- `mode_t mode`: 새 디렉터리의 접근 권한을 설정합니다.

**Use Case**:
- 특정 디렉터리를 기준으로 새 디렉터리를 생성하는 데 사용됩니다. 주로 보안이 중요한 환경에서 디렉터리 구조를 생성할 때 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/mkdirat.2.html

### Rmdir
> 디렉터리를 삭제합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 삭제할 디렉터리의 경로.

**Use Case**:
- 빈 디렉터리를 삭제할 때 사용됩니다. 디렉터리가 비어 있어야만 삭제할 수 있으며, 비어 있지 않으면 실패합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/rmdir.2.html

### Getcwd
> 현재 작업 중인 디렉터리 경로를 가져옵니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `char *buf`: 현재 작업 중인 디렉터리 경로를 저장할 버퍼.
- `size_t size`: 버퍼의 크기.

**Use Case**:
- 프로그램이 현재 작업 중인 디렉터리의 절대 경로를 가져올 때 사용됩니다. 파일 경로를 확인하거나 변경할 때 유용합니다.

**Return Value**:
- 성공 시 경로 문자열을 반환하고, 실패 시 `NULL`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/getcwd.2.html

### Chdir
> 현재 작업 중인 디렉터리를 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *path`: 새로 설정할 디렉터리의 경로.

**Use Case**:
- 프로세스가 작업 중인 디렉터리를 변경할 때 사용됩니다. 특정 디렉터리에서 작업을 진행할 때 필수적입니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/chdir.2.html

### Fchdir
> 파일 디스크립터를 사용해 현재 작업 중인 디렉터리를 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `int fd`: 새로 설정할 디렉터리의 파일 디스크립터.

**Use Case**:
- 열린 디렉터리의 파일 디스크립터를 통해 작업 중인 디렉터리를 변경할 때 사용됩니다. 디렉터리를 미리 열어 놓고 작업할 경우 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fchdir.2.html

### Chroot
> 루트 디렉터리를 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *path`: 새로 설정할 루트 디렉터리 경로.

**Use Case**:
- 주로 보안 환경에서 프로세스의 루트 디렉터리를 격리하기 위해 사용됩니다. 프로세스가 루트 경로에 접근하는 것을 제한할 때 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/chroot.2.html

### Getdents
> 디렉터리 엔트리를 가져옵니다.

**LIBRARY**:
- `#include <dirent.h>`

**Arguments**:
- `unsigned int fd`: 디렉터리의 파일 디스크립터.
- `struct dirent *dirp`: 디렉터리 엔트리 정보를 저장할 버퍼.
- `unsigned int count`: 읽을 바이트 수.

**Use Case**:
- 디렉터리의 엔트리를 읽어 파일 목록을 얻는 데 사용됩니다. 파일 시스템 탐색에 활용됩니다.

**Return Value**:
- 읽은 바이트 수를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/getdents.2.html

### Getdents64
> 64비트 환경에서 디렉터리 엔트리를 가져옵니다.

**LIBRARY**:
- `#include <dirent.h>`

**Arguments**:
- `unsigned int fd`: 디렉터리의 파일 디스크립터.
- `struct dirent64 *dirp`: 디렉터리 엔트리 정보를 저장할 버퍼.
- `unsigned int count`: 읽을 바이트 수.

**Use Case**:
- 64비트 시스템에서 디렉터리의 엔트리를 읽어 파일 목록을 얻는 데 사용됩니다. 대용량 파일 시스템에서의 탐색에 유용합니다.

**Return Value**:
- 읽은 바이트 수를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/getdents64.2.html

### Lookup_dcookie
> 디렉터리 엔트리의 경로를 반환합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `u64 cookie`: 특정 디렉터리 엔트리를 식별하는 쿠키.
- `char *buffer`: 반환된 경로를 저장할 버퍼.
- `size_t len`: 버퍼의 크기.

**Use Case**:
- 특정 디렉터리 엔트리에 대한 경로 정보를 얻는 데 사용됩니다. 주로 디버깅 또는 파일 시스템 관련 작업에서 활용됩니다.

**Return Value**:
- 성공 시 경로 문자열의 길이를 반환하며, 실패 시 `-1`을 반환하고 `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/lookup_dcookie.2.html



## Link operations

### Link
> 파일에 하드 링크를 만듭니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *oldpath`: 하드 링크를 생성할 기존 파일의 경로.
- `const char *newpath`: 새로 생성할 하드 링크의 경로.

**Use Case**:
- 동일한 파일을 참조하는 새로운 파일 경로를 생성하고, 동일한 데이터에 대해 여러 이름을 가질 때 사용됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/link.2.html

### Linkat
> 디렉터리 파일 디스크립터를 기준으로 파일에 하드 링크를 만듭니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `int olddirfd`: 기존 파일이 위치한 디렉터리의 파일 디스크립터.
- `const char *oldpath`: 하드 링크를 생성할 기존 파일의 경로.
- `int newdirfd`: 하드 링크가 생성될 디렉터리의 파일 디스크립터.
- `const char *newpath`: 새로 생성할 하드 링크의 경로.
- `int flags`: 링크 생성 동작을 제어하는 플래그 (옵션).

**Use Case**:
- 특정 디렉터리 파일 디스크립터를 기준으로 하드 링크를 생성할 때 사용되며, 보안이 중요한 환경에서 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/linkat.2.html

### Symlink
> 파일에 심볼릭 링크를 만듭니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *target`: 심볼릭 링크가 참조할 대상 파일 경로.
- `const char *linkpath`: 생성할 심볼릭 링크의 경로.

**Use Case**:
- 다른 파일을 참조하는 심볼릭 링크(소프트 링크)를 생성할 때 사용됩니다. 대상 파일이 삭제되거나 변경될 수 있는 환경에서 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/symlink.2.html

### Symlinkat
> 디렉터리 파일 디스크립터를 기준으로 파일에 심볼릭 링크를 만듭니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *target`: 심볼릭 링크가 참조할 대상 파일 경로.
- `int newdirfd`: 심볼릭 링크가 생성될 디렉터리의 파일 디스크립터.
- `const char *linkpath`: 생성할 심볼릭 링크의 경로.

**Use Case**:
- 특정 디렉터리 파일 디스크립터를 기준으로 심볼릭 링크를 생성할 때 사용되며, 보안이 중요한 환경에서 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/symlinkat.2.html

### Unlink
> 파일 이름을 삭제하고, 필요하면 파일 자체도 삭제합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 삭제할 파일의 경로.

**Use Case**:
- 파일 시스템에서 파일 이름을 제거할 때 사용됩니다. 파일이 더 이상 참조되지 않으면, 해당 파일 데이터도 삭제됩니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/unlink.2.html

### Unlinkat
> 디렉터리 파일 디스크립터를 기준으로 파일 이름을 삭제하고, 필요하면 파일 자체도 삭제합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int dirfd`: 파일이 위치한 디렉터리의 파일 디스크립터.
- `const char *pathname`: 삭제할 파일의 경로.
- `int flags`: 파일 삭제 동작을 제어하는 플래그 (옵션).

**Use Case**:
- 특정 디렉터리 파일 디스크립터를 기준으로 파일 이름을 제거할 때 사용되며, 보안이 중요한 환경에서 유용합니다.

**Return Value**:
- 성공 시 `0`을 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/unlinkat.2.html

### Readlink
> 심볼릭 링크의 값을 읽습니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 읽을 심볼릭 링크의 경로.
- `char *buf`: 심볼릭 링크의 값을 저장할 버퍼.
- `size_t bufsiz`: 버퍼의 크기.

**Use Case**:
- 심볼릭 링크가 참조하는 파일 경로를 확인할 때 사용됩니다.

**Return Value**:
- 성공 시 읽은 바이트 수를 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/readlink.2.html

### Readlinkat
> 디렉터리 파일 디스크립터를 기준으로 심볼릭 링크의 값을 읽습니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `int dirfd`: 링크가 위치한 디렉터리의 파일 디스크립터.
- `const char *pathname`: 읽을 심볼릭 링크의 경로.
- `char *buf`: 심볼릭 링크의 값을 저장할 버퍼.
- `size_t bufsiz`: 버퍼의 크기.

**Use Case**:
- 특정 디렉터리 파일 디스크립터를 기준으로 심볼릭 링크의 경로를 확인할 때 사용되며, 보안이 중요한 환경에서 유용합니다.

**Return Value**:
- 성공 시 읽은 바이트 수를 반환하고, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/readlinkat.2.html


## Basic file attributes
### Umask
> Set file mode creation mask.

파일 모드 생성 마스크를 설정합니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/stat.h>`

**Arguments**:
- `mode_t mask`: 새로운 파일 모드 마스크.

**Return Value**:
- 이전의 마스크 값을 반환합니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/umask.2.html



### Stat
> Get file metadata.

파일의 메타데이터를 가져옵니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/stat.h>`
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 메타데이터를 가져올 파일 경로.
- `struct stat *statbuf`: 메타데이터를 저장할 버퍼.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/stat.2.html



### Lstat
> Get link metadata.

심볼릭 링크의 메타데이터를 가져옵니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/stat.h>`
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 메타데이터를 가져올 링크 경로.
- `struct stat *statbuf`: 메타데이터를 저장할 버퍼.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/lstat.2.html



### Fstat
> Get file metadata using a file descriptor.

파일 디스크립터를 사용하여 파일의 메타데이터를 가져옵니다.

**LIBRARY**:
- `#include <sys/types.h>`
- `#include <sys/stat.h>`
- `#include <unistd.h>`

**Arguments**:
- `int fd`: 메타데이터를 가져올 파일 디스크립터.
- `struct stat *statbuf`: 메타데이터를 저장할 버퍼.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fstat.2.html



### Fstatat
> Get file metadata relative to a directory file descriptor.

디렉토리 파일 디스크립터를 기준으로 파일 메타데이터를 가져옵니다.

**LIBRARY**:
- `#include <fcntl.h>`
- `#include <sys/stat.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리 파일 디스크립터.
- `const char *pathname`: 메타데이터를 가져올 파일 경로.
- `struct stat *statbuf`: 메타데이터를 저장할 버퍼.
- `int flags`: 플래그 옵션.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fstatat.2.html



### Chmod
> Change permissions of a file.

파일의 권한을 변경합니다.

**LIBRARY**:
- `#include <sys/stat.h>`

**Arguments**:
- `const char *pathname`: 권한을 변경할 파일 경로.
- `mode_t mode`: 새 권한 모드.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/chmod.2.html



### Fchmod
> Change permissions of a file using a file descriptor.

파일 디스크립터를 사용하여 파일의 권한을 변경합니다.

**LIBRARY**:
- `#include <sys/stat.h>`

**Arguments**:
- `int fd`: 권한을 변경할 파일 디스크립터.
- `mode_t mode`: 새 권한 모드.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fchmod.2.html



### Fchmodat
> Change permissions of a file relative to a directory file descriptor.

디렉토리 파일 디스크립터를 기준으로 파일의 권한을 변경합니다.

**LIBRARY**:
- `#include <fcntl.h>`
- `#include <sys/stat.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리 파일 디스크립터.
- `const char *pathname`: 권한을 변경할 파일 경로.
- `mode_t mode`: 새 권한 모드.
- `int flags`: 플래그 옵션.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fchmodat.2.html



### Chown
> Change ownership of a file.

파일의 소유자를 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 소유자를 변경할 파일 경로.
- `uid_t owner`: 새 소유자의 사용자 ID.
- `gid_t group`: 새 소유자의 그룹 ID.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/chown.2.html



### Lchown
> Change ownership of a link.

심볼릭 링크의 소유자를 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 소유자를 변경할 링크 경로.
- `uid_t owner`: 새 소유자의 사용자 ID.
- `gid_t group`: 새 소유자의 그룹 ID.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/lchown.2.html



### Fchown
> Change ownership of a file using a file descriptor.

파일 디스크립터를 사용하여 파일의 소유자를 변경합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `int fd`: 소유자를 변경할 파일 디스크립터.
- `uid_t owner`: 새 소유자의 사용자 ID.
- `gid_t group`: 새 소유자의 그룹 ID.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fchown.2.html



### Fchownat
> Change ownership of a file relative to a directory file descriptor.

디렉토리 파일 디스크립터를 기준으로 파일의 소유자를 변경합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리 파일 디스크립터.
- `const char *pathname`: 소유자를 변경할 파일 경로.
- `uid_t owner`: 새 소유자의 사용자 ID.
- `gid_t group`: 새 소유자의 그룹 ID.
- `int flags`: 플래그 옵션.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/fchownat.2.html



### Utime
> Change file last access and modification times.

파일의 마지막 접근 및 수정 시간을 변경합니다.

**LIBRARY**:
- `#include <utime.h>`

**Arguments**:
- `const char *filename`: 시간을 변경할 파일 경로.
- `const struct utimbuf *times`: 새 접근 및 수정 시간을 담고 있는 구조체.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/utime.2.html



### Utimes
> Change file last access and modification times with microsecond precision.

파일의 마지막 접근 및 수정 시간을 마이크로초 단위로 변경합니다.

**LIBRARY**:
- `#include <sys/time.h>`

**Arguments**:
- `const char *filename`: 시간을 변경할 파일 경로.
- `const struct timeval times[2]`: 접근 및 수정 시간을 담고 있는 구조체 배열.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/utimes.2.html



### Futimesat
> Change timestamps of a file relative to a directory file descriptor.

디렉토리 파일 디스크립터를 기준으로 파일의 시간을 변경합니다.

**LIBRARY**:
- `#include <fcntl.h>`
- `#include <sys/time.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리 파일 디스크립터.
- `const char *pathname`: 시간을 변경할 파일 경로.
- `const struct timeval times[2]`: 접근 및 수정 시간을 담고 있는 구조체 배열.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/futimesat.2.html



### Utimensat
> Change file timestamps with nanosecond precision.

나노초 단위로 파일의 시간을 변경합니다.

**LIBRARY**:
- `#include <fcntl.h>`
- `#include <sys/stat.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리 파일 디스크립터.
- `const char *pathname`: 시간을 변경할 파일 경로.
- `const struct timespec times[2]`: 접근 및 수정 시간을 담고 있는 구조체 배열.
- `int flags`: 플래그 옵션.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/utimensat.2.html



### Access
> Check real user's permissions for a file.

파일에 대해 실제 사용자의 권한을 확인합니다.

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**:
- `const char *pathname`: 권한을 확인할 파일 경로.
- `int mode`: 확인할 권한 모드.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/access.2.html



### Faccessat
> Check real user's permissions for a file relative to a directory file descriptor.

디렉토리 파일 디스크립터를 기준으로 파일에 대한 실제 사용자의 권한을 확인합니다.

**LIBRARY**:
- `#include <fcntl.h>`

**Arguments**:
- `int dirfd`: 기준 디렉토리 파일 디스크립터.
- `const char *pathname`: 권한을 확인할 파일 경로.
- `int mode`: 확인할 권한 모드.
- `int flags`: 플래그 옵션.

**Return Value**:
- 성공 시 `0`, 실패 시 `-1`을 반환하며, `errno`에 오류 코드가 설정됩니다.

**manual page**:
- http://man7.org/linux/man-pages/man2/faccessat.2.html



# END
> [!WARNING]
> This part of the docs is incomplete, contributions are very welcome
> 
> 이 문서는 불완전합니다. 기여를 환영합니다.

[^1]: [linasm.sourceforge.net](https://linasm.sourceforge.net/docs/syscalls/index.php)
[^2]: [Linux man pages](https://man7.org/linux/man-pages/dir_section_2.html)