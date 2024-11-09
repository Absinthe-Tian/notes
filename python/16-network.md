---
title: 16.网络编程
date: 2022-10-16 00:00:00
categories: Python语言
tags: 
  - socket
  - tcp
  - udp
  - 网络协议
---

# 网络协议

## TCP/IP协议栈的五层模型：

1. 物理层（Physical Layer）：物理层定义了物理介质、电压级别、数据传输速率等硬件细节，以确保数据能够在网络上传输。它负责传输比特流。物理层包括有关电缆、光纤、无线传输等物理媒介的规范。
2. 数据链路层（Data Link Layer）：数据链路层负责数据的分帧、物理地址寻址（MAC地址）、流量控制和错误检测。它通常分为两个子层：逻辑链路控制（LLC）和媒体访问控制（MAC）。以太网协议（Ethernet）是数据链路层中的一个常见协议，一条数据帧由报头和报文构成，报头18个字节由源地址、目标地址、数据描述信息三部分组成，且地址必须是唯一的MAC地址，已经满足了子网通信。
3. 网络层（Network Layer）：网络层处理数据包的路由和转发，将数据从源主机传输到目标主机，并使用IP地址来标识主机和路由器。IPv4和IPv6是两个常见的网络层协议。遵循ARP协议，将网络层的IP地址映射到数据链路层的MAC地址，已经满足了广域网通信。
4. 传输层（Transport Layer）：传输层负责端到端的数据传输，确保数据可靠性和流量控制。它定义了端口号、数据分段、错误检测和纠正。TCP（传输控制协议）和UDP（用户数据报协议）是两个常见的传输层协议。端口范围`0—65535`。
5. 应用层（Application Layer）：应用层是网络协议栈的最高层，它包含了各种应用程序和服务，例如Web浏览器、电子邮件、文件传输、远程登录等。应用层协议与用户交互，并提供通信的用户界面。常见的应用层协议包括HTTP、FTP、SMTP、POP3、IMAP、DNS等。

## 传输层的TCP/UDP

TCP传输也称流（stream）传输。
UDP传输也称数据报（dgram）传输。

### TCP协议

#### TCP连接的三次握手

1. 客户端发送请求（SYN）：客户端（通常是浏览器、应用程序等）首先向服务器发起连接请求。它发送一个TCP数据包，其中包含一个标志位（SYN，同步）和一个初始序列号（ISN，Initial Sequence Number）。此时，客户端进入SYN_SENT状态，等待服务器的响应。
2. 服务器响应请求（SYN + ACK）：服务器接收到客户端的连接请求后，确认并要求回应。服务器发送一个TCP数据包，其中包含SYN和ACK（确认）标志位，以及一个自己的初始序列号。此时，服务器进入SYN_RECEIVED状态。
3. 客户端确认响应（ACK）：客户端接收到服务器的响应后，发送一个确认（ACK）数据包，以确认收到服务器的确认响应。此时，服务器收到客户端的确认后，连接建立完成，双方可以开始进行数据传输。客户端和服务器都进入ESTABLISHED状态。

#### TCP断开的四次挥手

1. 客户端发送关闭请求（FIN）：客户端首先向服务器发送一个FIN（Finish）标志位，表示客户端不再发送数据，但仍然可以接收数据。此时，客户端进入FIN_WAIT_1状态。
2. 服务器确认客户端的关闭请求（ACK）：服务器接收到客户端的FIN后，会向客户端发送一个确认ACK（Acknowledgment）数据包，以确认收到关闭请求。此时，服务器进入CLOSE_WAIT状态。
3. 服务器发送关闭请求（FIN）：当服务器不再有数据要发送给客户端时，它向客户端发送一个FIN标志位，表示服务器也准备关闭连接。此时，服务器进入LAST_ACK状态。
4. 客户端确认服务器的关闭请求（ACK）：客户端接收到服务器的FIN后，发送一个确认ACK，表示接受服务器的关闭请求。此时，客户端进入TIME_WAIT状态；进入TIME_WAIT状态的客户端会等待一段时间（等待2倍的最大报文段寿命时间），以确保服务器收到确认，然后再正式关闭连接。这个等待状态有助于处理可能在网络中延迟的数据包，以确保连接正常关闭。

### UDP协议

UDP数据包被发送到目标设备，没有建立连接的握手过程。

UDP的优点包括低开销，较小的头部开销（相对于TCP），以及适用于实时应用程序的低延迟。但UDP的缺点是它不提供数据的可靠性，因此在丢失或损坏数据包时，应用程序必须自行处理。这使得UDP更适合于一些特定的应用，而不适合需要可靠数据传输的应用。

# Socket套接字

Socket（套接字）是一种编程接口，用于实现网络通信。它提供了一种抽象层，允许应用程序通过网络进行数据传输。Socket抽象层位于操作系统内核和应用程序之间，提供了一组函数和方法，使应用程序能够创建、绑定、连接、发送和接收数据包。

## TCP传输

### 服务端

```python
import socket

# 指定使用IPv4地址簇，TCP（流）套接字
server = socket.socket(family=socket.AF_INET, type=socket.SOCK_STREAM)
# 端口复用
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR)
# 绑定IP及端口
server.bind(("127.0.0.1", 8000))
# 最大连接数
server.listen(10)

# 阻塞等待连接，返回客户端连接和客户端地址
conn, client_addr = server.accept()
# 接收客户端数据
data = conn.recv(1024)
# 发送数据
conn.send(data.upper())

# 断开连接
conn.close()
server.close()
```

### 客户端

```python
import socket

# 指定使用IPv4地址簇，TCP（流）套接字
client = socket.socket(family=socket.AF_INET, type=socket.SOCK_STREAM)
# 发起连接
client.connect(("127.0.0.1", 8000))

# 发送数据
client.send(input(">>").encode('utf-8'))
# 接收数据
data = client.recv(1024)
# 关闭连接
client.close()
```

> TCP传输时，客户端在发送数据量小且时间间隔很短的情况下，会使用Nagle优化算法，将多个数据包合并发送，在服务端会发生粘包现象。
> 
> 如果接收的数据字节不够，会先通过socket将数据发送到操作系统的内存中，然后网卡通过内存取走一部分数据进行传输，由于数据的残留，也会发送粘包现象。
>
> 解决方法：自定义一个报头，包含数据信息，先发送一个报头长度，根据长度获取报头，对报头进行解析，获取数据的长度，最后根据数据长度多次循环读取即可解决粘包问题。

## UDP传输

### 服务端

```python
import socket

# 指定使用IPv4地址簇，TCP（流）套接字
server = socket.socket(family=socket.AF_INET, type=socket.SOCK_DGRAM)

# 绑定IP及端口
server.bind(("127.0.0.1", 8000))

# 接收数据
data, client_addr = server.recvfrom(1024)

# 发送数据到指定客户端
server.sendto(data.upper(), client_addr)

# 关闭socket
server.close()
```

### 客户端

```python
import socket

# 指定使用IPv4地址簇，TCP（流）套接字
client = socket.socket(family=socket.AF_INET, type=socket.SOCK_DGRAM)

# 发送数据到服务端
client.sendto(input(">>").encode('utf-8'), ('127.0.0.1', 8000))

# 接收数据
data, server_addr = client.recvfrom(1024)

# 关闭socket
client.close()
```

> UDP传输时，虽然没有粘包现象，但是如果接收的数据字节不够，那么会直接丢失剩下的数据，存在数据安全问题。