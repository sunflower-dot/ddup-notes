## 简述
当在定义类的时候，类中都会有相应的属性和方法。而属性和方法都是通过创建本类对象调用的。当在调用对象的某个方法时，这个方法没有访问到对象的特有数据时，方法创建这个对象有些多余。可是不创建对象，方法又调用不了，这时就会想，那么我们能不能不创建对象，就可以调用方法呢？可以的，我们可以通过static关键字来实现。

static 表示“静态”的意思，用来修饰成员变量和成员方法，也可以形成静态 static 代码块。


## 静态的特点

- 被static修饰的成员变量属于类，不属于这个类的某个对象。（也就是说，多个对象在访问或修改static修饰的成员变量时，其中一个对象将static成员变量值进行了修改，其他对象中的static成员变量值跟着改变，即多个对象共享同一个static成员变量）
- 被static修饰的成员可以并且建议通过类名直接访问。访问静态成员的格式:
> 类名.静态成员变量名
> 类名.静态成员方法名(参数)

- 静态的加载优先于对象,随着类的加载而加载
- 
## 静态的注意事项

- 静态成员只能直接访问静态成员
- 非静态成员既可以访问非静态成员也可以访问静态成员

```java
package com.example;
/*
 * static的注意事项：
 * 			静态方法：
 * 				可以调用静态的成员变量
 * 				可以调用静态的成员方法
 * 				不可以调用非静态成员变量
 * 				不可以调用非静态成员方法
 * 				静态方法只能调用静态的成员
 * 			非静态方法：
 * 				可以调用静态的成员变量
 * 				可以调用静态的成员方法
 * 				可以调用非静态的成员变量
 * 				可以调用非静态的成员方法
 * 		
 * 	静态的方法中是否有this这个对象？没有的
 * 				
 * 
 */
public class StaticDemo2 {
	public static void main(String[] args) {
		Student.graduateFrom = "黄淮学院";
		Student.study();
	}
}


class Student {
	String name;
	int age;
	static String graduateFrom;//毕业院校
	
	public static void study() {
		///System.out.println(graduateFrom);
		//sleep();
		
		//System.out.println(name);
		
		//eat();
		
	}
	
	public static void sleep() {
		System.out.println("sleep");
		
	}
	
	public void eat() {
		System.out.println("eat");
		
		System.out.println(graduateFrom);
		sleep();
		
		
	}
	
}

```

## static修饰符的使用

### 静态变量

- 我们用 static 表示变量的级别，一个类中的静态变量，不属于类的对象或者实例。因为静态变量与所有的对象实例共享，因此他们不具线程安全性。
- 通常，静态变量常用 final 关键来修饰，表示通用资源或可以被所有的对象所使用。如果静态变量未被私有化，可以用“类名.变量名”的方式来使用。

```java
	//static variable example
    private static int count;
    public static String str;
    public static final String DB_USER = "myuser";
```



### 静态方法

- 与静态变量一样，静态方法是属于类而不是实例。
- 一个静态方法只能使用静态变量和调用静态方法。通常静态方法通常用于想给其他的类使用而不需要创建实例。例如：Collections class(类集合)。

Java的包装类和实用类包含许多静态方法。main()方法就是Java程序入口点，是静态方法。

```java
	//static method example
    public static void setCount(int count) {
        if(count > 0){
            StaticExample.count = count;
        }
    }
    
    //static util method
    public static int addInts(int i, int...js){
        int sum=i;
        for(int x : js) sum+=x;
        return sum;
    }

```



### 静态代码块

- Java 的静态块是一组指令在类装载的时候在内存中由 Java ClassLoader 执行。
- 静态块常用于初始化类的静态变量。大多时候还用于在类装载时候创建静态资源。
- Java 不允许在静态块中使用非静态变量。一个类中可以有多个静态块，尽管这似乎没有什么用。静态块只在类装载入内存时，执行一次。

```java
	static{
        //can be used to initialize resources when class is loaded
        System.out.println("StaticExample static block");
        //can access only static variables and methods
        str="Test";
        setCount(2);
    }
```



### 静态类

Java可以嵌套使用静态类，但是静态类不能用于嵌套的顶层。

静态嵌套类的使用与其他顶层类一样，嵌套只是为了便于项目打包。

```java
public class StaticExample {

    //static class example - used for packaging convenience only
    public static class MyStaticClass{
        public int count;
        
    }

}
```

### 静态import

一般，Java允许用静态成员使用类引用，从Java1.5开始，我们可以使用静态import而不用类引用。下面是一个简单的静态import的例子。

```java
package com.journaldev.test;

public class A {

	public static int MAX = 1000;
	
	public static void foo(){
		System.out.println("foo static method");
	}
}
```



```java
package com.journaldev.test;

import static com.journaldev.test.A.MAX;
import static com.journaldev.test.A.foo;

public class B {

	public static void main(String args[]){
		System.out.println(MAX); //normally A.MAX
		foo(); // normally A.foo()
	}
}
```



## 静态的优缺点

- 静态优点:
1. 对对象的共享数据提供单独空间的存储，节省空间，没有必要每一个对象都存储一份
1. 可以直接被类名调用,不用在堆内存创建对象
1. 静态成员可以通过类名直接访问,相对创建对象访问成员方便
- 静态弊端:

访问出现局限性。（静态虽好，但只能访问静态）



## 类变量与实例变量辨析

- 类变量，其实就是静态变量

  > 定义位置：定义在类中方法外
  > 所在内存区域：方法区
  > **生命周期：随着类的加载而加载**
  > 特点：无论创建多少对象,类变量仅在方法区中,并且只有一份

- 实例变量，其实就是非静态变量

  > 定义位置：定义在类中方法外
  > 所在内存区域：堆
  > **生命周期：随着对象的创建而加载**
  > 特点：每创建一个对象,堆中的对象中就有一份实例变量


