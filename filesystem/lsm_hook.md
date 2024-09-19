# Filesystem-Related LSM Hooks in Linux

> **Warning**  
> This part of the docs is incomplete, contributions are very welcome.

이 문서는 불완전합니다. 기여를 환영합니다.

## security_inode_permission

파일 접근 권한을 확인할 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *inode`: 접근하려는 파일의 inode 구조체 포인터
- `int mask`: 접근 권한 마스크 (읽기, 쓰기, 실행 등)

**Use Case**:

파일 접근 시 보안 정책을 적용하여 읽기, 쓰기, 실행 권한을 강화하거나 제한할 때 사용.  
접근 제어를 통해 파일 시스템의 보안성을 높일 수 있음.

**Return Value**:

- 성공 시 `0` 반환 (액세스 허용)
- 실패 시 음수의 오류 코드 반환 (액세스 거부)

**Manual Page**:

없음

---

## security_inode_create

새로운 inode가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 파일의 dentry 구조체 포인터
- `umode_t mode`: 파일의 모드(bits)

**Use Case**:

파일 또는 디렉토리 생성 이벤트를 모니터링하거나 제한하여 보안 정책을 강화할 때 사용.  
특정 디렉토리에서의 파일 생성 제한 등 보안 목적에 활용.

**Return Value**:

- 성공 시 `0` 반환 (생성 허용)
- 실패 시 음수의 오류 코드 반환 (생성 거부)

**Manual Page**:

없음

---

## security_inode_link

하드 링크가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *old_dentry`: 기존 파일의 dentry 구조체 포인터
- `struct inode *dir`: 새로운 링크의 부모 디렉토리 inode 구조체 포인터
- `struct dentry *new_dentry`: 새로운 링크의 dentry 구조체 포인터

**Use Case**:

하드 링크 생성 시 보안 정책을 적용하여 무단 링크 생성을 방지하거나 모니터링할 때 사용.  
파일 시스템의 무결성을 유지하기 위한 보안 조치로 활용.

**Return Value**:

- 성공 시 `0` 반환 (링크 생성 허용)
- 실패 시 음수의 오류 코드 반환 (링크 생성 거부)

**Manual Page**:

없음

---

## security_inode_unlink

파일 또는 디렉토리가 삭제될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 삭제할 파일의 dentry 구조체 포인터

**Use Case**:

파일 또는 디렉토리 삭제를 감시하거나 제한하여 중요 데이터의 무단 삭제를 방지할 때 사용.  
시스템 활동을 감사하기 위한 로그 수집에 활용.

**Return Value**:

- 성공 시 `0` 반환 (삭제 허용)
- 실패 시 음수의 오류 코드 반환 (삭제 거부)

**Manual Page**:

없음

---

## security_inode_symlink

심볼릭 링크가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 심볼릭 링크의 dentry 구조체 포인터
- `const char *old_name`: 심볼릭 링크가 가리키는 경로명

**Use Case**:

심볼릭 링크 생성 시 보안 정책을 적용하여 잠재적인 보안 위협을 방지하거나 모니터링할 때 사용.  
심볼릭 링크를 통한 우회 공격을 차단하는 데 활용.

**Return Value**:

- 성공 시 `0` 반환 (생성 허용)
- 실패 시 음수의 오류 코드 반환 (생성 거부)

**Manual Page**:

없음

---

## security_inode_mkdir

새로운 디렉토리가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 디렉토리의 dentry 구조체 포인터
- `umode_t mode`: 디렉토리의 모드(bits)

**Use Case**:

디렉토리 생성 이벤트를 제어하거나 모니터링하여 보안 정책을 강화할 때 사용.  
특정 위치에서의 디렉토리 생성 제한 등 보안 목적에 활용.

**Return Value**:

- 성공 시 `0` 반환 (생성 허용)
- 실패 시 음수의 오류 코드 반환 (생성 거부)

**Manual Page**:

없음

---

## security_inode_rmdir

디렉토리가 삭제될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 삭제할 디렉토리의 dentry 구조체 포인터

**Use Case**:

