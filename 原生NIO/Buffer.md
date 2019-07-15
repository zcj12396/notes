## Buffer

### interface : Buffer

主要包含

```java
// Invariants: mark <= position <= limit <= capacity
//position和limit的含义取决于Buffer处在读模式还是写模式。不管Buffer处在什么模式，capacity的含义总是一样的。
private int mark = -1;
//写数据时：初始为0，写入一个后移，最大值为capatiry-1
//读数据时：从position处开始读，读完后移一位。（当Buffer从写模式切换为读时，position置为0）
private int position = 0;
//写模式时：等于capacity，最大写入限制。
//读模式时：等于从写切换为读时的最大position，为最大读取限制
private int limit;
private int capacity; //容量（单位：比如写入capatiry个int）
```



Buffer一般使用步骤

1. 写入数据到Buffer
2. 调用`flip()`方法（掉转标记）
3. 从Buffer中读取数据
4. 调用`clear()`方法或者`compact()`方法 来清空缓冲区

##### 创建Buffer

`ByteBuffer buf = ByteBuffer.allocate(1024);//创建长度为1024的Buffer`

##### 写入Buffer

1. 从Channel中读取

`int bytesRead = inChannel.read(buf);`

2. Buffer的`put()`方法

   `buf.put(100)`

##### 读取

`flip()`方法会切换写模式为读模式

```java
public final Buffer flip() {
        limit = position;
        position = 0;
        mark = -1;
        return this;
    }
```



position = 0

同样有两种方法读取Buffer：`Channel.write(buf)`和`get（）`

###### 重新读取

rewind方法：

```java
public final Buffer rewind() {
        position = 0;
        mark = -1;
        return this;
    }
```

##### 放弃读取

clear()：完全重置当前Buffer，未读取的数据也会被清除

 compact() ： 重置当前Buffer，但会把未读取的数据写入到Buffer开头

##### 标记

mark（）：记住当前position的值

reset（）： position = mark

##### 比较

`equals()`

当满足下列条件时，表示两个Buffer相等：（* 只比较剩余部分)

1. 有相同的类型（byte、char、int等）。
2. Buffer中剩余的byte、char等的个数相等。
3. Buffer中所有剩余的byte、char等都相同。

`compareTo()`

比较两个Buffer的剩余元素(byte、char等)， 如果满足下列条件，则认为一个Buffer“小于”另一个Buffer：

1. 第一个不相等的元素小于另一个Buffer中对应的元素 。
2. 所有元素都相等，但第一个Buffer比另一个先耗尽(第一个Buffer的元素个数比另一个少)。

*（注：剩余元素是从 position到limit之间的元素）*