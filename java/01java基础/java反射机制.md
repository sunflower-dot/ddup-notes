## JAVA反射机制
在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为java语言的反射机制。
## Class类
Class是开启反射的源头.在Object类中定义了以下的方法，此方法将被所有子类继承：
```java
public final Class getClass();
```


以上的方法返回值的类型是一个Class类，此类是Java反射的源头，实际上所谓反射从程 序的运行结果来看也很好理解，即：可以通过对象反射求出类的名称。
![](https://cdn.nlark.com/yuque/0/2020/jpeg/1039463/1594439354288-c7774bea-3bb1-4a27-bab6-acce7fbccad8.jpeg#align=left&display=inline&height=136&margin=%5Bobject%20Object%5D&originHeight=131&originWidth=640&size=0&status=done&style=none&width=662)
### 获取Class类对象（四种方法）
**
（1）若已知具体的类，通过类的class属性获取，该方法最为安全可靠，程序性能最高:
```java
Class clazz = String.class;
```


（2）已知某个类的实例，调用该实例的getClass()方法获取Class对象:
```java
Person person = new Person();Class clazz = person.getClass();
```


（3）已知一个类的全类名，且该类在类路径下，可通过Class类的静态方法forName()获取，可能抛ClassNotFoundException:
```java
String className = "java.lang.String";
Class clazz = Class.forName(className);

```


（4）通过类加载器来获取:
```java
ClassLoader cl = this.getClass().getClassLoader();
Class clazz = cl.loadClass("类的全类名");
```


### 示例
```java
    public static void main(String[] args) throws IllegalAccessException, InstantiationException, ClassNotFoundException {
        //(1) 通过具体的类来实例化对象
        Class<Student> studentClass = Student.class;
        Student student = studentClass.newInstance();
        student.setName("李白");
        student.setAge(220);
        student.selfIntroduction();
        //(2) 通过类的实例化对象
        Student student1 = new Student("张飞", 22);
        Class<? extends Student> student1Class = student1.getClass();
        Student student2 = student1Class.newInstance();
        student2.selfIntroduction();

        //(3) 通过类路径（Class.forName）
        Class<Student> forName = (Class<Student>) Class.forName("com.haan.reflect.Student");
        Student student3 = forName.newInstance();
        student3.selfIntroduction();

        //(4) 通过类加载器+类路径
        ClassLoader classLoader = ClassTest.class.getClassLoader();
        Class<Student> loadClass = (Class<Student>) classLoader.loadClass("com.haan.reflect.Student");
        Student student4 = loadClass.newInstance();
        student4.selfIntroduction();
    }
```
Student类定义如下：
```java
public class Student {
    private String name;
    private String gender;
    private String grade;
    private Integer age;

    public Student() {
        System.out.println("通过无参构造Student、、、");
    }

    public Student(String name, Integer age) {
        this.name = name;
        this.age = age;
        System.out.println("通过有参构造Student、、、");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public String getGrade() {
        return grade;
    }

    public void setGrade(String grade) {
        this.grade = grade;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public void selfIntroduction(){
        System.out.println("my name is "+name+", i am "+age+" years old!");
    }
}
```
### 判断是否为某个类的实例


## Java反射相关的主要API
Class类 代表类的实体，在运行的Java应用程序中表示类和接口
Field类 代表类的成员变量（成员变量也称为类的属性）
Method类 代表类的方法
Constructor类 代表类的构造方法