중요한 디렉토리의 삭제를 제한하거나 모니터링하여 시스템 보안을 강화할 때 사용.  
크리티컬한 영역에서의 디렉토리 제거를 통제.

**Return Value**:

- 성공 시 `0` 반환 (삭제 허용)
- 실패 시 음수의 오류 코드 반환 (삭제 거부)

**Manual Page**:

없음

---

## security_inode_rename

파일 또는 디렉토리의 이름이 변경될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *old_dir`: 기존 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *old_dentry`: 기존 항목의 dentry 구조체 포인터
- `struct inode *new_dir`: 새로운 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *new_dentry`: 새로운 항목의 dentry 구조체 포인터
- `unsigned int flags`: 리네임 플래그

**Use Case**:

이름 변경 작업을 감시하거나 제한하여 보안 감사나 추적에 활용.  
파일 시스템의 무결성을 유지하고 변경 사항을 추적.

**Return Value**:

- 성공 시 `0` 반환 (이름 변경 허용)
- 실패 시 음수의 오류 코드 반환 (이름 변경 거부)

**Manual Page**:

없음

---

## security_inode_setattr

파일 속성이 수정될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터
- `struct iattr *attr`: 변경할 속성 정보를 담은 구조체 포인터

**Use Case**:

파일 메타데이터(권한, 타임스탬프 등)의 변경을 감시하거나 제한하여 보안성을 높일 때 사용.  
무단 속성 변경을 방지하고 시스템 무결성 유지.

**Return Value**:

- 성공 시 `0` 반환 (속성 변경 허용)
- 실패 시 음수의 오류 코드 반환 (속성 변경 거부)

**Manual Page**:

없음

---

## security_inode_getattr

파일 속성이 조회될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct vfsmount *mnt`: 파일 시스템 마운트 지점 구조체 포인터
- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터

**Use Case**:

파일 메타데이터 접근을 추적하여 속성 조회 활동을 감사하거나 모니터링할 때 사용.  
민감한 파일의 속성 조회를 제한하여 보안 강화.

**Return Value**:

- 성공 시 `0` 반환 (속성 조회 허용)
- 실패 시 음수의 오류 코드 반환 (속성 조회 거부)

**Manual Page**:

없음

---

## security_file_open

파일이 열릴 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 열리는 파일의 파일 구조체 포인터
- `const struct cred *cred`: 작업 크레덴셜 구조체 포인터

**Use Case**:

파일 열기 작업을 모니터링하거나 제한하여 보안 감사나 접근 제어를 수행할 때 사용.  
무단 파일 접근을 방지하고 로그를 수집.

**Return Value**:

- 성공 시 `0` 반환 (파일 열기 허용)
- 실패 시 음수의 오류 코드 반환 (파일 열기 거부)

**Manual Page**:

없음

---

## security_file_permission

파일을 읽거나 쓰기 전에 권한을 확인할 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터
- `int mask`: 접근 권한 마스크

**Use Case**:

파일 접근에 대한 보안 정책을 적용하여 읽기, 쓰기 권한을 강화하거나 제한할 때 사용.  
세밀한 접근 제어를 통해 시스템 보안성 향상.

**Return Value**:

- 성공 시 `0` 반환 (액세스 허용)
- 실패 시 음수의 오류 코드 반환 (액세스 거부)

**Manual Page**:

없음

---

## security_file_ioctl

파일에서 `ioctl` 시스템 호출이 이루어질 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터
- `unsigned int cmd`: `ioctl` 명령어 코드
- `unsigned long arg`: `ioctl` 인자

**Use Case**:

하드웨어 디바이스와의 상호 작용을 제어하거나 모니터링하여 보안 정책을 강화할 때 사용.  
무단 `ioctl` 호출을 방지하고 시스템 안정성 유지.

**Return Value**:

- 성공 시 `0` 반환 (ioctl 허용)
- 실패 시 음수의 오류 코드 반환 (ioctl 거부)

**Manual Page**:

없음

---

## security_file_fcntl

