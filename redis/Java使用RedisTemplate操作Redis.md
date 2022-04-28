# Redis 基本介绍

在项目中对数据的访问往往都是直接访问数据库的方式，但如果对数据的访问量很大或者访问很频繁的话，将会对数据库来很大的压力，甚至造成数据库崩溃。为了解决这类问题 redis 数据库脱颖而出，redis 数据库出现时是以非关系数据库的光环展示在广大程序猿的面前的，后来 redis 的迭代版本支持了缓存数据、登录 session 状态（分布式 session 共享）等。所以又被作为内存缓存的形式应用到大型企业级项目中。

Redis 是完全开源免费的，遵守 BSD 协议，是一个高性能的 key-value 非关系性数据库 (NoSql)。

Redis 与其他 key - value 缓存产品有以下三个特点：

- Redis 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
- Redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。
- Redis 支持数据的备份，即 master-slave 模式的数据备份


# Redis 安装

## windows环境

- https://www.runoob.com/redis/redis-install.html

​	

启用Redis:

- Redis服务器运行命令：Redis安装目录/redis-server.exe
- Redis客户端运行命令：Redis安装目录/redis

## Linux环境安装
- https://www.runoob.com/redis/redis-install.html

## docker环境安装
- https://cloud.tencent.com/developer/article/1670205


# Redis基本数据类型

Redis 可以存储键与 5 种不同数据结构类型之间的映射，这 5 种数据结构类型分别为 String（字符串）、List（列表）、Set（集合）、Hash（散列）和 Zset（有序集合）。

| 类型                 | 简介                                                   | 特性                                                         | 场景                                                         |
| :------------------- | :----------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| String(字符串)       | 二进制安全                                             | 可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M | ---                                                          |
| Hash(字典)           | 键值对集合,即编程语言中的Map类型                       | 适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去) | 存储、读取、修改用户属性                                     |
| List(列表)           | 链表(双向链表)                                         | 增删快,提供了操作某一段元素的API                             | 1,最新消息排行等功能(比如朋友圈的时间线) 2,消息队列          |
| Set(集合)            | 哈希表实现,元素不重复                                  | 1、添加、删除,查找的复杂度都是O(1) 2、为集合提供了求交集、并集、差集等操作 | 1、共同好友 2、利用唯一性,统计访问网站的所有独立ip 3、好友推荐时,根据tag求交集,大于某个阈值就可以推荐 |
| Sorted Set(有序集合) | 将Set中的元素增加一个权重参数score,元素按score有序排列 | 数据插入集合时,已经进行天然排序                              | 1、排行榜 2、带权重的消息队列                                |



## string



# 在SpringBoot中操作Redis

## 引入依赖

在使用 springboot 时，我们有时候会整合 Redis 进行相关操作，首先在 pom.xml 中添加 redis 相关依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

## 配置

在 application.properties 中添加相关配置：

```properties
#=========redis基础配置=========
spring.redis.database=0
spring.redis.host=127.0.0.1
spring.redis.port=6379
# 连接超时时间 单位 ms（毫秒）
spring.redis.timeout=3000

#=========redis线程池设置=========
# 连接池中的最大空闲连接，默认值也是8。
spring.redis.pool.max-idle=200

#连接池中的最小空闲连接，默认值也是0。
spring.redis.pool.min-idle=200

# 如果赋值为-1，则表示不限制；pool已经分配了maxActive个jedis实例，则此时pool的状态为exhausted(耗尽)。
spring.redis.pool.max-active=2000

# 等待可用连接的最大时间，单位毫秒，默认值为-1，表示永不超时
spring.redis.pool.max-wait=1000
```

## 认识操作类

配置好后，使用的操作类主要有两种

- RedisTemplate
- StringRedisTemplate

**RedisTemplate**

RedisTemplate 是最基本的操作类，它默认的序列化方式是 JdkSerializationRedisSerializer，在存值时，键值会被序列化为字节数组，可读性差，取值时也是一样，如果 redis 中存的值正常的字符串形式，取值时将返回 null

**StringRedisTemplate**

StringRedisTemplate 继承于 RedisTemplate<String, String>，默认的序列化方式是 StringRedisSerializer，存值取值都是按照字符串的形式

**如何使用？**

- 如果存的是字符串，建议直接使用 StringRedisTemplate

- 如果是对象，可采取以下两种方法
  - 使用 RedisTemplate 存，取值时可以直接强转
  - 使用 StringRedisTemplate，存值得时候使用 json 工具类将对象转化为 json 字符串，取值时再将 json 字符串转为对象



