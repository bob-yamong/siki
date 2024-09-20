# Network-Related kprobes in Linux
네트워크 조작과 관련된 kprobe 정리 문서

### tcp_connect

> Probes TCP connection initiation. Can be used to trace outgoing connection attempts.

TCP 연결이 시작될 때 호출됩니다. 시스템에서 발생하는 TCP 연결 시도를 추적하는 데 사용됩니다.

**Use Case**: Helps in detecting connection attempts from an application or monitoring security violations related to unexpected outgoing connections.

**LIBRARY**: net/ipv4/tcp.c

**Arguments**: 
- `struct sock *sk` 
- `struct sockaddr *uaddr`
- `int addr_len`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### tcp_accept

> Probes when a TCP connection is accepted by a listening socket.

대기 중인 소켓이 TCP 연결을 수락할 때 호출됩니다.

**Use Case**: Can be used to trace incoming connections on a server or to audit connection handling in an application.

**LIBRARY**: net/ipv4/tcp.c

**Arguments**: 
- `struct sock *sk`
- `struct sock *newsk`

**Return Value**:
- Pointer to the accepted socket structure

---

### tcp_close

> Probes the closing of a TCP connection.

TCP 연결이 종료될 때 호출됩니다.

**Use Case**: Useful for monitoring and logging when a connection is terminated, which can assist in debugging connection leaks.

**LIBRARY**: net/ipv4/tcp.c

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- None

---

### tcp_sendmsg

> Probes when a message is sent over a TCP socket.

TCP 소켓을 통해 메시지가 전송될 때 호출됩니다.

**Use Case**: Helps in tracking data transmission, which can be essential for performance monitoring and debugging application traffic.

**LIBRARY**: net/ipv4/tcp.c

**Arguments**: 
- `struct sock *sk`
- `struct msghdr *msg`
- `size_t len`

**Return Value**:
- Number of bytes sent
- Negative error code on failure

---

### tcp_recvmsg

> Probes when a message is received on a TCP socket.

TCP 소켓에서 메시지를 수신할 때 호출됩니다.

**Use Case**: Can be used to trace incoming TCP data, useful for analyzing data reception issues or bottlenecks.

**LIBRARY**: net/ipv4/tcp.c

**Arguments**: 
- `struct sock *sk`
- `struct msghdr *msg`
- `size_t len`
- `int flags`

**Return Value**:
- Number of bytes received
- Negative error code on failure

---

### udp_sendmsg

> Probes when a message is sent over a UDP socket.

UDP 소켓을 통해 메시지가 전송될 때 호출됩니다.

**Use Case**: Can be useful in monitoring UDP traffic, particularly in real-time applications where packet transmission time is critical.

**LIBRARY**: net/ipv4/udp.c

**Arguments**: 
- `struct sock *sk`
- `struct msghdr *msg`
- `size_t len`

**Return Value**:
- Number of bytes sent
- Negative error code on failure

---

### udp_recvmsg

> Probes when a message is received on a UDP socket.

UDP 소켓에서 메시지를 수신할 때 호출됩니다.

**Use Case**: Helps in monitoring UDP packet reception, useful in tracking packet loss or diagnosing communication issues in UDP-based protocols.

**LIBRARY**: net/ipv4/udp.c

**Arguments**: 
- `struct sock *sk`
- `struct msghdr *msg`
- `size_t len`
- `int flags`

**Return Value**:
- Number of bytes received
- Negative error code on failure

---

### inet_csk_accept

> Probes when an IPv4 or IPv6 connection is accepted by a listening socket.

IPv4 또는 IPv6 연결이 대기 중인 소켓에 의해 수락될 때 호출됩니다.

**Use Case**: Useful for tracking connection acceptances in IPv4 and IPv6, particularly helpful in monitoring incoming connections on a server.

**LIBRARY**: net/ipv4/inet_connection_sock.c

**Arguments**: 
- `struct sock *sk`
- `struct sock *newsk`

**Return Value**:
- Pointer to the accepted socket structure

---

### inet_csk_listen_start

> Probes when a socket starts listening for incoming connections.

소켓이 들어오는 연결을 대기 상태로 설정할 때 호출됩니다.

**Use Case**: Can be used to monitor when a server application starts listening for incoming connections, useful for security or performance analysis.

**LIBRARY**: net/ipv4/inet_connection_sock.c

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### ip_queue_xmit

> Probes when an IP packet is queued for transmission.

IP 패킷이 전송 대기열에 넣어질 때 호출됩니다.

**Use Case**: Helps in monitoring packet queuing behavior, useful in diagnosing transmission delays or network congestion.

**LIBRARY**: net/ipv4/ip_output.c

**Arguments**: 
- `struct sk_buff *skb`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### icmp_send

> Probes when ICMP messages (e.g., ping requests) are sent.

ICMP 메시지가 전송될 때 호출됩니다 (예: ping 요청).

**Use Case**: Useful for tracking ICMP traffic, particularly ping requests and responses, which can be used to diagnose network reachability.

**LIBRARY**: net/ipv4/icmp.c

**Arguments**: 
- `struct sk_buff *skb`
- `int type`
- `int code`
- `__be32 info`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### sock_recvmsg

> Probes generic socket message reception.

소켓 메시지가 수신될 때 호출됩니다.

**Use Case**: Can be used for auditing socket-level data reception across various protocols like TCP, UDP, etc.

**LIBRARY**: net/core/sock.c

