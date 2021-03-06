# 解决方案

[toc]

## Impala + Kudu 

Kudu+Impala为实时数据仓库存储提供了良好的解决方案。这套架构在支持随机读写的同时还能保持良好的Scan性能，同时其对Spark等流式计算框架有官方的客户端支持。这些特性意味着数据可以从Spark实时计算中实时的写入Kudu，上层的Impala提供BI分析SQL查询，对于数据挖掘和算法等需求可以在Spark迭代计算框架上直接操作Kudu底层数据。



## Flink + Canal  增量同步

[基于Canal与Flink实现数据实时增量同步(一)]([https://jiamaoxiang.top/2020/03/05/%E5%9F%BA%E4%BA%8ECanal%E4%B8%8EFlink%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%A2%9E%E9%87%8F%E5%90%8C%E6%AD%A5-%E4%B8%80/](https://jiamaoxiang.top/2020/03/05/基于Canal与Flink实现数据实时增量同步-一/))

[基于Canal与Flink实现数据实时增量同步(二) | Jmx's Blog](https://jiamaoxiang.top/2020/03/24/%E5%9F%BA%E4%BA%8ECanal%E4%B8%8EFlink%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E5%AE%9E%E6%97%B6%E5%A2%9E%E9%87%8F%E5%90%8C%E6%AD%A5-%E4%BA%8C/)



## 得到的实时大数据运营设计

> [第16期实录：用户分群 -- 得到实时大数据运营探索](https://mp.weixin.qq.com/s/yz3GCgAY1t8cTbviaNtnFQ)

 ![image](https://static.lovedata.net/20-07-03-cd6ce673112f45040cb8aeb138acd3e3.png-wm)

###  Merge流程

1. 首先对Binlog按 **主键+事件类型** group by，取出**delete** 事件与**存量数据**进行left outer join，这样就从存量数据中剔除了删除数据；
2. 对剩余Binlog数据进行二次筛选，按主键 **group by 取最新的Binlog**，无论是**insert或是update**；
3. 将步骤2中生成的数据与存量数据合并，生成最新数据。如图中的黄色的1、2是受Binlog的update重放影响的，绿色的4是insert新增的。

![image](https://static.lovedata.net/20-07-03-d99d464e7c25bea52f2cd0e3dfc879a5.png-wm)

![image](https://static.lovedata.net/20-07-03-174c85904728226c3d499924c23d9ef5.png-wm)