## 使用 RedisTemplete 

### 五种基本数据类型操作

#### 操作string类型

```java
package com.example.RedisTempletDemo.string;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.concurrent.TimeUnit;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class StringDemo {

    @Autowired
    private StringRedisTemplate redisTemplate;
    
    @Test
    public void demo1() {
        //设置k,v
        redisTemplate.opsForValue().set("name", "example");
        //取值
        System.out.println(redisTemplate.opsForValue().get("name"));
    }
    
    @Test
    public void demo2() throws InterruptedException {
        //设置k,v以及有效时长，TimeUnit为单位
        redisTemplate.opsForValue().set("name", "example", 10, TimeUnit.SECONDS);
        Thread.sleep(11000);
        System.out.println(redisTemplate.opsForValue().get("name"));
    }
    
    @Test
    public void demo3() {
        redisTemplate.opsForValue().set("key", "hello world");
        //从偏移量开始对给定key的value进行覆写，若key不存在，则前面偏移量为空
        redisTemplate.opsForValue().set("key", "redis", 6);
        System.out.println(redisTemplate.opsForValue().get("key"));
    }
    
    @Test
    public void demo4() {
        redisTemplate.opsForValue().set("name", "test");
        //若key不存在，设值
        redisTemplate.opsForValue().setIfAbsent("name", "test2");
        System.out.println(redisTemplate.opsForValue().get("name"));//还是test
    }
    
    @Test
    public void demo5() {
        //批量k,v设值
        Map<String, String> map = new HashMap<String, String>();
        map.put("k1", "v1");
        map.put("k2", "v2");
        map.put("k3", "v3");
        redisTemplate.opsForValue().multiSet(map);
        
        //批量取值
        List<String> keys = new ArrayList<String>();
        keys.add("k1");
        keys.add("k2");
        keys.add("k3");
        List<String> values = redisTemplate.opsForValue().multiGet(keys);
        System.out.println(values);
        
        //批量设值，若key不存在，设值
        //redisTemplate.opsForValue().multiSetIfAbsent(map);
        
    }
    
    @Test
    public void demo6() {
        redisTemplate.opsForValue().set("name", "example");
        //设值并返回旧值，无旧值返回null
        System.out.println(redisTemplate.opsForValue().getAndSet("ttt", "calvin"));
    }
    
    @Test
    public void demo7() {
        //自增操作，原子性。可以对long进行double自增，但不能对double进行long自增，会抛出异常
        redisTemplate.opsForValue().increment("count", 1L);//增量为long
        System.out.println(redisTemplate.opsForValue().get("count"));
        
        redisTemplate.opsForValue().increment("count", 1.1);//增量为double
        System.out.println(redisTemplate.opsForValue().get("count"));
    }
    
    @Test
    public void demo8() {
        //key不存在，设值
        redisTemplate.opsForValue().append("str", "hello");
        System.out.println(redisTemplate.opsForValue().get("str"));
        //key存在，追加
        redisTemplate.opsForValue().append("str", " world");
        System.out.println(redisTemplate.opsForValue().get("str"));

    }
    
    @Test
    public void demo9() {
        redisTemplate.opsForValue().set("key", "hello world");
        //value的长度
        System.out.println(redisTemplate.opsForValue().size("key"));//11
    }
    
    @Test
    public void demo10() {      
        redisTemplate.opsForValue().set("bitTest","a");
        // 'a' 的ASCII码是 97  转换为二进制是：01100001
        // 'b' 的ASCII码是 98  转换为二进制是：01100010
        // 'c' 的ASCII码是 99  转换为二进制是：01100011
        
        //因为二进制只有0和1，在setbit中true为1，false为0，因此我要变为'b'的话第六位设置为1，第七位设置为0
        redisTemplate.opsForValue().setBit("bitTest",6, true);
        redisTemplate.opsForValue().setBit("bitTest",7, false);
        System.out.println(redisTemplate.opsForValue().get("bitTest"));
        
        //判断offset处是true1还是false0
        System.out.println(redisTemplate.opsForValue().getBit("bitTest",7));
    }

}
```

#### 操作list类型

