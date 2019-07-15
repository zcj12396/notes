## Java原生NIO

#### 核心组成部分

+ Channel
+ Buffer
+ Selector

##### 举例

###### Channels

- FileChannel

- DatagramChannel

- SocketChannel

- ServerSocketChannel

###### Buffers

- ByteBuffer
- CharBuffer
- DoubleBuffer
- FloatBuffer
- IntBuffer
- LongBuffer
- ShortBuffer



Selector可以使单线程处理多个Channel

1. 向Selector注册Channel
2. 调用`select()`方法（会一直阻塞直到有返回）
3. work线程处理