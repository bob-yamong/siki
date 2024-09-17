# Network-Related LSM Hooks in Linux
네트워크 조작과 관련된 lsm hook 정리 문서

### 1. `security_socket_create`
- Triggered when a socket is created. This hook can be used to monitor socket creation for specific protocol families (like TCP, UDP, etc.).
- 소켓이 생성될 때 트리거됩니다. 이 훅은 TCP, UDP와 같은 특정 프로토콜 패밀리의 소켓 생성을 모니터링하는 데 사용될 수 있습니다.

### 2. `security_socket_connect`
- Triggered when a socket is connecting to a remote address. This can be used to enforce or log policy on outgoing network connections.
- 소켓이 원격 주소에 연결될 때 트리거됩니다. 이 훅은 외부로 나가는 네트워크 연결에 대한 정책을 적용하거나 기록하는 데 사용될 수 있습니다.

### 3. `security_socket_bind`
- Triggered when a socket is being bound to an address. This hook can be used to monitor and control which addresses and ports are used.
- 소켓이 주소에 바인딩될 때 트리거됩니다. 이 훅은 사용되는 주소와 포트를 모니터링하고 제어하는 데 사용될 수 있습니다.

### 4. `security_socket_sendmsg`
- Triggered when a process sends a message through a socket. Useful for inspecting data sent through a socket (e.g., enforcing policy on data exfiltration).
- 프로세스가 소켓을 통해 메시지를 보낼 때 트리거됩니다. 소켓을 통해 전송되는 데이터를 검사하거나 (예: 데이터 유출 방지 정책 적용) 사용할 수 있습니다.

### 5. `security_socket_recvmsg`
- Triggered when a message is received from a socket. This can be used for monitoring inbound traffic and enforcing rules on data that is allowed to be received.
- 소켓에서 메시지를 받을 때 트리거됩니다. 들어오는 트래픽을 모니터링하거나 수신 가능한 데이터에 대한 규칙을 적용하는 데 사용될 수 있습니다.

### 6. `security_socket_listen`
- Triggered when a socket enters the listening state, typically for servers. This hook is useful for monitoring services as they start accepting connections.
- 소켓이 대기 상태로 들어갈 때 트리거됩니다. 주로 서버에서 사용되며, 서비스가 연결을 수락하기 시작할 때 모니터링하는 데 유용합니다.

### 7. `security_socket_accept`
- Triggered when a connection is accepted on a listening socket. It allows control over which connections are accepted by a service.
- 대기 중인 소켓에서 연결을 수락할 때 트리거됩니다. 이 훅은 서비스에서 허용할 연결을 제어할 수 있게 합니다.

### 8. `security_socket_shutdown`
- Triggered when a socket is closed (shutdown). This can be useful to monitor network session termination events.
- 소켓이 닫힐 때 (종료) 트리거됩니다. 네트워크 세션 종료 이벤트를 모니터링하는 데 유용할 수 있습니다.

### 9. `security_socket_setsockopt`
- Triggered when options are set on a socket (e.g., protocol-level options like timeouts, buffer sizes). This hook allows the enforcement of policies on socket configuration.
- 소켓에서 옵션이 설정될 때 트리거됩니다 (예: 타임아웃, 버퍼 크기 등 프로토콜 수준 옵션). 이 훅은 소켓 설정에 대한 정책을 적용할 수 있게 합니다.

### 10. `security_socket_getsockopt`
- Triggered when socket options are retrieved. It can be used for auditing or controlling access to sensitive socket options.
- 소켓 옵션을 조회할 때 트리거됩니다. 민감한 소켓 옵션에 대한 접근을 감사하거나 제어하는 데 사용할 수 있습니다.

### 11. `security_sock_rcv_skb`
- Triggered when a socket receives a packet (skb). This is useful for monitoring inbound traffic at a more granular level (packet inspection).
- 소켓이 패킷을 받을 때 (skb) 트리거됩니다. 패킷 수준에서 세밀한 트래픽 모니터링을 위해 유용합니다.

