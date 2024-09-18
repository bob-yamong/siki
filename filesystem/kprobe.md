# Filesystem-Related Kprobes in Linux

> **Warning**  
> This part of the docs is incomplete, contributions are very welcome.

이 문서는 불완전합니다. 기여를 환영합니다.

## vfs_open

파일이 열릴 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct path *path`: 열려는 파일의 경로 정보를 담은 구조체 포인터
- `struct file *file`: 파일 구조체 포인터

### Use Case:

파일 시스템 계층에서 파일 열기 이벤트를 모니터링하여 파일 접근 패턴을 분석하거나 보안 감사를 수행할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_read

파일에서 데이터를 읽을 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct file *file`: 읽을 파일의 파일 구조체 포인터
- `char __user *buf`: 사용자 공간의 버퍼 포인터
- `size_t count`: 읽을 바이트 수
- `loff_t *pos`: 파일 내에서의 오프셋 위치

### Use Case:

파일 읽기 작업을 추적하여 보안 감사나 파일 접근 모니터링을 수행할 때 사용

### Return Value:

- 성공 시 읽은 바이트 수 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_write

파일에 데이터를 쓸 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct file *file`: 쓸 파일의 파일 구조체 포인터
- `const char __user *buf`: 사용자 공간의 버퍼 포인터
- `size_t count`: 쓸 바이트 수
- `loff_t *pos`: 파일 내에서의 오프셋 위치

### Use Case:

파일 쓰기 이벤트를 모니터링하여 보안 분석이나 시스템 성능 최적화를 위한 데이터 수집에 사용

### Return Value:

- 성공 시 기록한 바이트 수 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_unlink

파일이 디렉토리에서 제거될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 삭제할 파일의 dentry 구조체 포인터
- `struct inode **delegated_inode`: 위임된 inode에 대한 이중 포인터 (옵션)

### Use Case:

파일 삭제를 추적하여 무단 파일 제거를 감지하거나 시스템 활동을 감사할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_mkdir

새로운 디렉토리가 생성될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 디렉토리의 dentry 구조체 포인터
- `umode_t mode`: 디렉토리의 모드(bits)

### Use Case:

디렉토리 생성 이벤트를 모니터링하여 파일 시스템 활동 분석이나 보안 감사를 수행할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_rmdir

디렉토리가 삭제될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 삭제할 디렉토리의 dentry 구조체 포인터

### Use Case:

디렉토리 삭제를 추적하여 무단 디렉토리 제거를 감지하거나 시스템 보안을 강화할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_rename

파일이나 디렉토리의 이름이 변경될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct inode *old_dir`: 기존 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *old_dentry`: 기존 파일의 dentry 구조체 포인터
- `struct inode *new_dir`: 새로운 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *new_dentry`: 새로운 파일의 dentry 구조체 포인터
- `struct inode **delegated_inode`: 위임된 inode에 대한 이중 포인터 (옵션)
- `unsigned int flags`: 리네임 플래그

### Use Case:

이름 변경 작업을 모니터링하여 파일 시스템의 변경 사항을 추적하고 감사할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_symlink

심볼릭 링크가 생성될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct inode *dir`: 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *dentry`: 생성할 심볼릭 링크의 dentry 구조체 포인터
- `const char *symname`: 심볼릭 링크가 가리키는 경로명

### Use Case:

심볼릭 링크 생성 이벤트를 추적하여 잠재적인 보안 취약점이나 비정상적인 동작을 감지할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_link

하드 링크가 생성될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct dentry *old_dentry`: 기존 파일의 dentry 구조체 포인터
- `struct inode *dir`: 새로운 부모 디렉토리의 inode 구조체 포인터
- `struct dentry *new_dentry`: 새로운 하드 링크의 dentry 구조체 포인터
- `struct inode **delegated_inode`: 위임된 inode에 대한 이중 포인터 (옵션)

### Use Case:

하드 링크 생성을 모니터링하여 파일 시스템의 무결성을 보장하고, 무단 복사를 방지할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_fsync

파일이 스토리지와 동기화될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct file *file`: 동기화할 파일의 파일 구조체 포인터
- `loff_t start`: 동기화 시작 위치
- `loff_t end`: 동기화 종료 위치
- `int datasync`: 데이터 동기화 여부 (메타데이터 제외 여부)

### Use Case:

파일 동기화 이벤트를 추적하여 데이터 플러시 이벤트를 감지하고 성능 튜닝이나 시스템 감사를 수행할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_getattr

