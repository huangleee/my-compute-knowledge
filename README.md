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