파일에서 `fcntl` 시스템 호출이 이루어질 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터
- `unsigned int cmd`: `fcntl` 명령어 코드
- `unsigned long arg`: `fcntl` 인자

**Use Case**:

파일 제어 작업(락 설정, 파일 디스크립터 설정 등)에 대한 정책을 적용하거나 모니터링할 때 사용.  
무단 파일 제어 작업을 방지하여 시스템 보안 강화.

**Return Value**:

- 성공 시 `0` 반환 (fcntl 허용)
- 실패 시 음수의 오류 코드 반환 (fcntl 거부)

**Manual Page**:

없음

---

## security_file_set_fowner

파일 소유자를 설정할 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터

**Use Case**:

파일 소유자 변경을 감시하거나 제한하여 무단 소유자 변경을 방지하고 보안성을 높일 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (소유자 설정 허용)
- 실패 시 음수의 오류 코드 반환 (소유자 설정 거부)

**Manual Page**:

없음

---

## security_file_lock

파일이 잠기거나 잠금 해제될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터
- `unsigned int cmd`: 잠금 명령어 코드
- `struct file_lock *fl`: 파일 잠금 구조체 포인터

**Use Case**:

파일 잠금 작업을 감시하거나 제한하여 멀티유저 환경에서의 데이터 무결성을 보장하고 충돌을 방지할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (잠금 허용)
- 실패 시 음수의 오류 코드 반환 (잠금 거부)

**Manual Page**:

없음

---

## security_file_receive

UNIX 소켓을 통해 파일을 받을 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 수신된 파일의 파일 구조체 포인터

**Use Case**:

소켓을 통한 파일 디스크립터 전송을 제어하거나 모니터링하여 프로세스 간의 무단 파일 공유를 방지할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (수신 허용)
- 실패 시 음수의 오류 코드 반환 (수신 거부)

**Manual Page**:

없음

---

## security_path_mkdir

경로 기반 API를 사용하여 디렉토리가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `const struct path *dir`: 부모 디렉토리의 경로 구조체 포인터
- `struct dentry *dentry`: 생성할 디렉토리의 dentry 구조체 포인터
- `umode_t mode`: 디렉토리의 모드(bits)

**Use Case**:

경로 기반 작업에서의 디렉토리 생성 이벤트를 제어하거나 모니터링하여 보안 정책을 강화할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (생성 허용)
- 실패 시 음수의 오류 코드 반환 (생성 거부)

**Manual Page**:

없음

---

## security_path_rmdir

경로 기반 API를 사용하여 디렉토리가 제거될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `const struct path *dir`: 부모 디렉토리의 경로 구조체 포인터
- `struct dentry *dentry`: 삭제할 디렉토리의 dentry 구조체 포인터

**Use Case**:

경로 기반 작업에서의 디렉토리 삭제를 감시하거나 제한하여 보안성을 높일 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (삭제 허용)
- 실패 시 음수의 오류 코드 반환 (삭제 거부)

**Manual Page**:

없음

---

## inode_post_setattr

inode 속성이 설정된 후에 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터
- `struct iattr *attr`: 설정된 속성 정보를 담은 구조체 포인터

**Use Case**:

속성 변경 후 추가적인 검증이나 로깅을 수행하여 보안 정책을 강화할 때 사용.

**Return Value**:

반환 값 없음 (void)

**Manual Page**:

없음

---

## inode_setxattr

inode에 확장 속성(xattr)이 설정될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터
- `const char *name`: 확장 속성의 이름
- `const void *value`: 설정할 값
- `size_t size`: 값의 크기
- `int flags`: 플래그

**Use Case**:

확장 속성(예: SELinux 레이블)의 변경을 제어하거나 로깅하여 보안 정책을 적용할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (설정 허용)
- 실패 시 음수의 오류 코드 반환 (설정 거부)

**Manual Page**:

없음

---

## inode_removexattr

inode에서 확장 속성(xattr)이 제거될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터
- `const char *name`: 제거할 확장 속성의 이름

**Use Case**:

중요한 확장 속성의 제거를 감시하거나 방지하여 시스템 보안을 강화할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (제거 허용)
- 실패 시 음수의 오류 코드 반환 (제거 거부)

