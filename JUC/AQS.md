## AQS

　AQS使用一个int成员变量来表示同步状态，通过内置的FIFO队列来完成获取资源线程的排队工作。

`    private volatile int state;//共享变量，使用volatile修饰保证线程可见性`


  