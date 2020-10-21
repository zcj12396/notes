Mybatis缓存

会话先访问二级缓存，二级缓存未命中再访问一级缓存



二级：应用级缓存

一级：会话级缓存



### 一级缓存命中场景

一级缓存是一个<K,V>容器



- sql语句相同，参数相同则命中

- 必须是同一session（因为一级缓存是会话级缓存）
- statement id【全路径类名.方法名】要相同
- 分页（rowBounds）必须相同

** 如果不想使用一级缓存，关闭：**

1. session.clearCache():在update,delete,insert,commit，rollback,closeSession时会触发
2. query && (isFlushCacheRequire || localCacheScope == STATEMENT[跟子查询有关])

写一个FirstCacheTest:

```java
//构建一个sql session
Session session = new SqlSessionFactoryBuilder().build().openSession()
```



一级缓存源码流程：

调用mybatis->Mapper->SqlSession（门面）->BaseExecutor->JDBC



BaseExecutor包含localCache(PerpetualCache<HashMap>提供put,get)



BaseExecutor.query()时，判断是否存在缓存？没有直接doQuery()访问jdbc



问题

- queryStack == 0 时才清空Cache

- 查询数据库之前为什么要加缓存占位符

- Mybatis集成Spring会失效

  

  mybatis的SqlSession实现类是DefaultSqlSession -> BaseExectour

  spring的SqlSession实现类是SqlSessionTemplate -> [DefaultSqlSession] sqlSessionFactory.build()新建一个新的会话（用于同一个事务），所以会话级的一级缓存失效了 

  

  区别在于Mybatis的Mapper是多例实现，只能与唯一的会话绑定

  Spring的Mapper是单例实现，是跨多个会话的