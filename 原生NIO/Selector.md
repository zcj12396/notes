## Selector

open创建

```java
public AbstractSelector openSelector() throws IOException {  
        return new WindowsSelectorImpl(this);  
    }  
    
WindowsSelectorImpl(SelectorProvider sp) throws IOException {  
        super(sp);  
        pollWrapper = new PollArrayWrapper(INIT_CAP);  
        wakeupPipe = Pipe.open();  
        wakeupSourceFd = ((SelChImpl)wakeupPipe.source()).getFDVal();  
  
        // Disable the Nagle algorithm so that the wakeup is more immediate  
        SinkChannelImpl sink = (SinkChannelImpl)wakeupPipe.sink();  
        (sink.sc).socket().setTcpNoDelay(true);  
        wakeupSinkFd = ((SelChImpl)sink).getFDVal();  
  
        pollWrapper.addWakeupSocket(wakeupSourceFd, 0);  
    }  

```

最主要的是`Pipe.open()`会创建一个PipeImpl（使用同一个SelectorProvider）

```java
PipeImpl(SelectorProvider sp) {  
        long pipeFds = IOUtil.makePipe(true);  
        int readFd = (int) (pipeFds >>> 32);  
        int writeFd = (int) pipeFds;  
        FileDescriptor sourcefd = new FileDescriptor();  
        IOUtil.setfdVal(sourcefd, readFd);  
        source = new SourceChannelImpl(sp, sourcefd);  
        FileDescriptor sinkfd = new FileDescriptor();  
        IOUtil.setfdVal(sinkfd, writeFd);  
        sink = new SinkChannelImpl(sp, sinkfd);  
 }  

```

Selector通过Pipe来实现被唤醒的功能。

