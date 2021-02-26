# MySQL逻辑架构介绍

MySQL逻辑架构图

<img src="..\images\image-20210223000339825.png" alt="image-20210223000339825" style="zoom:80%;" />

## 连接层

最上层是一些客户端和连接服务，包含本地sock通信和大多数基于客户端/服务端工具实现的类似于tcp/ip通信。主要完成一些类似于连接处理、授权认证及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全的客户端提供线程。同样在该层上可以实现SSL的安全链接。服务器也会为安全接入的每个客户验证它所具有的操作权限。

## 服务层

第二层架构主要完成大多数的核心功能，如SQL接口，并完成缓存的查询，SQL的分析和优化及部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定查询表的顺序，是否利用索引等，最后生成相应的执行操作。如果select语句，服务器还会查询内部的缓存。如果缓存空间足够大，还可以在解决大量读操作的环境中很好地提升系统性能。

## 引擎层

存储引擎层，存储引擎真正的负责了MySQL中数据的存储和读取，服务器通过API与存储引擎进行通信。不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取。后面介绍MyISAM和InnoDB。

## 存储层

数据存储层。主要是将数据存储在运行于裸设备的文件系统之上，并完成于存储引擎的交互。

# 存储引擎简介

> 查看命令

```sql
mysql> show engines;
#查看当前默认的存储引擎
mysql> show variables like '%storage_engine%';
+---------------------------------+-----------+
| Variable_name                   | Value     |
+---------------------------------+-----------+
| default_storage_engine          | InnoDB    |
| default_tmp_storage_engine      | InnoDB    |
| disabled_storage_engines        |           |
| internal_tmp_mem_storage_engine | TempTable |
+---------------------------------+-----------+
4 rows in set, 1 warning (1.00 sec)
```

## MyISAM和InnoDB

|          | MyISAM                                                     | InnoDB                                                       |
| :------: | ---------------------------------------------------------- | ------------------------------------------------------------ |
|  主外键  | 不支持                                                     | 支持                                                         |
|   事务   | 不支持                                                     | 支持                                                         |
|  行表锁  | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作。 | 行锁，操作时只影响一行，不对其他行有影响，适合高并发的操作   |
|   缓存   | 只缓存索引，不缓存数据。                                   | 不仅缓存索引还要缓存数据，对内存要求较高，而且内存大小对性能有决定性影响。 |
|  表空间  | 小                                                         | 大                                                           |
|  关注点  | 性能                                                       | 事务                                                         |
| 默认安装 | Y                                                          | Y                                                            |

## 淘宝、阿里巴巴用什么

![image-20210223125608299](..\images\image-20210223125608299.png)

| 产品           | 价格 | 目标                                     | 主要功能 | 是否投入生产 |
| -------------- | ---- | ---------------------------------------- | -------- | ------------ |
| Percona Server | 免费 | 提供XtraDB存储引擎的包装器和其他分析工具 | XtraDB   | 是           |
| MariaDB        | 免费 | 扩展MySQL以包含XtraDB和其他性能改进      | XtraDB   | 是           |
| Drizzle        | 免费 | 提供比MySQL更强大的可扩展性和性能改进    | 高可用性 | 是           |

- Percona Server为MySQL数据库服务器进行了改进，在功能和性能上较MySQL有着显著的提升。改版本提供了高负载情况下InnoDB的性能、为DBA提供了一些非常有用的性能诊断工具；另外有更多的参数和命令来控制服务器行为。
- 该公司新建了一款存储引擎叫XtraDB完全可以替代InnoDB，并且性能和并发上做的更好。
- 阿里巴巴大部分MySQL数据库其实使用的percona的原型加以修改。
- Alisql+AliRedis

# 索引优化分析

> 性能下降SQL慢，执行时间长，等待时间长

查询语句写的不好

索引失效

关联太多join(设计缺陷或不得已的需求)

服务器调优及各个参数配置，缓冲，线程数

## 常见通用join查询

### SQL执行顺序

> 手写

1. select
2. from
3. join on
4. where
5. group by
6. having
7. order by
8. limit

> 机读

1. from
2. on
3. join
4. where
5. having
6. select
7. distinct
8. order by
9. limit

> 总结

![image-20210223133840143](..\images\image-20210223133840143.png)

### join图

![](..\images\image-20210223134250654.png)

### 后面测试用的SQL

