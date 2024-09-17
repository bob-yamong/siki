# 네트워크 tracepoint

생성자: 창현 이
ID: ASSET-55
태그: eBPF, 내부자료
최종 편집 일시: 2024년 9월 18일 오전 1:35

---

# SYSCALLS

## 소켓 생성

| [SOCKET](http://man7.org/linux/man-pages/man2/socket.2.html) | 41 | Create an endpoint for communication |
| --- | --- | --- |
| [SOCKETPAIR](http://man7.org/linux/man-pages/man2/socketpair.2.html) | 53 | Create a pair of connected sockets |
| [SETSOCKOPT](http://man7.org/linux/man-pages/man2/setsockopt.2.html) | 54 | Set options on sockets |
| [GETSOCKOPT](http://man7.org/linux/man-pages/man2/getsockopt.2.html) | 55 | Get options on sockets |
| [GETSOCKNAME](http://man7.org/linux/man-pages/man2/getsockname.2.html) | 51 | Get socket name |
| [GETPEERNAME](http://man7.org/linux/man-pages/man2/getpeername.2.html) | 52 | Get name of connected peer socket |
| [BIND](http://man7.org/linux/man-pages/man2/bind.2.html) | 49 | Bind a name to a socket |
| [LISTEN](http://man7.org/linux/man-pages/man2/listen.2.html) | 50 | Listen for connections on a socket |
| [ACCEPT](http://man7.org/linux/man-pages/man2/accept.2.html) | 43 | Accept a connection on a socket |
| [ACCEPT4](http://man7.org/linux/man-pages/man2/accept4.2.html) | 288 | Accept a connection on a socket |
| [CONNECT](http://man7.org/linux/man-pages/man2/connect.2.html) | 42 | Initiate a connection on a socket |
| [SHUTDOWN](http://man7.org/linux/man-pages/man2/shutdown.2.html) | 48 | Shut down part of a full-duplex connection |
1. tracepoint/syscalls/sys_enter_socket
    1. 역할
        
        통신을 위한 엔드포인트 생성
        
    2. 인자
    int socket(int domain, int type, int protocol)
    3. 사용자 행위
        
        네트워크 연결이 필요한 모든 행동이 시작할 때
        
    4. return 값
        
        새 소켓에 대한 파일 디스크립터를 반환 → 이를 통해 소켓에 대한 입출력 작업 수행 가능
        
    
2. tracepoint/syscalls/sys_enter_socketpair
    1. 역할
        
        연결된 소켓 쌍을 생성
        
    2. 인자
    int socketpair(int domain, int type, int protocol, int sv[2])
    3. 사용자 행위
        
        프로세스간 통신(IPC)를 위한 연결된 소켓을 생성
        
        탐지를 피해 내부통신을 수행할 때
        
    
3. tracepoint/syscalls/sys_enter_setsockopt
    1. 역할
        
        소켓 옵션 설정
        
    2. 인자
    int setsockopt(int sockfd, int level, int optname, const void optval[.optlen], socklen_t optlen)
    1. 사용자 행위
    
4. tracepoint/syscalls/sys_enter_getsockopt
    1. 역할
        
        소켓 옵션 가져오기
        
    2. 인자
        
        int getsockopt(int sockfd, int level, int optname, void optval[restrict *.optlen], socklen_t *restrict optlen)
        
    3. 사용자 행위
        
        현재 네트워크 설정을 확인할 때
        
        ex) 수신 버퍼 크기 확인, TCP keep-alive 설정 확인 
        

1. tracepoint/syscalls/sys_enter_getsockname
    1. 역할
        
        소켓 이름 가져오기(소켓이 바인딩 된 로컬 네트워크 정보를 얻기)
        
    2. 인자
    int getsockname(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict addrlen)
    3. 사용자 행위
        
        시스템의 네트워크 구성을 파악하려 할 때
        
        ex)  IP주소나 바인딩 된 포트를 알 수 있음
        

1. tracepoint/syscalls/sys_enter_getpeername
    1. 역할
        
        연결된 피어 소켓의 이름 가져오기(연결된 peer의 네트워크 정보 얻기)
        
    2. 인자
    int getpeername(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict addrlen)
    3. 사용자 행위
        
        peer 시스템의 네트워크 구성을 파악하려 할 때
        
        ex) IP주소나 바인딩 된 포트를 알 수 있음
        

1. tracepoint/syscalls/sys_enter_bind
    1. 역할 
        
        네트워크 연결이 이루어지기 직전에 소켓을 특정 IP와 포트에 바인딩하기 위해 호출
        
    2. 인자
    int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen)
    3. 사용자 행위
        
        모든 네트워크 연결을 필요로 하는 행위를 할 때
        
    
2. tracepoint/syscalls/sys_enter_listen
    1. 역할
        
        소켓 연결 요청을 수신
        
    2. 인자
    int listen(int sockfd, int backlog)
    3. 사용자 행위
        
        네트워크 연결을 하려 할 때 요청을 받는 쪽이 이 요청을 수신
        

1. tracepoint/syscalls/sys_enter_accept
    1. 역할
        
        소켓 연결을 수락
        
    2. 인자
    int accept(int sockfd, struct sockaddr *_Nullable restrict addr, socklen_t *_Nullable restrict addrlen)
    3. 사용자 행위
        
        네트워크 연결을 하려 할 때 네트워크 연결 요청을 수락
        

1. tracepoint/syscalls/sys_enter_accept4
    1. 역할
    소켓 연결을 수락
    2. 인자
    int accept4(int sockfd, struct sockaddr *_Nullable restrict addr, socklen_t *_Nullable restrict addrlen, int flags)
    3. 사용자 행위
        
        네트워크 연결을 하려 할 때 네트워크 연결 요청을 수락
        

1. tracepoint/syscalls/sys_enter_connect
    1. 역할
    소켓 연결을 시작
    2. 인자
    int connect(int sockfd, const struct sockaddr * addr, socklen_t addrlen)
    3. 사용자 행위
    외부 서버와 연결을 시도할 때

1. tracepoint/syscalls/sys_enter_shutdown
    1. 역할
    소켓 연결의 일부나 전부를 종료
    2. 인자
    int shutdown(int sockfd, int how);
    3. 사용자 행위
    네트워크 통신을 끝낼 때

사용 예시(커널에서 정의된 구조체를 사용하기 위해서는 vmlinux.h을 참조해야 됨)

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

```

## 송수신

| [RECVFROM](http://man7.org/linux/man-pages/man2/recvfrom.2.html) | 45 | Receive a message from a socket |
| --- | --- | --- |
| [RECVMSG](http://man7.org/linux/man-pages/man2/recvmsg.2.html) | 47 | Receive a message from a socket |
| [RECVMMSG](http://man7.org/linux/man-pages/man2/recvmmsg.2.html) | 299 | Receive multiple messages from a socket |
| [SENDTO](http://man7.org/linux/man-pages/man2/sendto.2.html) | 44 | Send a message on a socket |
| [SENDMSG](http://man7.org/linux/man-pages/man2/sendmsg.2.html) | 46 | Send a message on a socket |
| [SENDMMSG](http://man7.org/linux/man-pages/man2/sendmmsg.2.html) | 307 | Send multiple messages on a socket |

## 네이밍

| [SETHOSTNAME](http://man7.org/linux/man-pages/man2/sethostname.2.html) | 170 | Set hostname |
| --- | --- | --- |
| [SETDOMAINNAME](http://man7.org/linux/man-pages/man2/setdomainname.2.html) | 171 | Set NIS domain name |

# NET

# SOCK

# TCP

# XDP

# SKB

# 참고

[https://linasm.sourceforge.net/docs/syscalls/network.php#socket](https://linasm.sourceforge.net/docs/syscalls/network.php#socket)

---