## 简述

- final 是 Java 中的一个关键字，它所表示的是“这部分是无法修改的”。
- final 关键字可以用来修饰引用、方法和类。

## 修饰变量

- 如果引用为基本数据类型，则该引用为常量，该值无法修改；

- 如果引用为引用数据类型，比如对象、数组，则该对象、数组本身可以修改，但指向该对象或数组的地址的引用不能修改。

- final修饰的变量，则必须当场赋值，否则编译会报错。

例子如下所示：


```javascript
final class Person {
    String name ="zs";
    //3. 此处不赋值会报错
    //final int age;
    final int age = 10;  
}
public class Demo01 {
    public static void main(String[] args) {
        //1. 基本数组类型为常量，无法修改
        final int i = 9;
        //i = 10;
       
        //2. 地址的引用不能修改，但是对象本身的属性可以修改
        Person p = new Person();
        p.name = "lisi";
        final int[] arr = {1,2,3,45};
        arr[3] = 999;
        //arr = new int[]{1,4,56,78};
    }
}
```


## 修饰方法

当使用 final 修饰方法时，这个方法将成为最终方法，无法被子类重写。但是，该方法仍然可以被继承。

```java
class Person {
    public final void say() {
        System.out.println("说....");
    }
    public void eat() {
        System.out.println("吃...");
    }
}
class Teacher extends Person {
    //1. final修饰的方法不能被重写，但此方法仍然被继承
    /*@Override
    public void say() {
        System.out.println("老师在一本正经的说...");
    }*/
    
}

public class Demo02 {
    public static void main(String[] args) {
        Teacher t = new Teacher();
        t.say();
    }    
}

```


## 修饰类

当用 final 修改类时，该类成为最终类，无法被继承。简称为 “断子绝孙类”。


```
/***
 * final用法3：修饰类，则该类成为最终类，无法被继承
 * @author Administrator
 *
 */
final class Person02 {    }
//class Teacher02 extends Person02 { }
//class MyString extends String{}
public class Demo03 {

}
```


比如常用的 String 类就是最终类。