```sql
create database db01;
use db01;
CREATE TABLE `tbl_dept` (

  `id` int(11) NOT NULL AUTO_INCREMENT,

  `deptName` varchar(30) DEFAULT NULL,

  `locAdd` varchar(40) DEFAULT NULL,

  PRIMARY KEY (`id`)

) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;



/*Data for the table `tbl_dept` */



insert  into `tbl_dept`(`id`,`deptName`,`locAdd`) values (1,'RD','11'),(2,'HR','12'),(3,'MK','13'),(4,'MIS','14'),(5,'FD','15');



/*Table structure for table `tbl_emp` */



DROP TABLE IF EXISTS `tbl_emp`;



CREATE TABLE `tbl_emp` (

  `id` int(11) NOT NULL AUTO_INCREMENT,

  `name` varchar(20) DEFAULT NULL,

  `deptId` int(11) DEFAULT NULL,

  PRIMARY KEY (`id`),

  KEY `fk_dept_id` (`deptId`)

) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8;



/*Data for the table `tbl_emp` */



insert  into `tbl_emp`(`id`,`name`,`deptId`) values (1,'z3',1),(2,'z4',1),(3,'z5',1),(4,'w5',2),(5,'w6',2),(6,'s7',3),(7,'s8',4),(8,'s9',51);

```

## 索引简介

> 官方定义

索引（index）是帮助MySQL高效获取数据的数据结构。

索引的目的在于提高查询效率，可以类比字典。

> 你可以简单理解为排好序的查询数据结构

在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引。下图就是一种可能的索引方式：

![image-20210224145730800](..\images\image-20210224145730800.png)

为了加快Col2的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找树在一定的复杂度内获取到相应数据，从而快速的检索出符合条件的记录。

一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储在磁盘上

我们平时所说的索引，如果没有特别的指明，都是B树（多路搜索树，并不一定是二叉的）结构组织索引，其中聚集索引，次要索引，覆盖索引，复合索引，前缀索引，唯一索引默认都是使用B+树索引，统称索引，当然除了B+树这种类型的索引外还有hash索引等

### 优劣

> 优势

提高数据检索的效率，降低IO成本

通过索引列对数据排序，降低排序的成本，降低CPU的消耗

> 劣势

实际上索引也是一张表，该表保存了主键和索引字段，并指向实体表的记录，所以索引也是要占用内存空间的

虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行insert、update、delete。因为要更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息

索引只是提高效率的一个因素，如果你的MySQL有大数据量的表，就需要花时间研究建立优秀的索引，或优化查询

### MySQL索引分类

> 单值索引

一个索引值包含单列，一个表可以创建多个索引

> 唯一索引

索引列的值必须唯一，但允许空值

> 复合索引

一个列包含多个索引

### 索引基本语法

> 创建

```sql
CREATE [UNIQUE] INDEX indexName ON mytable(columnname(length))

ALTER mytable add[unique] index [indexname] on (columnname(length))
```

> 删除

```sql
DROP INDEX[INDEXNAME] ON Mytable
```

> 查看

```sql
show index from table_name\G
```

> 使用alter命令，有四种方式添加索引

```sql
-- 添加一个主键,这意味着索引值必须是唯一的,且不能为null
alter table tbl_name add primary key (column_list);

-- 创建的索引值必须是唯一的(除了null之外,null可能会出现多次)
alter table tbl_name add unique index_name(column_list);

-- 添加普通索引,索引值可能出现多次
alter table tbl_name add index index_name(column_list);

-- 指定了索引为fulltext,用于全文索引
alter table tbl_name add fulltext index_name(column_list);
```

### MySQL索引结构

B-tree、hash、full-text、R-tree

> B树

![image-20210224202511630](..\images\image-20210224202511630.png)

> B+树

![image-20210224202301463](..\images\image-20210224202301463.png)



### 哪些情况需要添加索引

1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段应该创建索引
3. 查询表中与其他表关联的字段，外键关系建立索引
4. 频繁更新的字段不合适建立索引——因为每次更新不单单会更新记录，还会更新索引
5. where条件里用不到的字段不创建索引
6. 单键/组合索引的选择问题，who？（在高并发下倾向于创建组合索引）
7. 查询排序的字段，排序字段若通过索引去访问将大大提高排序速度
8. 查询中统计或分组字段

### 哪些情况不适合索引

1. 表记录较少
2. 经常增删改的表
3. 数据重复且分布平均的表字段，因此应该只为最经常查询和最经常排序的数据建立索引。注意，如果某个数据列包含许多重复内容，为它建立索引就没有太大的实际效果。

## 性能分析

### MySQL Query Optimizer

1. MySQL中有专门的负责优化select语句的优化器模块，主要功能：通过计算分析系统中收集到的统计信息，为客户端请求的Query提供它认为最优的执行计划（认为最优的检索方式，但不见得是DBA认为最优的，这一部分最耗费时间）。
2. 当客户端向MySQL请求一条Query，命令解析器模块完成请求分类，区别出是select并转发给MySQL Query Optimizer时，MySQL Query Optimizer 首先会对整条Query进行优化，处理掉一些常量表达式的预算，直接换算成常量值，并对Query中的查询条件进行简化和转换，如去掉一些无用或显而易见的条件、结构调整等。然后分析query中的Hint信息（如果有），看显示Hint信息是否可以完全确定该Query的执行计划。如果没有Hint或Hint信息还不足以完全确定执行计划，则会读取涉及对象的统计信息，根据Query进行写相应的计算分析，然后再得出最后的执行计划。

