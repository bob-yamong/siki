# Filesystem-Related LSM Hooks in Linux

### 1. `security_inode_permission`

- **설명**: 파일 접근 권한을 확인할 때 트리거됩니다.
- **Use Case**: Enforce security policies on file access (read, write, execute).

### 2. `security_inode_create`

- **설명**: 새로운 inode가 생성될 때 트리거됩니다.
- **Use Case**: Monitor or restrict file creation events (files, directories).

### 3. `security_inode_link`

- **설명**: 하드 링크가 생성될 때 트리거됩니다.
- **Use Case**: Control and monitor hard link creation for security purposes.

### 4. `security_inode_unlink`

- **설명**: 파일 또는 디렉토리가 삭제될 때 트리거됩니다.
- **Use Case**: Audit or restrict file deletion.

### 5. `security_inode_symlink`

- **설명**: 심볼릭 링크가 생성될 때 트리거됩니다.
- **Use Case**: Monitor symbolic link creation to prevent potential security threats.

### 6. `security_inode_mkdir`

- **설명**: 새로운 디렉토리가 생성될 때 트리거됩니다.
- **Use Case**: Control or monitor directory creation events.

### 7. `security_inode_rmdir`

- **설명**: 디렉토리가 삭제될 때 트리거됩니다.
- **Use Case**: Enforce policies on directory removal, especially in critical areas.

### 8. `security_inode_rename`

- **설명**: 파일 또는 디렉토리가 이름이 변경될 때 트리거됩니다.
- **Use Case**: Audit and track renaming operations for security or auditing purposes.

### 9. `security_inode_setattr`

- **설명**: 파일 속성이 수정될 때 트리거됩니다.
- **Use Case**: Monitor or restrict changes to file metadata (permissions, timestamps).

### 10. `security_inode_getattr`

- **설명**: 파일 속성이 조회될 때 트리거됩니다.
- **Use Case**: Track access to file metadata to audit attribute reads.

### 11. `security_file_open`

- **설명**: 파일이 열릴 때 트리거됩니다.
- **Use Case**: Monitor file opening operations for auditing or access control.

### 12. `security_file_permission`

- **설명**: 파일을 읽거나 쓰기 전에 권한을 확인할 때 트리거됩니다.
- **Use Case**: Enforce permissions on file access based on security policies.

### 13. `security_file_ioctl`

- **설명**: 파일에서 ioctl 시스템 호출이 이루어질 때 트리거됩니다.
- **Use Case**: Control or monitor ioctl operations, often used to manage hardware devices.

### 14. `security_file_fcntl`

- **설명**: 파일에서 fcntl 시스템 호출이 이루어질 때 트리거됩니다.
- **Use Case**: Enforce policies on file control operations such as locking or setting file descriptors.

### 15. `security_file_set_fowner`

- **설명**: 파일 소유자를 설정할 때 트리거됩니다.
- **Use Case**: Monitor or control changes to file ownership.

### 16. `security_file_lock`

- **설명**: 파일이 잠기거나 잠금 해제될 때 트리거됩니다.
- **Use Case**: Audit or restrict file locking operations, useful in multi-user environments.

### 17. `security_file_receive`

- **설명**: UNIX 소켓을 통해 파일을 받을 때 트리거됩니다.
- **Use Case**: Control the transfer of file descriptors over sockets.

### 18. `security_path_mkdir`

- **설명**: 경로 기반 API를 사용하여 디렉토리가 생성될 때 트리거됩니다.
- **Use Case**: Monitor or control directory creation using path-based operations.

### 19. `security_path_rmdir`

- **설명**: 경로 기반 API를 사용하여 디렉토리가 제거될 때 트리거됩니다.
- **Use Case**: Enforce security policies on directory deletion.

# Additional Filesystem-Related LSM Hooks in Linux

### 1. `inode_post_setattr`

- **설명**: inode 속성이 설정된 후 호출됩니다.
- **Use Case**: Useful for enforcing post-modification checks or logging.

### 2. `inode_setxattr`

- **설명**: inode에 확장 속성(xattr)이 설정될 때 호출됩니다.
- **Use Case**: Used to control or log modifications to extended attributes like SELinux labels.

### 3. `inode_removexattr`

- **설명**: inode에서 확장 속성(xattr)이 제거될 때 호출됩니다.
- **Use Case**: Allows monitoring or blocking the removal of important xattrs.

### 4. `inode_set_acl`

- **설명**: inode에 ACL(Access Control List)이 설정될 때 호출됩니다.
- **Use Case**: Enforces ACL policies or tracks changes to file permissions.

### 5. `file_permission`

- **설명**: 파일 접근 권한이 확인될 때 호출됩니다.
- **Use Case**: Enforces or audits access control policies.

### 6. `file_mprotect`

- **설명**: 메모리 보호 작업 중에 호출됩니다 (예: 메모리 권한 변경).
- **Use Case**: Ensures safe memory protection changes for processes, especially useful for security-focused modules like SELinux.

### 7. `file_receive`

- **설명**: UNIX 도메인 소켓을 통해 파일 디스크립터를 받을 때 호출됩니다.
- **Use Case**: Controls or audits the sharing of file descriptors between processes.

### 8. `task_alloc`

- **설명**: 새로운 태스크가 할당될 때 (프로세스 생성 시) 호출됩니다.
- **Use Case**: Monitors task creation for potential malicious activities.

### 9. `task_free`

- **설명**: 태스크가 해제될 때 (프로세스 종료 시) 호출됩니다.
- **Use Case**: Ensures any necessary cleanup and auditing is performed when a task terminates.

### 10. `file_ioctl`

- **설명**: 파일에서 ioctl 작업이 수행될 때 호출됩니다.
- **Use Case**: Controls or logs system calls that interact with hardware devices via ioctl.

### 11. `file_post_open`

- **설명**: 파일이 열린 후에 호출됩니다.
- **Use Case**: Useful for enforcing access policies or logging after the file open event.
