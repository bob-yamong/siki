# Network-Related tracepoint in Linux
네트워크 조작과 관련된 tracepoint 정리 문서

## Syscalls

### socket

> Create an endpoint for communication

통신을 위한 엔드포인트 생성

**Use Case**: 네트워크 연결이 필요한 모든 행위가 시작할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int domain` 
- `int type`
- `int protocol`

**Return Value**:
- File descriptor for the new socket on success
- -1 on failure

---

### socketpair

> Create a pair of connected sockets

연결된 소켓 쌍을 생성

**Use Case**: 프로세스간 통신(IPC)을 하거나 내부 통신을 수행할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int domain`
- `int type`
- `int protocol`
- `int sv[2]`

**Return Value**:
- 0 on success
- -1 on failure

---

### setsockopt

> Set options on sockets

소켓 옵션 설정

**Use Case**: 현재 네트워크 설정을 변경할 때(버퍼 크기 변경, 브로드캐스트 허용, 보안 설정 비활성화 등)

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `int level`
- `int optname` 
- `const void optval[.optlen]`
- `socklen_t optlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### getsockopt

> Get options on sockets

소켓 옵션 가져오기

**Use Case**: 현재 네트워크 설정을 확인할 때(수신 버퍼 크기 확인, TCP keep-alive 설정 확인)

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `int level`
- `int optname` 
- `void optval[restrict *.optlen]`
- `socklen_t *restrict optlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### getsockname

> Get socket name

소켓 이름 가져오기(소켓이 바인딩 된 로컬 네트워크 정보 얻기)

**Use Case**: 시스템의 네트워크 구성을 파악하려 할 때(IP 주소나 바인딩 된 포트를 알 수 있음)

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct sockaddr *restrict addr`
- `socklen_t *restrict addrlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### getpeername

> Get name of connected peer socket

연결된 피어 소켓의 이름 가져오기(연결된 peer의 네트워크 정보 얻기)

**Use Case**: Peer 시스템의 네트워크 구성을 파악하려 할 때(peer의 IP 주소나 바인딩 된 포트를 알 수 있음)

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct sockaddr *restrict addr`
- `socklen_t *restrict addrlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### bind

> Bind a name to a socket

네트워크 연결이 이루어지기 직전에 소켓을 특정 IP와 포트에 바인딩하기 위해 호출

**Use Case**: 네트워크 연결을 필요로 하는 모든 행위를 할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `const struct sockaddr *addr`
- `socklen_t addrlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### listen

> Listen for connections on a socket

소켓 연결 요청을 수신

**Use Case**: 네트워크 연결을 위한 요청을 받을 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `int backlog`

**Return Value**:
- 0 on success
- -1 on failure

---

### accept

> Accept a connection on a socket

소켓 연결을 수락

**Use Case**: 네트워크 연결을 위한 네트워크 연결 요청을 수락할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct sockaddr *_Nullable restrict addr`
- `socklen_t *_Nullable restrict addrlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### accept4

> Accept a connection on a socket

소켓 연결을 수락

**Use Case**: 네트워크 연결을 위한 네트워크 연결 요청을 수락할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct sockaddr *_Nullable restrict addr`
- `socklen_t *_Nullable restrict addrlen`
- `int flags`

**Return Value**:
- 0 on success
- -1 on failure

---

### connect

> Initiate a connection on a socket

소켓 연결을 시작

**Use Case**: 네트워크 연결을 시도할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `const struct sockaddr * addr` 
- `socklen_t addrlen`

**Return Value**:
- 0 on success
- -1 on failure

---

### shutdown

> Shut down part of a full-duplex connection

소켓 연결의 일부나 전부를 종료

**Use Case**: 네트워크 통신을 종료할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `int how`

**Return Value**:
- 0 on success
- -1 on failure

---

### recv

> Receive a message from a socket

소켓에서 메시지(데이터) 받기 → 송신자에 대한 정보 모름

**Use Case**: 연결지향형 통신(TCP 통신)할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `void buf[.len]`
- `size_t len`
- `int flags`

**Return Value**:
- 0 or number of bytes received on success
- -1 on failure

---

### recvfrom

> Receive a message from a socket

소켓에서 메시지(데이터) 받기 → 송신자 정보 포함

**Use Case**: 비연결지향형 통신(UDP 통신)할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `void buf[restrict .len]`
- `size_t len`
- `int flags`
- `struct sockaddr *_Nullable restrict src_addr`
- `socklen_t *_Nullable restrict addrlen`

