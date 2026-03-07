Redis 是一种使用**键值对**存储的 NoSQL 数据库。
# 基本概念

.....
- 经过一下午艰苦卓绝的斗争，我终于安装好了 RockyLinux 和 Redis。

## patterns
Redis 里面的 patterns 是一种**匹配格式**的表达式。
*下面的例子展示了 patterns 的用法：*
- `h?llo` matches `hello`, `hallo` and `hxllo`
- `h*llo` matches `hllo` and `heeeello`
- `h[ae]llo` matches `hello` and `hallo,` but not `hillo`
- `h[^e]llo` matches `hallo`, `hbllo`, ... but not `hello`
- `h[a-b]llo` matches `hallo` and `hbllo`


# 命令
可以通过查看[官方文档](https://redis.io/docs/latest/commands/)或者在 **redis-cli** 中使用 `help` 命令查看。

## 基本数据结构
Redis 是一个基于 `key-value` 的数据库，其中的 **key**的一般是 **String** 类型，但是 **value** 的类型有很多种。如 `String`、`Hash` 、`List`... 
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307141232074.png)


## 通用命令
就是 redis-cli 中的 `help @generic` 组的相关命令。这些命令是**通用命令**，不分数据类型，全部可以使用。


