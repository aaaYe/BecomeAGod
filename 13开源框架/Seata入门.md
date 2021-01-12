### Seata是什么？

Seata是一款开源的分布式解决方案，致力于提高高性能和简单易用的分布式事务服务。Seata将为用户提供了AT、TCC、SAGA和XA事务模式，为用户打造一站式的分布式解决方案。

------

### 分布式事务处理过程

分布式事务处理过程的一ID+三组件模型：

Transaction ID XID：全局唯一事务ID

TC（Transaction Coordinator）：事务协调者。维护全局和分支事务的状态，驱动全局事务提交或回滚。

TM（Transaction Manager）：事务管理器。定义全局事务的范围、提交或回滚事务。

RM（Transaction Manager）：资源管理器。管理分支事务处理的资源，与TC交谈以注册事务和报告分享分支事务的状态，并驱动分支事务提交或回滚。

> 处理过程

1.TM向TC申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的XID；

2.XID在微服务调用的上下文中传播；

3.RM向TC注册分支事务，将其纳入XID对应全局事务的管辖；

4.TM向TC发起针对XID的全局提交或回滚决议；

5.TC调度XID管辖下的全部分支事务完成提交或回滚请求。

<img src="..\images\13优秀开源框架-Seata入门-Seata分布式处理过程.png" alt="Seata分布式处理过程" style="zoom:80%;" />