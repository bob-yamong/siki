# Network-Related kprobes in Linux
네트워크 조작과 관련된 kprobe 정리 문서

### 1. `tcp_connect`
- Probes TCP connection initiation. Can be used to trace outgoing connection attempts.
- TCP 연결이 시작될 때 호출됩니다. 시스템에서 발생하는 TCP 연결 시도를 추적하는 데 사용됩니다.
- **Use Case**: Monitor outbound TCP connections.

### 2. `tcp_accept`
- Probes when a TCP connection is accepted by a listening socket.
- 대기 중인 소켓이 TCP 연결을 수락할 때 호출됩니다.
- **Use Case**: Monitor incoming TCP connections accepted by servers.

### 3. `tcp_close`
- Probes the closing of a TCP connection.
- TCP 연결이 종료될 때 호출됩니다.
- **Use Case**: Track TCP connection termination events.

### 4. `tcp_sendmsg`
- Probes when a message is sent over a TCP socket.
- TCP 소켓을 통해 메시지가 전송될 때 호출됩니다.
- **Use Case**: Monitor outbound TCP traffic.

### 5. `tcp_recvmsg`
- Probes when a message is received on a TCP socket.
- TCP 소켓에서 메시지를 수신할 때 호출됩니다.
- **Use Case**: Monitor inbound TCP traffic.

### 6. `udp_sendmsg`
- Probes when a message is sent over a UDP socket.
- UDP 소켓을 통해 메시지가 전송될 때 호출됩니다.
- **Use Case**: Monitor outbound UDP traffic.

### 7. `udp_recvmsg`
- Probes when a message is received on a UDP socket.
- UDP 소켓에서 메시지를 수신할 때 호출됩니다.
- **Use Case**: Monitor inbound UDP traffic.

### 8. `inet_csk_accept`
- Probes when an IPv4 or IPv6 connection is accepted by a listening socket.
- IPv4 또는 IPv6 연결이 대기 중인 소켓에 의해 수락될 때 호출됩니다.
- **Use Case**: Track accepted incoming connections (both IPv4 and IPv6).

### 9. `inet_csk_listen_start`
- Probes when a socket starts listening for incoming connections.
- 소켓이 들어오는 연결을 대기 상태로 설정할 때 호출됩니다.
- **Use Case**: Monitor sockets that start listening for incoming connections.

### 10. `ip_queue_xmit`
- Probes when an IP packet is queued for transmission.
- IP 패킷이 전송 대기열에 넣어질 때 호출됩니다.
- **Use Case**: Track outbound IP packet transmission events.

### 11. `icmp_send`
- Probes when ICMP messages (e.g., ping requests) are sent.
- ICMP 메시지가 전송될 때 호출됩니다 (예: ping 요청).
- **Use Case**: Monitor ICMP traffic, useful for network diagnostics or security auditing.

### 12. `sock_recvmsg`
- Probes generic socket message reception.
- 소켓 메시지가 수신될 때 호출됩니다.
- **Use Case**: Monitor incoming messages for all socket types.

### 13. `sock_sendmsg`
- Probes generic socket message sending.
- 소켓 메시지가 전송될 때 호출됩니다.
- **Use Case**: Monitor outgoing messages for all socket types.

### 14. `dev_queue_xmit`
- Probes when a packet is queued for transmission on a network device.
- 네트워크 장치에 패킷이 전송 대기열에 추가될 때 호출됩니다.
- **Use Case**: Monitor outgoing traffic on specific network devices.

### 15. `netif_receive_skb`
- Probes when a packet is received by a network interface and passed up the stack.
- 네트워크 인터페이스가 패킷을 수신하고 상위 계층으로 전달할 때 호출됩니다.
- **Use Case**: Monitor incoming packets at the network interface level.

### 16. `skb_clone`
- Probes when a socket buffer (skb) is cloned.
- 소켓 버퍼(skb)가 복제될 때 호출됩니다.
- **Use Case**: Track packet handling and duplication.

### 17. `ip_rcv`
- Probes when an IP packet is received.
- IP 패킷이 수신될 때 호출됩니다.
- **Use Case**: Monitor inbound IPv4 packets.

### 18. `ip_output`
- Probes when an IPv4 packet is sent out.
- IPv4 패킷이 전송될 때 호출됩니다.
- **Use Case**: Monitor outbound IPv4 packets.

### 19. `inet_bind`
- Probes when a socket is bound to an address (IP and port).
- 소켓이 IP 주소 및 포트에 바인딩될 때 호출됩니다.
- **Use Case**: Track socket binding events for both TCP and UDP sockets.

### 20. `inet_release`
- Probes when an Internet socket is closed.
- 인터넷 소켓이 닫힐 때 호출됩니다.
- **Use Case**: Monitor socket closure events for IPv4/IPv6 sockets.

### 21. `tcp_v4_do_rcv`
- Probes when a TCP packet is received for an IPv4 connection.
- IPv4 연결에 대해 TCP 패킷이 수신될 때 호출됩니다.
- **Use Case**: Monitor incoming IPv4 TCP packets.

### 22. `tcp_v6_connect`
- Probes when a TCP connection is initiated for an IPv6 connection.
- IPv6 연결에 대해 TCP 연결이 시작될 때 호출됩니다.
- **Use Case**: Monitor outgoing IPv6 TCP connections.

### 23. `tcp_v6_rcv`
- Probes when a TCP packet is received for an IPv6 connection.
- IPv6 연결에 대해 TCP 패킷이 수신될 때 호출됩니다.
- **Use Case**: Monitor incoming IPv6 TCP packets.

### 24. `__dev_queue_xmit`
- Probes when a packet is queued for transmission to a network device at lower layers.
- 네트워크 장치로 전송하기 위해 패킷이 하위 네트워크 계층에서 대기열에 넣어질 때 호출됩니다.
- **Use Case**: Monitor packet transmission at the lower layers of the network stack.
