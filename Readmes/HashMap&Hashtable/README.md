HashMap和HashTable
==================

#相同

都是用于存储键值对的数据类型。

#不同

1. HashMap非线程安全；允许键和值为null；实现Map接口。
2. HashTable线程安全，其所有方法都是synchronized的保证线程安全；不允许键或值为null；继承自Dictionary。