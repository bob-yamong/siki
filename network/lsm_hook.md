# Network-Related LSM Hooks in Linux
네트워크 조작과 관련된 lsm hook 정리 문서


### security_socket_create

> Probes when a socket is created.

소켓이 생성될 때 호출됩니다. 특정 프로토콜 패밀리 (예: TCP, UDP 등)의 소켓 생성을 모니터링하는 데 유용합니다.

**Use Case**: Can be used to monitor socket creation for specific protocol families.

**Library**: `security/socket.h`

**Arguments**: 
- `int family`
- `int type`
- `int protocol`
- `int kern`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_connect

> Probes when a socket is connecting to a remote address.

소켓이 원격 주소에 연결될 때 호출됩니다. 외부 네트워크 연결에 대한 정책 적용 및 기록에 유용합니다.

**Use Case**: Enforce or log policy on outgoing network connections.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct sockaddr *address`
- `int addr_len`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_bind

> Probes when a socket is bound to an address.

소켓이 특정 주소에 바인딩될 때 호출됩니다. 사용되는 주소와 포트를 제어하는 데 유용합니다.

**Use Case**: Monitor and control socket bindings to specific addresses and ports.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct sockaddr *address`
- `int addr_len`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_sendmsg

> Probes when a message is sent through a socket.

소켓을 통해 메시지를 보낼 때 호출됩니다. 데이터 유출 방지와 같은 정책 적용에 유용합니다.

**Use Case**: Enforce policy on data sent through a socket.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct msghdr *msg`
- `int size`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_recvmsg

> Probes when a message is received from a socket.

소켓에서 메시지를 받을 때 호출됩니다. 들어오는 트래픽 모니터링 및 수신 데이터에 대한 규칙 적용에 유용합니다.

**Use Case**: Monitor inbound traffic and enforce rules on received data.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct msghdr *msg`
- `int size`
- `int flags`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_listen

> Probes when a socket enters the listening state.

소켓이 대기 상태로 전환될 때 호출됩니다. 서비스가 연결을 수락하기 시작할 때 모니터링에 유용합니다.

**Use Case**: Monitor when services start accepting connections.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int backlog`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_accept

> Probes when a connection is accepted on a listening socket.

대기 중인 소켓에서 연결이 수락될 때 호출됩니다. 서비스에서 허용할 연결을 제어하는 데 유용합니다.

**Use Case**: Control over which connections are accepted.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct socket *newsock`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_shutdown

> Probes when a socket is closed (shutdown).

소켓이 닫힐 때 호출됩니다. 네트워크 세션 종료 이벤트를 모니터링하는 데 유용합니다.

**Use Case**: Monitor network session termination events.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int how`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_setsockopt

> Probes when options are set on a socket.

소켓에 옵션이 설정될 때 호출됩니다. 소켓 설정에 대한 정책 적용에 유용합니다.

**Use Case**: Enforce policies on socket configuration.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int level`
- `int optname`
- `char __user *optval`
- `unsigned int optlen`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_socket_getsockopt

> Probes when socket options are retrieved.

소켓 옵션이 조회될 때 호출됩니다. 소켓 옵션 접근 감사 및 제어에 유용합니다.

**Use Case**: Audit or control access to sensitive socket options.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int level`
- `int optname`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_sock_rcv_skb

> Probes when a socket receives a packet (skb).

소켓이 패킷을 받을 때 호출됩니다. 패킷 수준에서 세밀한 트래픽 모니터링에 유용합니다.

**Use Case**: Monitor inbound traffic at the packet level (packet inspection).

**Library**: `security/packet.h`

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_inet_conn_request

> Probes when a new connection request is received.

새로운 연결 요청이 수신될 때 호출됩니다. 어떤 연결 요청을 허용할지에 대한 정책을 적용하는 데 유용합니다.

**Use Case**: Enforce policies on connection requests.

**Library**: `security/inet.h`

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`
- `struct request_sock *req`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_inet_csk_clone

> Probes when a new socket is created in response to an incoming connection.

들어오는 연결에 대한 응답으로 새로운 소켓이 생성될 때 호출됩니다. 새로운 소켓 인스턴스 생성을 모니터링하거나 제한하는 데 유용합니다.

