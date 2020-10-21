# rabbitmq
## broker指什么？Cluster指什么？

## **vhost**的作用

一个mini-mqserver。含有独立的queue、exchange和binding。

同时也有独立的权限系统。

## 一条消息的路由

Exchange绑定到一条Queue，然后定义一个Routing Key

Msg（携带Routing Key）---> 根据Exchange的Routing Key投到一条Queue

**默认交换器，隐式地绑定到每个队列，路由键等于队列名称**

## rabbitmq的queue中存放的message是否有数量限制

无限制，但受限于机器内存

## Exchange的类型

- direct： 如果路由键完全匹配，就会走这条交换器，到达对应Queue
- fanout：如果交换器受到消息，就会广播到所有绑定的队列（直接转发）
- topic：生产者发送的消息中的Routing Key为“A.B.C...”,exchange绑定以不同的topic表达式绑定不同的queue，来区分消息。

## 保证消息的正确发出和消费

### 消息的正确发送

**发送方确认模式**

将信道设置为confirm模式（发送方确认模式），所有消息都会有一个唯一ID，消息被MQ正确接受（存到Queue或者持久化）后，以这个ID来确认消息已被成功接收。

这个模式是异步的。

### 消费放正确消费

消费方的消息接收确认

rabbitmq没有消息消费超时机制，只要消费者不断连，都可以认为在消费中

特殊情况：

	- 消费者在确认前断连或者取消订阅，mq会发给下一个消费者。
	- 如果消费者长时间没有确认消息，则不会给这个消费者再分配消息。

### 消息是否需要ID

发送消息的时候，MQ可以自动生成id

消费消息时，为了避免重复消费，可以生成业务上的全局唯一ID（订单ID等）来保证。

## Rabbitmq保证消息的顺序性

场景：三条消息，A，B，C分别给了三个消费者，结果非A先消费完。



- 方案1： 拆分多个Queue，每个Queue对应一个Customer。
- 方案2： 还是只有一个Queue。但是多个Customer使用内存队列来排序消费。

## 如何确保消息不丢失（消息持久化）

将交换器/队列设置durable=true。



