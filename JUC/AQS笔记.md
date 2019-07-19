## AQS

通过维护一个state变量和等待线程队列来实现同步功能。

Structure

- class Node

- class ConditionObject

  ```java
  private transient volatile Node head;
  
  private transient volatile Node tail;
  
  private volatile int state;
  
  static final long spinForTimeoutThreshold = 1000L;
  
  private static final Unsafe unsafe = Unsafe.getUnsafe();
  private static final long stateOffset;
  private static final long headOffset;
  private static final long tailOffset;
  private static final long waitStatusOffset;
  private static final long nextOffset;
  ```





实现一个自定义的锁

ReentrantLock举例

里面有一个实现了AbstractQueueSynchronizer的Sync类

### 非公平锁和公平锁的实现区别

```java
//非公平锁，会直接尝试插队一次来获取锁。
//所以如果一直有线程插队成功就会导致某些线程饥饿
final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
}


	//公平锁
final void lock() {
            acquire(1);
        }
```



锁抢占成功后的操作：

state ++；

设置owner线程为当前线程



#### AQS加锁实现代码

```java

public final void acquire(int arg) {
    //tryAcquire由Sync类实现。
	//主要实现方式是
	//if state == 0 .return (CAS抢占结果） or state > 1 state ++ (需要考虑int移除)
	//else return false
        if (!tryAcquire(arg) &&
            //尝试把当前线程加入等待队列
            //Node.EXCLUSIVE 独占模式
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }



```

```java
//把当前节点放到tail节点后面
private Node addWaiter(Node mode) {
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
        Node pred = tail;
        if (pred != null) {
            node.prev = pred;
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        enq(node);
        return node;
    }
```



```java

final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                // p == tail  p.pre
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
```