### MySQL常见瓶颈

1. CPU：CPU在饱和的时候一般发生在数据装入内存或从磁盘上读取数据的时候
2. IO：磁盘I/O瓶颈发生在装入数据远大于内存容量的时候
3. 服务器硬件资源的性能瓶颈：top、free、iostat和vmstat来查看系统的性能状态

## Explain

使用explain关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句，分析你的查询语句或是表结构的性能瓶颈。

> 能干嘛

表的读取顺序

数据读取操作的操作类型

哪些索引可以使用

哪些索引被实际使用

表之间的引用

每张表有多少行被优化器查询

> 怎么玩

explain SQL语句

执行计划

![image-20210224230457294](..\images\image-20210224230457294.png)

### id

select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序

三种情况：

#### **id相同，执行顺序由上到下**

![image-20210224230935743](..\images\image-20210224230935743.png)

#### **id不同**

，如果是子查询，id的序号会递增，id越大优先级越高

![image-20210224231152795](..\images\image-20210224231152795.png)

#### **id相同不同，同时存在的情况**

![image-20210224231507478](..\images\image-20210224231507478.png)

### select_type

> 有哪些？

![image-20210224232155833](..\images\image-20210224232155833.png)

查询的类型，主要是用于区别，普通查询，联合查询，子查询等复杂查询。

1. SIMPLE：简单的select查询，查询中不包含子查询或者UNION
2. PRIMARY：查询中若包含负责的子部分，最外层则被标记为
3. SUBQUERY：在select或where列表中包含子查询
4. DERIVED：在from列表中包含子查询被标记为DERIVED（衍生）MySQL会递归执行这些子查询，把结果放到临时表中
5. UNION：若第二个select出现在UNION之后，则被标记为UNION，若UNION包含在from子句的子查询中，外层select将被标记为DERIVED
6. UNION RESULT：从UNION表获取结果select

### type

![image-20210224233527625](..\images\image-20210224233527625.png)

访问类型排列

显示查询了使用了何种类型，从最好到最差依次是：

system > const > eq_ref > ref > range > index > ALL

1. system：表中只有一行记录（等于系统表），这是const类型的特例，平时不会出现，忽略不计
2. const：表示通过索引一次就找到了，const用于比较primary key或者unique索引，因为只匹配一行数据，所以很快将主键置于where列表中，MySQL就能将该查询转换为一个常量
3. eq_ref：唯一性索引扫描，对每个索引键，表中只有一条记录与之匹配常见于主键或唯一索引扫描
4. ref：非唯一性索引扫描，返回匹配的某个单独值所有行，本质上也是一种索引访问，它返回所有匹配单个单独的行，然而它可能会找到多个符合条件的行，所以它应该属于查找和扫描的混合体。
5. range：只检索给定范围的行，使用一个索引来选择行，key列显示使用了哪个索引，一般就是在你的where语句中出现了between <> in等查询，这种范围扫描索引比扫描全表要好，因为它开始于索引的某一点，而结束于另一点，不用扫描全部索引。
6. index：Full Index Scan与ALL区别为index类型只遍历索引书，这通常比ALL快，因为，索引文件通常比数据文件小
7. ALL：Full Table Scan 将遍历全表以找到匹配的行

==备注：一般来说要保证达到range，最好达到ref。​==

### possible_keys

显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用

### key

实际使用的索引，如果为null，则没有使用索引。

查询中若使用了覆盖索引，则该索引仅出现在key列表中。

### key_len

表示索引中使用的字节数，可通过该列计算查询使用的索引长度，在不损失精确性的情况下，长度越短越好。

ken_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检而得。

### ref

显示索引的哪一列被使用了，如果可能的话，是一个常数，那些列或常量被用于查找所有列上的值

### rows

根据表统计信息和索引选用情况，大致估算出找到所需记录所需要的读取行数。

### Extra

包含不适合在其他列中显示但十分重要的额外信息

1. Using filesort：说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取，MySQL无法利用索引完成排序操作，称为“文件排序”。
2. Using temporary：使用临时表保存了中间结果，MySQL在对查询结果排序时使用了临时表，常见于排序order by和分组查询group by
3. Using index：表示相应的select操作中使用了覆盖索引（Convering index）避免访问了表的数据行，效率不错。如果同时出现了Using where 表明索引被用来执行索引键值的查找。如果没有出现Using where 表明该索引用来读取数据而非执行查找操作。
4. Using where：表示 where 连接
5. using join buffer：使用了连接缓存
6. impossible where：where 子句的值总是false，不能用来获取任何元组
7. select tables optimized away：在没有group by 子句的情况下，基于索引优化MIN/MAX操作或者对于MyISAM引擎优化count(*)操作，不必等到执行阶段再进行计算，查询执行计划生成的阶段完成优化。
8. distinct：优化distinct操作，再找到第一匹配的元素后停止找同样值的动作。

### 热身case

![image-20210225143631797](..\images\image-20210225143631797.png)