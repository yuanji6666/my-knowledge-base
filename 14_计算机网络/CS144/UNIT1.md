# CS144 Unit 1 — Internet 架构

### 1.1 A day in the life of an application
- **应用视角**：应用通过 **Socket** 接口与网络交互，将数据流（Byte Stream）交给传输层。
- **抽象**：网络对应用来说是一个透明的、可靠或不可靠的传输通道。

### 1.2 The four layer Internet model
- **链路层 (Link)**：负责物理相邻节点间的包传递（如 WiFi, Ethernet）。
- **网络层 (Network/IP)**：**最核心，最薄**。实现全球范围内的端到端连接，定义了所有节点的通用语言（IP 包）。
- **传输层 (Transport)**：在端系统间提供通信服务（TCP 提供可靠流，UDP 提供原始数据报）。
- **应用层 (Application)**：各种网络应用协议（HTTP, DNS, gRPC）。

### 1.3 The IP service model
- **尽力而为 (Best-effort)**：不保证送达，不保证顺序，不保证时延。
- **简单化**：将复杂性（重传、流控）推向网络边缘（端系统），保持网络核心（IP 层）极其精简。

### 1.4 A Day in the Life of a Packet
- **建立连接**：通过 **TCP 三次握手**（SYN, SYN-ACK, ACK）在端系统间同步状态。
- **逐跳处理 (Inside each hop)**：
  1. **解封装**：从链路层剥离，读取 IP 头部。
  2. **查表转发**：根据目的 IP 查路由表（最长前缀匹配）。
  3. **头部更新**：递减 TTL，重新计算 Checksum。
  4. **重新封装**：封装进下一跳的链路层帧并发出。
- **端到端传输**：数据包跨越多个异构网络，由每跳路由器独立完成局部路由决策。

### 1.5 Packet Switching
**核心优势**
- **简单 (Simple)**：中间节点（路由器）**无状态**，不维护连接信息，仅需独立转发每个包。
- **高效 (Efficient)**：通过 **统计复用** 动态共享带宽，极大提升了突发流量下的链路利用率。

**转发模型**
- **Store-and-forward**：整包接收后再转发。
- **Destination routing**：仅带目的地址，每跳独立查表。
- **Source routing**：发送端指定路径（废弃）。

**代价**：不保证带宽，可能排队/丢包；可靠性由端系统（TCP）负责。

### 1.6 Layering (分层)
**分层原因**
1. **模块化 (Modularization)**：将复杂系统拆解为易管理的模块。
2. **定义明确的服务 (Well-defined service)**：层间接口清晰，隐藏实现细节。
3. **复用性 (Reuse)**：上层可共享下层提供的标准服务。
4. **关注点分离 (Separation of concerns)**：每层只解决特定维度的通信问题。
5. **持续演进 (Continuous improvement)**：各层可独立迭代优化，互不干扰。

**一句话总结**：以模块化解耦复杂性，平衡通用性与演进速度。

### 1.7 Encapsulation Principle (封装原则)
- **核心**：上层数据被视为下层的 **Payload (有效负载)**。
- **VPN 隧道 (以 SSL/TLS VPN 封装可以递归的)**：
  - **结构**：`[外层 IP][外层 TCP/UDP][TLS][内层 IP][内层 TCP][Data]`
  - **TLS 的角色**：在应用层与传输层之间建立加密管道，保护封装在内的整个「私网 IP 包」。
  - **分层灵活性**：
    - **嵌套**：证明了可以在应用层（TLS）中跑网络层（IP）。
    - **伪装**：通过 TLS 封装，VPN 流量可以伪装成普通 HTTPS 流量绕过防火墙。
  - **结论**：每一层只需关注如何处理其 Payload，而不必关心 Payload 内部是否又是一个完整的协议栈。
