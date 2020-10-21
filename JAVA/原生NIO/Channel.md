##### 创建Channel

以Socket Channel举例

调用open方法

1. 创建一个SelecorProvider
2. 用Provider创建一个Channel

```java
/**
     * Opens a socket channel.
     *
     * <p> The new channel is created by invoking the {@link
     * java.nio.channels.spi.SelectorProvider#openSocketChannel
     * openSocketChannel} method of the system-wide default {@link
     * java.nio.channels.spi.SelectorProvider} object.  </p>
     *
     * @return  A new socket channel
     *
     * @throws  IOException
     *          If an I/O error occurs
     */
    public static SocketChannel open() throws IOException {
        //1. 创建一个SelecorProvider
		//2. 用Provider创建一个Channel
        return SelectorProvider.provider().openSocketChannel();
    }
```

`SelectorProvider.provider()`方法入下：

```java
public static SelectorProvider provider() {
    	//单例
        synchronized (lock) {
            if (provider != null)
                return provider;
            return AccessController.doPrivileged(
                new PrivilegedAction<SelectorProvider>() {
                    public SelectorProvider run() {
                        	//配置项反射创建
                        	//java.nio.channels.spi.SelectorProvider
                            if (loadProviderFromProperty())
                                return provider;
                            
                            if (loadProviderAsService())
                                return provider;
                        	//创建一个DefaultSelectorProvider
                            provider = sun.nio.ch.DefaultSelectorProvider.create();
                            return provider;
                        }
                    });
        }
    }
```

创建一个DefaultSelectorProvider（构造方法无方法体)



创建Channel`openSocketChannel()`会执行`new SocketChannelImpl(this);//this为SelectorProvider`

```java
//new SocketChannelImpl(this);
SocketChannelImpl(SelectorProvider sp) throws IOException {
        super(sp);
    	//文件描述符初始化，绑定
        this.fd = Net.socket(true);
        this.fdVal = IOUtil.fdVal(fd);
    	//创建完成后改变状态为UNCONNECTED（ST_UNINITIALIZED，ST_INUSE，ST_KILLED）三种状态
        this.state = ST_UNCONNECTED;
    }
```



看看`super（sp）`

会调用到AbstractSelectableChannel 的构造方法，此时AbstractSelectableChannel类被加载，执行它的静态块代码，初始化一些属性

```java
// The provider that created this channel
    private final SelectorProvider provider;//

    // Keys that have been created by registering this channel with selectors.
    // They are saved because if this channel is closed the keys must be
    // deregistered.  Protected by keyLock.
    //
    private SelectionKey[] keys = null;
    private int keyCount = 0;

    // Lock for key set and count
    private final Object keyLock = new Object();

    // Lock for registration and configureBlocking operations
    private final Object regLock = new Object();

    // Blocking mode, protected by regLock
    boolean blocking = true;

//被调用的构造函数。这里传入的是WindowsSelectorProvider(因为是windows系统)
protected AbstractSelectableChannel(SelectorProvider provider) {
        this.provider = provider;
    }
```



上文中的文件描述符 可以理解为指向了一个IP+PORT

`this.fd = Net.socket(true);`

```java
static FileDescriptor serverSocket(boolean stream) {
    	//实际调用的时候传值为socket0(true,true,true,false)
        return IOUtil.newFD(socket0(isIPv6Available(), stream, true, fastLoopback));
    }

// Due to oddities SO_REUSEADDR on windows reuse is ignored
    private static native int socket0(boolean preferIPv6, boolean stream, boolean reuse,
                                      boolean fastLoopback);
```

```c
JNIEXPORT int JNICALL
Java_sun_nio_ch_Net_socket0(JNIEnv *env, jclass cl, jboolean preferIPv6,
                            jboolean stream, jboolean reuse)
    
    int fd;
    int type = (stream ? SOCK_STREAM : SOCK_DGRAM);
#ifdef AF_INET6
    int domain = (ipv6_available() && preferIPv6) ? AF_INET6 : AF_INET;
#else
    int domain = AF_INET;
#endif

    fd = socket(domain, type, 0);
    if (fd < 0) {
        return handleSocketError(env, errno);
    }


```

以及后面的fdVal的初始化

```c
jint
fdval(JNIEnv *env, jobject fdo)
{
    return (*env)->GetIntField(env, fdo, fd_fdID);
}
```



这里的代码会在Channel中设置本连接的文件描述符，我们就可以通过操作这个文件描述符来控制这个连接。

也就实现了单线程管理多个连接



#### 然后进行Bind

```java
@Override
    public ServerSocketChannel bind(SocketAddress local, int backlog) throws IOException {
        //bind操作加锁
        synchronized (lock) {
            if (!isOpen())
                throw new ClosedChannelException();
            if (isBound())
                throw new AlreadyBoundException();
            InetSocketAddress isa = (local == null) ? new InetSocketAddress(0) :
                Net.checkAddress(local);
            SecurityManager sm = System.getSecurityManager();
            if (sm != null)
                sm.checkListen(isa.getPort());
            //nothing to do 
            NetHooks.beforeTcpBind(fd, isa.getAddress(), isa.getPort());
            //调用native方法进行bind
            Net.bind(fd, isa.getAddress(), isa.getPort());
            //调用native方法进行listen
            Net.listen(fd, backlog < 1 ? 50 : backlog);
            synchronized (stateLock) {
                //设置localAddress为监听地址
                localAddress = Net.localAddress(fd);
            }
        }
        return this;
    }
```

这个时候，ServerSocketChannel就初始化好了



