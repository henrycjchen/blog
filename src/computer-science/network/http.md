## HTTP/1.1

特性

- 持久连接。
  - 请求可复用 TCP 连接
  - Content-Length 请求截断
- 分块传输编码。不用等数据全部加载完再传输，多用于文件上传
- PUT, PATCH, HEAD, OPTIONS, DELETE

不足

- 队头阻塞。虽然 TCP 可复用连接，但所有的数据通信是按次序进行的



## HTTP/2

- 二进制协议。二进制解析更方便
- 多工。复用的 TCP 连接中，一个连接可以同时处理多个请求和响应，而不用一一对应
- 信息头压缩。gzip 压缩、服务端缓存请求头
- 服务端推送资源



## HTTP/3

HTTP over QUIC

QUIC 是 google 公司创建的基于 **UDP** 的网络协议

QUIC = UDP + TLS1.3 + HTTP/2





## HTTPS

HTTP + TLS，解决纯 HTTP 下的安全问题：

1. **窃听风险**：第三方可以获知通信内容
2. **篡改风险**：第三方可以修改通信内容
3. **冒充风险**：第三方可以冒充他人身份参与通信

针对上面三个问题，TLS 有以下三个特点

1. 加密传播
2. 校验机制
3. 身份证书

TLS 大致过程：

1. 客户端发送支持的 TLS 版本号等信息到服务端
2. 服务端把即将使用的加密方式和证书（包含非对称加密公钥）发送给客户端
3. 客户端生成对称加密用随机数并用证书中的公钥加密，发送给服务端
4. 服务端用私钥解出随机数，用其进行对称加密

后面的交流便都通过对称加密进行



## TLS1.2 vs TLS 1.3

TLS 1.2 采用 RSA 公私钥加密形式，首次建立连接需要 2RTT

```
C -- 发起请求      -> S
C <- 证书/公钥     -- S
C -- 公钥加密随机数 -> S
```

TLS 1.3 采用 ECDH 椭圆函数算法(A = 随机数 a * 大数 G，由 a G 可得 A，但由 A G 得不到 a)，首次建立连接需要 1RTT

```
C -- A/G -> S
C <- B/G -- S
```

连接复用情况下

TLS1.2 客户端用 session-id 到服务端换通信秘钥，需要 1RTT

TLS1.3 客户端直接自行加密，实现 0RTT 通信



## 参考资料

1. [《从HTTP/0.9到HTTP/2：一文读懂HTTP协议的历史演变和设计思路》](http://www.52im.net/thread-1709-1-1.html) ，2019 年，JackJiang

2. [《SSL/TLS协议运行机制的概述》](http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)

3. [《详解TLS1.3的握手过程》](https://blog.csdn.net/zk3326312/article/details/80245756)

4. [《TLS1.3/QUIC 是怎样做到 0-RTT 的》]([https://liudanking.com/network/tls1-3-quic-%E6%98%AF%E6%80%8E%E6%A0%B7%E5%81%9A%E5%88%B0-0-rtt-%E7%9A%84/](https://liudanking.com/network/tls1-3-quic-是怎样做到-0-rtt-的/))