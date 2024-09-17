# Filesystem-Related kprobes in Linux

### 1. `vfs_open`

- **설명**: 파일이 열릴 때 호출됩니다.
- **Use Case**: Monitor file open events in the VFS layer.

### 2. `vfs_read`

- **설명**: 파일에서 데이터를 읽을 때 호출됩니다.
- **Use Case**: Track file read operations for security auditing or monitoring file access.

### 3. `vfs_write`

- **설명**: 파일에 데이터를 쓸 때 호출됩니다.
- **Use Case**: Monitor file write events for security or system performance analysis.

### 4. `vfs_unlink`

- **설명**: 파일이 디렉토리에서 제거될 때 호출됩니다.
- **Use Case**: Track file deletions to detect unauthorized file removal or audit system activity.

### 5. `vfs_mkdir`

- **설명**: 새로운 디렉토리가 생성될 때 호출됩니다.
- **Use Case**: Monitor directory creation events for filesystem activity analysis.

### 6. `vfs_rmdir`

- **설명**: 디렉토리가 삭제될 때 호출됩니다.
- **Use Case**: Track directory removal to detect unauthorized deletions.

### 7. `vfs_rename`

- **설명**: 파일이나 디렉토리가 이름이 변경될 때 호출됩니다.
- **Use Case**: Monitor renaming operations, useful for auditing and tracking changes in the filesystem.

### 8. `vfs_symlink`

- **설명**: 심볼릭 링크가 생성될 때 호출됩니다.
- **Use Case**: Track symbolic link creation, important for detecting potential security exploits or unusual behavior.

### 9. `vfs_link`

- **설명**: 하드 링크가 생성될 때 호출됩니다.
- **Use Case**: Monitor hard link creation to ensure filesystem integrity.

### 10. `vfs_fsync`

- **설명**: 파일이 스토리지와 동기화될 때 호출됩니다.
- **Use Case**: Track file synchronization to detect data flushing events, useful in performance tuning or auditing.

### 11. `vfs_getattr`

- **설명**: 파일 속성이 검색될 때 호출됩니다.
- **Use Case**: Monitor attribute access to detect metadata access patterns in the filesystem.

### 12. `vfs_setattr`

- **설명**: 파일 속성이 수정될 때 호출됩니다.
- **Use Case**: Track attribute modification events, useful for detecting unauthorized changes in file metadata.

### 13. `vfs_truncate`

- **설명**: 파일이 잘릴 때 호출됩니다.
- **Use Case**: Track file truncation operations, useful for detecting potential data loss or unusual activity.

### 14. `do_filp_open`

- **설명**: 커널에서 파일이 열릴 때 호출됩니다.
- **Use Case**: Track kernel-level file open events to audit file system access at a lower level.

### 15. `do_unlinkat`

- **설명**: `unlinkat` 호출을 통해 파일이나 디렉토리가 제거될 때 호출됩니다.
- **Use Case**: Monitor file or directory deletion via `unlinkat` system call.

### 16. `d_instantiate`

- **설명**: dentry가 인스턴스화될 때 호출됩니다.
- **Use Case**: Track inode and dentry relationship events, useful for understanding inode lifecycle.

### 17. `do_mkdirat`

- **설명**: `mkdirat` 호출을 통해 디렉토리가 생성될 때 호출됩니다.
- **Use Case**: Track directory creation via `mkdirat` syscall.

### 18. `do_renameat2`

- **설명**: `renameat2` 호출을 통해 파일이나 디렉토리가 이름이 변경될 때 호출됩니다.
- **Use Case**: Monitor renaming operations for file and directory changes.

### 19. `generic_write_checks`

- **설명**: 파일에 쓰기 전에 권한과 조건을 확인할 때 호출됩니다.
- **Use Case**: Monitor file write checks for potential security concerns.