```java
package com.example.RedisTempletDemo.list;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ListDemo {

    @Autowired
    private StringRedisTemplate redisTemplate;
    
    @Test
    public void demo1() {
        //从左边插入，即插入到列表头部
        redisTemplate.opsForList().leftPush("product:list", "iphone xs max");
        redisTemplate.opsForList().leftPush("product:list", "thinkpad x1 carbon");
        redisTemplate.opsForList().leftPush("product:list", "mackBook pro13");
        redisTemplate.opsForList().leftPush("product:list", "HuaWei Mate20 pro");   
    }
    
    @Test
    public void demo2() {
        //从左边插入一个数组
        String[] books = new String[] {"java编程思想", "springboot从入门到精通"};
        redisTemplate.opsForList().leftPushAll("book:list", books);
    }
    
    @Test
    public void demo3() {
        //从左边插入一个集合
        List<String> list = new ArrayList<String>();
        list.add("鬼泣5");
        list.add("荒野大镖客2");
        list.add("仙剑奇侠传7");
        redisTemplate.opsForList().leftPushAll("game:list", list);
    }
    
    @Test
    public void demo4() {
        //如果存在key对应的列表，则从左插入，不存在不做操作
        redisTemplate.opsForList().leftPushIfPresent("fruit:list", "1");
    }
    
    @Test
    public void demo5() {
        //在key对应的列表中从左边开始找，找到第一个pivot，然后把value插到pivot左边，没有不做操作
        redisTemplate.opsForList().leftPush("product:list", "HuaWei Mate20X", "xiaomi mix");
    }
    
    //也可以从右边插入，把上面的left改为right即可
    
    @Test
    public void demo6() {
        //指定位置重新设置指定值
        redisTemplate.opsForList().set("product:list", 1, "dell xps13");
    }
    
    @Test
    public void demo7() {
        //删除和value相同的count个元素，count < 0，从右开始,count > 0，从左开始,count = 0，全部
        redisTemplate.opsForList().remove("product:list", -1, "HuaWei Mate20 pro");
    }
    
    @Test
    public void demo8() {
        //获取制定下标对应的值 index,从0开始，有正负两套下标
        //[a,b,c,d] 下标有[0,1,2,3]和[0,-3,-2,-1];
        String value = redisTemplate.opsForList().index("product:list", 1);
        System.out.println(value);
    }
    
    @Test
    public void demo9() {
        //查询list中指定范围的内容 
        List<String> list = redisTemplate.opsForList().range("product:list", 0, -1);
        System.out.println(list);
        
        //修剪列表，使其只包含指定范围内的元素
        redisTemplate.opsForList().trim("product:list", 0, 2);
        
        //查询列表长度
        System.out.println(redisTemplate.opsForList().size("product:list"));
    }
    
    @Test
    public void demo10() {
        //弹出最左边元素
        redisTemplate.opsForList().leftPop("product:list");
        //移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时。
        redisTemplate.opsForList().leftPop("k1", 10, TimeUnit.SECONDS);
        
        
        //弹出最右边元素
        redisTemplate.opsForList().rightPop("product:list");
        
        //弹出k1最右边元素并放入k2最左边
        redisTemplate.opsForList().rightPopAndLeftPush("product:list", "game:list");
    }
}
```

