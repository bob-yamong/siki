# Filesystem-Related Kprobes in Linux

> **Warning**  
> This part of the docs is incomplete, contributions are very welcome.

이 문서는 불완전합니다. 기여를 환영합니다.

## vfs_open

파일이 열릴 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct path *path`: 열려는 파일의 경로 정보를 담은 구조체 포인터
- `struct file *file`: 파일 구조체 포인터

**Use Case**:

파일 시스템 계층에서 파일 열기 이벤트를 모니터링하여 파일 접근 패턴을 분석하거나 보안 감사를 수행할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_read

파일에서 데이터를 읽을 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 읽을 파일의 파일 구조체 포인터
- `char __user *buf`: 사용자 공간의 버퍼 포인터
- `size_t count`: 읽을 바이트 수
- `loff_t *pos`: 파일 내에서의 오프셋 위치

**Use Case**:

파일 읽기 작업을 추적하여 보안 감사나 파일 접근 모니터링을 수행할 때 사용

**Return Value**:

- 성공 시 읽은 바이트 수 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_write

파일에 데이터를 쓸 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 쓸 파일의 파일 구조체 포인터
- `const char __user *buf`: 사용자 공간의 버퍼 포인터
- `size_t count`: 쓸 바이트 수
- `loff_t *pos`: 파일 내에서의 오프셋 위치

**Use Case**:

파일 쓰기 이벤트를 모니터링하여 보안 분석이나 시스템 성능 최적화를 위한 데이터 수집에 사용

**Return Value**:

- 성공 시 기록한 바이트 수 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_unlink

파일이 디렉토리에서 제거될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 삭제할 파일의 dentry 구조체 포인터
- `struct inode **delegated_inode`: 위임된 inode에 대한 이중 포인터 (옵션)

**Use Case**:

파일 삭제를 추적하여 무단 파일 제거를 감지하거나 시스템 활동을 감사할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_mkdir

새로운 디렉토리가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 디렉토리의 dentry 구조체 포인터
- `umode_t mode`: 디렉토리의 모드(bits)

**Use Case**:

디렉토리 생성 이벤트를 모니터링하여 파일 시스템 활동 분석이나 보안 감사를 수행할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_rmdir

디렉토리가 삭제될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 삭제할 디렉토리의 dentry 구조체 포인터

**Use Case**:

디렉토리 삭제를 추적하여 무단 디렉토리 제거를 감지하거나 시스템 보안을 강화할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_rename

파일이나 디렉토리의 이름이 변경될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *old_dir`: 기존 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *old_dentry`: 기존 파일의 dentry 구조체 포인터
- `struct inode *new_dir`: 새로운 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *new_dentry`: 새로운 파일의 dentry 구조체 포인터
- `struct inode **delegated_inode`: 위임된 inode에 대한 이중 포인터 (옵션)
- `unsigned int flags`: 리네임 플래그

**Use Case**:

이름 변경 작업을 모니터링하여 파일 시스템의 변경 사항을 추적하고 감사할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_symlink

심볼릭 링크가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 심볼릭 링크의 dentry 구조체 포인터
- `const char *symname`: 심볼릭 링크가 가리키는 경로명

**Use Case**:

심볼릭 링크 생성 이벤트를 추적하여 잠재적인 보안 취약점이나 비정상적인 동작을 감지할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_link

하드 링크가 생성될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct dentry *old_dentry`: 기존 파일의 dentry 구조체 포인터
- `struct inode *dir`: 새로운 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *new_dentry`: 새로운 하드 링크의 dentry 구조체 포인터
- `struct inode **delegated_inode`: 위임된 inode에 대한 이중 포인터 (옵션)

**Use Case**:

하드 링크 생성을 모니터링하여 파일 시스템의 무결성을 보장하고, 무단 복사를 방지할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음

---

## vfs_fsync

파일이 스토리지와 동기화될 때 호출됩니다.

**LIBRARY**:

커널 내부 함수

**Arguments**:

- `struct file *file`: 동기화할 파일의 파일 구조체 포인터
- `loff_t start`: 동기화 시작 위치
- `loff_t end`: 동기화 종료 위치
- `int datasync`: 데이터 동기화 여부 (메타데이터 제외 여부)

**Use Case**:

파일 동기화 이벤트를 추적하여 데이터 플러시 이벤트를 감지하고 성능 튜닝이나 시스템 감사를 수행할 때 사용

**Return Value**:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

**Manual Page**:

없음
