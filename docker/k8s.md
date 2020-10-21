## 控制器
 - ReplicationController: 定义Pod模板信息来实现水平扩展：1.新增或删除Pod来保证数量。2.支持滚动更新Pod。
 - ReplicationSet: 具有副本筛选功能，但无法滚动更新。
 - Deployment控制器： 以ReplicationSet为基础。可以滚动更新。
 - DaemonSet控制器：在节点上运行一个单一的pod副本。
 - Job：一次性的任务。
 ## 服务和存储
 - Service组件： 通过Service来访问Pods
 - Ingress：用于整合Services。可以通过同一域名下的不同路径来访问不同的Service组件，实现在同一域名下发布多个服务。
 ## 存储
  - 存储卷： 用于Pod中，Pod中所有的容器均共享。
  - 持久存储卷： 可以自定义Pod退出后的动作。