**Use Case**: Monitor or limit the creation of new socket instances.

**Library**: `security/inet.h`

**Arguments**: 
- `struct sock *sk`
- `struct request_sock *req`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_secmark_relabel_packet

> Used to assign security labels to network packets.

네트워크 패킷에 보안 레이블을 할당하는 데 사용됩니다. SELinux와 같은 패킷 레이블링을 사용하는 시스템과 통합할 때 유용합니다.

**Use Case**: Assign security labels to network packets for systems like SELinux.

**Library**: `security/secmark.h`

**Arguments**: 
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### security_xfrm_policy_alloc

> Probes during IPsec policy allocation.

IPsec 정책 할당 시 호출됩니다. 통신에 대한 보안 정책을 모니터링하거나 제어하는 데 유용합니다.

**Use Case**: Monitor or control the allocation of secure network policies.

**Library**: `security/xfrm.h`

**Arguments**: 
- `struct xfrm_policy *xp`

**Return Value**:
- 0 on success, negative error code on failure.

<br>
<br>

# Additional Network-Related LSM Hooks in Linux

### unix_stream_connect

> Triggered when a UNIX stream socket connects.

UNIX 스트림 소켓이 연결될 때 호출됩니다.

**Use Case**: Monitor UNIX stream socket connections.

**Library**: `security/unix.h`

**Arguments**: 
- `struct socket *sock`
- `struct sockaddr *address`
- `int addr_len`

**Return Value**:
- 0 on success, negative error code on failure.

---

### unix_may_send

> Used to check whether data can be sent between two UNIX sockets.

두 UNIX 소켓 간에 데이터를 보낼 수 있는지 확인할 때 호출됩니다.

**Use Case**: Enforce data transfer restrictions between UNIX sockets.

**Library**: `security/unix.h`

**Arguments**: 
- `struct socket *sock`
- `struct socket *peer`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_create

> Triggered when a new socket is created, specifying the protocol family and type.

새로운 소켓이 생성될 때, 프로토콜 패밀리와 타입을 지정하여 호출됩니다.

**Use Case**: Monitor socket creation across various protocol families.

**Library**: `security/socket.h`

**Arguments**: 
- `int family`
- `int type`
- `int protocol`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_post_create

> Called after a socket has been created, allowing further processing.

소켓이 생성된 후 추가 처리를 위해 호출됩니다.

**Use Case**: Extend processing of newly created sockets.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int family`
- `int type`
- `int protocol`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_socketpair

> Triggered when a pair of connected sockets is created.

연결된 소켓 쌍이 생성될 때 호출됩니다.

**Use Case**: Monitor creation of socket pairs.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock1`
- `struct socket *sock2`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_bind

> Triggered when a socket is bound to a specific address.

소켓이 특정 주소에 바인딩될 때 호출됩니다.

**Use Case**: Monitor or restrict socket bindings to specific addresses.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct sockaddr *address`
- `int addr_len`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_connect

> Triggered when a socket connects to an address.

소켓이 주소에 연결될 때 호출됩니다.

**Use Case**: Enforce policies or monitor outgoing connections.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct sockaddr *address`
- `int addr_len`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_listen

> Triggered when a socket is put into listening mode.

소켓이 대기 모드로 설정될 때 호출됩니다.

**Use Case**: Track when services are prepared to accept connections.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int backlog`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_accept

> Triggered when a connection is accepted on a listening socket.

대기 중인 소켓에서 연결이 수락될 때 호출됩니다.

**Use Case**: Control which connections can be accepted by a service.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct socket *newsock`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_sendmsg

> Triggered when a message is sent through a socket.

소켓을 통해 메시지가 전송될 때 호출됩니다.

**Use Case**: Enforce restrictions on sent data.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct msghdr *msg`
- `int size`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_recvmsg

> Triggered when a message is received from a socket.

소켓에서 메시지를 수신할 때 호출됩니다.

**Use Case**: Monitor or filter inbound messages.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct msghdr *msg`
- `int size`
- `int flags`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_getsockname

> Triggered to retrieve the local address of a socket.

소켓의 로컬 주소를 조회할 때 호출됩니다.

**Use Case**: Control access to sensitive socket addresses.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_getpeername