파일 속성이 조회될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct path *path`: 대상 파일의 경로 구조체 포인터
- `struct kstat *stat`: 파일 상태 정보를 저장할 구조체 포인터
- `u32 request_mask`: 요청된 정보 마스크
- `unsigned int flags`: 추가 플래그

### Use Case:

속성 접근을 모니터링하여 파일 시스템의 메타데이터 접근 패턴을 분석하거나 보안 감사를 수행할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_setattr

파일 속성이 수정될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct user_namespace *mnt_userns`: 사용자 네임스페이스
- `struct dentry *dentry`: 대상 파일의 dentry 구조체 포인터
- `struct iattr *attr`: 변경할 속성 정보를 담은 구조체 포인터

### Use Case:

속성 수정 이벤트를 추적하여 파일 메타데이터의 무단 변경을 감지하고 보안성을 높일 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## vfs_truncate

파일이 잘릴 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct path *path`: 대상 파일의 경로 구조체 포인터
- `loff_t length`: 잘린 후의 파일 길이

### Use Case:

파일 잘림 작업을 추적하여 잠재적인 데이터 손실이나 비정상적인 활동을 감지하고 예방할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## do_filp_open

커널에서 파일이 열릴 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `int dfd`: 파일 디스크립터 (디렉토리 파일 디스크립터)
- `const char *pathname`: 열려는 파일의 경로명
- `const struct open_flags *op`: 파일 열기 플래그 구조체

### Use Case:

커널 수준에서의 파일 열기 이벤트를 추적하여 파일 시스템 접근을 더 낮은 수준에서 감사하고 분석할 때 사용

### Return Value:

- 성공 시 파일 구조체 포인터 반환
- 실패 시 오류 포인터 반환 (ERR_PTR 매크로 사용)

### Manual Page:

없음

---

## do_unlinkat

`unlinkat` 시스템 호출을 통해 파일이나 디렉토리가 제거될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `int dfd`: 파일 디스크립터 (디렉토리 파일 디스크립터)
- `struct filename *name`: 삭제할 파일의 이름 구조체 포인터
- `int flag`: 추가 플래그 (AT_REMOVEDIR 등)

### Use Case:

`unlinkat` 시스템 호출을 통한 파일 또는 디렉토리 삭제를 모니터링하여 보안 감사나 시스템 활동 추적에 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## d_instantiate

`dentry`가 인스턴스화될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct dentry *dentry`: 인스턴스화될 dentry 구조체 포인터
- `struct inode *inode`: 연결할 inode 구조체 포인터

### Use Case:

`inode`와 `dentry` 간의 관계 형성 이벤트를 추적하여 `inode`의 수명 주기와 파일 시스템 동작을 이해하고 분석할 때 사용

### Return Value:

- 반환 값 없음 (void)

### Manual Page:

없음

---

## do_mkdirat

`mkdirat` 시스템 호출을 통해 디렉토리가 생성될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `int dfd`: 파일 디스크립터 (디렉토리 파일 디스크립터)
- `struct filename *pathname`: 생성할 디렉토리의 이름 구조체 포인터
- `umode_t mode`: 디렉토리의 모드(bits)

### Use Case:

`mkdirat` 시스템 호출을 통한 디렉토리 생성 작업을 추적하여 파일 시스템 활동을 모니터링하고 보안성을 높일 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## do_renameat2

`renameat2` 시스템 호출을 통해 파일이나 디렉토리의 이름이 변경될 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `int olddfd`: 기존 디렉토리의 파일 디스크립터
- `struct filename *oldname`: 기존 파일의 이름 구조체 포인터
- `int newdfd`: 새로운 디렉토리의 파일 디스크립터
- `struct filename *newname`: 새로운 파일의 이름 구조체 포인터
- `unsigned int flags`: 리네임 플래그

### Use Case:

`renameat2` 시스템 호출을 통한 이름 변경 작업을 모니터링하여 파일 및 디렉토리 변경 사항을 추적하고 감사할 때 사용

### Return Value:

- 성공 시 `0` 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음

---

## generic_write_checks

파일에 쓰기 전에 권한과 조건을 확인할 때 호출됩니다.

### LIBRARY:

커널 내부 함수

### Arguments:

- `struct kiocb *iocb`: 입출력 제어 블록 구조체 포인터
- `struct iov_iter *from`: 데이터 소스에 대한 입출력 벡터
- `ssize_t *pos`: 파일 내에서의 위치 포인터
- `size_t count`: 쓰기 바이트 수

### Use Case:

파일 쓰기 전에 수행되는 권한 검사와 조건 확인을 모니터링하여 잠재적인 보안 문제나 비정상적인 동작을 감지할 때 사용

### Return Value:

- 성공 시 양수의 쓰기 가능 바이트 수 반환
- 실패 시 음수의 오류 코드 반환

### Manual Page:

없음