**Manual Page**:

없음

---

## inode_set_acl

inode에 ACL(Access Control List)이 설정될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터
- `const char *acl_name`: 설정할 ACL의 이름
- `const void *acl_value`: 설정할 ACL 값

**Use Case**:

파일 권한에 대한 ACL 설정을 감시하거나 제어하여 보안 정책을 적용할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (ACL 설정 허용)
- 실패 시 음수의 오류 코드 반환 (ACL 설정 거부)

**Manual Page**:

없음

---

## file_permission

파일 접근 권한이 확인될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터
- `int mask`: 접근 권한 마스크

**Use Case**:

접근 제어 정책을 적용하여 파일 시스템의 보안성을 높이고 무단 접근을 방지할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (접근 허용)
- 실패 시 음수의 오류 코드 반환 (접근 거부)

**Manual Page**:

없음

---

## file_mprotect

메모리 보호 작업 중에 호출됩니다 (예: 메모리 권한 변경).

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct vm_area_struct *vma`: 가상 메모리 영역 구조체 포인터
- `unsigned long reqprot`: 요청된 메모리 보호 속성
- `unsigned long prot`: 실제 설정될 메모리 보호 속성

**Use Case**:

프로세스의 메모리 보호 변경을 감시하거나 제한하여 보안성을 강화할 때 사용.  
SELinux와 같은 보안 모듈에서 활용.

**Return Value**:

- 성공 시 `0` 반환 (변경 허용)
- 실패 시 음수의 오류 코드 반환 (변경 거부)

**Manual Page**:

없음

---

## file_receive

UNIX 도메인 소켓을 통해 파일 디스크립터를 받을 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 수신된 파일의 파일 구조체 포인터

**Use Case**:

프로세스 간 파일 디스크립터 공유를 제어하거나 감시하여 보안성을 높일 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (수신 허용)
- 실패 시 음수의 오류 코드 반환 (수신 거부)

**Manual Page**:

없음

---

## task_alloc

새로운 태스크가 할당될 때 (프로세스 생성 시) 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct task_struct *task`: 생성된 태스크 구조체 포인터
- `unsigned long clone_flags`: 프로세스 생성 플래그

**Use Case**:

프로세스 생성 시 보안 검사를 수행하여 악의적인 활동을 감지하거나 방지할 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (태스크 생성 허용)
- 실패 시 음수의 오류 코드 반환 (태스크 생성 거부)

**Manual Page**:

없음

---

## task_free

태스크가 해제될 때 (프로세스 종료 시) 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct task_struct *task`: 종료된 태스크 구조체 포인터

**Use Case**:

프로세스 종료 시 필요한 정리 작업이나 감사 로깅을 수행하여 시스템 무결성을 유지할 때 사용.

**Return Value**:

반환 값 없음 (void)

**Manual Page**:

없음

---

## file_ioctl

파일에서 `ioctl` 작업이 수행될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 대상 파일의 파일 구조체 포인터
- `unsigned int cmd`: `ioctl` 명령어 코드
- `unsigned long arg`: `ioctl` 인자

**Use Case**:

하드웨어 디바이스와의 상호 작용을 제어하거나 로깅하여 보안 정책을 강화할 때 사용.  
무단 `ioctl` 호출을 방지하고 시스템 안정성 유지.

**Return Value**:

- 성공 시 `0` 반환 (ioctl 허용)
- 실패 시 음수의 오류 코드 반환 (ioctl 거부)

**Manual Page**:

없음

---

## file_post_open

파일이 열린 후에 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 열린 파일의 파일 구조체 포인터
- `const struct cred *cred`: 작업 크레덴셜 구조체 포인터

**Use Case**:

파일 열기 이벤트 후 추가적인 접근 제어 정책을 적용하거나 로깅하여 보안성을 높일 때 사용.

**Return Value**:

- 성공 시 `0` 반환 (추가 작업 허용)
- 실패 시 음수의 오류 코드 반환 (추가 작업 거부)

**Manual Page**:

없음