> Triggered to retrieve the remote address of a socket.

소켓의 원격 주소를 조회할 때 호출됩니다.

**Use Case**: Log or restrict access to peer addresses.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_getsockopt

> Triggered when socket options are retrieved.

소켓 옵션이 조회될 때 호출됩니다.

**Use Case**: Audit or enforce policies on socket options.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int level`
- `int optname`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_setsockopt

> Triggered when socket options are set.

소켓 옵션이 설정될 때 호출됩니다.

**Use Case**: Control or log changes to socket options.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int level`
- `int optname`
- `char __user *optval`
- `unsigned int optlen`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_shutdown

> Triggered when a socket is shut down.

소켓이 종료될 때 호출됩니다.

**Use Case**: Monitor socket shutdown events.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `int how`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_sock_rcv_skb

> Triggered when a socket receives a packet (skb).

소켓이 패킷(skb)을 수신할 때 호출됩니다.

**Use Case**: Monitor and control inbound packets.

**Library**: `security/packet.h`

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_getpeersec_stream

> Triggered to get peer security information for stream sockets.

스트림 소켓의 피어 보안 정보를 가져올 때 호출됩니다.

**Use Case**: Audit or enforce peer security on stream sockets.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### socket_getpeersec_dgram

> Triggered to get peer security information for datagram sockets.

데이터그램 소켓의 피어 보안 정보를 가져올 때 호출됩니다.

**Use Case**: Audit or enforce peer security on datagram sockets.

**Library**: `security/socket.h`

**Arguments**: 
- `struct socket *sock`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sk_alloc_security

> Called during the allocation of a socket structure.

소켓 구조체 할당 중에 호출됩니다.

**Use Case**: Enforce security during socket structure allocation.

**Library**: `security/socket.h`

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sk_free_security

> Called when a socket structure is being freed.

소켓 구조체가 해제될 때 호출됩니다.

**Use Case**: Ensure proper security handling during socket freeing.

**Library**: `security/socket.h`

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sk_clone_security

> Called when a socket is cloned.

소켓이 복제될 때 호출됩니다.

**Use Case**: Ensure security context is correctly cloned.

**Library**: `security/socket.h`

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sk_getsecid

> Used to get the security ID of a socket.

소켓의 보안 ID를 가져오는 데 사용됩니다.

**Use Case**: Retrieve security context for audit or enforcement.

**Library**: `security/socket.h`

**Arguments**: 
- `struct sock *sk`
- `u32 *secid`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sock_graft

> Called when a socket is grafted into another structure.

소켓이 다른 구조체에 연결될 때 호출됩니다.

**Use Case**: Ensure security policies are maintained during grafting.

**Library**: `security/socket.h`

**Arguments**: 
- `struct sock *sk`
- `struct socket *sock`

**Return Value**:
- 0 on success, negative error code on failure.

---

### inet_conn_request

> Triggered when an incoming connection request is received.

들어오는 연결 요청이 수신될 때 호출됩니다.

**Use Case**: Control or log inbound connection requests.

**Library**: `security/inet.h`

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### inet_csk_clone

> Triggered when a new socket is cloned in response to an incoming connection.

들어오는 연결에 대한 응답으로 새로운 소켓이 복제될 때 호출됩니다.

**Use Case**: Track or enforce policies during socket cloning.

**Library**: `security/inet.h`

**Arguments**: 
- `struct sock *sk`
- `struct request_sock *req`

**Return Value**:
- 0 on success, negative error code on failure.

---

### inet_conn_established

> Triggered when a TCP connection is established.

TCP 연결이 성립될 때 호출됩니다.

**Use Case**: Monitor or log established connections.

**Library**: `security/inet.h`

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### secmark_relabel_packet

> Used to relabel a network packet for security purposes.

네트워크 패킷에 보안 레이블을 다시 지정하는 데 사용됩니다.

**Use Case**: Ensure correct labeling of network packets.

**Library**: `security/net.h`

**Arguments**: 
- `struct sk_buff *skb`
- `u32 secmark`

**Return Value**:
- 0 on success, negative error code on failure.

---

### secmark_refcount_inc

> Increases the reference count for security markings.

보안 마킹의 참조 카운트를 증가시킵니다.

