Redis 是一种使用**键值对**存储的 NoSQL 数据库。
# 基本概念

.....
- 经过一下午艰苦卓绝的斗争，我终于安装好了 RockyLinux 和 Redis。

## patterns
**patterns**是Redis里面的一种**匹配格式**的表达式。一种**通配符**。
*下面的例子展示了 patterns 的用法：*
- `h?llo` matches `hello`, `hallo` and `hxllo`
- `h*llo` matches `hllo` and `heeeello`
- `h[ae]llo` matches `hello` and `hallo,` but not `hillo`
- `h[^e]llo` matches `hallo`, `hbllo`, ... but not `hello`
- `h[a-b]llo` matches `hallo` and `hbllo`

## Key 的层级结构
在 redis 中，因为是按照**键值对**的方式存储，没有**表**的结构，那么如果两个商品的 id 都为 1，该怎么区分呢？

键可以使用 `:` 来划分**层级结构**，例如：
```
Shop:User:1
Shop:User:2
Shop:Product:1
Shop:Product:2
```

那么，在 RESP 中看到的就是这样的：
这些键就具有了层级结构。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307152343531.png)


# 命令
可以通过查看[官方文档](https://redis.io/docs/latest/commands/)或者在 **redis-cli** 中使用 `help` 命令查看。

## 通用命令
就是 redis-cli 中的 `help @generic` 组的相关命令。这些命令是**通用命令**，不分数据类型，全部可以使用。
*一些常用的**通用命令**：*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307143133942.png)

## 基本数据结构
Redis 是一个基于 `key-value` 的数据库，其中的 **key**的一般是 **String** 类型，但是 **value** 的类型有很多种。如 `String`、`Hash` 、`List`... 
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307141232074.png)

### String 类型
说是 String 类型，其实就是最简单的各种**字符串**。可以分成 3 类：
- string: 字符串
- int : 整数
- float：浮点数
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307150548164.png)

*常见命令：*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307150614122.png)


