# 排序

## Comparable

Comparable表示可被排序的，实现该接口的类的对象自动拥有排序功能。

Comparable的中文意思就是可被排序的，代表本身支持排序功能。只要我们的类实现了这个接口，那么这个类的对象就会自动拥有了可被排序的能力。而且这个排序被称为类的**自然顺序**。这个类的对象的列表可以被`Collections.sort` 和 `Arrays.sort` 来执行排序。同时这个类的实例具备作为`sorted map ` 的key和 `sorted set `的元素的资格。

建议（虽然不是必需的）最好使自然排序与 equals 一致。这是因为在使用自然排序与 equals 不一致的元素（或键）时，没有显式比较器的有序集合（和有序映射表）行为表现“怪异”。尤其是，这样的有序集合（或有序映射表）违背了根据 equals 方法定义的集合（或映射表）的常规协定。

例如，如果将两个键 a 和 b 添加到没有使用显式比较器的有序集合中，使 (!a.equals(b) && a.compareTo(b) == 0)，那么第二个 add 操作将返回 false（有序集合的大小没有增加），因为从有序集合的角度来看，a 和 b 是相等的。

实际上，所有实现 Comparable 的 Java 核心类都具有与 equals 一致的自然排序。java.math.BigDecimal 是个例外，它的自然排序将值相等但精确度不同的 BigDecimal 对象（比如 4.0 和 4.00）视为相等。

### 源码

```java
public interface Comparable<T> {
    public int compareTo(T o);
}
```

该接口只有一个抽象方法 `compareTo`，这个方法主要就是为了定义我们的类所要排序的方式。`compareTo`方法用于比较当前元素a与指定元素b，结果为 int 值:

- 如果a > b，int>0；
- 如果a=b，int=0；
- 如果a<b，int<0。

### 需求

1. 定义一个学生类Student，具有年龄age和姓名username两个属性，并通过Comparable接口提供比较规则.
2. 定义测试类Test，在测试类Test中定义测试方法Comparable getMax(Comparable c1,Comparable c2)完成测试。

```java
package it.hanliukui;

/**
 * @Author hanliukui
 * @Date 2021/11/21 13:37
 * @Description xxx
 */
public class ComparableTest {

    public static void main(String[] args) {
        // 测试
        Student a = new Student("张三",22);
        Student b = new Student("李四",23);
        Comparable max = getMax(a, b);
        System.out.println(max);
    }


    /**
     * 获取两个元素中最大的那个元素
     * @param a 元素a
     * @param b 元素b
     * @return
     */
    private static Comparable getMax(Comparable a,Comparable b){
        int i = a.compareTo(b);
        if (i>0){
            return a;
        }
        return b;
    }
}

class Student implements Comparable<Student>{
    private String username;
    private int age;

    public Student() {
    }

    public Student(String username, int age) {
        this.username = username;
        this.age = age;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "username='" + username + '\'' +
                ", age=" + age +
                '}';
    }

    // 定义比较规则
    @Override
    public int compareTo(Student o) {
        return this.getAge() - o.getAge();
    }
}

```



## 冒泡排序

冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。

走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小（大）的元素会经由交换慢慢“浮”到数列的顶端。 

### 算法描述

以下描述的出发点是按从小到大排序：

1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
2. 对每一对相邻元素做同样的工作，从开始第一对元素到结尾的最后一对元素。最终最后位置的元素就是最大
   值；
3. 重复以上的步骤；

![](./images/BubbleSort.gif)



### 代码实现





## 选择排序





## 插入排序





## 希尔排序





## 快速排序