### 12. `security_inet_conn_request`
- Triggered when a new connection request is received (for example, in a TCP server). It can be used to enforce policies on which connection requests to accept or deny.
- 새로운 연결 요청을 받을 때 (예: TCP 서버에서) 트리거됩니다. 어떤 연결 요청을 허용할지 또는 거부할지에 대한 정책을 적용하는 데 사용할 수 있습니다.

### 13. `security_inet_csk_clone`
- Triggered when a new socket is created in response to an incoming connection. This can be used to monitor or limit the creation of new socket instances, especially in busy servers.
- 들어오는 연결에 대한 응답으로 새로운 소켓이 생성될 때 트리거됩니다. 특히 바쁜 서버에서 새로운 소켓 인스턴스 생성을 모니터링하거나 제한하는 데 사용할 수 있습니다.

### 14. `security_secmark_relabel_packet`
- Used to assign security labels to network packets, particularly for integrating with systems that use packet labeling, like SELinux.
- 네트워크 패킷에 보안 레이블을 할당하는 데 사용되며, SELinux와 같이 패킷 레이블링을 사용하는 시스템과 통합하는 데 유용합니다.

### 15. `security_xfrm_policy_alloc`
- Triggered during IPsec policy allocation. This can be used to monitor or control the allocation of secure network policies (encryption, authentication) for communications.
- IPsec 정책 할당 중에 트리거됩니다. 통신에 대한 보안 네트워크 정책 (암호화, 인증) 할당을 모니터링하거나 제어하는 데 사용할 수 있습니다.


# Additional Network-Related LSM Hooks in Linux

### 1. `unix_stream_connect`
- Triggered when a UNIX stream socket connects.
- UNIX 스트림 소켓이 연결될 때 트리거됩니다.

### 2. `unix_may_send`
- Used to check whether data can be sent between two UNIX sockets.
- 두 UNIX 소켓 간에 데이터를 보낼 수 있는지 확인할 때 사용됩니다.

### 3. `socket_create`
- Triggered when a new socket is created, specifying the protocol family and type.
- 새로운 소켓이 생성될 때, 프로토콜 패밀리와 타입을 지정하여 트리거됩니다.

### 4. `socket_post_create`
- Called after a socket has been created, allowing further processing.
- 소켓이 생성된 후 호출되며, 추가 처리를 허용합니다.

### 5. `socket_socketpair`
- Triggered when a pair of connected sockets is created.
- 연결된 소켓 쌍이 생성될 때 트리거됩니다.

### 6. `socket_bind`
- Triggered when a socket is bound to a specific address.
- 소켓이 특정 주소에 바인딩될 때 트리거됩니다.

### 7. `socket_connect`
- Triggered when a socket connects to an address.
- 소켓이 주소에 연결될 때 트리거됩니다.

### 8. `socket_listen`
- Triggered when a socket is put into listening mode.
- 소켓이 대기 모드로 설정될 때 트리거됩니다.

### 9. `socket_accept`
- Triggered when a connection is accepted on a listening socket.
- 대기 중인 소켓에서 연결이 수락될 때 트리거됩니다.

### 10. `socket_sendmsg`
- Triggered when a message is sent through a socket.
- 소켓을 통해 메시지가 전송될 때 트리거됩니다.

### 11. `socket_recvmsg`
- Triggered when a message is received from a socket.
- 소켓에서 메시지를 수신할 때 트리거됩니다.

### 12. `socket_getsockname`
- Triggered to retrieve the local address of a socket.
- 소켓의 로컬 주소를 조회할 때 트리거됩니다.

### 13. `socket_getpeername`
- Triggered to retrieve the remote address of a socket.
- 소켓의 원격 주소를 조회할 때 트리거됩니다.

### 14. `socket_getsockopt`
- Triggered when socket options are retrieved.
- 소켓 옵션을 조회할 때 트리거됩니다.

### 15. `socket_setsockopt`
- Triggered when socket options are set.
- 소켓 옵션이 설정될 때 트리거됩니다.

