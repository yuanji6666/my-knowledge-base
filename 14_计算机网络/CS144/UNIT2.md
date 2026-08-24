# CS144 Unit 2 — Transport

### 2.1 TCP service model
- **字节流模型 (Byte Stream)**：应用看到的是**连续字节流**，不是一个个报文。TCP 自行分段、排序、重传；**不保留应用的消息边界**（两次 `write` 可能被合成一段，一次 `write` 也可能被拆成多段）。
- **特点**：面向连接、全双工、点对点；在不可靠 IP 之上提供**可靠、有序**交付；带**流量控制**（接收窗口）和**拥塞控制**。
- **三次握手 (Connection setup)**：两端交换初始序号 (ISN)，确认对方可达且愿意通信。
  1. Client → `SYN, seq=x`
  2. Server → `SYN-ACK, seq=y, ack=x+1`
  3. Client → `ACK, ack=y+1`
  - SYN 本身占一个序号；握手完成后才开始传数据。
- **连接关闭 (Connection teardown)**：两个方向**独立关闭**（半关闭）。典型四次挥手：
  1. A → `FIN`（我这边写完了）
  2. B → `ACK`
  3. B → `FIN`（我也写完了）
  4. A → `ACK`
  - 主动关闭方进入 **TIME_WAIT**，防止旧包干扰新连接、确保最后的 ACK 能重传。
- **Header 关键字段**：
  - **Src/Dst Port**：多路复用，把数据交给正确进程。
  - **Seq**：本段第一个数据字节在字节流中的位置。
  - **Ack**：我**下一个需要**的字节序号（累积确认）。
  - **Window**：接收窗口，告诉对方还能再发多少字节。
  - **Flags**：`SYN` 建连、`ACK` 确认、`FIN` 关闭、`RST` 异常重置、`PSH` 尽快上交应用。
  - **Checksum**：覆盖伪首部 + TCP 头 + 数据。

### 2.2 UDP service model
- **User Datagram Protocol**：在 IP 上加一层很薄的封装，把「主机到主机」变成「**进程到进程**」。
- **特点**：无连接、不可靠、不保证顺序、不重传、无流控/拥塞控制；**保留消息边界**（一个 datagram 一次收齐或整份丢）。适合 DNS、实时音视频等「宁可丢、不要等」的场景。
- **Header 关键字段**（仅 8 字节）：
  - **Src/Dst Port**：解复用到正确应用。
  - **Length**：UDP 头 + 数据总长。
  - **Checksum**：可选（IPv4）/ 必选（IPv6）；错了直接丢。
- **一句话**：UDP ≈ IP + 端口号。可靠性若需要，由应用自己做。

### 2.3 ICMP service model
- **不是传输层**。ICMP 挂在 IP 旁边，给**主机和路由器**提供网络层控制/差错信息（目的不可达、TTL 耗尽、需要分片等）。不传应用数据。
- **ping**：直接使用 ICMP。
  1. 本机发 **Echo Request**
  2. 对端回 **Echo Reply**
  3. 用往返时延判断可达性。
- **traceroute**：通常发 **UDP**（目的端口是一个没人听的高端口），**依赖沿途路由器回 ICMP**。
  1. 探测包 TTL=1, 2, 3, … 依次发出。
  2. 第 n 跳路由器 TTL 减到 0，丢包并回 **Time Exceeded** → 暴露该跳 IP。
  3. 到达目的主机后，因端口不存在，回 **Port Unreachable** → 路径探测结束。
- **对照**：ping 是 ICMP 端到端问答；traceroute 是「用 UDP 当探针 + ICMP 当回声」画出路径。
### 2.4 End-to-end principle
- **核心**：功能要「完全正确」，只能在**端点（应用）**实现；路径上的节点可以帮忙，但**不能单独保证正确**。中间层的实现只算性能优化，端点仍须自己再检查一遍。
- **强端到端**：网络保持通用、尽力而为，**中间不要做应用语义**（不要让路由器保证可靠、按应用改包）。目的是灵活性：新应用不用改核心网就能部署。链路层重传、更快转发这类不改变服务语义的优化仍然允许。
- **对照**：普通 e2e 回答「正确性放哪」；强 e2e 回答「为了未知应用，网络该有多笨」。
- **举例**：
  1. **可靠文件传输**：每跳校验/重传仍可能在磁盘、主机崩溃、最后一跳出问题 → 应用必须端到端校验；跳到跳可靠只是少重传整文件。
  2. **加密**：逐跳加密则中间节点可见明文 → 机密性只能端到端加密。
  3. **投递确认**：「网络说送到了」≠「应用处理完了」。
### 2.5 Finite State Machines

### 2.7 Reliable Communications — Stop and wait

### 2.8 Reliable Communications — Sliding window

### 2.9 Reliable Communications — Retransmission strategies

### 2.10 Reliable Communications — TCP header

### 2.11 Reliable Communications — Connection setup and teardown

### 2.12 总结 Transport recap