**Arguments**: 
- `struct socket *sock`
- `struct msghdr *msg`
- `size_t size`
- `int flags`

**Return Value**:
- Number of bytes received
- Negative error code on failure

---

### sock_sendmsg

> Probes generic socket message sending.

소켓 메시지가 전송될 때 호출됩니다.

**Use Case**: Helps track outgoing data transmissions on any socket, assisting in debugging or performance optimization.

**LIBRARY**: net/core/sock.c

**Arguments**: 
- `struct socket *sock`
- `struct msghdr *msg`
- `size_t size`

**Return Value**:
- Number of bytes sent
- Negative error code on failure

---

### dev_queue_xmit

> Probes when a packet is queued for transmission on a network device.

네트워크 장치에 패킷이 전송 대기열에 추가될 때 호출됩니다.

**Use Case**: Useful for diagnosing packet transmission issues at the device driver level, including tracking dropped packets or delays.

**LIBRARY**: net/core/dev.c

**Arguments**: 
- `struct sk_buff *skb`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### netif_receive_skb

> Probes when a packet is received by a network interface and passed up the stack.

네트워크 인터페이스가 패킷을 수신하고 상위 계층으로 전달할 때 호출됩니다.

**Use Case**: Used to trace packet reception at the interface level, useful in debugging networking stack issues or performance bottlenecks.

**LIBRARY**: net/core/dev.c

**Arguments**: 
- `struct sk_buff *skb`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### skb_clone

> Probes when a socket buffer (skb) is cloned.

소켓 버퍼(skb)가 복제될 때 호출됩니다.

**Use Case**: Can be used to monitor buffer cloning, which is critical in analyzing memory usage and buffer management.

**LIBRARY**: net/core/skbuff.c

**Arguments**: 
- `struct sk_buff *skb`

**Return Value**:
- Pointer to cloned `sk_buff`

---

### ip_rcv

> Probes when an IP packet is received.

IP 패킷이 수신될 때 호출됩니다.

**Use Case**: Useful for tracking incoming IP packets at the network layer, which can be important for packet inspection or network filtering tasks.

**LIBRARY**: net/ipv4/ip_input.c

**Arguments**: 
- `struct sk_buff *skb`
- `struct net_device *dev`
- `struct packet_type *pt`
- `struct net_device *orig_dev`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### ip_output

> Probes when an IPv4 packet is sent out.

IPv4 패킷이 전송될 때 호출됩니다.

**Use Case**: Useful for tracking outgoing IP packets, particularly useful for diagnosing routing or transmission issues in a network.

**LIBRARY**: net/ipv4/ip_output.c

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### inet_bind

> Probes when a socket is bound to an address (IP and port).

소켓이 IP 주소 및 포트에 바인딩될 때 호출됩니다.

**Use Case**: Can be useful in tracking socket binding events, which can assist in detecting unauthorized services or socket misconfigurations.

**LIBRARY**: net/ipv4/af_inet.c

**Arguments**: 
- `struct sock *sk`
- `struct sockaddr *addr`
- `int addr_len`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### inet_release

> Probes when an Internet socket is closed.

인터넷 소켓이 닫힐 때 호출됩니다.

**Use Case**: Helps in monitoring socket closure, useful for auditing session terminations or analyzing connection stability.

**LIBRARY**: net/ipv4/af_inet.c

**Arguments**: 
- `struct sock *sk`

**Return Value**:
- None

---

### tcp_v4_do_rcv

> Probes when a TCP packet is received for an IPv4 connection.

IPv4 연결에 대해 TCP 패킷이 수신될 때 호출됩니다.

**Use Case**: Useful for tracing TCP traffic in an IPv4 connection, which can assist in debugging packet loss or latency issues.

**LIBRARY**: net/ipv4/tcp_input.c

**Arguments**: 
- `struct sock *sk`
- `struct sk_buff *skb`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### tcp_v6_connect

> Probes when a TCP connection is initiated for an IPv6 connection.

IPv6 연결에 대해 TCP 연결이 시작될 때 호출됩니다.

**Use Case**: Can be used to monitor outgoing IPv6 connections, useful for analyzing application behavior in an IPv6 environment.

**LIBRARY**: net/ipv6/tcp_ipv6.c

**Arguments**: 
- `struct sock *sk`
- `struct sockaddr *uaddr`
- `int addr_len`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### tcp_v6_rcv

> Probes when a TCP packet is received for an IPv6 connection.

IPv6 연결에 대해 TCP 패킷이 수신될 때 호출됩니다.

**Use Case**: Helps in tracing incoming TCP traffic in an IPv6 environment, aiding in packet analysis or network troubleshooting.

**LIBRARY**: net/ipv6/tcp_ipv6.c

**Arguments**: 
- `struct sk_buff *skb`
- `struct net_device *dev`
- `struct packet_type *pt`
- `struct net_device *orig_dev`

**Return Value**:
- 0 on success
- Negative error code on failure

---

### __dev_queue_xmit

> Probes when a packet is queued for transmission to a network device at lower layers.

네트워크 장치로 전송하기 위해 패킷이 하위 네트워크 계층에서 대기열에 넣어질 때 호출됩니다.

**Use Case**: Useful for analyzing the queuing process of a packet at lower layers of the network stack, crucial for performance diagnostics.

**LIBRARY**: net/core/dev.c

**Arguments**: 
- `struct sk_buff *skb`

**Return Value**:
- 0 on success
- Negative error code on failure
