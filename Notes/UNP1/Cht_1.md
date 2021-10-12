### 本书焦点——TCP/IP协议族 即 网际协议族

Web客户与服务器之间用TCP 传输控制协议 通信，TCP 视同 IP 网际协议 通信，IP通过某种形式的数据链路层通信

### TCP 三次握手

1. 服务器必须调用socket、bind和listen 准备好接收外来的连接， 称为 passive open
2. 客户端通过 connect 发起 active open
   - 导致客户端 TCP 发送一个 SYN 分节，告诉服务器客户将在 待建立的连接中发送的数据的初始序列号
   - 通常 SYN 分节不携带数据，其所在的 IP 数据报 只含有 一个IP首部、一个 TCP 首部 和可能有的TCP选项
3. 服务器必须确认 ACK 客户的 SYN，同时自己也发送一个 SYN 分节，其含有胡武器将在同一连接中发送的数据的初始化序列号
4. 客户必须确认服务器的 SYN

这种交换至少需要3个分组，因此称为 TCP three-way handshake



### TCP 连接终止

1. 某个应用进程首先调用close，称为 active close
   - 该端的 TCP 发送一个 FIN 分节，标识数据发送完毕
2. 接收到这个 FIN 的一端称为 passive close
   - 这个 FIN 由 TCP 确认，接收也作为一个文件结束符 end-of-file，传递给接收端应用进程
3. 一段时间后，接收到这个文件结束符的应用进程调用close 关闭它的套接字
   - 该端的 TCP 也发送一个 FIN
4. 接收这个最终 FIN 的原发送端 TCP 确认 这个 FIN

每个方向都需要一个 FIN 和 ACK，通常需要4个分节

类似 SYN，一个 FIN 也占据一字节的序列号空间，因此，每个 FIN 的 ACK 确认号就是这个 FIN 的序列号加1

步骤2-3 之间，passive close 一端 到 active close 一端的流动数据时可能的，称为 half-close

下图未包括两种罕见情况

- 同时打开 simultaneous open
- 同时关闭 simultaneous close

![](https://i.loli.net/2021/10/12/cn6pB4NVKTW9OFI.jpg)

- TIME_WAIT 状态存在的两个理由
  - 可靠地实现TCP全双工连接的终止
  - 允许老的重复分节在网络中消逝



### SCTP 四路握手

1. 服务器通过调用 socket、bind 和 listen 来准备好接收外来的关联，称为 passive open

2. 客户通过调用 connect 或者 发送一个 隐式打开该关联的消息进行 active open

   - 客户 SCTP 发送 一个 INIT 消息，告诉 服务器 客户的 IP地址清单、初始序列号、用于标识本关联中所有分组的起始标记、客户请求的 外出流的数目 以及 客户能够支持的 外来流的数目

3. 服务器以一个INIT ACK 消息确认客户的 INIT 消息

   - 其中含有 服务器的 IP地址清单、初始序列号、起始标记、服务器请求的外出流的数目、服务器能够支持的外来流的数目 以及 一个状态 cookie

   - 状态 cookie 包含服务器用于确信本关联有效所需的所有状态，是数字化签名过的，以确保其有效性

4. 客户以一个 COOKIE ECHO 消息回射服务器的状态 cookie

   - 除 COOKIE ECHO外，该消息可能在同一个分组中还捆绑了用户数据

5. 服务器以一个COOKIE ACK 消息确认客户回射的 cookie 是正确的，本关联建立。

   - 该消息可能在同一个分组中还捆绑了用户数据

以上交换过程至少需要4个分组，称之为 SCTP 的 four-way handshake
