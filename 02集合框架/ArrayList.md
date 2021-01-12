> ArrayList有用过吗？它是一个什么东西？用来做什么？

ArrayList是一个**可扩容的数组队列**，用来存储数据，相当于动态数组，当存储的数据是基本数据类型时，只能存储对应的包装类。和Vector不同，ArrayList是线程不安全的，要线程安全请使用Vector或CopyOnWriteArrayList。和ArrayList相似的还有LinkedList，ArrayList的查询访问速度更快，但新增和删除较慢。

> ArrayList的底层结构是怎么实现的？扩容机制？

ArrayList的无参构造函数会生成一个容量为0的数组，当真正对数据进行添加add时，才会分配默认的10容量。当add操作发现满了的时候，会进行扩容操作，再添加数据。ArrayList的add和remove操作实际上都是copy操作，这是造成ArrayList增删慢的原因。因为如果数组很大的话，一样需要复制和移动的位置就很大了。

扩容操作：判断Size*1.5是否满足集合的最小容量，如果满足，则扩容至1.5倍；如果不满足，则扩容至满足集合的最小容量。







