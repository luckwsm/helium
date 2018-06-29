# helium

IM

## 相关原理

### 报文类型
  - 请求报文 R
  - 应答报文 A
  - 通知报文 N
  
### 普通消息投递
  用户a向b发送“你好”
  - 客户端a向服务器发送请求报文 msg:R
  - 服务器收到后给客户端a发送应答报文 msg:A
  - 服务器给客户端b发送通知报文 msg:N

  可能出现的问题
  - 服务器崩溃 N未发出
  - 网络波动 N包被丢弃
  - 客户端b崩溃 N未接收
  
  增加确认报文
  - 客户端b向服务器发送确认请求 act:R
  - 服务器向客户端b发送确认响应 act:A
  - 服务器向客户端a发送确认通知 act:N
  
  消息的超时与重传
  - client-A发出了msg:R，收到了msg:A之后，在一个期待的时间内，如果没有收到ack:N，client-A会尝试将msg:R重发。
  - 可能client-A同时发出了很多消息，故client-A需要在本地维护一个等待ack队列，并配合timer超时机制，来记录哪些消息没有收到ack:N，以定时重发
  
  消息的去重
  - 绑定msgid
  
  相关
  - 客户端重传，保证服务端无状态
  - 离线消息需要伪造 act:N
  - 离线消息拉取也要act，发送offline:R报文拉取消息，收到offline:A后，再发送offlineack:R删除离线消息
  
### 离线消息投递
  消息接收方不在线时的典型消息发送流程
  - 客户端a发送消息到服务器 msg:R
  - 服务器收到后给客户端a发送应答 msg:A
  - 服务器发现客户端b不在线，持久化消息
  - 服务器给客户端a发送确认通知 act:N
  
  离线消息存储主要字段
  - 消息接收者
  - 消息唯一id
  - 发送时间
  - 消息发送者
  - 消息类型
  - 消息体
  
  拉取离线消息流程
  - 客户端b拉取客户端a发送的消息请求
  - 服务器获取离线消息
  - 服务器删除消息
  - 服务器返回客户端b消息
  
  多用户拉取离线消息优化
  - 先拉各个好友离线消息数量，真正去看消息再去单独拉取
  - 一次性拉取所有离线消息，客户端去分组