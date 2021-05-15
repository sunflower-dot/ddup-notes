## 注解(Annotation)概述
（1）注解起到标识做用，比如Junit的@Test注解。
Junit会在运行时检查方法上是否存在此注解，如果存在，就通过反射来运行你的方法。注意标红的反射两个字，**反射在注解里相当重要，写完你的自定义注解类后没啥用，必须要用反射才能让它动起来**！
（2）从 JDK 5.0 开始，Java 增加了对 Annotation(注解)的支持。
（3）注解其实就是代码里的特殊标记，它**可以用于替代配置文件**，也就是说，传统方式通过配置文件告诉类如何运行，有了注解技术后，开发人员可以通过注解告诉类如何运行。用了注解以后，可以减少项目的配置文件，使代码看起来更优雅。**在Java技术里注解的典型应用是：可以通过反射技术去得到类里面的注解，以决定怎么去运行类。**


## 掌握注解技术的要点

- 如何定义注解
- 如何反射注解，并根据反射的注解信息，决定如何去运行类



## 如何定义注解(Annotation)
### 定义注解@interface
在java中使用关键字@interface定义一个注解接口。
```java
public @interface MyAnnotation{ }
```
### 定义注解中的属性
基本形式：类型 属性名称();    
```java
public @interface MyAnnotation {
    String name();
}
```
注解属性定义完以后，使用：`@MyAnnotation(name="hello")`
**特别注意：注解的属性的类型只能是：基本类型、String、Class、枚举、注解类型及以上类型的一维数组。**
### 定义注解属性的默认值
类型 属性名称() default 默认值;
```java
public @interface MyAnnotation {
    String name() default "hello";
}
```
### 注解中的特殊属性
（1）特殊属性`value`
如果注解中只有一个属性，而且这个属性的名称是`value`的话，那么使用注解时可以省略value=部分，可以直接写成这样`@MyAnnotation("xxx")`；


（2）特殊类型`[] value()`
如果注解中只有一个属性，而且这个属性名称是`value`且数据类型是数组，那么使用下面四种方式都ok，根据情况而定：
`@MyAnnotationDemo1(value={"a","b"})`
`@MyAnnotationDemo1({"a","b"})`
`@MyAnnotationDemo1({"a"})`
`@MyAnnotationDemo1("a")`


## 初步测试


（1）声明一个注解`MyAnnotation` :
```java
public @interface MyAnnotation01 {
}
```
（2）声明一个测试类，在测试类上的一个方法上添加注解
```java
public class AnnotationTest01 {

    @MyAnnotation01
    public void test1(){
        System.out.println("test1执行了");
    }

    public void test2(){
        System.out.println("test2执行了");
    }

}

```
（3）程序入口
```java
public class Test {
    public static void main(String[] args) throws InvocationTargetException, IllegalAccessException, InstantiationException {
        Class<AnnotationTest01> test01Class = AnnotationTest01.class;
        //获取AnnotationTest01中的多个方法
        Method[] methods = test01Class.getMethods();
        for (Method method:methods){
            System.out.println("method:"+method.getName());
            //方法添加了 @MyAnnotation01 注解
            if (method.isAnnotationPresent(MyAnnotation01.class)) {
                //执行
                method.invoke(test01Class.newInstance(),null);
            }
        }
    }
}
```
（4）执行结果
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1039463/1594459251971-6cc5bb81-3b60-48ea-a330-4c3fa76232ea.png#align=left&display=inline&height=349&margin=%5Bobject%20Object%5D&name=image.png&originHeight=341&originWidth=959&size=45023&status=done&style=none&width=981)
正常情况下，正确的执行效果是：test1方法执行了，test2方法没执行.

但是，从结果可以看出和咱们预期的并不一样，那到底怎么回事呢？原因是：定义注解的时候除了上边说的语法，还需要一个东西，那就是**“元注解”。**
**
## 元注解
什么是元注解呢？简单的来说服务于注解的注解就是元注解，它主要用来标识你写的注解保留范围（作用范围）以及出现的位置。
JDK中定义了四种元注解

- @Retention 注解的保留范围
- @Target 注解出现的位置
- @Documented: 用于指定被该元 Annotation 修饰的 Annotation 类将被 javadoc 工具提取成文档.
- @Inherited: 被它修饰的 Annotation 将具有继承性



### @Retention
`Retention`注解有一个属性`value`，是`RetentionPolicy`类型的，`Enum RetentionPolicy`是一个枚举类型，这个枚举决定了`Retention`注解应该如何去保持，也可理解为`Rentention `搭配 `RententionPolicy`使用。

-  RetentionPolicy.SOURCE：表示注解的信息会被编译器抛弃，不会留在class文件中，注解的信息只会留在源文件中；
-  RetentionPolicy.CLASS：表示注解的信息被保留在class文件(字节码文件)中当程序编译时，但不会被虚拟机读取在运行的时候；
-  RetentionPolicy.RUNTIME：表示注解的信息被保留在class文件(字节码文件)中当程序编译时，会被虚拟机保留在运行时



所以他们可以用反射的方式读取。**RetentionPolicy.RUNTIME 可以让你从JVM中读取Annotation注解的信息，以便在分析程序的时候使用.**
### @Target
@Target注解控制了注解能够被使用的位置(比如字段上、方法上等)，也是个枚举，有如下可选值

- ElementType.TYPE
- **ElementType.FIELD：表示该注解只能用来修饰在字段属性上**
- **ElementType.METHOD：表示该注解只能用来修饰在方法上**
- ElementType.PARAMETER
- ElementType.CONSTRUCTOR
- ElementType.LOCAL_VARIABLE
- ElementType.ANNOTATION_TYPE
- ElementType.PACKAGE
- ElementType.TYPE_PARAMETER
- ElementType.TYPE_USE
### @Documented
@Documented用于指定被该元 Annotation 修饰的 Annotation 类将被 javadoc 工具提取成文档.
### @Inherited
@Inherited表明被它修饰的 Annotation 将具有继承性.如果某个类使用了被 @Inherited 修饰的 Annotation, 则其子类将自动具有该注解


了解了元注解后，下面把咱们的代码加上元注解再试一下：

修改`MyAnnotation01`注解：
```java
@Retention(value = RetentionPolicy.RUNTIME)
@Target(value = ElementType.METHOD)
@Inherited
public @interface MyAnnotation01 {

}
```
再次执行`Test` :
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1039463/1594460346301-cfaf0cf8-458e-47d0-8df5-133a4ba24796.png#align=left&display=inline&height=381&margin=%5Bobject%20Object%5D&name=image.png&originHeight=366&originWidth=942&size=46743&status=done&style=none&width=981)
发现test1方法执行了， test2方法没执行，和预期的一样。