#### 操作hash类型

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class HashDemo {

    @Autowired 
    private StringRedisTemplate redisTemplate;
    
    @Test
    public void demo1() {
        //put方法
        redisTemplate.opsForHash().put("k1", "name", "baipengfei");
        redisTemplate.opsForHash().put("k1", "age", "22");
        redisTemplate.opsForHash().put("k1", "height", "176");
        
        //hashKey不存在时，才设值
        //redisTemplate.opsForHash().putIfAbsent(key, hashKey, value)
    }
    
    @Test
    public void demo2() {
        //putAll方法
        Map<String, String> data = new HashMap<>();
        data.put("name", "jack ma");
        data.put("company", "alibaba");
        data.put("age", "500");
        redisTemplate.opsForHash().putAll("k2", data);
    }
    
    @Test
    public void demo3() {
        //delete方法，删除key对应的hash的hashkey及其value
        redisTemplate.opsForHash().delete("k2", "name");
    }
    
    @Test
    public void demo4() {
        //hasKey方法，确定hashkey是否存在
        System.out.println(redisTemplate.opsForHash().hasKey("k2", "name"));
    }
    
    @Test
    public void demo5() {
        //get方法，根据key和hashkey找出对应的值
        System.out.println(redisTemplate.opsForHash().get("k1", "name"));
    }
    
    @Test
    public void demo6() {
        //multiGet方法，根据key和多个hashkey找出对应的多个值
        Collection<Object> keys = new ArrayList<>();
        keys.add("name");
        keys.add("age");
        System.out.println(redisTemplate.opsForHash().multiGet("k1", keys));
    }
    
    @Test
    public void demo7() {
        //increment方法，对key和hashkey对应的值进行增加操作
        //增加长整形（无法对浮点数据使用本方法）
        System.out.println(redisTemplate.opsForHash().increment("k1", "age", 1));
        //增加浮点型（可以对整形数据使用本方法）
        System.out.println(redisTemplate.opsForHash().increment("k1", "age", 1.0));
    }
    
    @Test
    public void demo8() {
        //keys方法，获取key对应的hash表的所有key
        Set<Object> keys = redisTemplate.opsForHash().keys("k1");
        System.out.println(keys);
        
        //values方法，获取key对应的hash表的所有value
        List<Object> values = redisTemplate.opsForHash().values("k1");
        System.out.println(values);
    }
    
    @Test
    public void demo9() {
        //keys方法，获取key对应的hash表的大小
        long size = redisTemplate.opsForHash().size("k1");
        System.out.println(size);
    }
    
    @Test
    public void demo10() {
        //keys方法，获取key对应的hash表的所有键值对
        Map<Object, Object> entries = redisTemplate.opsForHash().entries("k1");
        System.out.println(entries);
    }
    
    
}
```

#### 操作set类型

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SetDemo {

    @Autowired
    private StringRedisTemplate redisTemplate;
    
    @Test
    public void demo1() {
        //集合中添加元素，返回添加个数
        redisTemplate.opsForSet().add("games", "鬼泣", "古墓丽影", "仙剑奇侠传", "LOL", "DOTA自走棋");
        printSet("games");
    }
    
    @Test
    public void demo2() {
        //从集合中删除指定元素
        redisTemplate.opsForSet().remove("games", "鬼泣");
        printSet("games");
    }
    
    @Test
    public void demo3() {
        //从集合中随机删除一个元素，并返回该元素
        String item = redisTemplate.opsForSet().pop("games");
        System.out.println(item);
        printSet("games");
    }
    
    @Test
    public void demo4() {
        //将集合1中的指定元素移到集合2
        redisTemplate.opsForSet().move("games", "仙剑奇侠传", "chinese-games");
        //打印集合1
        printSet("games");
        //打印集合2
        printSet("chinese-games");
    }
    
    
    
    @Test
    public void demo5() {
        //获得集合大小
        Long size = redisTemplate.opsForSet().size("games");
        printSet("games");
        System.out.println(size);
    }
    
    @Test
    public void demo6() {
        //判断集合中是否存在某元素
        Boolean ifExist = redisTemplate.opsForSet().isMember("games", "LOL");
        System.out.println(ifExist);
    }
    
    @Test
    public void demo7() {
        //添加集合a
        redisTemplate.opsForSet().add("set-a", "a", "b", "c", "d");
        //添加集合b
        redisTemplate.opsForSet().add("set-b", "a", "b");
        //求交集
        Set<String> intersection = redisTemplate.opsForSet().intersect("set-a", "set-b");
        //也可以和多个key对应的集合求交集                  Set<V> intersect(K key, K otherKey);
        System.out.println(intersection);
    }
    
    @Test
    public void demo8() {
        //添加集合a
        redisTemplate.opsForSet().add("set-a", "a", "b", "c", "d");
        //添加集合b
        redisTemplate.opsForSet().add("set-b", "a", "b");
        //求交集并放入集合c
        redisTemplate.opsForSet().intersectAndStore("set-a", "set-b", "set-c");
        //也可以和多个key对应的集合求交集                 Long intersectAndStore(K key, Collection<K> otherKeys, K destKey);
        //打印集合c
        printSet("set-c");
    }
    
    @Test
    public void demo9() {
        //添加集合m
        redisTemplate.opsForSet().add("set-m", "a", "b", "c", "d");
        //添加集合n
        redisTemplate.opsForSet().add("set-n", "c", "d", "e", "f");
        //求并集
        Set<String> union = redisTemplate.opsForSet().union("set-m", "set-n");
        System.out.println(union);
        
        //其他操作与intersect类似 如和多个key求并集，求并集并放入集合等
    }
    
    @Test
    public void demo10() {
        //添加集合d
        redisTemplate.opsForSet().add("set-d", "a", "b", "c", "d");
        //添加集合e
        redisTemplate.opsForSet().add("set-e", "c", "d", "e", "f");
        //添加集合f
        redisTemplate.opsForSet().add("set-f", "e", "f", "g", "h");
        
        //求差集(属于d不属于e和f)
        Set<String> difference = redisTemplate.opsForSet().difference("set-d", Arrays.asList("set-e", "set-f"));
        System.out.println(difference);
        //其他操作与交集并集类似
    }
    
    @Test
    public void demo11() {
        //随机获取集合中的一个元素
        System.out.println(redisTemplate.opsForSet().randomMember("games"));
        
        //随机获取集合中指定个数的元素（有可能重复）
        System.out.println(redisTemplate.opsForSet().randomMembers("games", 10));
        
        //随机获取集合中指定个数的元素（不重复）
        System.out.println(redisTemplate.opsForSet().distinctRandomMembers("games", 10));

    
    }
    
    /**
     *  打印集合
     * @date 2019年2月12日
     * @author baipengfei
     * @param key
     */
    private void printSet(String key) {
        //获取所有成员
        Set<String> members = redisTemplate.opsForSet().members(key);
        System.out.println(members);
    }
}
```

