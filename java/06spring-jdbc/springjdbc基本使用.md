## 认识 spring jdbc
JDBC已经能够满足大部分用户最基本的需求，但是在使用JDBC时，必须自己来管理数据库资源如：获取PreparedStatement，设置SQL语句参数，关闭连接等步骤。

Spring对数据库的操作在jdbc上面做了深层次的封装，使用spring的注入功能，可以把DataSource注册到JdbcTemplate之中。

JdbcTemplate是Spring的一部分。JdbcTemplate处理了资源的建立和释放。他帮助我们避免一些常见的错误，比如忘了总要关闭连接。他运行核心的JDBC工作流，如Statement的建立和执行，而我们只需要提供SQL语句和提取结果。

JdbcTemplate 位于spring-jdbc 中。其全限定命名为 org.springframework.jdbc.core.JdbcTemplate 。要使用 JdbcTemlate  还需一个 spring-tx 这个包包含了一下事务和异常控制。

`JdbcTemplate`主要提供以下五类方法：

 - `execute`方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
 - `update`方法及`batchUpdate`方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
 - `query`方法及`queryForXXX`方法：用于执行查询相关语句；
 - `call`方法：用于执行存储过程、函数相关语句。

## 在spring中使用JdbcTemplate

**配置 Spring 配置文件 application.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">

<!--    配置DataSource-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="org.postgresql.Driver"/>
        <property name="url" value="jdbc:postgresql://localhost:5432/spring-jdbc?currentSchema=spring-jdbc"/>
        <property name="username" value="postgres"/>
        <property name="password" value="aaaaaa"/>
    </bean>

<!--    配置 JdbcTemplate-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

</beans>
```

通常将数据库的配置信息单独放到一个文件中，这样也为了方便后期维护:

在 src 下面新建一个属性配置文件 db.properties :

```properties
jdbc.driverClass=com.postgres.Driver
jdbc.jdbcUrl=jdbc:postgresql://localhost:5432/spring-jdbc?currentSchema=spring-jdbc
jdbc.user=postgrea
jdbc.password=aaaaaa
```

修改 application.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--    配置DataSource-->
<!--    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">-->
<!--        <property name="driverClassName" value="org.postgresql.Driver"/>-->
<!--        <property name="url" value="jdbc:postgresql://localhost:5432/spring-jdbc?currentSchema=spring-jdbc"/>-->
<!--        <property name="username" value="postgres"/>-->
<!--        <property name="password" value="aaaaaa"/>-->
<!--    </bean>-->
  
<!-- 在Spring的配置文件中引入属性文件-->
<!--    <bean class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">-->
<!--        <property name="location" value="classpath:db.properties"/>-->
<!--    </bean>-->
    <context:property-placeholder location="db.properties"/>
    
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
        <property name="driverClassName" value="${jdbc.driverClass}" />
        <property name="url" value="${jdbc.jdbcUrl}" />
        <property name="username" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
    </bean>

<!--    配置 JdbcTemplate-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

<!--    声明bean-->
    <bean id="userDao" class="spring.jdbc.demo.UserDaoImpl">
        <constructor-arg ref="jdbcTemplate"/>
    </bean>

</beans>
```

## 在springboot中引入spring jdbc

数据库配置：

```properties
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/s_springjdbc?currentSchema=public
spring.datasource.username=postgres
spring.datasource.password=aaaaaa

```



添加依赖：

```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
  </dependency>

```



## JdbcTemplate 操作数据库

### execute 新建删除表
代码示例：
注入`JdbcTemplate`
```java
    @Autowired
    JdbcTemplate jdbcTemplate;
```
创建表：   
 ```java
    /**
     * 创建表
     */
    @Test
    void testExecute() {
        String createTableSql = "create table customer ( " +
                "id varchar(36) primary key, " +
                "name varchar(36), " +
                "age int," +
                "phone varchar(11) " +
                ")";
        jdbcTemplate.execute(createTableSql);
        System.out.println("表创建成功！");
    }
 ```

删除表：
```java
    /**
     * 删除表
     */
    @Test
    void testExecute2(){
      String dropTableSql="drop table customer";
      jdbcTemplate.execute(dropTableSql);
      System.out.println("删除表成功！");
    }
```
执行操作后，customer数据表被删除。
备注：若表不存在，将会抛出异常：org.springframework.jdbc.BadSqlGrammarException: StatementCallback; bad SQL grammar [drop table XXX]; nested exception is org.postgresql.util.PSQLException: ERROR: table "cc" does not exist。

### update执行数据增删改
 新增数据:
```java
    /**
     * 插入数据，不带参数
     */
    @Test
    void testUpdateInsert(){
        String sql = "insert into customer (id,name,age,phone) values ('001','客户李白',22,'10086')";
        int num = jdbcTemplate.update(sql);
        System.out.println("执行插入了"+num+"条新数据！");
    }

    /**
     * 插入数据，带参数
     */
    @Test
    void testUpdateInsert2(){
        String sql = "insert into customer (id,name,age,phone) values (?,?,?,?)";
        List<Object> params = new ArrayList<>();
        params.add("002");
        params.add("客户赵云");
        params.add(21);
        params.add("10010");
        int num = jdbcTemplate.update(sql,params.toArray());
        System.out.println("执行插入了"+num+"条新数据！");
    }
    /**
     * 插入数据，带参数
     */
    @Test
    void testUpdateInsert3(){
        String sql = "insert into customer (id,name,age,phone) values (?,?,?,?)";
        int num = jdbcTemplate.update(sql,"003","客户张飞",22,"10011");
        System.out.println("执行插入了"+num+"条新数据！");
    }
```
更新数据：
```java
    /**
     * 更新数据，不带参数
     */
    @Test
    void testUpdateUpdate1(){
        String sql = "update customer set name='客户李白加强' where id='001' ";
        int num = jdbcTemplate.update(sql);
        System.out.println("更新了"+num+"条数据！");
    }
    /**
     * 更新数据，带参数
     */
    @Test
    void testUpdateUpdate2(){
        String sql = "update customer set name=? where id=? ";
        int num = jdbcTemplate.update(sql,new Object[]{"客户赵云加强","002"});
        System.out.println("更新了"+num+"条数据！");
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200217144333753.png)
删除数据：

```java
    @Test
    void testUpdateDelete1(){
        String sql = "delete from customer where id='003'";
        int count = jdbcTemplate.update(sql);
        System.out.println("删除了"+count+"条数据！");
    }
```
### batchUpdate批量增删改
```java
    /**
     * 新增数据，批量
     */
    @Test
    void testBatchUpdate(){
        String sql1 = "insert into customer (id,name,age,phone) values ('004','客户鲁班',22,'12687')";
        String sql2 = "insert into customer (id,name,age,phone) values ('005','客户虞姬',22,'12689')";
        String sql3 = "insert into customer (id,name,age,phone) values ('006','客户后裔',22,'12688')";
        int[] counts = jdbcTemplate.batchUpdate(sql1, sql2, sql3);
        int sum=0;
        for (int i=0;i<counts.length;i++) {
            sum+=counts[i];
        }
        System.out.println("共插入了"+sum+"条数据！");
    }

    /**
     * 更新数据
     */
    @Test
    void testBatchUpdate2(){
        String sql="update customer set phone =? where age=?";
        Object[] batchArgs1 = new Object[]{"10088",22};
        Object[] batchArgs2 = new Object[]{"10077",21};
        Object[] batchArgs3 = new Object[]{"10066",20};
        List<Object[]> argsList = new ArrayList<>();
        argsList.add(batchArgs1);
        argsList.add(batchArgs2);
        argsList.add(batchArgs3);
        int[] counts = jdbcTemplate.batchUpdate(sql, argsList);
        int sum=0;
        for (int i=0;i<counts.length;i++) {
            sum+=counts[i];
        }
        System.out.println("共更新了"+sum+"条数据！");
    }
```

### queryForList
```java
    /**
     * 查询数据
     */
    @Test
    void testQueryForList(){
        String sql="select * from customer";
        List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
        System.out.println("数据库查询结果：");
        list.forEach(stringObjectMap -> {
            System.out.println(stringObjectMap.toString());
        });
    }
    
    /**
     * 查询数据
     */
    @Test
    void testQueryForList2(){
        String sql="select * from customer where age>? and name like ?";
        List<Map<String, Object>> list = jdbcTemplate.queryForList(sql,21,"客户%");
        System.out.println("数据库查询结果：");
        list.forEach(stringObjectMap -> {
            System.out.println(stringObjectMap.toString());
        });
    }
```
### queryForMap
```java
    /**
     * 数据查询
     */
    @Test
    void testQueryForMap(){
        String sql ="select * from customer where id=?";
        Map<String, Object> map = jdbcTemplate.queryForMap(sql,"001");
        System.out.println("数据库查询结果：");
        System.out.println(map);
    }
    
```
### queryForObject
```java
    /**
     * 数据查询,queryForObject返回单个实体
     */
    @Test
    void testQueryForObject(){
        String sql = "select * from customer where id=?";
        RowMapper<Customer> rowMapper = new BeanPropertyRowMapper(Customer.class);
        Customer customer = jdbcTemplate.queryForObject(sql,rowMapper,"001");
        System.out.println("数据库查询结果：");
        System.out.println(customer);
    }
```
### query

query 可以完成以上全部

#### 读取到单对象

```java
String sql="select id,name,deptid from user where id=?";

RowMapper<User> rowMapper=new BeanPropertyRowMapper<User>(User.class);
User user= jdbcTemplate.queryForObject(sql, rowMapper,52);
System.out.println(user);
```

> 【注意】：
>
> 1、使用 `BeanProperytRowMapper` 要求 sql 数据查询出来的列和实体属性需要一一对应。如果数据中列明和属性名不一致，在 sql 语句中需要用 as 重新取一个别名
>
> 2、使用 JdbcTemplate 对象不能获取关联对象

#### 读取多个对象

```java
String sql="select id,name,deptid from user";

RowMapper<User> rowMapper=new BeanPropertyRowMapper<User>(User.class);
List<User> users= jdbcTemplate.query(sql, rowMapper);
for (User user : users) {
    System.out.println(user);
}
```

### 获取某个记录某列或者 count、avg、sum 等函数返回唯一值

```java
String sql="select count(*) from user";
 
int count= jdbcTemplate.queryForObject(sql, Integer.class);
System.out.println(count);
```