**Use Case**: Track or enforce security marking lifecycles.

**Library**: `security/net.h`

**Arguments**: 
- `u32 secmark`

**Return Value**:
- 0 on success, negative error code on failure.

---

### secmark_refcount_dec

> Decreases the reference count for security markings.

보안 마킹의 참조 카운트를 감소시킵니다.

**Use Case**: Track or enforce security marking lifecycles.

**Library**: `security/net.h`

**Arguments**: 
- `u32 secmark`

**Return Value**:
- 0 on success, negative error code on failure.

---

### req_classify_flow

> Used to classify the flow of a network request.

네트워크 요청의 흐름을 분류하는 데 사용됩니다.

**Use Case**: Classify traffic for security purposes.

**Library**: `security/net.h`

**Arguments**: 
- `struct request_sock *req`

**Return Value**:
- 0 on success, negative error code on failure.

---

### tun_dev_alloc_security

> Allocates security information for a tun device.

tun 장치에 대한 보안 정보를 할당합니다.

**Use Case**: Assign security contexts to tun devices.

**Library**: `security/tun.h`

**Arguments**: 
- `struct tun_struct *tun`

**Return Value**:
- 0 on success, negative error code on failure.

---

### tun_dev_create

> Triggered when a tun device is created.

tun 장치가 생성될 때 호출됩니다.

**Use Case**: Monitor creation of tun devices.

**Library**: `security/tun.h`

**Arguments**: 
- `struct tun_struct *tun`

**Return Value**:
- 0 on success, negative error code on failure.

---

### tun_dev_attach_queue

> Triggered when a queue is attached to a tun device.

tun 장치에 큐가 연결될 때 호출됩니다.

**Use Case**: Track or control tun device queue attachments.

**Library**: `security/tun.h`

**Arguments**: 
- `struct tun_struct *tun`
- `struct tun_file *file`

**Return Value**:
- 0 on success, negative error code on failure.

---

### tun_dev_attach

> Triggered when a tun device is attached to a socket.

tun 장치가 소켓에 연결될 때 호출됩니다.

**Use Case**: Enforce policies on tun device attachments.

**Library**: `security/tun.h`

**Arguments**: 
- `struct tun_struct *tun`

**Return Value**:
- 0 on success, negative error code on failure.

---

### tun_dev_open

> Triggered when a tun device is opened.

tun 장치가 열릴 때 호출됩니다.

**Use Case**: Monitor access to tun devices.

**Library**: `security/tun.h`

**Arguments**: 
- `struct tun_struct *tun`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sctp_assoc_request

> Triggered when an SCTP association request is received.

SCTP 연결 요청이 수신될 때 호출됩니다.

**Use Case**: Monitor or restrict SCTP association requests.

**Library**: `security/sctp.h`

**Arguments**: 
- `struct sctp_endpoint *ep`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sctp_bind_connect

> Triggered during an SCTP bind or connect operation.

SCTP 바인드 또는 연결 작업 중에 호출됩니다.

**Use Case**: Monitor or restrict SCTP binds or connects.

**Library**: `security/sctp.h`

**Arguments**: 
- `struct sctp_endpoint *ep`
- `struct sctp_association *asoc`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sctp_sk_clone

> Called when an SCTP socket is cloned.

SCTP 소켓이 복제될 때 호출됩니다.

**Use Case**: Ensure security context during SCTP socket cloning.

**Library**: `security/sctp.h`

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- 0 on success, negative error code on failure.

---

### sctp_assoc_established

> Triggered when an SCTP association is established.

SCTP 연결이 성립될 때 호출됩니다.

**Use Case**: Monitor or log established SCTP associations.

**Library**: `security/sctp.h`

**Arguments**: 
- `struct sctp_association *asoc`

**Return Value**:
- 0 on success, negative error code on failure.

---

### mptcp_add_subflow

> Triggered when a new subflow is added to an MPTCP connection.

MPTCP 연결에 새로운 서브플로우가 추가될 때 호출됩니다.

**Use Case**: Monitor or enforce policies on MPTCP subflows.

**Library**: `security/mptcp.h`

**Arguments**: 
- `struct mptcp_subflow *subflow`

**Return Value**:
- 0 on success, negative error code on failure.

---

