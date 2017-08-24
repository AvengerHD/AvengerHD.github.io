---
layout: default
title: Netty 学习笔记
permalink: /pages/netty
---
## 基本原则
```
1) 注意资源的释放，比如 Unpooled, eventLoop 的关闭
2) 异步事件驱动架构
3) 最佳实践 : 创建两个 NioEventLoopGroup, 隔离 NIO Acceptor 和 NIO I/O 线程
4) Reactor 线程建议捕获 Throwable 
``` 

## 关键类解析
### 1) EventLoop + EventLoopGroup
```
NioEventLoopGroup
NIO 线程组(Reactor 线程组), 处理网络事件
一般创建两个, 一个接收客户端连接，一个进行 SocketChannel 的网络读写

附注：
1) 常见线程模型
    <1>. Reactor 单线程模型, 所有 I/O 操作都在一个 NIO 线程上, 同时是服务端和客户端
         缺点 : 性能低, 高负载导致处理速度变慢, 线程挂掉就不能处理消息
    <2>. Reactor 多线程模型, 一组线程执行 I/O 操作, 一个 Acceptor 线程监听服务端, 接收客户端连接, 读写由一个 NIO 线程池负责(队列 + 工作线程) 
         缺点 : 单一 Acceptor 线程性能低
    <3>. 主从 Reactor 多线程模型(Netty 官方推荐) : 服务端接收客户端连接的是一个 NIO 线程池

```

### 2) ServerBootstrap
```
启动 NIO 服务端的辅助启动类, 降低服务端开发复杂性
```

### 3) Bootstrap
```
客户端辅助启动类 Bootstrap
```

### 4) ByteBuf
```
类似 java.nio.ByteBuffer
ByteBuffer 缺点
    <1>. 长度固定，超过容量抛异常
    <2>. 只有一个指针，读写需要手工调用 flip() rewind()
    <3>. 功能有限

ByteBuf 功能
    <1>. 顺序读(read)
    <2>. 顺序写(write)
    <3>. readerIndex, writerIndex
    <4>. discardable bytes
    <5>. readable bytes, writable bytes
    <6>. clear (操作指针)
    <7>. mark, reset
    <8>. find
    <9>. derived buffers  
    <10>. convert to ByteBuffer
    <11>. 随机读写(set, get )

ByteBuf 分类
    <1>. UnpooledHeapByteBuf(推荐使用) : 基于堆内存分配内存，没有对象池，每次 I/O 创建新对象，大块内存频繁操作性能低，但申请和释放成本低
    <2>. UnpooledDirectByteBuf
    <3>. UnpooledUnsafeDirectByteBuf
    <4>. PooledDirectByteBuf
    <5>. PooledHeapByteBuf
    <6>. PooledUnsafeHeapByteBuf
```

### 5) ChannelInboundHandlerAdapter
```
important methods

1) channelActive
    当客户端和服务端 TCP 链路建立成功后， Netty 的 NIO 线程会调用
2) channelRead
    服务端返回应答信息时调用
3) exceptionCaught
    发生异常时调用
```

### 6) Channel
```
Netty 网络 I/O 读写相关接口

主要子类
NioServerSocketChannel : 读取操作是接收客户端连接，创建 NioSocketChannel 对象

NioSocketChannel : 连接服务端，读写操作
```

### 7) ChannelPipeline + ChannelHandler
```
该组合机制类似于 Servlet 和 Filter

ChannelPipeline : 持有 I/O 事件拦截器 ChannelHandler 的链表，负责事件调度，本事不处理 I/O 事件
ChannelHandler : 对 I/O 事件进行拦截和处理

pipeline 中以 fireXXX 命名的方法都是从 I/O 线程流向用户业务 Handler 的 inbound 事件
由用户线程或者代码发起的 I/O 操作被称为 outbound 事件

附注：
ChannelPipeline 支持动态添加删除 ChannelHandler
ChannelPipeline 线程安全
ChannelHandler 非线程安全
```

## TCP 粘包/拆包问题
```
问题：一个完整的业务包可能被 TCP 拆分成多个包进行发送，也可能把多个小的包封装成一个大的数据包发送
产生原因：
    1) 应用程序写入的字节大小大于套接字发送缓冲区的大小
    2) 进行 MSS(TCP数据包每次能够传输的最大数据分段) 大小的 TCP 分段
    3) 以太网帧的 payload 大于 MTU(以太网EthernetII最大的数据帧,剩下承载上层协议的地方也就是Data域称之为MTU) 进行 IP 分片

解决方案：
    1) LineBasedFrameDecoder + StringDecoder 按行切换的文本解码器组合
        LineBasedFrameDecoder 依次遍历 ByteBuf 中的可读字节，判断是否有 "\n" or "\r\n"， 有就结束，为一行
        StringDecoder 接收的对象转换成字符串
    2) DelimiterBasedFrameDecoder + StringDecoder
        以分隔符作为码流结束标识的消息解码，解码后去掉分隔符
    3) FixedLengthFrameDecoder + StringDecoder
        固定长度解码器
    4) LengthFieldPrepender + LengthFieldBasedFrameDecoder
        LengthFieldPrepender 在 ByteBuf 前加上2个字节的消息长度字段
        LengthFieldBasedFrameDecoder 处理半包消息
```

