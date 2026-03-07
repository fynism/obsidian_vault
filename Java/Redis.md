Redis 是一种使用**键值对**存储的 NoSQL 数据库。
# 基本概念

.....
经过一下午艰苦卓绝的斗争，我终于安装好了 RockyLinux 和 Redis。
# 命令
## 基本数据结构
Redis 是一个基于 `key-value` 的数据库，其中的 `key` 一般是 String 类型，但是 `value` 的类型有很多种。如 `String`、`Hash` 、`List`... 
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307141232074.png)


## 通用命令
可以在进入 `redis-cli` 后输入 `help @generic` 来查看，当然，也可以参考[官方网站](https://redis.io/docs/latest/commands/redis-6-2-commands/#generic-commands)上的文档。