### 16. `socket_shutdown`
- Triggered when a socket is shut down.
- 소켓이 종료될 때 트리거됩니다.

### 17. `socket_sock_rcv_skb`
- Triggered when a socket receives a packet (skb).
- 소켓이 패킷(skb)을 수신할 때 트리거됩니다.

### 18. `socket_getpeersec_stream`
- Triggered to get peer security information for stream sockets.
- 스트림 소켓의 피어 보안 정보를 가져올 때 트리거됩니다.

### 19. `socket_getpeersec_dgram`
- Triggered to get peer security information for datagram sockets.
- 데이터그램 소켓의 피어 보안 정보를 가져올 때 트리거됩니다.

### 20. `sk_alloc_security`
- Called during the allocation of a socket structure.
- 소켓 구조체 할당 중에 호출됩니다.

### 21. `sk_free_security`
- Called when a socket structure is being freed.
- 소켓 구조체가 해제될 때 호출됩니다.

### 22. `sk_clone_security`
- Called when a socket is cloned.
- 소켓이 복제될 때 호출됩니다.

### 23. `sk_getsecid`
- Used to get the security ID of a socket.
- 소켓의 보안 ID를 가져오는 데 사용됩니다.

### 24. `sock_graft`
- Called when a socket is grafted into another structure.
- 소켓이 다른 구조체에 연결될 때 호출됩니다.

### 25. `inet_conn_request`
- Triggered when an incoming connection request is received.
- 들어오는 연결 요청이 수신될 때 트리거됩니다.

### 26. `inet_csk_clone`
- Triggered when a new socket is cloned in response to an incoming connection.
- 들어오는 연결에 대한 응답으로 새로운 소켓이 복제될 때 트리거됩니다.

### 27. `inet_conn_established`
- Triggered when a TCP connection is established.
- TCP 연결이 성립될 때 트리거됩니다.

### 28. `secmark_relabel_packet`
- Used to relabel a network packet for security purposes.
- 네트워크 패킷에 보안 레이블을 다시 지정하는 데 사용됩니다.

### 29. `secmark_refcount_inc`
- Increases the reference count for security markings.
- 보안 마킹의 참조 카운트를 증가시킵니다.

### 30. `secmark_refcount_dec`
- Decreases the reference count for security markings.
- 보안 마킹의 참조 카운트를 감소시킵니다.

### 31. `req_classify_flow`
- Used to classify the flow of a network request.
- 네트워크 요청의 흐름을 분류하는 데 사용됩니다.

### 32. `tun_dev_alloc_security`
- Allocates security information for a tun device.
- tun 장치에 대한 보안 정보를 할당합니다.

### 33. `tun_dev_create`
- Triggered when a tun device is created.
- tun 장치가 생성될 때 트리거됩니다.

### 34. `tun_dev_attach_queue`
- Triggered when a queue is attached to a tun device.
- tun 장치에 큐가 연결될 때 트리거됩니다.

### 35. `tun_dev_attach`
- Triggered when a tun device is attached to a socket.
- tun 장치가 소켓에 연결될 때 트리거됩니다.

### 36. `tun_dev_open`
- Triggered when a tun device is opened.
- tun 장치가 열릴 때 트리거됩니다.

### 37. `sctp_assoc_request`
- Triggered when an SCTP association request is received.
- SCTP 연결 요청이 수신될 때 트리거됩니다.

### 38. `sctp_bind_connect`
- Triggered during an SCTP bind or connect operation.
- SCTP 바인드 또는 연결 작업 중에 트리거됩니다.

### 39. `sctp_sk_clone`
- Called when an SCTP socket is cloned.
- SCTP 소켓이 복제될 때 호출됩니다.

### 40. `sctp_assoc_established`
- Triggered when an SCTP association is established.
- SCTP 연결이 성립될 때 트리거됩니다.

### 41. `mptcp_add_subflow`
- Triggered when a new subflow is added to an MPTCP connection.
- MPTCP 연결에 새로운 서브플로우가 추가될 때 트리거됩니다.
