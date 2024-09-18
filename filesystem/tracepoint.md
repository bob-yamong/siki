# Filesystem-Related Tracepoints in Linux

> **Warning**  
> This part of the docs is incomplete, contributions are very welcome.

이 문서는 불완전합니다. 기여를 환영합니다.

---

## sys_enter_open

`open` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `const char *filename`: 열려는 파일의 경로명
- `int flags`: 파일 열기 플래그
- `umode_t mode`: 파일 모드(bits)

### Use Case:

파일 열기 요청을 모니터링하여 어떤 파일이 언제 열리는지 추적할 때 사용.  
보안 감사나 파일 접근 패턴 분석에 활용.

### Return Value:

해당 없음 (트레이스포인트는 반환 값을 가지지 않습니다)

### Manual Page:

없음

---

## sys_exit_open

`open` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `open` 시스템 호출의 반환 값 (파일 디스크립터 또는 오류 코드)

### Use Case:

파일 열기 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
파일 접근 오류의 원인을 분석하는 데 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_read

`read` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `unsigned int fd`: 읽을 파일의 파일 디스크립터
- `char __user *buf`: 데이터가 저장될 사용자 공간 버퍼
- `size_t count`: 읽을 바이트 수

### Use Case:

파일 읽기 요청을 모니터링하여 데이터 접근 패턴을 분석하거나 보안 감사를 수행할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_read

`read` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `read` 시스템 호출의 반환 값 (읽은 바이트 수 또는 오류 코드)

### Use Case:

파일 읽기 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
데이터 유출 감지나 성능 분석에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_write

`write` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `unsigned int fd`: 쓸 파일의 파일 디스크립터
- `const char __user *buf`: 사용자 공간의 데이터 버퍼
- `size_t count`: 쓸 바이트 수

### Use Case:

파일 쓰기 요청을 모니터링하여 데이터 변경 활동을 추적하거나 보안 감사를 수행할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_write

`write` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `write` 시스템 호출의 반환 값 (쓴 바이트 수 또는 오류 코드)

### Use Case:

파일 쓰기 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
데이터 무결성 검증이나 성능 분석에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_unlink

`unlink` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `const char *pathname`: 삭제할 파일의 경로명

### Use Case:

파일 삭제 요청을 모니터링하여 무단 파일 제거를 감지하거나 시스템 활동을 감사할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_unlink

`unlink` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `unlink` 시스템 호출의 반환 값 (0 또는 오류 코드)

### Use Case:

파일 삭제 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
시스템 보안 강화나 문제 해결에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_mkdir

`mkdir` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `const char *pathname`: 생성할 디렉토리의 경로명
- `umode_t mode`: 디렉토리의 모드(bits)

### Use Case:

디렉토리 생성 요청을 모니터링하여 파일 시스템 활동을 추적하거나 보안 감사를 수행할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_mkdir

`mkdir` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `mkdir` 시스템 호출의 반환 값 (0 또는 오류 코드)

### Use Case:

디렉토리 생성 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
파일 시스템 무결성 검증에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_rename

`rename` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `const char *oldname`: 기존 파일 또는 디렉토리의 경로명
- `const char *newname`: 새로운 경로명

### Use Case:

이름 변경 요청을 모니터링하여 파일 및 디렉토리 변경 사항을 추적하거나 감사할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_rename

`rename` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `rename` 시스템 호출의 반환 값 (0 또는 오류 코드)

### Use Case:

이름 변경 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
시스템 변경 사항의 이력을 관리하거나 문제 해결에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_stat

`stat` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `const char *filename`: 정보를 조회할 파일의 경로명
- `struct stat *statbuf`: 파일 상태 정보를 저장할 구조체 포인터

### Use Case:

파일 메타데이터 접근을 모니터링하여 속성 조회 활동을 감사하거나 분석할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_stat

`stat` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `stat` 시스템 호출의 반환 값 (0 또는 오류 코드)

### Use Case:

메타데이터 조회 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
파일 시스템 상태 검증이나 문제 해결에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_fsync

`fsync` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `unsigned int fd`: 동기화할 파일의 파일 디스크립터

### Use Case:

파일 동기화 요청을 모니터링하여 데이터 플러시 이벤트를 추적하거나 성능 분석을 수행할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_fsync

`fsync` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `fsync` 시스템 호출의 반환 값 (0 또는 오류 코드)

### Use Case:

동기화 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
데이터 무결성 보장이나 성능 튜닝에 활용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_enter_openat

`openat` 시스템 호출이 진입할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `int dfd`: 기준 디렉토리의 파일 디스크립터
- `const char *filename`: 열려는 파일의 경로명
- `int flags`: 파일 열기 플래그
- `umode_t mode`: 파일 모드(bits)

### Use Case:

상대 경로를 사용하는 파일 열기 요청을 모니터링하여 파일 시스템 접근 패턴을 분석하거나 보안 감사를 수행할 때 사용.

### Return Value:

해당 없음

### Manual Page:

없음

---

## sys_exit_openat

`openat` 시스템 호출이 종료할 때 호출됩니다.

### LIBRARY:

커널 내부 트레이스포인트

### Arguments:

- `long ret`: `openat` 시스템 호출의 반환 값 (파일 디스크립터 또는 오류 코드)

### Use Case:

파일 열기 결과를 확인하여 성공 여부나 오류를 추적할 때 사용.  
파일 접근 오류의 원인을 분석하는 데 활용.

### Return Value:

해당 없음

### Manual Page:

없음
