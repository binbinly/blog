# HTTPS通信流程详解

HTTPS通信主要包括几个节点，`发起请求`、`验证身份`、`协商秘钥`、`加密会话`，具体流程如下(此例子只有客户端对服务端的单向验证)：

1. 客户端向服务端发起建立HTTPS请求。
2. 服务器向客户端发送数字证书。
3. 客户端验证数字证书，证书验证通过后客户端生成会话密钥(双向验证则此处客户端也会向服务器发送证书)。
4. 服务器生成会话密钥(双向验证此处服务端也会对客户端的证书验证)。
5. 客户端与服务端开始进行加密会话。

## 具体的步骤

### 第一步：客户端向服务端发起请求

1. 客户端生成随机数R1 发送给服务端;
2. 告诉服务端自己支持哪些加密算法;

### 第二步：服务器向客户端发送数字证书

1. 服务端生成随机数R2;
2. 从客户端支持的加密算法中选择一种双方都支持的加密算法(此算法用于后面的会话密钥生成);
3. 服务端生成把证书、随机数R2、会话密钥生成算法，一同发给客户端;

### 第三步：客户端验证数字证书

1. 验证证书的可靠性，先用CA的公钥解密被加密过后的证书,能解密则说明证书没有问题，然后通过证书里提供的摘要算法进行对数据进行摘要，然后通过自己生成的摘要与服务端发送的摘要比对。
2. 验证证书合法性，包括证书是否吊销、是否到期、域名是否匹配，通过后则进行后面的流程
3. 获得证书的公钥、会话密钥生成算法、随机数R2
4. 生成一个随机数R3。
5. 根据会话秘钥算法使用R1、R2、R3生成会话秘钥。
6. 用服务端证书的公钥加密随机数R3并发送给服务端。

### 第四步：服务器得到会话密钥

1. 服务器用私钥解密客户端发过来的随机数R3
2. 根据会话秘钥算法使用R1、R2、R3生成会话秘钥

### 第五步：客户端与服务端进行加密会话

1. 客户端发送加密数据给服务端

> 发送加密数据：客户端加密数据后发送给服务端。

2. 服务端响应客户端

> 解密接收数据：服务端用会话密钥解密客户端发送的数据;
>
> 加密响应数据：用会话密钥把响应的数据加密发送给客户端。

3. 客户端解密服务端响应的数据

> 解密数据：客户端用会话密钥解密响应数据

## [原文](https://www.safedog.cn/news.html?id=5032)