# 计算机知识杂烩总结 #

## 计算机网络 ##

### TCP/IP分层模型
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/TCP-IP%E5%88%86%E5%B1%82%E6%A8%A1%E5%9E%8B.png)

### 数据包流转整体流程
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E6%95%B4%E4%BD%93%E6%B5%81%E7%A8%8B.png)

### 数据包编解码流程
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E6%95%B0%E6%8D%AE%E5%8C%85%E7%BC%96%E7%A0%81%E8%A7%A3%E7%A0%81%E6%B5%81%E7%A8%8B.png)

### 数据包编解码示例
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E6%95%B0%E6%8D%AE%E5%8C%85%E7%BC%96%E7%A0%81%E8%A7%A3%E7%A0%81%E7%A4%BA%E4%BE%8B.jpg)

### 数据传输层
#### TCP
###### 三次握手
1. 状态变更

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E7%8A%B6%E6%80%81%E5%8F%98%E6%9B%B4.jpg)

2. 注意事项
	1. 因为传输层是面向连接的、可靠的传输层协议，所以**接收方每次收到数据包之后，都会回一个ack的包**，确认应答。如果发送方没有收到应答，就会尝试再次发送该数据包，直到收到应答。**重试会有一个时间间隔**，多次重试之后，会认为网络异常，断开连接。
	2. 同时为了保证数据包的有序性，会在数据包中，加上一个序列号，用于表示数据包的顺序。
	3. 发送方为了提高速度，还会**一次性发送多个数据包**，接收方收到多个包之后，一次性发起多个包的相应应答。发送方收到应答之后，继续发送。如果没有收到应答，或者缺少了某个数据包的应答相应，发送方还需要重新发送该数据包。即**滑动窗口控制**

###### 四次挥手

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B%E7%8A%B6%E6%80%81%E5%8F%98%E6%9B%B4.jpg)

### 网络层与链路层

mac地址变更流程

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/compute/main/img/net/mac%E5%9C%B0%E5%9D%80%E5%8F%98%E6%9B%B4.png)

### 通信总结：
#### 客户端
1. 用户进程想要访问某个服务器的某个应用，发送对应的数据包，则需要知道服务器IP、端口信息，并得知道是采用TCP 还是 UDP。
2. 在用户进程，准备好数据之后，并将要发送的数据，按照指定的应用层协议，封装数据包，并将数据包，以及上面的连接信息，调用内核。
3. 数据传输层收到调用信号之后，判断是何种类型的包，如果是TCP，会构造新的数据包，主动发起三次握手，从而与服务端建立连接。然后在原数据包前，加上数据传输层的头部信息（源端口号、源协议类型、目的端口号、目的协议类型）。然后继续调用网络层。UDP不进行细致讲解。
4. 网络层收到调用请求后，会根据目的IP，去本机的路由表，用目的IP，依次与路由规则中的掩码，进行二进制与操作，得到网络地址，从而找到下一跳的IP地址。找到之后，会将原数据包，加上IP层的头部信息（源IP、目的IP），并调用链路层传递数据包。
5. 链路层在知道下一跳的IP地址之后，会去自己的arp表中，查找该IP对应的mac地址，如果找不到，就会广播一个arp数据包，问局域网内的哪台主机有该IP地址。如果主机没有该IP地址，会忽略该包，不回复。如果主机有该IP，会回复一个数据包给原主机，告诉它IP地址对应网卡的mac地址。然后原主机，就根据找到的mac地址，在原数据包上，加上链路层的头部信息（源mac地址、目的mac地址）。
6. 调用物理层，将数据包发送出去。

#### 中间节点  /  服务端
1. 网卡收到数据包之后，链路层会解包，判断头部信息中，目的mac地址是否是本机的mac地址，不是则丢弃，是则接着往下走。
2. **此时会到防火墙，并经过防火墙规则过滤之后，继续往下走。**
2. 判断数据包头部信息中的数据类型，传给对应的模块，如IP、ARP等。
3. 到IP层之后，解包，判断目的IP是否是本机IP，如果是，则继续将数据包，传递给链路层。如果不是，则需要转发出去（需要开启转发功能），去本地的路由表中，找下一跳，然后调用链路层，传递数据。
4. 到链路层之后，会根据目的协议和目的端口，找到对应的进程id，将数据从缓冲区，拷贝到对应的用户进程中。
5. 用户层则根据用户层协议，得到最终数据。

用快递打个比方，模拟网络通信：
1. A想发一个快递给B，传输层需要做的就是在快递上写下来收件人姓名。
2. 网络层就是需要在快递上写下小区地址。并告诉快递员，这个快递，你应该先送去哪个地方。
3. 链路层就是直到了要送的地方，就赶快将快递送到下一个地址。

### 扩展
#### NAT
解释：网络地址转换

转换方式：
1. SNAT： 将源地址，转换为指定地址（IP+端口）
2. DNAT： 将目的地址，转换为指定地址（IP+端口）

分类：
1. 静态nat转换：	源地址与被转换地址是一对一的关系（指IP）
2. 动态nat转换：	源地址与被转换地址是多对多的关系（指IP）
3. 网络地址端口转换：多个源地址（即多个IP+端口），被转换为同一个IP的多个端口。


#### 防火墙（iptables | firewalld）
数据包经过网卡，进入内核中，再由链路层解码之后，判断是本机的mac地址，就需要防火墙来过滤。
	
> iptables与firewalld都不是真正的防火墙，它们都只是用来定义防火墙策略的防火墙管理工具而已，或者说，它们只是一种服务。iptables服务会把配置好的防火墙策略交由内核层面的**netfilter**网络过滤器来处理，而firewalld服务则是把配置好的防火墙策略交由内核层面的**nftables**包过滤框架来处理