**Return Value**:
- 0 or number of bytes received on success
- -1 on failure

---

### recvmsg

> Receive a message from a socket

소켓에서 메시지(데이터) 받기 → 부가적인 제어 정보 및 복잡한 정보를 같이 수신 가능

**Use Case**: 상세한 네트워크 활동을 분석할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct msghdr *msg`
- `int flags`

**Return Value**:
- 0 or number of bytes received on success
- -1 on failure

---

### recvmmsg

> Receive multiple messages on a socket

소켓에서 여러개의 메시지(데이터) 받기 → recvmsg의 확장 버전

**Use Case**: 실시간 스트리밍 서비스, 로그 수집 서버, 네트워크 모니터링 활동 등과 같이 대량의 패킷을 처리해야하는 서비스

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct mmsghdr *msgvec`
- `unsigned int vlen`
- `int flags`
- `struct timespec *timeout`

**Return Value**:
- Number of messages received in msgvec on success
- -1 on failure

---

### send

> Send a message on a socket

소켓에 메시지(데이터) 보내기 → 이미 연결된 소켓에서

**Use Case**: 연결지향형 통신(TCP 통신)할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `const void buf[.len]`
- `size_t len`
- `int flags`

**Return Value**:
- Number of bytes sent on success
- -1 on failure

---

### sendto

> Send a message on a socket

소켓에 메시지(데이터) 보내기 → 데이터와 함께 목적지 설정 가능

**Use Case**: 비연결지향형 통신(UDP 통신)할 때

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `const void buf[.len]`
- `size_t len`
- `int flags`
- `const struct sockaddr *dest_addr`
- `socklen_t addrlen`

**Return Value**:
- Number of bytes sent on success
- -1 on failure

---

### sendmsg

> Send a message on a socket

소켓에 메시지(데이터) 보내기 → 여러 버퍼의 데이터를 한번에 전송할 수 있음

**Use Case**: 파일 데이터와 메타 데이터를 같이 전송할 때, DB 시스템이 복잡한 쿼리문을 전달할 때 등

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `const struct msghdr *msg`
- `int flags`

**Return Value**:
- Number of bytes sent on success
- -1 on failure

---

### sendmmsg

> Send multiple messages on a socket

소켓에 여러개의 메시지(데이터) 보내기 → sendmsg의 확장버전

**Use Case**: 실시간 스트리밍 서비스, 로그 수집 서버, 네트워크 모니터링 활동 등과 같이 대량의 패킷을 처리해야하는 서비스

**LIBRARY**: libc/sys/socket.h

**Arguments**: 
- `int sockfd`
- `struct mmsghdr *msgvec`
- `unsigned int vlen`
- `int flags`

**Return Value**:
- Number of messages sent in msgvec on success
- -1 on failure

---

### sethostname

> Set hostname

시스템의 호스트 이름을 설정할 때 사용

**Use Case**: 시스템 관리자가 호스트 이름을 변경할 때, 새로운 가상머신이나 컨테이너를 생성할 때 등

**LIBRARY**: libc/unistd.h

**Arguments**: 
- `const char *name`
- `size_t len`

**Return Value**:
- 0 on success
- -1 on failure

---

### setdomainname

> Set NIS domain name

시스템의 NIS(Network Information Service) 도메인 이름을 설정할 때 사용

**Use Case**: 시스템 관리자가 네트워크 구성을 변경하면서 도메인 이름을 변경하거나 NIS 환경을 변경할 때, 새로운 가상머신이나 컨테이너를 생성할 때 등

**LIBRARY**: libc/unistd.h

**Arguments**: 
- `const char *name`
- `size_t len`

**Return Value**:
- 0 on success
- -1 on failure

---

## Usage

```c
SEC("tracepoint/syscalls/sys_enter_socket")
int trace_sys_enter_socket(struct trace_event_raw_sys_enter *ctx)
{
    int domain = (int)ctx->args[0];
    int type = (int)ctx->args[1];
    int protocol = (int)ctx->args[2];
    
    bpf_trace_printk("Enter socket: domain=%d, type=%d, protocol=%d\n", domain, type, protocol);
    return 0;
}

SEC("tracepoint/syscalls/sys_exit_socket")
int trace_sys_exit_socket(struct trace_event_raw_sys_exit *ctx)
{
    long ret = ctx->ret;
    
    if (ret < 0) {
        bpf_trace_printk("Exit socket: failed with error code %ld\n", ret);
    } else {
        bpf_trace_printk("Exit socket: success, fd=%ld\n", ret);
    }
    
    return 0;
}