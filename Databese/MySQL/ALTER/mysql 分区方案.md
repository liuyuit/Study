# mysql 分区方案

## references

> https://www.cnblogs.com/wangtao_20/p/7119469.html

## 分区的原理

- 分表是将数据从逻辑上按照一定规则分为了多个表。例如按照id范围分表

- 而分区在逻辑上还是一个表，只不过在底层实现上将数据分块存放了。每个分区都有单独的数据和索引文件。
  - 根据分区字段进行查询，mysql 会根据分区规则计算出结果集存在于哪个分区，然后去指定分区进行查找
  - 不根据分区字段进行查询，无法定位到具体的分区，会在所有的分区查询

#### 分区后的查询语句

语句还是按照原来的使用。但为了提高性能。还是尽量避免跨越多个分区匹配数据。

尽量使用分区字段作为查询条件

#### 分区思路和分区语句

id字段的值范围来分区：在1-2千万分到p0分区，4千万到-6千万p1分区。6千万到8千万p2分区

```
CREATE TABLE `fs_punch_in_log` (
`id`  bigint(20) UNSIGNED NOT NULL AUTO_INCREMENT ,
`user_id`  varchar(50) ,
`punch_in_time`  int(10),
PRIMARY KEY (`id`)
)  partition BY RANGE (id) (
    PARTITION p1 VALUES LESS THAN (40000000),
    PARTITION p2  VALUES LESS THAN (80000000), 
    PARTITION p3  VALUES LESS THAN (120000000),
    PARTITION p4  VALUES LESS THAN MAXVALUE
);
```

如果表结构已经定义好了，里面有数据了，怎么进行分区呢？使用alter语句修改即可

```
ALTER TABLE `fs_punch_in_log`
PARTITION BY RANGE (id)
(
PARTITION p1 VALUES LESS THAN (40000000),
PARTITION p2  VALUES LESS THAN (80000000),
PARTITION p3  VALUES LESS THAN (120000000),
PARTITION p4  VALUES LESS THAN MAXVALUE
)
```

## 四种分区类型

mysql分区包括四种分区方式：hash分区、按range分区、按key分区、list分区。

**HASH分区**

有常规hash和线性hash两种方式。

- 常规hash是基于分区个数取模（%）运算。根据余数插入到指定的分区。打算分4个分区，根据id字段来分区。 

- 线性HASH(LINEAR HASH)稍微不同点。

  实际上线性hash算法，就是我们memcache接触到的那种一致性hash算法。使用虚拟节点的方式，解决了上面hash方式分区时，当新增加分区后，涉及到的数据需要大量迁移的问题。

**按照range范围分区**

范围分区，可以自由指定范围。比如指定1-2000是一个分区，2000到5000千又是一个分区。范围完全可以自己定。

**按key分区**

  类似于按HASH分区，区别在于KEY分区只支持计算一列或多列，且MySQL服务器提供其自身的哈希函数。必须有一列或多列包含整数值。

**按list方式分区** 

可以把list看成是在range方式的基础上的改进版。list和range本质都是基于范围，自己控制范围。