## 业界主流的编解码框架
```
1) Google Protobuf
2) Facebook Thrift
3) JBoss Marshalling
```

## 自定义编码器/解码器
```
1) MessageToByteEncoder<T> 将 POJO 对象编码成 ByteBuf
2) MessageToMessageDecoder<T> 将一个 POJO 对象二次解码为其他 POJO 对象，在 ByteToMessageDecoder 之后
3) MessageToMessageEncoder<T> 将 POJO 对象编码成另一个 POJO 对象
4) ByteToMessageDecoder<T> 将 ByteBuf 解码成 POJO
```

## HTTP 编码器/解码器组合
```
server
HttpRequestDecoder + HttpObjectAggregator + HttpResponseEncoder + ChunkedWriteHandler + ...

HttpRequestDecoder : HTTP 请求消息解码器
HttpObjectAggregator : 将多个消息转换成单一的 FullHttpRequest or FullHttpResponse
HttpResponseEncoder : HTTP 响应消息编码器
ChunkedWriteHandler ： 支持异步发送大的码流，不会使用过多的内存

client
注意： 1) 如果使用 chunked 编码，最后需要发送编码结束的空消息体
      2) 如果是非 Keep-Alive 的，最后一包消息发送完成之后，服务端要主动关闭连接
```

## WebSocket 编码器/解码器组合
```
server
HttpServerCodec + HttpObjectAggregator + ChunkedWriteHandler + WebSocketFrameDecoder + WebSocketFrameEncoder

HttpServerCodec : 将请求和应答消息编码或解码为 HTTP 消息
HttpObjectAggregator : 将多个消息转换成单一的 FullHttpRequest or FullHttpResponse
ChunkedWriteHandler ：向客户端发送 HTML5 文件，支持浏览器和服务端进行 WebSocket 通信
WebSocketFrameDecoder + WebSocketFrameEncoder ： 在 WebSocketServerHandshaker 中添加
```

## 私有协议栈开发流程示例
```
1) 功能设计
   <1>. 基于 Netty 的 NIO 框架，提供高性能异步通信能力
   <2>. 提供消息编解码框架，实现 POJO 的序列化和反序列化
   <3>. 提供基于 IP 地址的白名单接入认证机制
   <4>. 链路有效性校验机制
   <5>. 断线重连机制
2) 通信模型
   握手--校验身份--应答--心跳--业务消息
3) 消息格式定义(消息头、消息体)
4) 编解码规范 : 各个字段如何赋值，格式规范
5) 定义如何建立和关闭链接
6) 可靠性设计 a. 心跳机制 b. 重连机制 c. 重复登录保护 d. 消息队列缓存
7) 安全性设计 a. 加密 b. 黑白名单
8) 心跳超时利用 ReadTimeoutHandler : 一定周期没有对方消息关闭链路，如果是客户端则重连，服务端则释放资源，清除客户端登录缓存

附注：
查看 TCP 状态 : netstat -ano | findstr "xxxx"
```

## Netty 架构剖析
```
三层架构

Reactor 通信调度层 
职责链 ChannelPipeline
业务逻辑编排层 Service ChannelHandler

如何实现高性能?
    <1>. 异步非阻塞 I/O
    <2>. TCP 接收发送缓冲区使用 direct 内存
    <3>. 内存池循环利用 ByteBuf
    <4>. 可定制化参数丰富
    <5>. 采用环形数组缓冲区实现无锁编程
    <6>. 合理使用线程安全容器和原子类
    <7>. 关键资源单线程串行化，避免锁竞争
    <8>. 引用计数器降低 GC 频率
```

## Netty 零拷贝
```
<1>. 接收发送 ByteBuffer 使用 direct buffer, 堆外内存 Socket 读写, 不进行字节缓冲区的二次拷贝
<2>. CompositeByteBuf 将多个 ByteBuf 合并成一个 ByteBuf, 添加 ByteBuf 不需要进行内存拷贝
<3>. 文件传输, 直接将文件缓冲区的内容发送到目标 channel
```

## 流量整形
```
GlobalTrafficShapingHandler 
全局流量整形是进程级的
对每次读取到的 ByteBuf 可写字节数统计, 与阈值对比, 超过后计算等待时间 delay, 将当前 ByteBuf 放到定时任务 Task 中缓存, delay 事件后再处理
与流量控制的区别 : 不会拒绝消息, 近似恒定速率发消息

ChannelTrafficShapingHandler
链路级流量整形
```