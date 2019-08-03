## interface：： condition

- await():void  // 等待，当前线程在接到信号或被中断之前一直处于等待状态
- awaitUninterruptibly():void   // 等待，当前线程在接到信号之前一直处于等待状态，不响应中断
- awaitNanos(long):long   //等待，当前线程在接到信号、被中断或到达指定等待时间之前一直处于等待状态 
- await(long,TimeUtil):boolean  // 等待，当前线程在接到信号、被中断或到达指定等待时间之前一直处于等待状态。
- awaitUntil(Date):boolean   // 等待，当前线程在接到信号、被中断或到达指定最后期限之前一直处于等待状态
- signal():void   // 唤醒一个等待线程。
- signalAll():void   // 唤醒所有等待线程。



Condition中维护有一个等待链表

```java
/** First node of condition queue. */
private transient Node firstWaiter;
/** Last node of condition queue. */
private transient Node lastWaiter;
```

tips：

transient关键字表示该值在序列化时不需要维持。



Node

```java
// 模式，分为共享与独占
        // 共享模式
		//判断是否共享是 return SHARED == nextWaiter;
        static final Node SHARED = new Node();
        // 独占模式
        static final Node EXCLUSIVE = null; 


// 结点状态
        // CANCELLED，值为1，表示当前的线程被取消
        // SIGNAL，值为-1，表示当前节点的后继节点包含的线程需要运行，也就是unpark
        // CONDITION，值为-2，表示当前节点在等待condition，也就是在condition队列中
        // PROPAGATE，值为-3，表示当前场景下后续的acquireShared能够得以执行
        // 值为0，表示当前节点在sync队列中，等待着获取锁
        static final int CANCELLED =  1;
        static final int SIGNAL    = -1;
        static final int CONDITION = -2;
        static final int PROPAGATE = -3;     
// 结点状态
        volatile int waitStatus;        
        // 前驱结点
        volatile Node prev;    
        // 后继结点
        volatile Node next;        
        // 结点所对应的线程
        volatile Thread thread;        
        // 下一个等待者
        Node nextWaiter;
```

说明：每个线程被阻塞的线程都会被封装成一个Node结点，放入队列。

每个节点包含了一个Thread类型的引用，并且每个节点都存在一个状态 