#### 操作zset类型

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ZSetDemo {

    @Autowired
    private StringRedisTemplate redisTemplate;
    
    @Test
    public void test1() {
        //向集合中插入元素，并设置分数
        redisTemplate.opsForZSet().add("ranking-list", "p1", 2.1);
        
        //向集合中插入多个元素
        DefaultTypedTuple<String> tuple1 = new DefaultTypedTuple<String>("p2", 1.1);
        DefaultTypedTuple<String> tuple2 = new DefaultTypedTuple<String>("p3", 3.1);
        redisTemplate.opsForZSet().add("ranking-list", new HashSet<>(Arrays.asList(tuple1, tuple2)));
        
        //打印
        printZSet("ranking-list");
    }
    
    @Test
    public void test2() {
        printZSet("ranking-list");
        //从集合中删除指定元素
        redisTemplate.opsForZSet().remove("ranking-list", "p1");
        printZSet("ranking-list");
    }
    
    @Test
    public void test3() {
        //为指定元素加分
        Double score = redisTemplate.opsForZSet().incrementScore("ranking-list", "p1", 2);
        System.out.println(score);//返回加分后的得分
        printZSet("ranking-list");
    }
    
    @Test
    public void test4() {
        //返回指定成员的排名（从小到大）
        Long rank = redisTemplate.opsForZSet().rank("ranking-list", "p1");
        //从大到小
        Long reverseRank = redisTemplate.opsForZSet().reverseRank("ranking-list", "p1");
        System.out.println(rank);
        System.out.println(reverseRank);
    }
    
    @Test
    public void test5() {
        //返回集合内元素的排名，以及分数（从小到大）
        Set<TypedTuple<String>> tuples = redisTemplate.opsForZSet().rangeWithScores("ranking-list", 0, -1);
        for (TypedTuple<String> tuple : tuples) {
            System.out.println(tuple.getValue() + " : " + tuple.getScore());
        }
    }
    
    @Test
    public void test6() {
        //返回集合内元素在指定分数范围内的排名（从小到大）
        Set<String> ranking = redisTemplate.opsForZSet().rangeByScore("ranking-list", 0, 5);
        System.out.println(ranking);
        //带偏移量和个数，下例意为从第二个开始，要三个
        Set<String> ranking2 = redisTemplate.opsForZSet().rangeByScore("ranking-list", 0, 5, 1, 3);
        System.out.println(ranking2);
        //也可以带分数返回，类似于test5
    }
    
    @Test
    public void test7() {
        //返回集合内指定分数范围的成员个数
        Long count = redisTemplate.opsForZSet().count("ranking-list", 0, 2);
        System.out.println(count);
        //返回集合内的成员个数
        Long size = redisTemplate.opsForZSet().size("ranking-list");//等同于zCard(key);
        System.out.println(size);
    }
    
    @Test
    public void test8() {
        //获得指定元素的分数
        Double score = redisTemplate.opsForZSet().score("ranking-list", "p1");
        System.out.println(score);
    }
    
    @Test 
    public void test9() {
        //删除指定索引范围的元素
        printZSet("ranking-list");
        redisTemplate.opsForZSet().removeRange("ranking-list", 0, 0);
        printZSet("ranking-list");
    }
    
    @Test 
    public void test10() {
        //删除指定分数范围内的元素
        printZSet("ranking-list");
        redisTemplate.opsForZSet().removeRangeByScore("ranking-list", 4, 5);
        printZSet("ranking-list");
        redisTemplate.opsForZSet();
    }
    
    //求交集并集与set类似
    
    
    private void printZSet(String key) {
        //按照排名先后(从小到大)打印指定区间内的元素, -1为打印全部
        Set<String> range = redisTemplate.opsForZSet().range(key, 0, -1);
        //reverseRange 从大到小 
        System.out.println(range);
    }
    
}
```











