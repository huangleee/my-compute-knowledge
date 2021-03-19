**计算机知识杂烩总结**

介绍：将自己的部分计算机知识，通过绘图等方式记录下来，加深记忆，同时方便以后回顾时，能快速捡起。持续更新中 -- hl


- [计算机网络](#计算机网络)
	- [TCP/IP分层模型](#tcpip分层模型)
	- [数据传输层](#数据传输层)
		- [TCP](#tcp)
			- [三次握手](#三次握手)
			- [四次挥手](#四次挥手)
	- [网络层与链路层](#网络层与链路层)
	- [通信总结：](#通信总结)
	- [扩展](#扩展)
		- [NAT](#nat)
		- [防火墙（iptables | firewalld）](#防火墙iptables--firewalld)
		- [HTTPS](#https)
- [IO](#io)
	- [同步阻塞IO（BIO）](#同步阻塞iobio)
	- [同步非阻塞IO (NIO)](#同步非阻塞io-nio)
	- [IO多路复用](#io多路复用)
		- [分类](#分类)
		- [epoll](#epoll)
	- [各种不同的IO模型对比](#各种不同的io模型对比)
	- [扩展](#扩展-1)
- [操作系统](#操作系统)
	- [进程&&线程](#进程线程)
- [redis](#redis)
	- [简介](#简介)
	- [redis线程、多路复用模型解析](#redis线程多路复用模型解析)
		- [系统调用跟踪](#系统调用跟踪)
		- [线程](#线程)
	- [值数据类型](#值数据类型)
	- [事务](#事务)
		- [命令](#命令)
		- [特性](#特性)
	- [过期策略](#过期策略)
		- [过期策略对持久化的影响](#过期策略对持久化的影响)
	- [内存淘汰机制](#内存淘汰机制)
		- [淘汰策略](#淘汰策略)
	- [高可用](#高可用)
		- [持久化流程分析](#持久化流程分析)
		- [RDB持久化](#rdb持久化)
		- [AOF持久化](#aof持久化)
		- [redis集群主从复制](#redis集群主从复制)
		- [redis集群-哨兵模式](#redis集群-哨兵模式)
		- [redis集群-集群模式](#redis集群-集群模式)
	- [扩展](#扩展-2)
		- [缓存击穿](#缓存击穿)
		- [缓存穿透](#缓存穿透)
		- [缓存雪崩](#缓存雪崩)
		- [分布式锁](#分布式锁)
- [docker](#docker)
- [kubernetes](#kubernetes)
- [nginx](#nginx)
- [mysql](#mysql)
	- [存储引擎](#存储引擎)
	- [索引](#索引)
		- [索引查找过程](#索引查找过程)
		- [索引类型](#索引类型)
		- [名词解析](#名词解析)
	- [事务](#事务-1)
		- [四大特性(ACID)](#四大特性acid)
- [算法](#算法)
	- [树](#树)
		- [二叉树](#二叉树)
			- [深度优先搜索DFS](#深度优先搜索dfs)
			- [广度优先搜索BFS](#广度优先搜索bfs)
		- [二叉搜索树](#二叉搜索树)
		- [N叉树](#n叉树)
		- [前缀树](#前缀树)
	- [链表](#链表)
		- [单向链表(golang实现)](#单向链表golang实现)
		- [双向链表(golang实现)](#双向链表golang实现)
	- [回溯算法](#回溯算法)
	- [DP](#dp)
	- [LRU算法](#lru算法)
		- [实现示例(golang)](#实现示例golang)
- [设计模式](#设计模式)

# 计算机网络 #

## TCP/IP分层模型
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/TCP-IP%E5%88%86%E5%B1%82%E6%A8%A1%E5%9E%8B.png)

- 数据包流转整体流程
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E6%95%B4%E4%BD%93%E6%B5%81%E7%A8%8B.png)

- 数据包编解码流程
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E6%95%B0%E6%8D%AE%E5%8C%85%E7%BC%96%E7%A0%81%E8%A7%A3%E7%A0%81%E6%B5%81%E7%A8%8B.png)

- 数据包编解码示例
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E6%95%B0%E6%8D%AE%E5%8C%85%E7%BC%96%E7%A0%81%E8%A7%A3%E7%A0%81%E7%A4%BA%E4%BE%8B.jpg)

## 数据传输层
### TCP

#### 三次握手
1. 状态变更

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E7%8A%B6%E6%80%81%E5%8F%98%E6%9B%B4.jpg)

2. 注意事项
	1. 因为传输层是面向连接的、可靠的传输层协议，所以**接收方每次收到数据包之后，都会回一个ack的包**，确认应答。如果发送方没有收到应答，就会尝试再次发送该数据包，直到收到应答。**重试会有一个时间间隔**，多次重试之后，会认为网络异常，断开连接。
	2. 同时为了保证数据包的有序性，会在数据包中，加上一个序列号，用于表示数据包的顺序。
	3. 发送方为了提高速度，还会**一次性发送多个数据包**，接收方收到多个包之后，一次性发起多个包的相应应答。发送方收到应答之后，继续发送。如果没有收到应答，或者缺少了某个数据包的应答相应，发送方还需要重新发送该数据包。即**滑动窗口控制**


#### 四次挥手

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E7%8A%B6%E6%80%81%E5%8F%98%E6%9B%B4.jpg)

## 网络层与链路层

mac地址变更流程

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/mac%E5%9C%B0%E5%9D%80%E5%8F%98%E6%9B%B4.png)

## 通信总结：
- 客户端
  1. 用户进程想要访问某个服务器的某个应用，发送对应的数据包，则需要知道服务器IP、端口信息，并得知道是采用TCP 还是 UDP。
  2. 在用户进程，准备好数据之后，并将要发送的数据，按照指定的应用层协议，封装数据包，并将数据包，以及上面的连接信息，调用内核。
  3. 数据传输层收到调用信号之后，判断是何种类型的包，如果是TCP，会构造新的数据包，主动发起三次握手，从而与服务端建立连接。然后在原数据包前，加上数据传输层的头部信息（源端口号、源协议类型、目的端口号、目的协议类型）。然后继续调用网络层。UDP不进行细致讲解。
  4. 网络层收到调用请求后，会根据目的IP，去本机的路由表，用目的IP，依次与路由规则中的掩码，进行二进制与操作，得到网络地址，从而找到下一跳的IP地址。找到之后，会将原数据包，加上IP层的头部信息（源IP、目的IP），并调用链路层传递数据包。
  5. 链路层在知道下一跳的IP地址之后，会去自己的arp表中，查找该IP对应的mac地址，如果找不到，就会广播一个arp数据包，问局域网内的哪台主机有该IP地址。如果主机没有该IP地址，会忽略该包，不回复。如果主机有该IP，会回复一个数据包给原主机，告诉它IP地址对应网卡的mac地址。然后原主机，就根据找到的mac地址，在原数据包上，加上链路层的头部信息（源mac地址、目的mac地址）。
  6. 调用物理层，将数据包发送出去。

- 中间节点  /  服务端
  1. 网卡收到数据包之后，链路层会解包，判断头部信息中，目的mac地址是否是本机的mac地址，不是则丢弃，是则接着往下走。
  2. **此时会到防火墙，并经过防火墙规则过滤之后，继续往下走。**
  3. 判断数据包头部信息中的数据类型，传给对应的模块，如IP、ARP等。
  4. 到IP层之后，解包，判断目的IP是否是本机IP，如果是，则继续将数据包，传递给链路层。如果不是，则需要转发出去（需要开启转发功能），去本地的路由表中，找下一跳，然后调用链路层，传递数据。
  5. 到链路层之后，会根据目的协议和目的端口，找到对应的进程id，将数据从缓冲区，拷贝到对应的用户进程中。
  6. 用户层则根据用户层协议，得到最终数据。

用快递打个比方，模拟网络通信：
1. A想发一个快递给B，传输层需要做的就是在快递上写下来收件人姓名。
2. 网络层就是需要在快递上写下小区地址。并告诉快递员，这个快递，你应该先送去哪个地方。
3. 链路层就是直到了要送的地方，就赶快将快递送到下一个地址。

## 扩展
### NAT
解释：网络地址转换

转换方式：
1. SNAT： 将源地址，转换为指定地址（IP+端口）
2. DNAT： 将目的地址，转换为指定地址（IP+端口）

分类：
1. 静态nat转换：	源地址与被转换地址是一对一的关系（指IP）
2. 动态nat转换：	源地址与被转换地址是多对多的关系（指IP）
3. 网络地址端口转换：多个源地址（即多个IP+端口），被转换为同一个IP的多个端口。


### 防火墙（iptables | firewalld）
数据包经过网卡，进入内核中，再由链路层解码之后，判断是本机的mac地址，就需要防火墙来过滤。
	
> iptables与firewalld都不是真正的防火墙，它们都只是用来定义防火墙策略的防火墙管理工具而已，或者说，它们只是一种服务。iptables服务会把配置好的防火墙策略交由内核层面的**netfilter**网络过滤器来处理，而firewalld服务则是把配置好的防火墙策略交由内核层面的**nftables**包过滤框架来处理

- 表链
  - raw表： 加速数据包穿过防火墙的表，也就是增强防火墙性能的表。
  - manage表： 一种特殊的表，通过mangle表可以实现数据包的拆分和还原。
  - filter表： 负责包过滤。
  - nat表： 实现网络地址转换。

- 规则链
  - PREROUTING： 在网络层判断ip之前，即路由决策之前（决策是本机的数据包，还是需要转发的数据包）
  - INPUT： 路由决策之后，只过滤进入本机的数据包。
  - FORWARD： 路由决策之后，只过滤转发的数据包。
  - OUTPUT： 只针对本机发出的数据包，在路由判断之后，进行过滤。
  - POSTROUTING： 针对本机、以及转发的数据包，进行过滤。

- 图示
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/net/%E9%98%B2%E7%81%AB%E5%A2%99.png)

### HTTPS
- 对称加密：客户端与服务端使用的加解密的密码一样
- 非对称加密：客户端与服务端 加解密的密码不一样

- 非对称加密，通信加解密过程

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/net/rsa.png)

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/net/rsa-process.png)

- https使用证书，通信加密过程

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/net/https-ca-ssl.png)

-----------------------------------



# IO
## 同步阻塞IO（BIO）

- 介绍：同步阻塞IO，服务端启动监听端口后，会**阻塞等待客户端连接**。客户端连接来了之后，会**起一个线程，去处理这个连接**。主线程会继续阻塞去等待新的客户端连接。而处理线程，则会**阻塞等待接收客户端发送来的消息**，并处理。

- 示例代码
```python
import socket
import threading


def deal_connect(c):
    while True:
        res = c.recv(1024)
        print(res)
        if res == 'down':
            break
    c.close()


if __name__ == '__main__':
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.bind(('0.0.0.0', 5010))
    s.listen(20)

    while True:
        c, addr = s.accept()
        t = threading.Thread(target=deal_connect, args=(c,))
        t.start()
```

- 内核调用跟踪 （注：linux环境下，使用strace命令，可以追踪系统调用）

  1. 执行命令： strace -ff -o out python BIO.py
  2. 在另一个窗口，可以看到有一个文件出现
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-1.png)
  3. 打开该文件，可以看到系统调用情况。socket(AF_INET, SOCK_STREAM, IPPROTO_IP) = 3 得到一个socket，该socket对应的文件fd为3；将该fd，作为参数传递给 bind 函数，即该 fd 绑定指定IP和端口；接着 调用 listen，即该fd 开始监听连接，最大连接客户端数为 20；最后一行就是阻塞等待客户端的连接
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-2.png)
  4. 此时在另一个窗口，使用nc命令建立客户端连接  nc 127.0.0.1 5010，可看到目录下多了一个日志文件。
  5. 可以看到主线程收到连接请求之后，生成了一个新的fd，指代这个请求。然后创建了一个新的线程处理连接（系统调用中，clone方法创建线程，与主线程有不同的栈，相同的堆），并返回了新的线程的id。然后又阻塞等待新的连接
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-3.png)
  6. 新线程阻塞等待客户端发送数据
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-4.png)
  7. **使用man命令可以查看系统调用介绍，如 man 2 socket,  man 2 clone**

- BIO 对应的系统调用伪代码
```
socket(AF_INET, SOCK_STREAM, IPPROTO_IP) = 3
bind(3, {sa_family=AF_INET, sin_port=htons(5010), sin_addr=inet_addr("0.0.0.0")}, 16) = 0
listen(3, 20)
while:
	accept(3, {sa_family=AF_INET, sin_port=htons(48782), sin_addr=inet_addr("127.0.0.1")}, [16]) = 4	# 阻塞
	clone()
	

# 处理连接线程
while:
	recvfrom(4		 阻塞
```
- 好处与弊端

**好处： 一个服务端可以接受N个客户端的连接，一个客户端对应一个线程**

**弊端： 阻塞调用，会创建N多个线程，消耗服务器资源，同时线程间切换，性能不高。**

--------------------------

## 同步非阻塞IO (NIO)
介绍：同步非阻塞IO：服务端启动监听端口后，**不会阻塞等待客户端连接**。客户端连接建立之后，**不会阻塞等待接收客户端发送的消息**，而是会去处理计算问题（其他连接），过段时间后，**再去向内核(kernel)询问是否有数据**。

- 示例代码
```
import socket


if __name__ == '__main__':
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.setblocking(False)    # socket设置非阻塞
    s.bind(('0.0.0.0', 5010))
    s.listen(20)
    linkList = []       # 所有已建立连接的sock
    delList = []        # 所有需要断开连接的sock
    while True:
        try:
            c, addr = s.accept()        # python 在没有连接时，会抛异常
            c.setblocking(False)  # client 也要设置非阻塞
            linkList.append(c)
        except BlockingIOError:
            pass
        for n in linkList:      # 遍历所有的连接，依次处理
            try:
                res = n.recv(1024)      # 非阻塞情况下调用recv，如果没有数据，会抛异常
                print(res)      # 也会调用内核的 write，即 写IO
                if not res:
                    # 从链表中删除该连接
                    delList.append(n)
            except BlockingIOError:
                pass
            except ConnectionResetError:
                delList.append(n)

        for n in delList:
            linkList.remove(n)
            n.close()
        delList = []
```
- 内核调用跟踪

  1. 执行程序，查看系统调用情况。可见先创建了一个socket，对应的fd为3；**紧接着给这个fd 加了一个非阻塞IO锁**；然后就绑定监听ip和端口；启动监听。此时，可见程序会不停触发 accept 调用。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/NIO-1.png)
  2. 另一个窗口，使用nc建立连接。可见此时有了一个客户端连接，这个sock 对应的fd 为 4；**紧接着给这个fd，也加了一个非阻塞IO锁**；此时程序调用recv 时，内核会立刻返回，而不会使整个程序阻塞。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/NIO-2.png)

- NIO 对应系统调用伪代码
```
list = [] 	# 存储sock
socket(AF_INET, SOCK_STREAM|SOCK_CLOEXEC, IPPROTO_IP) = 3
ioctl(3, FIONBIO, [1])
bind(3, {sa_family=AF_INET, sin_port=htons(5010), sin_addr=inet_addr("0.0.0.0")}, 16) = 0
listen(3, 20)                           = 0
accept4(3, ...)  		# 不阻塞，返回 -1 | sock
if sock:
   ioctl(sock, FIONBIO, [1])		# 给sock加非阻塞IO锁
   list.append(sock)
for sock in list:
   sock.recv()		# 步阻塞， 返回 -1 | str
```

- 好处与弊端

**好处：不用创建多个线程去处理客户端连接，避免资源浪费，单个线程就能非阻塞的处理所有客户端请求。**

**弊端：会不停的触发recv系统调用，即使这些sock并没有数据。频繁的系统调用，会降低程序性能。**


------------------------------------

## IO多路复用
介绍：IO多路复用：使用select、poll、epoll来同时监听多个IO，*当IO对象有数据时，通知用户进程处理*

### 分类
**1. select：效率最低，有最大文件描述符限制。(用户进程查询哪些IO有变化时，需要传入fd列表)**

**2. poll：同select，但是无最大描述符限制。(同select，查询IO变化时，需要传入fd列表，但是此时没有列表中fd的数量限制)**

**3. epoll：效率最高，没有最大描述符限制。(内核会记录所有注册的IO，用户进程查询哪些IO有数据时，无需传入fd列表，内核会告诉用户进程所有的有数据的IO列表)**

### epoll
- linux系统下epoll操作
  - epoll_create: 创建一个epoll对象，返回一个fd，代表该epoll对象(linux下一切皆文件)
  - epoll_ctl: 控制一个epoll对象。定义: int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
    - op: EPOLL_CTL_ADD, EPOLL_CTL_MOD, EPOLL_CTL_DEL
    - event: EPOLLIN, EPOLLOUT, EPOLLRDHUP , EPOLLPRI, EPOLLERR, EPOLLHUP, EPOLLET, EPOLLONESHOT 
  - epoll_wait: 等待io事件，返回事件列表。

- python3 使用epoll示例
```
import socket
import selectors

# 对应系统调用就是epoll_create
sel = selectors.DefaultSelector()       # 会默认选择当前系统最适合的方式：select | poll | epoll


def accept(sock, mask):
    conn, addr = sock.accept()
    conn.setblocking(False)
    sel.register(fileobj=conn, events=selectors.EVENT_READ, data=read)


def read(conn, mask):
    try:
        data = conn.recv(1024)
        if not data:
            sel.unregister(fileobj=conn)
            return
        print(data)
        conn.send(b"server return: %s" % data)
    except ConnectionResetError:
        sel.unregister(fileobj=conn)
        conn.close()


if __name__ == '__main__':
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.bind(('', 5010))
    s.listen(5)
    s.setblocking(False)  # 设置socket的接口为非阻塞
    
    # 对应系统调用就是 epoll_ctl
    sel.register(fileobj=s, events=selectors.EVENT_READ, data=accept)   # fileobj 事件(即fd), events：EVENT_READ|EVENT_WRITE, data: 有IO时，回调函数
    while True:
    	# 对应系统调用就是 epoll_wait
        events = sel.select(timeout=None)
        for event_obj, mask in events:
            callbackfunc = event_obj.data
            callbackfunc(event_obj.fileobj, mask)
```
- 系统调用跟踪
  1. 执行程序，查看系统调用。
    1.1 可见先调用epoll_create创建了epoll，对应的fd为3
    1.2 然后创建了socket，对应fd为4
    1.3 给socket 绑定监听端口、ip，设置为非阻塞IO，并开始监听
    1.4 调用epoll_ctl，将socket添加到epoll中
    1.5 程序调用 系统调用epoll_wait，等待返回有IO变化的事件。(此处是阻塞调用，也可以设置非阻塞，从而让程序可以执行其他计算操作)
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/epoll-1.png)
  2. 另一个窗口建立连接，观察系统调用。
    2.1 epoll_wait 返回了一条有IO变化的事件
    2.2 应用程序拿到这个事件之后，判断是有连接来了，调用accept建立连接
    2.3 建立连接之后，得到sock，并设置为非阻塞IO
    2.4 继续调用epoll_ctl，将sock也添加到epoll中，由epoll来监控IO变化
    2.5 应用程序逻辑处理完成，调用epoll_wait，等待新的IO事件。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/epoll-2.png)

  3. 在已有连接，发送一条数据，观察系统调用。
    3.1 epoll_wait 再次返回了一条有IO变化的事件
    3.2 应用程序拿到事件后，判断是sock读取事件，就调用recvfrom，读取客户端发来的消息
    3.3 系统调用write，打印到标准输出，fd为1，即标准输出
    3.4 系统调用sendto，发送到客户端
    3.4 应用程序逻辑处理完成，继续等待新的IO事件。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/epoll-3.png)

- 系统调用伪代码
```
epoll_create() -> 3
socket() -> 4
ioctl(4) 
bind(4, ip, addr)
listen(4)
epoll_ctl(3, EPOLL_CTL_ADD, 4, )
while:
   events = epoll_wait(3)
   for event in events:
   	if 事件 == accept:
	   sock = accept() -> 5	# 系统调用accept，得到sock
	   ioctl(5) 
	   epoll_ctl(3, EPOLL_CTL_ADD, 5, )
	if 事件 == recv:
	   data = recv()	# 系统调用recv
```

## 各种不同的IO模型对比

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/compare-1.png)

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/compare-2.png)

**同步IO和异步IO的区别在于，同步IO都需要用户进程主动去调用内核recv去获取数据。而异步IO，用户进程不需要去调用内核拿数据，内核会主动将数据拷贝到用户进程空间，再通知用户进程去buff中取**

## 扩展
- 内核
  - 内核是操作系统启动时，**第一个加载到内存中运行的程序**。
  - **内核控制所有硬件设备，应用程序没办法直接操作硬件设备**，必须通过系统调用的方式，来调用内核，完成对硬件设备的间接使用。即内核提供了方法，供应用程序调用，以达到使用硬件的目的。
  - 所有的IO模型演变，都是基于内核来实现的，只有内核更新了，支持某种IO模型，应用程序才能使用。
  - **应用程序并非通过系统调用，直接调用内核中的方法**，而是通过cpu，来间接调用内核中的方法。因为出于保护内核的原因，**内核有保护模式，应用程序无法直接访问内存中的部分地址**，只能通过cpu间接调用。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/cpu.png)

---------------
# 操作系统
介绍：计算机系统由处理器(CPU)，内存，等各种输入输出设备的硬件设备组成。但是我们写的程序，处于安全的考虑，不能直接操作这些硬件设备，所以必须有一个程序，来帮助其他应用程序，完成硬件设备的操作，这个程序就是**操作系统**(kernel)。

- **中断**：在上面的IO中，最后一张图已提到过中断的概念，现在再介绍一下，如图所示：
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/operation/interrupt.png)

- DMA：
  - 磁盘在将数据放入到磁盘缓冲区之后，使用中断的方式，来让CPU去将数据拷贝到内核，再拷贝到用户空间
  - DMA 则是 在数据准备完之后，直接由 DMA 控制器，直接将数据拷贝到内存中，而不用通过中断的方式，来让CPU搬运数据


- **指令级别**

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/operation/instrcut-level.png)

- **系统调用流程**
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/operation/system-call.png)


## 进程&&线程
- 进程：操作系统**资源分配的最小单位**
- 线程：任务**调度和执行的基本单位**。多线程共用进程的内存空间。

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/operation/process-thread-coroutine.png)

- 写时复制：

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/operation/copy-on-write.png)


-----------
# redis
## 简介
- 一种kv的非关系型数据库，数据全部存储在内存中，当重启或宕机时，数据会全部丢失。但是支持数据持久化，将数据保存在磁盘中，重启时加载，进而恢复数据。
- redis是单线程，基于epoll的。(6.0之后，支持多线程，但是对数据的操作，还是单线程，即只有一个work)。

## redis线程、多路复用模型解析
### 系统调用跟踪
**redis使用epoll来处理并发请求**。同时**redis调用epoll_wait()时，是非阻塞的**，因为redis除了处理请求外，还有一些其他操作，如定时执行持久化操作等，如果阻塞等待请求，在没有请求时，这些操作永远无法执行。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/IO-1.png)

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/IO-2.png)

### 线程
**redis使用单线程执行work**。6.0以后的版本，可以使用多线程，但是还是只有一个work线程，其他线程只是辅助线程，帮助并发执行读取或写入操作，所有的计算操作，还是在同一个work中。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/thread.png)

## 值数据类型
- string：字符串。**bitmap**
- list：有序的字符串列表。
- hash：哈希键值对。
- set：字符串无序集合，不可重复。
- zset：字符串有序集合，不可重复，添加值时，需要给值加上分数，用于排序。

## 事务
### 命令
- Multi：标记一个事务块的开始
- Exec：执行事务块内所有的命令
- Discard：取消事务
- Watch：监视一个(或多个)key，如果事务执行之前，key的值被其他命令修改了，事务被打断
- UnWatch：取消watch命令对所有key的监视

### 特性
- 事务中的所有命令，会按顺序串行执行
- **事务开始之后，不会在为其他客户端使用**
- 队列中的任务要么全部处理，要么全部丢弃
- 如果开启了AOF，写操作全部会被写入到aof文件中，防止数据丢失
- 不支持回滚。开启事务之后，在事务exec（提交前），所有命令都会被检查(**类似编译，编译失败，命令全部执行失败**)。如果检查通过了，再提交事务，发现有命令执行失败，此时会跳过错误命令，而且事务不会失败，同时不能回滚到事务之前的状态(**类似编译通过，运行时错误，此时部分会成功，部分会失败，不能回滚**)。

## 过期策略
- 惰性删除：客户端每次访问key时，检查key是否过期。能节省cpu计算资源，但是会浪费内存资源。
- 定时删除：在给key设置过期时间时，会为key创建一个定时器，key过期立刻被定时器删除。很消耗cpu资源，但是节省内存资源。
- 定期删除：每个一段时间，定时任务对设置了过期时间的key进行扫描，过期的key，删除。折中方案。

redis使用惰性删除和定期删除，和通过redis.conf 中配置hz 来配置扫描频率，默认为10，每秒进行10次过期扫描：
- 从过期字典中随机20个key
- 删除这20个key中已过期的
- 如果超过25%的key过期，则重复第一步
- 设置了扫描的时间上限，默认不会超过25ms


### 过期策略对持久化的影响
- rdb持久化：
  - 在持久化到rdb文件之前，会检查key是否过期，过期的key不持久化到rdb文件中。
  - 在从rdb文件中恢复数据时，会判断key是否过期，已过期的key不会导入到数据库中

- aof持久化：
  - **因为aof持久化，记录的是执行的命令，所以在持久化时，不需要去检查key是否过期，全部写入即可，在key过期之后，也会有对应的删除命令被记录下来。**
  - **aof重写，原aof文件过大时，触发aof重写，此时会检查原aof中的key是否已过期，已过期的话，就不会记录，减少记录的指令数。**

## 内存淘汰机制
介绍：**因为redis使用内存存储数据，所以会有个最大内存使用量的限制，当达到这个阈值时，会触发淘汰机制**

### 淘汰策略
- noeviction(默认策略)：对于写请求不再提供服务，直接返回错误（DEL请求和部分特殊请求除外）
- allkeys-random：从所有key中随机淘汰数据
- allkeys-lru：从所有key中使用LRU算法进行淘汰
- allkeys-lfu：在所有的key中使用LFU算法淘汰数据
- volatile-random：从设置了过期时间的key中随机淘汰
- volatile-lru：从设置了过期时间的key中使用LRU算法进行淘汰
- volatile-ttl：在设置了过期时间的key中，根据key的过期时间进行淘汰，越早过期的越优先被淘汰
- volatile-lfu：在设置了过期时间的key中使用LFU算法淘汰key

## 高可用
### 持久化流程分析
1. 用户程序调用 system-call 的 write，将数据从用户程序buffer中，写入到内核buffer中
2. 内核将内核buffer中的数据，转移到磁盘缓冲区
3. 内核调用fsync，将数据从磁盘缓冲区，落盘到真正的硬盘介质中。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/io.png)

故障分析：
- 如果在用户程序，在执行第一步之前，出现用户程序宕机的情况，此时数据就丢失了。
- 如果在执行第一步之后，执行第三步之前，此时数据在内核中；用户程序宕机，不会出现数据丢失。如果出现服务器宕机，内核也崩了，此时数据就会丢失。
- 如果在执行第三步之后，数据已经落盘，即时服务器崩溃，数据也不会丢失

### RDB持久化
介绍：rdb在某个时刻，对数据库进行的类似**快照**的功能。会将当前时刻下，数据库中所有的数据，以二进制的形式，持久化到磁盘中的rdb文件。在数据库重启时，通过rdb文件，恢复数据库数据。

- **触发方式**
  - save命令：**阻塞执行，会导致redis服务暂停执行其他客户端命令**
  - bgsave命令：非阻塞执行，后台异步执行内存快照，同时主程序继续响应客户端请求。实现方式：**主进程fork一个子进程**(根据操作系统原理可知，fork的子进程，和主进程有相同的内存映像，即会从主进程，将数据拷贝一份到子进程空间)，子进程会将当前的内存数据，全部快照到rdb文件中。主进程继续接收请求(此时新的请求，子进程不会感知到)。
  - 自动触发：配置文件设置 "save m n", 即 m 秒 有 n 次修改，自动触发bgsave。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/bgsave.png)

- **优势与劣势**
  - 优势： 二进制文件，恢复速度快。
  - 劣势： 易丢数据。如果在某个时间段内，未执行快照，数据丢失。

### AOF持久化
介绍：简而言之就是**操作日志**。redis将写操作的命令，记录下来，持久化到aof文件中。

- **触发方式**
  - always：每次写操作，都记录日志到aof文件。**注：此时走的是完整的持久化流程，直接落盘**，故而及其影响性能。
  - everysec：每隔一秒，将操作日志，记录到aof文件。**注：1s内的操作会记录到缓冲区中，到时间，走完整的持久化流程，落盘**，可能会丢1s内的数据。
  - 不同步：redis只将操作日志记录到了缓冲区，具体何时调用fsync，由内核自己控制。易丢失数据

**引入问题：因为aof记录的是写操作的日志，随着时间线拉长，文件会越来越大，而文件中的很多操作，也过期了，也会影响到恢复时的性能（执行了很多无效命令）。
- **aof重写**：为解决上面的问题，redis引入了aof重写机制，即bgrewrite，当aof文件大小超过阈值时，触发重写操作，生成新的aof文件。
  - 主进程会**fork一个子进程，即快照的形式**，得到当前内存的数据，然后**将内存数据，以命令的形式改写**，写到新的临时的aof文件。
  - 主进程同时创建一个临时的的aof 写入 buffer，在完成重写之前，所有的新的写入日志都会记录到这个buffer中。
  - 子进程写入完成之后，主进程会将临时buffer中的数据，落盘到临时文件。
  - 主进程在临时buffer中的数据落盘完成之后，将临时文件，替换旧的aof文件。
  - 新的写入操作，重新开始写入旧的buffer，并最后落盘到旧的aof文件
 ![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/aof_rewrite.png)
 
 - **优势与劣势**
   - 优势：
     - 数据不易丢失
     - 误操作可恢复(记录的是日志，恢复到误操作之前的日志即可)
   - 劣势：
     - 恢复速度慢，记录的是命令，需要一步步执行。
     - 文件体积大
 
 **RDB与AOF持久化，都只能一定限度内，解决程序或操作系统宕机的情况，无法解决硬件损坏时，系统的高可用**
 ### redis集群主从复制
 介绍：在redis主服务器的硬件设备出现问题的情况下，**手动切换**程序的redis为另一台从服务器，保证系统的可用性。
 
 - **同步方式**
   - 全量复制：一般在从服务器第一次加入集群时，执行全量复制。从redis加入之后，给主redis发送SYNC命令，来进行全量复制。从服务器可以随时发起SYNC指令来进行全量复制
   - 增量复制：主服务器接收写操作时，会发送一份给从服务器。

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/sync.png)
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/redis/sync-add.png)

### redis集群-哨兵模式
介绍：主从模式在主服务器出现宕机时，需要手动切换主节点，此时在宕机到手动切换过程中，服务处于不可用状态。因此可以增加一个哨兵进程，这个进程会不停监控所有节点的状态，如果发现主节点不可用了，主动将一个从节点切换为主节点。

### redis集群-集群模式
介绍：多个redis主节点，构成一个集群，每个主节点都可以配置从节点。每个主节点存储不同的数据，一个主节点以及它对应的从节点都宕机时，集群不可用。

## 扩展
**redis 最常用的场景是作为缓存，以及实现分布式锁**

### 缓存击穿
介绍：客户端请求服务端的某个数据，该数据在数据库中，但是在redis中，已经过期了，因此就会直接查询到数据库中。

### 缓存穿透
介绍：客户端请求服务端的某个数据，该数据不在数据库中，也不在redis中。

### 缓存雪崩
介绍：基于缓存击穿，即客户端同时访问多个数据，而这些数据在redis中都都过期了，大量的查询落到数据库上。（尽量给数据设置不同的过期时间，避免大量缓存数据同时过期）

### 分布式锁
分布式锁：在分布式场景下，**数据只有一份，而服务有多个**，为了避免多个服务**同时**对同一个数据操作，造成数据损坏的情况，就必须给数据加锁，来保证多个服务，对同一个数据的串行化操作。

- 分布式锁实现方法
  - 数据库：操作数据之前，给公共的mysql或其他数据库，插入一条数据，其他服务想要操作该数据时，发现数据库中有记录，就不能操作。
  - redis
  - zookeeper

- redis实现分布式锁：因为redis是单线程的，所有的命令在其内部都是串行执行。
  - **setnx + expire**：setnx 即 set if not exists，如果key不存在，则设置。使用setnx就能保证只有第一个服务能执行成功。expire 给key加上过期时间，避免长时间占用锁。**存在问题：setnx 与 expire两个命令不是原子操作，如果setnx成功，而expire设置失败，即没有成功设置超时时间，就会造成死锁。**
  - **set**: 使用set命令，同时设置过期时间，保证原子性

```
set key value [EX seconds] [PX milliseconds] [NX|XX]
EX seconds：设置失效时长，单位秒
PX milliseconds：设置失效时长，单位毫秒
NX：key不存在时设置value，成功返回OK，失败返回(nil)
XX：key存在时设置value，成功返回OK，失败返回(nil)
```



-------------------
# docker

---------------
# kubernetes

- pod 间网络通信
 ![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/kuberneters/k8s-pod2pod-network.png)

----------------
# nginx

------------------
# mysql
## 存储引擎
介绍：数据在磁盘上的不同组织形式。引擎不同，在磁盘上的表现形式不同。因此，在数据的存储、读取方式上，都会有差别。

- 类型：
  - **InnoDB**
  - **MyISAM**
  - MEMORY
  - FEDERATED
  - PERFORMANCE_SCHEMA
  - MRG_MYISAM
  - BLACKHOLE
  - CSV
  - ARCHIVE

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/mysql/engines.png)

- 底层数据结构：大部分存储引擎，索引底层的数据结构，默认都是B+树，而MEMEORY ，底层数据结构是 哈希

**B+ 树 是对 B树的优化，以下为 B树查找示例**
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/mysql/BTree.png)

- InnoDB: 默认存储引擎。
  - **支持事务**
  - **支持表级锁、行级锁**，适合高并发写入场景。同时，开销会因为行级锁变大，也更容易死锁。
  - **支持外键**

- MyISAM
  - 不支持事务
  - 表级锁，不适合高并发写入场景。锁带来的开销会小，不容易死锁。
  - 不支持外键
  - 查询性能更好，适用于大量查询的场景。

**注：MyISAM存储引擎，单独记录了数据库中数据的数量，所以在查询数据数量(count操作)，会有很高的效率。而InnoDB则需要通过计算得到。

## 索引
介绍：索引用于提高查询效率。数据在磁盘中是按块存放的，使用索引，能尽量多的减少IO，快速定位到指定的块，并找到指定的数据。

- InnoDB引擎
   - **一条数据必须要跟某个索引绑定**。索引可以是主键，也可以是唯一索引，亦或者一个6字节的rowid。优先于主键绑定，没有主键，再是唯一索引，如果都没有，会生成一个6字节的row_id为主键。
   - **数据最终存放在主键的树的叶子结点**，如果有多个其他列索引，**其他列索引的叶子结点，会存该记录对应的主键**。通过非主键查找时，会先找到该记录的主键，最后通过主键，在主键的数中找到结果(**回表**)。

- 聚簇 | 非聚簇索引
  - 聚簇索引：数据与索引放在一起，称为聚簇索引
  - 非聚簇索引：数据与索引没有放在一起

- MyISAM引擎
  - 数据与索引分别存放在不同的文件，索引只存储数据的地址。(故而只有非聚簇索引)

### 索引查找过程
- IO与预读：数据存储在磁盘中，而在磁盘中则是**以块的形式存放，一堆数据组成一个块**。想要读取某个地址的数据时，会**把该地址所在的块，全部读取到内存中**，然后在内存中找到指定数据。
- 索引底层数据结构是B+树。
- 查找指定元素，会从**树的根节点查起**。首先会**读取根节点的块到内存中**，然后使用二分查找，**从中找到符合的索引**、或者指向下一个块的指针。若是指针，则**继续读取指针指向的下一个块的数据到内存中**，继续上面的查找过程。**每次读取数据库到内存，就是一次IO操作**，而在内存中的查找是很快的。如果**没有索引，则每个块都需要读出来查找一次，IO次数会很多**。

### 索引类型
- 普通索引(index)
- 唯一索引
  - 主键索引(primary key)：唯一且不能为空
  - 唯一索引(unique)：唯一
- 联合索引
  - 联合主键索引：primary key(id,name)
  - 联合唯一索引：unique(id,name)
  - 普通联合索引：index(id,name)
- 全文索引
- 空间索引

### 名词解析
  - 回表：表中有多个索引，此时需要获取某条记录的全量数据, 如: select * from user where name = 'xx'; (主键为id，name为普通索引)。此时需要通过索引 name 的B+树的叶子节点，查找到对应数据在主键B+树上的索引，再去主键的树上查找，并拿到全量数据。
  - 索引覆盖：表中有多个索引，此时需要获取某条记录的部分数据（只包含索引与主键索引）, 如: select id, name from user where name = 'xx'; 此时只需要在 索引 name 的 B+ 树上找到对应的叶子节点，就能得到 主键id 和 自己的数据。

  - 最左匹配原则: **创建索引时，使用了联合索引**，如(name, age, sex)。此时使用name、age、sex 去查找记录时，必须满足最左匹配原则。**只有联合索引才有该限制，但是查询的列，都是索引列时，不需要满足最左匹配原则，索引也生效**
    - 原因：使用联合索引创建B+树时，是按照从左到右创建搜索树的，所以根据联合索引查找数据时，也必须满足从左到右的顺序。
    - 示例：
      - select * from user where name = '张三' and age = 18 and sex = 'man'; 此时满足最左匹配原则，先根据name，找到相同的，再去根据age，最后根据sex查找。
      - select * from user where age = 18 and sex = 'man'; 此时不满足最左匹配原则，因为 name 是联合索引最外层索引，没有 name 就没办法查找。
      - select * from user where name = '张三' and sex = 'man'; 此时满足最左匹配原则，先根据 name 找到相同的，但是 age 缺失，只能从所有满足 name 条件的数据里面，挨个去匹配 sex。
	
  - 索引下推：同最左匹配原则，必须是联合索引才有。
    - 示例：select * from user where name = '张三' and age = '18'; 符合最左匹配原则
    - 没有索引下推时，会通过 name 找到相同的，然后读取所有数据到内存中(从磁盘读出，IO操作)，再根据 age 去查找记录。
    - 有索引下推时，直接通过 name 和 age 去存储引擎中查找符合的数据，再读出并返回(少了中间的磁盘读出操作)。

**最左匹配**
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/mysql/left.png)

**索引覆盖**

虽然使用了联合索引，但是因为查询的列，都是索引列，所以不需要最左匹配原则

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/mysql/cover.png)

**最左匹配场景示例
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/mysql/example.png)

  - 索引失效：使用索引时，一些错误使用会导致索引不生效
    - 使用like时，% 在字符最前面，会导致索引失效
    - 组合索引时，不符合最左匹配规则。
    - 使用or，有些场景会失效，最好在使用时，使用explain 去调试：
      - 当or条件中包含非索引字段，索引失效。
      - 当or条件中都是普通索引时，索引生效。
    - 范围操作(> < between >= <=), 普通索引可以生效，但是在组合索引中，按照最左匹配规则，范围操作后的索引都会失效
    - 查询操作中，有隐式类型转换时，索引失效，如 字段 xx 为 varchar类型，此时用 where xx = 1; 索引就会失效，因为会隐式的将xx的值从int转换为varchar


## 事务

- 名词解释：
  - 脏读：一个事务读到了另一个事务中未提交的数据，即读到的数据不是最终的数据，是脏数据。
  - 不可重复度：同一事务内，多次读取同一个数据，结果都不一致。可能受其他事务的影响，主要针对 update 操作
  - 可重复度：同一事务内，在开始以及结束前任意时刻，读同一数据，都是开始时读到的结果。针对 update 操作
  - 幻读：针对 insert 操作，事务A insert 了一条指定行的记录，但是未提交。事务B 在 A 提交前也 insert 了同一行的记录，此时事务A提交后，查询会觉得 插入 未生效。即 幻读。

  - 一致性锁定读：使用select 时，指定锁
    - SELECT ... LOCK IN SHARE MODE：加共享锁，其他事务只能读，不能改
    - SELECT ... FOR UPDATE：其他事务不能读也不能改

  - 一致性非锁定读：即使用 select 读时，不加任何锁



### 四大特性(ACID)
- 原子性: undo log, 记录并保存 与 执行操作相反的操作，在事务执行失败时，直接执行记录的操作即可。undo log 还参与了mvcc(多版本并发控制)
- 一致性
- 隔离性：通过**锁**来实现，有四种隔离级别。隔离级别越低，效率越高，越不安全
  - 读未提交：一个事务可以读取到另一个事务未提交的数据。带来了脏读、幻读、不可重复读。
  - 读已提交：一个事务只能读取到另一个事务已提交的数据。解决了脏读问题。
  - 可重复读(默认级别)：一个事务重复读取同一个数据的结果是一致的。解决了脏读，不可重复读的问题。
  - 串行化：串行化执行
- 持久性：redo log，保证在发生异常宕机情况下，即时数据没有持久化成功，日志持久化成功，数据也能恢复。


------------
# 算法
## 树
介绍：一种典型的数据结构。树的每个节点都有一个值，以及包含子节点的列表。

### 二叉树
介绍：每个节点都包含一个值，同时，每个节点只有两个子节点，分别是左子节点与右子节点。

- 数据结构：
```
type TreeNode struct {
	Val   int
	Left  *TreeNode
	Right *TreeNode
}
```

- 二叉树遍历
  - 深度优先搜索(DFS)：通常用**递归 或 栈**来实现遍历
    - 前序遍历：根 - 左 - 右
    - 中序遍历：左 - 根 - 右
    - 后续遍历：左 - 右 - 根
  - 广度优先搜索(BFS)：层序遍历，通常使用**队列**来实现遍历

#### 深度优先搜索DFS
- 递归遍历模板
```
func traverse(TreeNode root) {
	// 前序遍历
	traverse(root.Left)
	// 中序遍历
	traverse(root.Right)
	// 后续遍历
}
```

- 前序遍历递归实现
```
func preorderTraversal(root *TreeNode) []int {
	if root == nil {
		return make([]int, 0)
	}
	var nodes = []int{root.Val}
	left := preorderTraversal(root.Left)
	nodes = append(nodes, left...)
	right := preorderTraversal(root.Right)
	nodes = append(nodes, right...)
	return nodes
}
```
- 前序遍历栈实现

```
func preorderTraversal(root *TreeNode) []int {
	// 非递归
	if root == nil {
		return nil
	}
	result := make([]int, 0)
	stack := make([]*TreeNode, 0)

	for root != nil || len(stack) != 0 {
		for root != nil {
			// 前序遍历，所以先保存结果
			result = append(result, root.Val)
			stack = append(stack, root)
			root = root.Left
		}
		// pop
		node := stack[len(stack)-1]
		stack = stack[:len(stack)-1]
		// result=append(result,node.Val) //中序遍历
		root = node.Right
	}
	return result
}
```
- 中序遍历递归实现

```
func inorderTraversal(root *TreeNode) []int {
	var vals = make([]int, 0)

	var inorder func(node *TreeNode)
	inorder = func(node *TreeNode) {	// 匿名函数，闭包
		if node == nil {
			return
		}
		inorder(node.Left)
		vals = append(vals, node.Val)
		inorder(node.Right)
	}
	inorder(root)
	return vals
}
```
- 中序遍历栈实现
```
func inorderTraversal(root *TreeNode) []int {
	var vals = make([]int, 0)
	var nodes = make([]*TreeNode, 0)
	cur := root
	for len(nodes) > 0 || cur != nil {
		for cur != nil { // 把当前节点的左节点依次压入栈中，那么栈中栈顶，就是最左节点
			nodes = append(nodes, cur)
			cur = cur.Left
		}
		node := nodes[len(nodes)-1]
		nodes = nodes[0 : len(nodes)-1] // 出栈，此时就是最左节点
		vals = append(vals, node.Val)
		if node.Right != nil { // 如果当前节点还有右节点，那意味这当前节点属于某个根节点
			cur = node.Right
		}
	}
	return vals
}
```

- 后续遍历递归实现
```
func postorderTraversal(root *TreeNode) []int {
	var vals = make([]int, 0)
	var postorder func(node *TreeNode)
	postorder = func(node *TreeNode) {
		if node == nil {
			return
		}
		postorder(node.Left)
		postorder(node.Right)
		vals = append(vals, node.Val)
	}
	postorder(root)
	return vals
}
```

- 后续遍历栈实现
```
func postorderTraversal(root *TreeNode) []int {
	// 通过lastVisit标识右子节点是否已经弹出
	if root == nil {
		return nil
	}
	result := make([]int, 0)
	stack := make([]*TreeNode, 0)
	var lastVisit *TreeNode
	for root != nil || len(stack) != 0 {
		for root != nil {
			stack = append(stack, root)
			root = root.Left
		}
		// 这里先看看，先不弹出
		node := stack[len(stack)-1]
		// 根节点必须在右节点弹出之后，再弹出
		if node.Right == nil || node.Right == lastVisit {
			stack = stack[:len(stack)-1] // pop
			result = append(result, node.Val)
			// 标记当前这个节点已经弹出过
			lastVisit = node
		} else {
			root = node.Right
		}
	}
	return result
}
```

#### 广度优先搜索BFS
```
func levelOrder(root *TreeNode) [][]int {
	var vals = make([][]int, 0)
	if root == nil {
		return vals
	}
	// 创建一个队列（用切片模拟）
	var queue = []*TreeNode{root}
	nodes := len(queue) // nodes 记录 每层 的 节点数量
	for nodes > 0 {
		inside := make([]int, nodes)
		// 从 队列中，取出 这层的节点，然后将每个节点的左右子节点，也放入到队列中，并将这个节点的值，放到该层的切片中
		for i := 0; i < nodes; i++ {
			node := queue[0]
			queue = queue[1:]
			inside[i] = node.Val
			if node.Left != nil {
				queue = append(queue, node.Left)
			}
			if node.Right != nil {
				queue = append(queue, node.Right)
			}
		}
		// 将这层的切片，放入到总的二维切片中
		vals = append(vals, inside)
		// 设置下一层的节点数量
		nodes = len(queue)
	}
	return vals
}
```

### 二叉搜索树
介绍：二叉树的一种特殊表现形式，具体表现为：

- 每个节点中的值必须大于（或等于）存储在其左侧子树中的任何值。
- 每个节点中的值必须小于（或等于）存储在其右子树中的任何值。

- 二叉搜索树查找元素
```
// 递归
func searchBST(root *TreeNode, val int) *TreeNode {
	var helper func(node *TreeNode) *TreeNode
	helper = func(node *TreeNode) *TreeNode {
		if node == nil {
			return nil
		}
		if node.Val == val {
			return node
		} else if node.Val > val {
			return helper(node.Left)
		} else {
			return helper(node.Right)
		}
	}
	return helper(root)
}

// 迭代
func searchBST1(root *TreeNode, val int) *TreeNode {
	for root != nil && root.Val != val {
		if root.Val > val {
			root = root.Left
		} else {
			root = root.Right
		}
	}
	return root
}
```

- 二叉搜索树插入元素: 在二叉搜索树中，找到合适的叶子节点，然后创建新节点，插入到该叶子节点合适的位置。
```
// 递归
func insertIntoBST(root *TreeNode, val int) *TreeNode {
	if root == nil {
		return &TreeNode{Val: val}
	}
	if root.Val > val {
		root.Left = insertIntoBST(root.Left, val)
	} else {
		root.Right = insertIntoBST(root.Right, val)
	}
	return root
}

// 递归
func insertIntoBST1(root *TreeNode, val int) *TreeNode {
	if root == nil {
		return &TreeNode{Val: val}
	}
	var node = root
	for node.Val != val {
		if node.Val < val && node.Right == nil {
			node.Right = &TreeNode{Val: val}
			break
		} else if node.Val < val && node.Right != nil {
			node = node.Right
		} else if node.Val > val && node.Left == nil {
			node.Left = &TreeNode{Val: val}
			break
		} else if node.Val > val && node.Left != nil {
			node = node.Left
		}
	}
	return root
}

```

- 二叉搜索树删除元素：

```
/*
分析：
1. 如果要删除的节点，是叶子节点，则直接就可以删除
2. 如果要删除的节点，不是叶子节点，有左右子树，则将其右子树，重新构造成一个（根节点没有左子树的二叉搜索树），然后将构造的树，替换该节点，原节点的左树，为新数的左子树
3. 如果要删除的节点，不是叶子节点，有左树，没有右子树，直接将其左节点，替换该节点
4. 如果要删除的节点，不是叶子节点，有右树，没左树，则直接将其右节点，替换该节点
*/
func deleteNode(root *TreeNode, key int) *TreeNode {
	if root == nil {
		return nil
	}
	if root.Val > key {
		root.Left = deleteNode(root.Left, key)
	} else if root.Val < key {
		root.Right = deleteNode(root.Right, key)
	} else {
		if root.Left == nil && root.Right == nil {
			return nil
		}
		if root.Left != nil && root.Right == nil {
			return root.Left
		}
		if root.Left == nil && root.Right != nil {
			return root.Right
		}
		// 如果要删除的节点有左右子树
		var t = root
		root = min(root.Right)          // 从其右子树中，找到最小的节点，即将自己替换成其右子树中的最小值
		root.Right = deleteMin(t.Right) // 再把原来节点右子树中的最小节点删掉，并赋值给最小节点的右子树     即相当于把右树中最小的节点，挪到了自己这个位置
		root.Left = t.Left              // 然后将自己的左子树，换成最小节点的左子树
	}
	return root
}

// 找到树中，最小的节点
func min(node *TreeNode) *TreeNode {
	// 如果没有左节点，此时自己就是最小的
	if node.Left == nil {
		return node
	}
	// 如果有左节点，最小的节点，在其左子树中
	return min(node.Left)
}

func deleteMin(node *TreeNode) *TreeNode {
	if node.Left == nil {
		return node.Right
	}
	node.Left = deleteMin(node.Left)
	return node
}
```

### N叉树
介绍：n个节点的树。n叉树的中序遍历没有标准定义，不考虑。

- 数据结构：
```
type Node struct {
	Val      int
	Children []*Node
}
```

- 深度优先搜索递归遍历模板
```
func traverse(TreeNode root) {
	// 前序遍历
	for i := 0; i < len(root.Children); i++ {
		traverse(root.Children[i])
	}
	// 后序遍历
}
```

- 前序遍历
```
func preorder(root *Node) []int {
	var res []int
	var preOrderFunc func(node *Node)
	preOrderFunc = func(node *Node) {
		if node == nil {
			return
		}
		// 处理自己的值
		res = append(res, node.Val)
		// 从左到右一个个处理自己的节点
		for i := 0; i < len(node.Children); i++ {
			preOrderFunc(node.Children[i])
		}
		return
	}
	preOrderFunc(root)
	return res
}
```

- 后序遍历
```
func postorder(root *Node) []int {
	var res []int = make([]int, 0)
	var postOrderFunc func(node *Node)
	postOrderFunc = func(node *Node) {
		if node == nil {
			return
		}
		for _, c := range node.Children {
			postOrderFunc(c)
		}
		res = append(res, node.Val)
	}
	postOrderFunc(root)
	return res
}
```
- 层序遍历
```
func LevelOrder(root *Node) [][]int {
	var res = make([][]int, 0)
	if root == nil {
		return res
	}
	var queue = []*Node{root}
	for len(queue) > 0 {
		var nodeVals = make([]int, len(queue))
		nodes := queue[:len(queue)]
		queue = queue[len(queue):]
		for i, n := range nodes {
			nodeVals[i] = n.Val 
			queue = append(queue, n.Children...)
		}
		res = append(res, nodeVals)
	}
	return res
}
```


### 前缀树
介绍：前缀树是N叉树的一种特殊表现形式，用于存储字符串，每个节点都存储了字符串中的一个字符。
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/algorithm/trie.png)

- 数据结构
```
// 第一种，以列表来存放子节点
type Trie struct {
	Nodes []*Trie		
	R     int
	IsEnd bool
}

// 第二种，以哈希来存放子节点
type Trie2 struct {
	Nodes map[byte]*Trie2
	IsEnd bool	// 是否是单词结尾
}
```

- 前缀树实现(哈希版)
```
// 前缀树节点表示方法二，用哈希存储子节点
type Trie2 struct {
	Nodes map[byte]*Trie2
	IsEnd bool
}

func (this *Trie2) containerKey(b byte) bool {
	_, ok := this.Nodes[b]
	return ok
}

func (this *Trie2) get(b byte) *Trie2 {
	return this.Nodes[b]
}

func (this *Trie2) put(b byte, node *Trie2) {
	this.Nodes[b] = node
}

func (this *Trie2) isEnd() bool {
	return this.IsEnd
}

func (this *Trie2) setEnd() {
	this.IsEnd = true
}

func NewTrie2() Trie2 {
	return Trie2{
		Nodes: make(map[byte]*Trie2),
		IsEnd: false,
	}
}

func (this *Trie2) Insert(word string) {
	root := this
	for i := 0; i < len(word); i++ {
		if root.containerKey(word[i]) {
			root = root.get(word[i])
		} else {
			node := NewTrie2()
			root.put(word[i], &node)
			root = &node
		}
	}
	root.setEnd()
}

func (this *Trie2) Search(word string) bool {
	root := this
	for i := 0; i < len(word); i++ {
		if !root.containerKey(word[i]) {
			return false
		} else {
			root = root.get(word[i])
		}
	}
	return root.IsEnd
}

func (this *Trie2) StartsWith(word string) bool {
	root := this
	for i := 0; i < len(word); i++ {
		if !root.containerKey(word[i]) {
			return false
		} else {
			root = root.get(word[i])
		}
	}
	return true
}
```


## 链表
### 单向链表(golang实现)
```go
type ListNode struct {
	Val  int
	Next *ListNode
}

// 哨兵节点
type MyLinkedList struct {
	Lens int
	Head *ListNode
}

func Constructor() MyLinkedList {
	return MyLinkedList{}
}

func (this *MyLinkedList) Get(index int) int {
	// 如果输入的下标，大于等于链表长度，或 下标小于 0 ，直接返回 -1
	if index >= this.GetLens() || index < 0 {
		return -1
	}
	if this.Head == nil { // 链表为空，直接返回 -1
		return -1
	}
	head := this.Head
	for i := 1; i <= index; i++ { // 此处必须是 <= index，因为就是要找到 index 位置的节点，并获取它的值
		head = head.Next
	}
	return head.Val
}

func (this *MyLinkedList) GetLens() int {
	return this.Lens
}

func (this *MyLinkedList) AddAtHead(val int) {
	this.AddAtIndex(0, val)
}

func (this *MyLinkedList) AddAtTail(val int) {
	this.AddAtIndex(this.Lens, val)
}

func (this *MyLinkedList) AddAtIndex(index, val int) {
	if index > this.GetLens() { // 如果要加入的下标，超过了链表的长度，比如说：链表共有8个节点，则最大的节点对应的index应该是7，则插入新的值到最后，index应该是8，此时传入的index都大于8了，就插入不了
		return
	}
	node := &ListNode{
		Val: val,
	}
	if index == 0 { // 如果插入的节点下标为 0 ，直接插入首节点
		node.Next = this.Head
		this.Head = node
		this.Lens++ // 插入完成记得要给链表长度加一
		return
	}
	head := this.Head
	for i := 1; i < index; i++ { // 如果不是插如首节点，就找到第 index - 1 的节点处
		head = head.Next
	}
	node.Next = head.Next
	head.Next = node
	this.Lens++
	return
}

func (this *MyLinkedList) DeleteAtHead() {
	this.DeleteAtIndex(0)
}

func (this *MyLinkedList) DeleteAtTail() {
	this.DeleteAtIndex(this.Lens - 1)
}

func (this *MyLinkedList) DeleteAtIndex(index int) {
	if index >= this.GetLens() { // 与插入不同，插入允许输入index == 链表长度，即在最后插入，但是删除不行，index >= 链表长度，就没有节点了
		return
	}
	if index == 0 { // 首节点
		if this.Head == nil {
			return
		}
		this.Head = this.Head.Next
		this.Lens-- // 删除完成记得修改链表长度
		return
	}

	head := this.Head
	for i := 1; i < index; i++ { // 非首节点，找到 index - 1 的节点处
		head = head.Next
	}
	this.Lens--
	head.Next = head.Next.Next

}
```

### 双向链表(golang实现)
```go
type ListNode struct {
	val  int
	prev *ListNode
	next *ListNode
}

func newListNode(v int, prev, next *ListNode) *ListNode {
	return &ListNode{v, prev, next}
}

type MyLinkedList struct {
	head *ListNode
	tail *ListNode
	size int
}

/** Initialize your data structure here. */
func Constructor() MyLinkedList {
	// 初始化伪头，伪尾
	head, tail := &ListNode{}, &ListNode{}
	head.next = tail
	tail.prev = head
	return MyLinkedList{
		head: head,
		tail: tail,
		size: 0,
	}
}

/** Get the value of the index-th node in the linked list. If the index is invalid, return -1. */
func (this *MyLinkedList) Get(index int) int {
	if index < 0 || index >= this.size {
		return -1
	}
	var cur *ListNode
	if index+1 < this.size-index {
		cur = this.head
		// 这里是直接获取index位置的节点,故需index+1
		for i := 0; i < index+1; i++ {
			cur = cur.next
		}
	} else {
		cur = this.tail
		for i := 0; i < this.size-index; i++ {
			cur = cur.prev
		}
	}
	return cur.val
}

/** Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. */
func (this *MyLinkedList) AddAtHead(val int) {
	// 前驱=伪头, 后继=伪头.next
	pred, succ := this.head, this.head.next
	toAdd := newListNode(val, pred, succ)
	pred.next = toAdd
	succ.prev = toAdd
	this.size++
}

/** Append a node of value val to the last element of the linked list. */
func (this *MyLinkedList) AddAtTail(val int) {
	// 前驱 = 伪尾.prev   后继 = 伪尾
	succ, pred := this.tail, this.tail.prev
	toAdd := newListNode(val, pred, succ)
	pred.next = toAdd
	succ.prev = toAdd
	this.size++
}

/** Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. */
func (this *MyLinkedList) AddAtIndex(index int, val int) {
	if index > this.size || index < 0 {
		return
	}
	var pred, succ *ListNode
	if index < this.size-index { // 如果index 小于 链表长度的一半，则要插入的节点在前半部分，从前往后找
		pred = this.head
		for i := 0; i < index; i++ {
			pred = pred.next
		}
		succ = pred.next
	} else { // 否则从后往前找
		succ = this.tail
		for i := 0; i < this.size-index; i++ {
			succ = succ.prev
		}
		pred = succ.prev
	}
	toAdd := newListNode(val, pred, succ)
	pred.next = toAdd
	succ.prev = toAdd
	this.size++
}

/** Delete the index-th node in the linked list, if the index is valid. */
func (this *MyLinkedList) DeleteAtIndex(index int) {
	if index < 0 || index >= this.size {
		return
	}
	var pred, succ *ListNode
	if index < this.size-index {
		pred = this.head
		for i := 0; i < index; i++ {
			pred = pred.next
		}
		succ = pred.next.next
	} else {
		succ = this.tail
		for i := 0; i < this.size-index-1; i++ {
			succ = succ.prev
		}
		pred = succ.prev.prev
	}
	pred.next = succ
	succ.prev = pred
	this.size--
}
```

## 回溯算法
- 回溯算法解决框架
```
result = []
def backtrack(路径, 选择列表):
	if 满⾜结束条件:
		result.add(路径)
		return
	for 选择 in 选择列表:
		做选择
		backtrack(路径, 选择列表)
		撤销选择
```


## DP

## LRU算法
- 介绍：一种缓存淘汰算法，全称 Least Recently Used, 即最近使用过的数据，淘汰最长时间未访问过的数据(**按照时间次序来淘汰数据**)。实际上就是实现一种数据结构而已。
- 扩展：**LFU：按照访问频率来淘汰数据。**

- 实现要求：
  - 输入一个容量，得到一个LRU数据结构对象
  - 调用Put方法，放入key， value
    - 如果key已存在，则需要修改 原key 的值为新的值，并把原key，放到头部。
    - 如果key不存在，同时数据未达到容量，则在头部添加一个新的值
    - 如果key已存在，则需要删掉最后一个值(最久没有访问的数据)，给新的数据腾出空间，然后添加新的数据到头部。
  - 调用Get方法，如果key不存在，则返回 -1 ，如果key已存在，则返回对应的值，同时，因为访问了该数据，需要将其放到头部。

- 实现分析：
  - 不考虑查询与插入的时间复杂度，最容易想到的方案就是使用队列加哈希表, 思想如下：

```go
type LRUCache struct {
	capacity int		// 记录容量
	values map[int]int   // 哈希表，记录key-value
	keys []int	// 队列(使用切片代替)
}

func Get(key int) int {
	if key 不存在 {
		return -1
	}
	// 存在, 则遍历 key 列表，找到key对应的索引位置
	for i := range keys {
		// 把该key放到队列的最前面
		// 原位置前的元素，全部往后顺移一位
	}
	return 值
}

func Put(key, value) {
	if key 存在 {
		 // 同Get方法，移动位置
		 values[key] = value 	// 修改原key对应的值为新值
		 return
	}
	// 不存在时，则判断已加入的元素数量，是否达到容量
	if len(keys) == capacity {
		// 从keys 删除最后一个key
		// 从values 中，删除最后一个key对应的值
		// 将新的key放入到keys队列的头，其他元素顺移一位
		// 把新的值，放入values中
		return
	}
	// 未达到容量时，直接在 keys 队列头加入key即可
	// 加入values
}
```

  - 上面这种方案，效率极低，插入和查询，时间复杂度都是O(n), 如何实现O(1)的时间复杂度呢？ **哈希+双向链表**
    - 使用双向链表可以做到O(1)时间复杂度的插入和删除操作
    - 使用哈希，存key 与节点直接的关系，可以做到O(1) 时间复杂度的查找


### 实现示例(golang)
```go
// 双向链表
type Node struct {
	key int
	value int
	prev *Node
	next *Node
}

func NewNode(key, value int, prev, next *Node) *Node {
	// 创建一个新的节点，并设置该节点的前驱和后继节点
	node := &Node{
		key: key,
		value: value,
		prev: prev,
		next: next,
	}
	return node
}

type LRUCache struct {
	head *Node
	tail *Node
	capacity int
	size int
	values map[int]*Node
}


func Constructor(capacity int) LRUCache {
	head := &Node{}		// 创建伪头节点
	tail := &Node{}		// 创建伪尾节点
	head.next = tail	// 数据为空时，伪头节点的后置节点指向伪尾
	tail.prev = head	// 伪尾的前驱节点，指向伪头

	return LRUCache{
		head: head,
		tail: tail,
		capacity: capacity,
		size: 0,
		values: make(map[int]*Node, capacity),		// 哈希表存key到节点的关系
	}
}

// 修改头节点为某个已存在的节点
func (this *LRUCache) setHead(node *Node) {

	prev := node.prev		// 已存在的节点 的 前驱节点
	next := node.next		// 已存在的节点 的 后置节点
	// 在自己原来位置，删除自己
	prev.next = next		// 修改当前节点的前驱节点的后置节点，为当前节点的后置节点
	next.prev = prev		// 修改当前节点的后置节点的前驱节点，为当前节点的前驱节点

	// 把自己放到头节点
	node.next = this.head.next
	node.prev = this.head
	this.head.next.prev = node
	this.head.next = node
}

// 添加新的节点为头节点
func (this *LRUCache) addHead(node *Node) {
	this.head.next.prev = node		// 修改 头节点的下一节点 的 前驱为新的节点
	this.head.next = node		// 修改 头节点 的 后置节点 为新节点
}

// 时间复杂度要求O(1)
func (this *LRUCache) Get(key int) int {
	node, ok := this.values[key]
	if !ok {
		return -1
	}
	// 如果查到了数据，则需要这个节点，放到头节点
	this.setHead(node)
	return node.value
}

// 时间复杂度要求O(1)
func (this *LRUCache) Put(key int, value int)  {
	if n, ok := this.values[key]; ok {
		n.value = value
		this.setHead(n)
		return
	}
	// 创建新节点
	node := NewNode(key, value, this.head, this.head.next)

	// 如果数据已满，则需要删除最后的数据，并将新的数据，加到头部
	if this.size == this.capacity {
		// 获取最后一个节点
		latest := this.tail.prev
		// 删除最后一个节点
		delete(this.values, latest.key)		// 因为需要从哈希表中删除旧节点，所以旧节点得存key值
		latest.prev.next = latest.next
		latest.next.prev = latest.prev

		// 将新的数据加到头部
		this.addHead(node)
		this.values[key] = node
		return
	}

	// 数据未满，加到头部
	this.addHead(node)

	// 加到哈希表中
	this.values[key] = node
	// 记录当前数据量
	this.size++
	return
}
```

------------
# 设计模式

- 设计模式原则
  - 开闭原则：一个软件实体如类、模块和函数应该对修改封闭，对扩展开放。
  - 单一职责原则：一个类只做一件事，一个类应该只有一个引起它修改的原因。
  - 里氏替换原则：子类应该可以完全替换父类。也就是说在使用继承时，只扩展新功能，而不要破坏父类原有的功能。
  - 依赖倒置原则：细节应该依赖于抽象，抽象不应依赖于细节。把抽象层放在程序设计的高层，并保持稳定，程序的细节变化由低层的实现层来完成。
  - 迪米特法则：又名「最少知道原则」，一个类不应知道自己操作的类的细节，换言之，只和朋友谈话，不和朋友的朋友谈话。
  - 接口隔离原则：客户端不应依赖它不需要的接口。如果一个接口在实现时，部分方法由于冗余被客户端空实现，则应该将接口拆分，让实现类只需依赖自己需要的接口方法。

- 创建者模式
  - 简单工厂模式
  - 工厂方法模式
  - 抽象工厂模式
  - 单例模式
  - 建造者模式
  - 原型模式(clone)

- 结构性模式
  - 适配器模式
  - 桥接模式
  - 组合模式
  - 装饰模式
  - 外观模式
  - 享元模式
  - 代理模式

- 行为型模式
  - 职责链模式
  - 命令模式
  - 解释器模式
  - 迭代器模式
  - 中介者模式
  - 备忘录模式
  - 观察者模式
  - 状态模式
  - 策略模式
  - 模板方法模式
  - 访问者模式

[https://github.com/senghoo/golang-design-pattern](https://github.com/senghoo/golang-design-pattern "golang设置模式实现示例")
