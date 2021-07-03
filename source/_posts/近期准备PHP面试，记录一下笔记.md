---
title: 近期准备PHP面试，记录一下笔记
top: 100
keywords:
  - PHP
  - 面试
description: 最近准备面试，复习一下基础知识
tags:
  - 面试
  - PHP
  - Redis
  - Mysql
categories:
  - PHP
  - 面试
translate_title: prepare-for-the-php-interview-in-the-near-future-and-take-notes
date: 2021-03-17 23:29:04
---

最近约了面试，复习一下基础知识。
Redis
Memcached
Mysql

<!-- more -->

## Redis
### Redis 类型
> String

redis的String类型和memcache的存储类型是一样的，以键值对的方式进行存储。
String类型是redis的最基础存储类型

> Hash

String类型存储的是键值对（key => value），Hash属于键值对的集合类型，`HMSET Key filed1 value filed2 value`，`HGET Key filed1`

> List

List是列表存储类型，一般用户秒杀/抢购等
```
// 先为商品库存存为list列表
$num = 100;
for ($i = 1; $i < $num; $i++) {
    $redis->lpush('goods_id_stock', $i);
}

/**
* 列表的pop操作是原子的，即使有很多用户同时到达，也是依次执行的
* 先将物品数量存入队列，每来一个用户，取出来一个
*/
$check = $redis->lPop('goods_store');

if(!$check){
    echo '已经售完';
}else{
    //如果抢购成功，将商品编号存入键值为user_id的列表中
    $redis->lPush('user_id',$check);
}
```

> Set

Set是无序集合，集合是通过哈希表实现的。
`sadd key member`
sadd命令，将一个string类型添加到Set中，具有唯一性，如果有不存在返回1，存在返回0。

> Zset 

Zset为有序集合，和Set一样，也是String类型的集合。
和Set不同的是，Zset拥有一个double类型的分数，redis正是使用分数来对集合进行排序。
`zadd key score member`

## Mysql
### InnoDB 和 MyISAM
> 两者之间的不同点。

1. InnoDB 支持事务而MyISAM不支持。
2. 表所差异，MyISAM只支持表级锁，InnoDb支持表级锁、行级锁。
3. MyISAM支持FULLTEXT，InnoDB不支持。
4. MyISAM不支持外键，InnoDB支持。

### 表锁
```
// 加锁命令
lock table table_name write/read
// 解除锁
unlock tables
```
`lock table table_name read`，对表加锁以后，表示这个表只能读取，所有线程包括自己都无法对表进行写入。

`lock table table_name write`，表示当前线程可对该表进行读写，其他线程对该表读写将会陷入阻塞。

当线程对某个表执行`write锁`，将无法对其他表进行读写操作，需解锁后方可读写，而其它线程不受此限制。

### 行锁
> 如何使用行锁

执行事务时，相当执行了锁，来保持数据的唯一性。
当有索引，并且将该索引作为条件时就是行锁，否则就是表锁。
```
// 查询加锁
select * from table where id = $id for update
```

> 两阶段锁协议

在InnoDB事物中，行锁是在需要的时候才加上的，但并不是不需要了就立刻释放，而是要等到事物提交了才会释放，这个就是两阶段锁协议；

知道了这个协议后，对我们的某些开发会得到效率提升，比如：

如果你的事物中需要锁多个行，要把最可能造成锁冲突、最可能影响并发度的锁尽量往后放，

### 悲观锁
就是每次拿数据的时候都会认为别人会修改，所以在查询时都会上锁，直到拿到了数据。

### 乐观锁
就是每次拿数据的时候认为别人不回修改，但是在更新数据的时候会判断一下，期间是否有人修改了数据。
