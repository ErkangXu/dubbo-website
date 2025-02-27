---
aliases:
    - /zh/overview/what/ecosystem/protocol/rmi/
description: ""
linkTitle: RMI
title: RMI
type: docs
weight: 6
---



## 特性说明
RMI 协议采用 JDK 标准的 `java.rmi.*` 实现，采用阻塞式短连接和 JDK 标准序列化方式。

* 连接个数：多连接
* 连接方式：短连接
* 传输协议：TCP
* 传输方式：同步传输
* 序列化：Java 标准二进制序列化
* 适用范围：传入传出参数数据包大小混合，消费者与提供者个数差不多，可传文件。
* 适用场景：常规远程服务方法调用，与原生RMI服务互操作

#### 约束

* 参数及返回值需实现 `Serializable` 接口
* dubbo 配置中的超时时间对 RMI 无效，需使用 java 启动参数设置：`-Dsun.rmi.transport.tcp.responseTimeout=3000`，参见下面的 RMI 配置


## 使用场景

是 Java 的一组拥护开发分布式应用程序的 API，实现了不同操作系统之间程序的方法调用。

## 使用方式 - Java

### 引入依赖

从 Dubbo 3 开始，RMI 协议已经不再内嵌在 Dubbo 中，需要单独引入独立的[模块](/zh-cn/download/spi-extensions/#dubbo-rpc)。
```xml
<dependency>
    <groupId>org.apache.dubbo.extensions</groupId>
    <artifactId>dubbo-rpc-rmi</artifactId>
    <version>1.0.0</version>
</dependency>
```

```sh
java -Dsun.rmi.transport.tcp.responseTimeout=3000
```
更多 RMI 优化参数请查看 [JDK 文档](https://docs.oracle.com/javase/6/docs/technotes/guides/rmi/sunrmiproperties.html)

### 接口说明
如果服务接口继承了 `java.rmi.Remote` 接口，可以和原生 RMI 互操作，即：

* 提供者用 Dubbo 的 RMI 协议暴露服务，消费者直接用标准 RMI 接口调用，
* 或者提供方用标准 RMI 暴露服务，消费方用 Dubbo 的 RMI 协议调用。

如果服务接口没有继承 `java.rmi.Remote` 接口：

* 缺省 Dubbo 将自动生成一个 `com.xxx.XxxService$Remote` 的接口，并继承 `java.rmi.Remote` 接口，并以此接口暴露服务，
* 但如果设置了 `<dubbo:protocol name="rmi" codec="spring" />`，将不生成 `$Remote` 接口，而使用 Spring 的 `RmiInvocationHandler` 接口暴露服务，和 Spring 兼容。

**定义 RMI 协议**

```xml
<dubbo:protocol name="rmi" port="1099" />
```

**设置默认协议**

```xml
<dubbo:provider protocol="rmi" />
```

**设置某个服务的协议**

```xml
<dubbo:service interface="..." protocol="rmi" />
```

**多端口**

```xml
<dubbo:protocol id="rmi1" name="rmi" port="1099" />
<dubbo:protocol id="rmi2" name="rmi" port="2099" />

<dubbo:service interface="..." protocol="rmi1" />
```

**Spring 兼容性**

```xml
<dubbo:protocol name="rmi" codec="spring" />
```


> - **如果正在使用 RMI 提供服务给外部访问，** 公司内网环境应该不会有攻击风险。
> - **同时应用里依赖了老的 common-collections 包的情况下，** dubbo 不会依赖这个包，请排查自己的应用有没有使用。
> - **存在反序列化安全风险。** 请检查应用：将 commons-collections3 请升级到 [3.2.2](https://commons.apache.org/proper/commons-collections/release_3_2_2.html)；将 commons-collections4 请升级到 [4.1](https://commons.apache.org/proper/commons-collections/release_4_1.html)。新版本的 commons-collections 解决了该问题。

## 使用方式 - Go

暂不支持

## 使用方式 - Node.js

暂不支持

## 使用方式 - Rust

暂不支持
