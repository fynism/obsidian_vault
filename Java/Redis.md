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

可以使用 `:` 来为键划分**层级结构**，例如：
```
Shop:User:1
Shop:User:2
Shop:Product:1
Shop:Product:2
```

那么，在 RESP 等**图形化界面**中看到的就是这样的：
这些键就具有了层级结构。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307152343531.png)

> [!notice] 注意，在 redis 的底层，所有的键都是**扁平化**储存的。所以存入带有冒号的键跟普通的键是**完全相同**的。这只是 GUI 的约定俗成，一种可视化技术。

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

### Hash 类型
- 类似 Java 中的 HashMap 的结构。
- 在 String 结构中，如果将对象转为 JSON 字符串存储，要修改 JSON 中的**某一个**字段很麻烦。
- 这时候可以使用 Hash 结构，每个字段都单独存储。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307153239769.png)

*常见命令：*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307153341634.png)

### List 类型
与 Java 中的 LinkedList 类似。可以看作一个**双链表**。

*常见命令：*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307160042898.png)

#### **BLPOP/BRPOP**
当我想要 pop 某个 List，但是它里面暂时还没有元素，可以使用**BLPOP/BRPOP**进行阻塞等待。
*eg:*
- 想要 BLPOP 键名为 `a` 的 List，但是 `a` 中还没有元素，那么使用 BLPOP 阻塞 100 秒。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307162007019.png)

- 这时候，通过另外一个客户端连接，执行 LPUSH 操作，为 `a` 中添加元素
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307162133802.png)

- 最后，回到第一个被阻塞的客户端中，发现此时已经有返回值了。当然，此时列表为空。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307162228870.png)

### Set 类型
与 Java 中的 HashSet 很像，可以看作一个 value 值为 **null** 的 HashMap。

*常见命令：*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307163514752.png)

### SortedSet 类型
这是一个**可排序**的 set 集合，和 Java 中的 TreeSet 的**功能**很像，但是底层数据结构差别很大。

*常见命令：*
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260307163826797.png)
- `ZREVRANK key member` : 获取获取sorted set 中的指定元素的**倒序**排名。
- `ZREVRANGE key min max`：按照score排序后，**倒序**获取指定排名范围内的元素。

# Jedis
好的那么。已经学了这么多基本语法，有没有方法能在 Java 中使用 Redis 呢？有的兄弟有的。
**Jedis 包**提供了在 Java 中连接 Redis 的功能。

## 基本使用
*eg:*
```java
public class RedisTest {  
    private Jedis jedis;  
    @BeforeEach  
    public void init() {  
        //创建Jedis对象  
        jedis = new Jedis("192.168.179.130",6379) ;  
        jedis.auth("Dwade520");  
  
        //选择Jedis库  
        jedis.select(0);  
    }  
  
    @Test  
    public void testString(){  
        jedis.set("b","30");  
        String b = jedis.get("b");  
        String a = jedis.get("a");  
        System.out.println(b);  
        System.out.println(a);  
    }  
  
    @AfterEach  
    //测试完成后关闭Jedis连接  
    public void destroy() {  
        if (jedis != null) {  
            jedis.close();  
        }  
    }  
}
```

## JedisConnectionPool

# SpringDataRedis
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260310105610962.png)


# 实际使用中常见的问题

## 缓存穿透

客户端请求的数据，在缓冲和数据库中都不存在，那么这个请求一定会到达数据库。
常见解决方式：
- 缓存空对象
- 布隆过滤
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260323154917212.png)

## 缓存雪崩

Redis 中缓存的多个 key 在同一时间同时失效，导致大量请求到达数据库。
常见解决方式：
- 把 key 的 TTL 设置为一个浮动的时间范围（如 `30±5` 分钟）

## 缓存击穿
在某一时刻，一个 key 失效之后，多个并行请求来访问这一个 key，就导致多个线程进行了从数据库中重新存入缓存中的操作。
常见解决方式：
- 使用同步锁保证只有一个线程进行数据