##### 表链
- raw表： 加速数据包穿过防火墙的表，也就是增强防火墙性能的表。
- manage表： 一种特殊的表，通过mangle表可以实现数据包的拆分和还原。
- filter表： 负责包过滤。
- nat表： 实现网络地址转换。

##### 规则链
- PREROUTING： 在网络层判断ip之前，即路由决策之前（决策是本机的数据包，还是需要转发的数据包）
- INPUT： 路由决策之后，只过滤进入本机的数据包。
- FORWARD： 路由决策之后，只过滤转发的数据包。
- OUTPUT： 只针对本机发出的数据包，在路由判断之后，进行过滤。
- POSTROUTING： 针对本机、以及转发的数据包，进行过滤。

##### 图示
![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/net/%E9%98%B2%E7%81%AB%E5%A2%99.png)


-----------------------------------




## IO
### 同步阻塞IO（BIO）
#### 解释
同步阻塞IO，服务端启动监听端口后，会**阻塞等待客户端连接**。客户端连接来了之后，会**起一个线程，去处理这个连接**。主线程会继续阻塞去等待新的客户端连接。而处理线程，则会**阻塞等待接收客户端发送来的消息**，并处理。

#### 示例代码
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

#### 内核调用跟踪
注：linux环境下，使用strace命令，可以追踪系统调用

1. 执行命令： strace -ff -o out python BIO.py
2. 在另一个窗口，可以看到有一个文件出现

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-1.png)

3. 打开该文件，可以看到系统调用情况。socket(AF_INET, SOCK_STREAM, IPPROTO_IP) = 3 得到一个socket，该socket对应的文件fd为3；将该fd，作为参数传递给 bind 函数，即该 fd 绑定指定IP和端口；接着 调用 listen，即该fd 开始监听连接，最大连接客户端数为 20；最后一行就是阻塞等待客户端的连接

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-2.png)

4. 此时在另一个窗口，使用nc命令建立客户端连接  nc 127.0.0.1 5010，可看到目录下多了一个日志文件。
5. 可以看到主线程收到连接请求之后，生成了一个新的fd，指代这个请求。然后创建了一个新的线程处理连接（系统调用中，clone方法创建线程，与主线程有不同的栈，相同的堆），并返回了新的线程的id。然后又阻塞等待新的连接

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-3.png)

5. 新线程阻塞等待客户端发送数据

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/BIO-4.png)

6. **使用man命令可以查看系统调用介绍，如 man 2 socket,  man 2 clone**

#### BIO 对应的系统调用伪代码
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
#### 好处与弊端
**好处： 一个服务端可以接受N个客户端的连接，一个客户端对应一个线程**

**弊端： 阻塞调用，会创建N多个线程，消耗服务器资源，同时线程间切换，性能不高。**

--------------------------

### 同步非阻塞IO (NIO)
#### 解释
同步非阻塞IO：服务端启动监听端口后，**不会阻塞等待客户端连接**。客户端连接建立之后，**不会阻塞等待接收客户端发送的消息**，而是会去处理计算问题（其他连接），过段时间后，**再去向内核(kernel)询问是否有数据**。

#### 示例代码
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
#### 内核调用跟踪

1. 执行程序，查看系统调用情况。可见先创建了一个socket，对应的fd为3；**紧接着给这个fd 加了一个非阻塞IO锁**；然后就绑定监听ip和端口；启动监听。此时，可见程序会不停触发 accept 调用。

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/NIO-1.png)

2. 另一个窗口，使用nc建立连接。可见此时有了一个客户端连接，这个sock 对应的fd 为 4；**紧接着给这个fd，也加了一个非阻塞IO锁**；此时程序调用recv 时，内核会立刻返回，而不会使整个程序阻塞。

![Aaron Swartz](https://raw.githubusercontent.com/huangleee/my-compute-knowledge/main/img/IO/NIO-2.png)

#### NIO 对应系统调用伪代码
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

#### 好处与弊端

**好处：不用创建多个线程去处理客户端连接，避免资源浪费，单个线程就能非阻塞的处理所有客户端请求。**

**弊端：会不停的触发recv系统调用，即使这些sock并没有数据。频繁的系统调用，会降低程序性能。**


------------------------------------

### IO多路复用
#### 解释
IO多路复用：使用select、poll、epoll来同时监听多个IO，*当IO对象有数据时，通知用户进程处理*

#### 分类
**1. select：效率最低，有最大文件描述符限制。(用户进程查询哪些IO有变化时，需要传入fd列表)**

**2. poll：同select，但是无最大描述符限制。(同select，查询IO变化时，需要传入fd列表，但是此时没有列表中fd的数量限制)**

**3. epoll：效率最高，没有最大描述符限制。(内核会记录所有注册的IO，用户进程查询哪些IO有数据时，无需传入fd列表，内核会告诉用户进程所有的有数据的IO列表)**

#### epoll
##### linux系统下epoll操作
1. epoll_create: 创建一个epoll对象，返回一个fd，代表该epoll对象(linux下一切皆文件)
2. epoll_ctl: 控制一个epoll对象。定义: int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
	2.1 op: EPOLL_CTL_ADD, EPOLL_CTL_MOD, EPOLL_CTL_DEL
	2.2 event: EPOLLIN, EPOLLOUT, EPOLLRDHUP , EPOLLPRI, EPOLLERR, EPOLLHUP, EPOLLET, EPOLLONESHOT 

3. epoll_wait: 等待io事件，返回事件列表。

#### python3 使用epoll示例
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
#### 系统调用跟踪
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
