`Spring`  引入外部配置文件
----------

## 基于XML引入外部配置文件

#### 直接配置数据库连接信息

```xml
<!--直接配置连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
 <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
 <property name="url" 
value="jdbc:mysql://localhost:3306/userDb?characterEncoding=utf-8"></property>
 <property name="username" value="root"></property>
 <property name="password" value="root"></property>
</bean>
```



#### 使用外部配置文件配置数据库信息

创建外部属性配置文件：`classpath:db.properties`:

```properties
db.driverclass=com.mysql.jdbc.Driver
db.url=jdbc:mysql://localhost:3306/userDb?characterEncoding=utf-8
db.username=root
db.password=root
```

把外部 properties 属性文件引入到 spring 配置文件中:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                     http://www.springframework.org/schema/beans/spring-beans.xsd
                     http://www.springframework.org/schema/util
                     http://www.springframework.org/schema/util/spring-util.xsd
                     http://www.springframework.org/schema/context
                     http://www.springframework.org/schema/context/spring-context.xsd">
     
    <!--引入外部属性文件方式一：使用PropertyPlaceholderConfigurer -->
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
         <property name="db-connection" value="db.properties"></property>
	</bean>

    <!-- 引入外部属性文件方法二: 要引入context命名空间-->
    <!-- <context:property-placeholder location="classpath:db.properties"/>-->

    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
         <property name="driverClassName" value="${db.driverclass}"></property>
         <property name="url" value="${db.url}"></property>
         <property name="username" value="${db.username}"></property>
         <property name="password" value="${db.password}"></property>
    </bean>
</beans>

```

## 基于注解`@PropertySource`引入配置文件

### 创建配置文件

创建属性配置文件 `classpath:person.properties`:

```properties
person.name=zhaoyun
person.age= 220
person.mobile=10010
```

### 在Java 配置类中引入配置文件

```java
package com.haan.springdemo.annotation.propertiesassigning;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.ConfigurableEnvironment;

import java.util.Map;

@Configuration
@PropertySource(value = {"classpath:/person.properties"})
public class MyApplication {
    
    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyApplication.class);

//        获取环境变量中配置文件中的key/value
        System.out.println("读取配置文件导入的环境变量key/value=======");
        ConfigurableEnvironment environment = applicationContext.getEnvironment();

        String personName = environment.getProperty("person.name");
        System.out.println(personName);
        String personAge = environment.getProperty("person.age");
        System.out.println(personAge);
        String personMobile = environment.getProperty("person.mobile");
        System.out.println(personMobile);

    }
}

```

