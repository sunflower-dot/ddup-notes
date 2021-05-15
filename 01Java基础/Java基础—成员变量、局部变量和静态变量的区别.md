> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/haovip123/article/details/43883109)



之前在刚开始学习 Java 的时候，就谈了谈 [Java 基础中的变量](http://blog.csdn.net/haovip123/article/details/42890579)，虽然知道这货以后会经常用到，但没想到了基本语法这里，竟然有冒出来了成员变量、局部变量和静态变量。变来变去太容易让人搞晕了，挑拣出来梳理一下喽！


要想区分这哥仨，首先，我们得知道它们分别是什么。先从成员变量下刀。


## **成员变量**


我们来研究一个事物：


属性：外在特征；例如人的身高，体重


行为：能够做什么；例如人有说话，打球等行为。


而在 Java 语言中，最基本的单位是类，类就是用来体现事物的。


用类 class 来描述事物也是如此：


属性：对应类中的成员变量


行为：对应类中的成员函数


定义类其实就是在定义类中的成员（成员变量和成员函数）


拓展：类是一个抽象的概念，而对象就是类的具体的存在，体现。例如：生活中的汽车，可以看做一个类，我们称之为汽车类，每一台车都有颜色和轮胎数（可以定义为属性，即成员变量），每一台车都可以跑（即车的行为，对应类总的成员函数），我们把汽车实例化一下，即会产生一个对象，比如奔驰、宝马（BMW）。


Demo1：


```
public class Car {
         private String color; // 定义汽车颜色，全局变量
         private int numLuntai; // 定义汽车轮胎数，全局变量
 
         public Car(String color, int numLuntai){
                   super();
                   this.color = color;
                   this.numLuntai = numLuntai;
         }
        public void run() {
                   System.out.println(this.numLuntai+ "个轮子的" +this.color + "轿车在马路上行驶着");
         }
}
```


```
public class ClassTest {
         public static void main(String[] args){
                   Car bmw = new Car("黑色", 4);      // 创建一个轿车对象，名字为bmw
                   bmw.run();
         }
}
```


运行结果：


4 个轮子的黑色轿车在马路上行驶着


其中 color 和 numLuntai 就称为 Car 类的**成员变量**，这个属性能够用来描述一个类的属性，否则，它就应该定义为**局部变量**。


例如一个 for 循环中的 i 即是局部变量。


```
                   for(int i = 0; i < args.length; i++) {
                           ......
                   }
```


又例如写在成员方法中变量是局部变量。


```
publicclass Car {
         private String color; // 定义汽车颜色，全局变量
         private int numLuntai; // 定义汽车轮胎数，全局变量
         public Car(String color, int numLuntai){
                   super();
                   this.color = color;
                   this.numLuntai = numLuntai;
         }
public void run() {
                   String carName=”宝马牌”;    //这个就是局部变量
                   System.out.println(this.numLuntai+ "个轮子的" +this.color + carName+"轿车在马路上行驶着");
         }
}
publicclass ClassTest {
         public static void main(String[] args){
                   Car bmw = new Car("黑色", 4);      // 创建一个轿车对象，名字为bmw
                   bmw.run();
         }
}
```


结果：


4 个轮子的黑色宝马牌轿车在马路上行驶着


## **成员变量和局部变量的区别**


成员变量：


①成员变量定义在类中，在整个类中都可以被访问。


②成员变量随着对象的建立而建立，随着对象的消失而消失，存在于对象所在的堆内存中。


③成员变量有默认初始化值。


局部变量：


①局部变量只定义在局部范围内，如：函数内，语句内等，只在所属的区域有效。


②局部变量存在于栈内存中，作用的范围结束，变量空间会自动释放。


③局部变量没有默认初始化值


在使用变量时需要遵循的原则为：就近原则


首先在局部范围找，有就使用；接着在成员位置找。


## **静态变量**


由 static 修饰的变量称为静态变量，其实质上就是一个全局变量。如果某个内容是被所有对象所共享，那么该内容就应该用静态修饰；没有被静态修饰的内容，其实是属于对象的特殊描述。


示例：


```
class Person{
       //成员变量，实例变量
       String name;
       //静态变量，类变量
       //所有对象共享的属性用static修饰
       static String country = "CN";
       public void show(){
            System. out.println(country + ":" + name);
             //等效语句：System.out.println(Person.country+ ":" + this.name);
      }
}
class StaticDemo{
       public static void main(String[]args){
            Personp = new Person();
            System. out.println(p.country);
             //可以用类名直接调用
            System. out.println(Person.country);
      }
}
```


结果：


CN


CN


## **成员变量和静态变量的区别**


1、两个变量的生命周期不同


成员变量随着对象的创建而存在，随着对象被回收而释放。


静态变量随着类的加载而存在，随着类的消失而消失。


2、调用方式不同


成员变量只能被对象调用。


静态变量可以被对象调用，还可以被类名调用。


3、别名不同


成员变量也称为实例变量。


静态变量也称为类变量。


4、数据存储位置不同


成员变量存储在堆内存的对象中，所以也叫对象的特有数据。


静态变量数据存储在方法区（共享数据区）的静态区，所以也叫对象的共享数据。


## **列表对比：**


成员变量、局部变量、静态变量的区别


|  |


**成员变量**


|


**局部变量**


|


**静态变量**


|
|


定义位置


|


在类中, 方法外


|


方法中, 或者方法的形式参数


|


在类中, 方法外


|
|


初始化值


|


有默认初始化值


|


无, 先定义, 赋值后才能使用


|


有默认初始化值


|
|


调用方式


|


对象调用


|

---

|


对象调用，类名调用


|
|


存储位置


|


堆中


|


栈中


|


方法区


|
|


生命周期


|


与对象共存亡


|


与方法共存亡


|


与类共存亡


|
|


别名


|


实例变量


|

---

|


类变量


|


## 小结：


尽管三者的本质都是变量，可是使用时却有相当大的区别，稍不留神就可能陷入陷阱。且先记住：在一个类中，如果一个变量能够用来描述一个类的属性，那就定义为成员变量，否则，它就应该定义为局部变量。而如果一个变量在全局中都能使用（某个内容是被所有对象所共享），那么我们就可以把这个变量用 static 来修饰，即为静态变量。（还要注意它与静态方法的紧密联系，这里且不细述了）
