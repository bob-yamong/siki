# Network-Related Tracepoint in Linux
네트워크 조작과 관련된 tracepoint 정리 문서

## Table of Content

1. **System calls**
    - [Socket operations](#socket-operations)
    - [Send/Receive](#sendreceive)
    - [Naming](#naming)

2. **Sub system**
    - [Net](#net)
    - [Sock](#sock)
    - [TCP](#tcp)
    - [UDP](#udp)
    - [XDP](#xdp)

3. **Usage**

## Socket operations

### socket

> 통신을 위한 엔드포인트 생성

**Use Case**: 네트워크 연결이 필요한 모든 행위가 시작할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int domain`: 통신에 사용될 프로토콜
- `int type`: 소켓 통신의 유형
- `int protocol`: 특정 프로토콜을 지정(일반적인 경우에는 0으로 domain, type에 따름)

**Return Value**:
- **성공 시**: 새로운 소켓의 파일 디스크립터
- **실패 시**: -1을 반환

---

### socketpair

연결된 소켓 쌍을 생성

**Use Case**: 프로세스간 통신(IPC)을 하거나 내부 통신을 수행할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int domain`: 통신에 사용될 프로토콜
- `int type`: 소켓 통신의 유형
- `int protocol`: 특정 프로토콜을 지정(일반적인 경우에는 0으로 domain, type에 따름)
- `int sv[2]`: 생성된 소켓 쌍의 파일 디스크립터를 저장

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### setsockopt

> 소켓 옵션 설정

**Use Case**: 현재 네트워크 설정을 변경할 때(버퍼 크기 변경, 브로드캐스트 허용, 보안 설정 비활성화 등)

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 옵션을 설정할 소켓의 파일 디스크립터
- `int level`: 옵션이 적용될 프로토콜 레벨
- `int optname`: 설정할 옵션의 이름
- `const void *optval`: 옵션에 설정할 값을 가리키는 포인터
- `socklen_t optlen`: optval 구조체의 크기

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### getsockopt

> 소켓 옵션 조회

**Use Case**: 현재 네트워크 설정을 확인할 때(수신 버퍼 크기 확인, TCP keep-alive 설정 확인)

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 옵션을 조회할 소켓의 파일 디스크립터
- `int level`: 옵션이 적용된 프로토콜 레벨
- `int optname`: 조회할 옵션의 이름
- `void *optval`: 옵션 값을 저장할 버퍼를 가리키는 포인터
- `socklen_t *optlen`: optval 구조체의 크기를 가리키는 포인터

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### getsockname

> 소켓 이름 조회(소켓이 바인딩 된 로컬 네트워크 정보 얻기)

**Use Case**: 시스템의 네트워크 구성을 파악하려 할 때(IP 주소나 바인딩 된 포트를 알 수 있음)

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 정보를 조회할 소켓의 파일 디스크립터
- `struct sockaddr *addr`: 소켓 주소 정보를 저장할 구조체 포인터
- `socklen_t *addrlen`: addr 구조체의 크기를 가리키는 포인터

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### getpeername

> 연결된 피어 소켓의 이름 조회(연결된 피어의 네트워크 정보 얻기)

**Use Case**: 피어 시스템의 네트워크 구성을 파악하려 할 때(피어의 IP 주소나 바인딩 된 포트를 알 수 있음)

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 정보를 조회할 소켓의 파일 디스크립터
- `struct sockaddr *addr`: 피어 소켓 주소 정보를 저장할 구조체 포인터
- `socklen_t *addrlen`: addr 구조체의 크기를 가리키는 포인터

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### bind

> 네트워크 연결이 이루어지기 직전에 소켓을 특정 IP와 포트에 바인딩하기 위해 호출

**Use Case**: 네트워크 연결을 필요로 하는 모든 행위를 할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 바인딩할 소켓의 파일 디스크립터
- `const struct sockaddr *addr`: 바인딩할 주소 정보를 담은 구조체 포인터
- `socklen_t addrlen`: addr 구조체의 크기

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### listen

> 소켓 연결 요청을 수신

**Use Case**: 네트워크 연결을 위한 요청을 받을 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 리스닝할 소켓의 파일 디스크립터
- `int backlog`: 대기 큐의 최대 길이

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### accept

> 소켓 연결을 수락

**Use Case**: 네트워크 연결을 위한 네트워크 연결 요청을 수락할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 리스닝 중인 소켓의 파일 디스크립터
- `struct sockaddr *addr`: 연결된 클라이언트의 주소 정보를 저장할 구조체 포인터
- `socklen_t *addrlen`: addr 구조체의 크기를 가리키는 포인터

**Return Value**:
- **성공 시**: 수락된 소켓의 파일 디스크립터를 반환
- **실패 시**: -1을 반환

---

### accept4

> 소켓 연결을 수락

**Use Case**: 네트워크 연결을 위한 네트워크 연결 요청을 수락할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 리스닝 중인 소켓의 파일 디스크립터
- `struct sockaddr *addr`: 연결된 클라이언트의 주소 정보를 저장할 구조체 포인터
- `socklen_t *addrlen`: addr 구조체의 크기를 가리키는 포인터
- `int flags`: 추가적인 소켓 옵션

**Return Value**:
- **성공 시**: 수락된 소켓의 파일 디스크립터를 반환
- **실패 시**: -1을 반환

---

### connect

> 소켓 연결을 시작

**Use Case**: 네트워크 연결을 시도할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 연결할 소켓의 파일 디스크립터
- `const struct sockaddr * addr`: 연결할 서버의 주소 정보를 담은 구조체 포인터
- `socklen_t addrlen`: addr 구조체의 크기

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

### shutdown

> 소켓 연결의 일부나 전부를 종료

**Use Case**: 네트워크 통신을 종료할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 종료할 소켓의 파일 디스크립터
- `int how`: 종료 모드

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

## Send/Receive

### recv -> tracepoint에 없음

> 소켓에서 메시지(데이터) 받기 → 송신자에 대한 정보 모름

**Use Case**: 연결지향형 통신(TCP 통신)할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 수신할 소켓의 파일 디스크립터
- `void *buf`: 수신한 데이터를 저장할 버퍼
- `size_t len`: 버퍼의 크기
- `int flags`: 수신 옵션

**Return Value**:
- **성공 시**: 0 또는 수신된 바이트 수를 반환
- **실패 시**: -1을 반환

---

### recvfrom

> 소켓에서 메시지(데이터) 받기 → 송신자 정보 포함

**Use Case**: 비연결지향형 통신(UDP 통신)할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 수신할 소켓의 파일 디스크립터
- `void *buf`: 수신한 데이터를 저장할 버퍼
- `size_t len`: 버퍼의 크기
- `int flags`: 수신 옵션
- `struct sockaddr *src_addr`: 송신자의 주소 정보를 저장할 구조체 포인터
- `socklen_t *addrlen`: src_addr 구조체의 크기를 가리키는 포인터

**Return Value**:
- **성공 시**: 0 또는 수신된 바이트 수를 반환
- **실패 시**: -1을 반환

---

### recvmsg

> 소켓에서 메시지(데이터) 받기 → 부가적인 제어 정보 및 복잡한 정보를 같이 수신 가능

**Use Case**: 상세한 네트워크 활동을 분석할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 수신할 소켓의 파일 디스크립터
- `struct msghdr *msg`: 메시지 헤더 구조체 포인터(수신 버퍼, 주소 정보, 제어 정보 등을 포함)
- `int flags`: 수신 옵션

**Return Value**:
- **성공 시**: 0 또는 수신된 바이트 수를 반환
- **실패 시**: -1을 반환

---

### recvmmsg

> 소켓에서 여러개의 메시지(데이터) 받기 → recvmsg의 확장 버전

**Use Case**: 실시간 스트리밍 서비스, 로그 수집 서버, 네트워크 모니터링 활동 등과 같이 대량의 패킷을 처리해야하는 서비스

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 수신할 소켓의 파일 디스크립터
- `struct mmsghdr *msgvec`: mmsghdr 구조체의 배열(각 구조체는 하나의 메시지에 대한 정보를 포함)
- `unsigned int vlen`: msgvec 배열의 길이
- `int flags`: 수신 옵션
- `struct timespec *timeout`: 수신 대기 시간 제한

**Return Value**:
- **성공 시**: msgvec 안의 수신된 메시지 수를 반환
- **실패 시**: -1을 반환

---

### send -> tracepoint에 없음

> 소켓에 메시지(데이터) 보내기 → 이미 연결된 소켓에서

**Use Case**: 연결지향형 통신(TCP 통신)할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 송신할 소켓의 파일 디스크립터
- `const void *buf`: 송신할 데이터가 저장된 버퍼
- `size_t len`: 송신할 데이터의 길이
- `int flags`: 송신 옵션

**Return Value**:
- **성공 시**: 전송된 바이트 수를 반환
- **실패 시**: -1을 반환

---

### sendto

> 소켓에 메시지(데이터) 보내기 → 데이터와 함께 목적지 설정 가능

**Use Case**: 비연결지향형 통신(UDP 통신)할 때

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 송신할 소켓의 파일 디스크립터
- `const void *buf`: 송신할 데이터가 저장된 버퍼
- `size_t len`: 송신할 데이터의 길이
- `int flags`: 송신 옵션
- `const struct sockaddr *dest_addr`: 수신자의 주소 정보를 담은 구조체 포인터
- `socklen_t addrlen`: dest_addr 구조체의 크기

**Return Value**:
- **성공 시**: 전송된 바이트 수를 반환
- **실패 시**: -1을 반환

---

### sendmsg

> 소켓에 메시지(데이터) 보내기 → 여러 버퍼의 데이터를 한번에 전송할 수 있음

**Use Case**: 파일 데이터와 메타 데이터를 같이 전송할 때, DB 시스템이 복잡한 쿼리문을 전달할 때 등

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 송신할 소켓의 파일 디스크립터
- `const struct msghdr *msg`: 메시지 헤더 구조체 포인터(송신 버퍼, 주소 정보, 제어 정보 등을 포함)
- `int flags`: 송신 옵션

**Return Value**:
- **성공 시**: 전송된 바이트 수를 반환
- **실패 시**: -1을 반환

---

### sendmmsg

> 소켓에 여러개의 메시지(데이터) 보내기 → sendmsg의 확장버전

**Use Case**: 실시간 스트리밍 서비스, 로그 수집 서버, 네트워크 모니터링 활동 등과 같이 대량의 패킷을 처리해야하는 서비스

**LIBRARY**:
- `#include <sys/socket.h>`

**Arguments**: 
- `int sockfd`: 송신할 소켓의 파일 디스크립터
- `struct mmsghdr *msgvec`: mmsghdr 구조체의 배열(각 구조체는 하나의 메시지에 대한 정보를 포함)
- `unsigned int vlen`: msgvec 배열의 길이
- `int flags`: 송신옵션

**Return Value**:
- **성공 시**: msgvec 안의 전송된 메시지 수를 반환
- **실패 시**: -1을 반환

---

## Naming

### sethostname

> 시스템의 호스트 이름을 설정할 때 사용

**Use Case**: 시스템 관리자가 호스트 이름을 변경할 때, 새로운 가상머신이나 컨테이너를 생성할 때 등

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**: 
- `const char *name`: 새로운 호스트 이름을 담고 있는 문자열
- `size_t len`: name 문자열의 길이

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환
 
---

### setdomainname

> 시스템의 NIS(Network Information Service) 도메인 이름을 설정할 때 사용

**Use Case**: 시스템 관리자가 네트워크 구성을 변경하면서 도메인 이름을 변경하거나 NIS 환경을 변경할 때, 새로운 가상머신이나 컨테이너를 생성할 때 등

**LIBRARY**:
- `#include <unistd.h>`

**Arguments**: 
- `const char *name`: 새로운 도메인 이름을 담고 있는 문자열
- `size_t len`: name 문자열의 길이

**Return Value**:
- **성공 시**: 0을 반환
- **실패 시**: -1을 반환

---

## Net

### net_dev_start_xmit

> 네트워크 디바이스(인터페이스)에서 패킷 전송 시작

**Use Case**: 네트워크 스택이 패킷을 실제 네트워크 인터페이스를 통해 전송을 시작할 때

**KERNEL**: 
- Linux Kernel Networking Subsystem

**Arguments**: 
- `const struct sk_buff *skb`: 전송할 패킷 정보가 담긴 소켓 버퍼
- `const struct net_device *dev`: 패킷을 전송할 네트워크 디바이스(인터페이스)

**Traced Information**:
- `name`: 네트워크 인터페이스의 이름
- `queue_mapping`: 패킷이 전송될 하드웨어 큐의 인덱스
- `skbaddr`: 소켓 버퍼(sk_buff) 구조체의 메모리 주소를 가리키는 포인터
- `vlan_tagged`: VLAN 태그가 있는지 여부를 나타내는 불리언 값
- `vlan_proto`: VLAN 프로토콜 타입
- `vlan_tci`: VLAN Tag Control Information(우선순위, VLAN ID 등 포함)
- `protocol`: 네트워크 계층 프로토콜 타입
- `ip_summed`: IP 체크섬 상태
- `len`: 패킷의 총 길이
- `data_len`: 패킷의 비선형 데이터 부분의 길이
- `network_offset`: 네트워크 계층 헤더의 시작 오프셋
- `transport_offset_valid`: 전송 계층 오프셋이 유효한지 여부
- `transport_offset`: 전송 계층 헤더의 시작 오프셋
- `tx_flags`: 전송 관련 플래그
- `gso_size`: GSO(Generic Segmentation Offload)에서 사용할 세그먼트 크기
- `gso_segs`: GSO에서 생성될 세그먼트의 수
- `gso_type`: GSO 유형

**Context**: 
- 일반적으로 send, sendto, sendmsg 등의 시스템 콜이 최종적으로 이 지점까지 패킷을 전달했을 때 호출됨

---

### net_dev_xmit

> 패킷 전송 완료 후 결과

**Use Case**: 네트워크 디바이스(인터페이스)가 패킷 전송을 시도한 후, 그 결과를 추적할 때 사용

**Kernel**: 
- Linux Kernel Networking Subsystem

**Arguments**:
- `struct sk_buff *skb`: 전송된 패킷의 소켓 버퍼
- `int rc`: 전송 시도의 결과 코드
- `struct net_device *dev`: 패킷을 전송한 네트워크 디바이스
- `unsigned int skb_len`: 전송된 패킷의 길이

**Traced Information**:
- `skbaddr`: 전송된 소켓 버퍼의 메모리 주소
- `len`: 전송된 패킷의 길이
- `rc`: 전송 시도의 결과 코드(성공 또는 오류 코드)
- `name`: 패킷을 전송한 네트워크 디바이스의 이름

**Context**:
- 패킷 전송 시도 직후에 호출되어 전송 성공 여부, 전송된 데이터의 크기, 사용된 네트워크 인터페이스 등 중요한 정보를 제공함

---

## Sock

## TCP

## UDP

## XDP

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
