## IO流概述及分类
- IO流用来处理设备之间的数据传输
- Java对数据的操作是通过流的方式
- Java用于操作流的类都在IO包中
- 流按流向分为两种：输入流，输出流

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1039463/1613993629301-12413e67-7d57-4acb-bf60-926eb617f70a.png#align=left&display=inline&height=270&margin=%5Bobject%20Object%5D&name=image.png&originHeight=270&originWidth=586&size=19346&status=done&style=none&width=586)
## FileWriter类使用
### FileWriter向文件中写数据
（1）FileWriter向文件中写数据操作步骤:

- 使用FileWriter流关联文件
- 利用FileWriter的写方法写数据
- 利用FileWriter的刷新方法将数据从内存刷到硬盘上
- 利用FileWriter的关流方法将释放占用的系统底层资源

（2）FileWriter方法:
构造方法
FileWriter(String fileName) 传入一个文件的路径
成员方法
void write(String str) 向文件中写str
void flush()  将内存中的数据刷新到文件中
void close()  关流释放系统底层资源
Example
```java
package com.itheima_01;
import java.io.FileWriter;
import java.io.IOException;

/*
 * 需求：往文件中写数据
 * 		写数据--输出流--FileWriter
 * 
 * FileWriter:
 * 		FileWriter(String fileName):传递一个文件名称
 * 
 * 输出流写数据的步骤：
 * 		A:创建输出流对象
 * 		B:调用输出流对象的写数据的方法
 * 		C:释放资源
 */
public class FileWriterDemo {
	public static void main(String[] args) throws IOException {
		//创建输出流对象
		FileWriter fw = new FileWriter("d:\\a.txt");
		/*
		 * 创建输出流对象做了哪些事情:
		 * 		A:调用系统资源创建了一个文件
		 * 		B:创建输出流对象
		 * 		C:把输出流对象指向文件
		 */
		
		//调用输出流对象的写数据的方法
		//写一个字符串数据
		fw.write("IO流你好");
		//数据没有直接写到文件，其实是写到了内存缓冲区
		fw.flush();
		
		//释放资源
		//通知系统释放和该文件相关的资源
		fw.close();
		
		//while(true) {}
	}
}

```
>   close()和flush()方法的区别：
>        flush():刷新缓冲区。流对象还可以继续使用。
>        close():先刷新缓冲区，然后通知系统释放资源。流对象不可以再被使用了。

### FileWriter其它写方法
void write(String str):写一个字符串数据
void write(String str,int index,int len):写一个字符串中的一部分数据
void write(int ch):写一个字符数据,这里写int类型的好处是既可以写char类型的数据，也可以写char对应的int类型的值。'a',97
void write(char[] chs):写一个字符数组数据
void write(char[] chs,int index,int len):写一个字符数组的一部分数据


demo：
```java
package com.itheima_01;
import java.io.FileWriter;
import java.io.IOException;

/*
 * void write(String str):写一个字符串数据
 * void write(String str,int index,int len):写一个字符串中的一部分数据
 * void write(int ch):写一个字符数据,这里写int类型的好处是既可以写char类型的数据，也可以写char对应的int类型的值。'a',97
 * void write(char[] chs):写一个字符数组数据
 * void write(char[] chs,int index,int len):写一个字符数组的一部分数据
 */
public class FileWriterDemo3 {
	public static void main(String[] args) throws IOException {
		//创建输出流对象
		FileWriter fw = new FileWriter("b.txt");
		
		//void write(String str):写一个字符串数据
		//fw.write("abcde");
		
		//void write(String str,int index,int len):写一个字符串中的一部分数据
		//fw.write("abcde",0,5);
		//fw.write("abcde",1,3);
		
		//void write(int ch):写一个字符数据,这里写int类型的好处是既可以写char类型的数据，也可以写char对应的int类型的值。'a',97
		//fw.write('a');
		//fw.write(97);
		
		//void write(char[] chs):写一个字符数组数据
		char[] chs = {'a','b','c','d','e'};
		//fw.write(chs);
		
		//void write(char[] chs,int index,int len):写一个字符数组的一部分数据
		//fw.write(chs,0,5);
		fw.write(chs,2,3);
		
		//释放资源
		fw.close();
	}
}

```
### FileWriter写入换行以及向文本末尾追加
```java
package com.itheima_01;

import java.io.FileWriter;
import java.io.IOException;

/*
 * 如何实现数据的换行?
 * 		\n可以实现换行，但是windows系统自带的记事本打开并没有换行，这是为什么呢?因为windows识别的换行不是\n，而是\r\n
 * 		windows:\r\n
 * 		linux:\n
 * 		mac:\r
 * 如何实现数据的追加写入?
 * 		FileWriter(String fileName, boolean append)
 */
public class FileWriterDemo4 {
	public static void main(String[] args) throws IOException {
		//创建输出流对象
		//FileWriter fw = new FileWriter("c.txt");
		FileWriter fw = new FileWriter("c.txt",true); //表示追加写入，默认是false
		
		for(int x=0; x<10; x++) {
			fw.write("hello"+x);
			fw.write("\r\n");
		}
		
		//释放资源
		fw.close();
	}
}

```
## FileReader类使用
### FileReader读数据一次读取一个字符
```java
package com.itheima_02;
import java.io.FileReader;
import java.io.IOException;

/*
 * 需求：从文件中读数据并显示到控制台
 * 		读数据--输入流--FileReader
 * 
 * FileReader:
 * 		FileReader(String fileName):传递文件名称
 * 
 * 输入流读文件的步骤：
 * 		A:创建输入流对象
 * 		B:调用输入流对象的读数据方法
 * 		C:释放资源
 * 
 * java.io.FileNotFoundException: fr.txt (系统找不到指定的文件。)
 */
public class FileReaderDemo {
	public static void main(String[] args) throws IOException {
		//创建输入流对象
//		FileReader fr = new FileReader("fr.txt");
		FileReader fr = new FileReader("FileWriterDemo.java");
		
		//调用输入流对象的读数据方法
		//int read():一次读取一个字符
		
		/*
		//第一次读数据
		int ch = fr.read();
		System.out.println(ch);
		System.out.println((char)ch);
		
		//第二次读数据
		ch = fr.read();
		System.out.println(ch);
		System.out.println((char)ch);
		
		//第三次读数据
		ch = fr.read();
		System.out.println(ch);
		System.out.println((char)ch);
		
		//这是时候，我们发现代码的重复度很高，想用循环改进，但是不知道循环的结束条件是什么
		ch = fr.read();
		System.out.println(ch);
		
		ch = fr.read();
		System.out.println(ch);
		//通过测试，我们知道，如果读取数据的返回值是-1的时候，就说明没有数据了，这也是我们循环的结束条件
		*/
		
		int ch;
		//1:fr.read()
		//2:ch=fr.read()
		//3:ch != -1
		while((ch=fr.read())!=-1) {
			//System.out.println(ch);
			//System.out.println((char)ch);
			System.out.print((char)ch);
		}
		
		//释放资源
		fr.close();
	}
}

```
### 利用FileReader和FileWriter完成文件复制
#### 读一次写一次
```java
package com.itheima_03;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

/*
 * 需求：
 * 		把项目路径下的FileWriterDemo.java中的内容复制到项目路径下的Copy.java中
 * 
 * 文件复制也是有规律可循的，也就是说有套路。
 * 
 * 数据源：
 * 		FileWriterDemo.java -- 读数据 -- FileReader
 * 目的地：
 * 		Copy.java -- 写数据 -- FileWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		//创建输入流对象
		FileReader fr = new FileReader("FileWriterDemo.java");
		//创建输出流对象
		FileWriter fw = new FileWriter("Copy.java");
		
		//读写数据
		int ch;
		while((ch=fr.read())!=-1) {
			fw.write(ch);
		}
		
		//释放资源
		fw.close();
		fr.close();
	}
}

```
### 利用字符数组拷贝文件
```java
package com.itheima_03;

import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

/*
 * 需求：
 * 		把项目路径下的FileWriterDemo.java中的内容复制到项目路径下的Copy.java中
 * 
 * 数据源：
 * 		FileWriterDemo.java -- 读数据 -- FileReader
 * 目的地：
 * 		Copy.java -- 写数据 -- FileWriter
 */
public class CopyFileDemo2 {
	public static void main(String[] args) throws IOException {
		//创建输入流对象
		FileReader fr  = new FileReader("FileWriterDemo.java");
		//创建输出流对象
		FileWriter fw = new FileWriter("Copy.java");
		
		//读写数据
		char[] chs = new char[1024];
		int len;
		//一次读取一个字符数组
		while((len=fr.read(chs))!=-1) {
			fw.write(chs, 0, len);
		}
		
		//释放资源
		fw.close();
		fr.close();
	}
}

```
### 拷贝文件的两种方式图解
![image.png](https://cdn.nlark.com/yuque/0/2021/png/1039463/1613994335014-bc9a1d17-33c1-4cdf-b931-bf09757d22ba.png#align=left&display=inline&height=268&margin=%5Bobject%20Object%5D&name=image.png&originHeight=268&originWidth=580&size=28283&status=done&style=none&width=580)
## 缓冲流介绍和使用
### 缓冲流的基本使用
```java
package com.itheima_04;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

/*
 * BufferedWriter:将文本写入字符输出流，缓冲各个字符，从而提供单个字符、数组和字符串的高效写入。 
 * BufferedReader:从字符输入流中读取文本，缓冲各个字符，从而实现字符、数组和行的高效读取。
 */
public class BufferedStreamDemo {
	public static void main(String[] args) throws IOException {
		//创建输出缓冲流对象
		/*
		BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));
		bw.write("hello");
		//bw.flush();
		bw.close();
		*/
		
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader("FileWriterDemo.java"));
		
		/*
		//一次读写一个字符
		int ch;
		while((ch=br.read())!=-1){
			System.out.print((char)ch);
		}
		*/
		
		//一次读写一个字符数组
		char[] chs = new char[1024];
		int len;
		while((len=br.read(chs))!=-1) {
			System.out.print(new String(chs,0,len));
		}
		
		//释放资源
		br.close();
		
	}
}

```
##  缓冲流复制文本文件的两种方式
  利用缓冲流把项目路径下的FileWriterDemo.java中的内容复制到项目路径下的Copy.java中
  第一种方式:使用缓冲流不使用字符数组
  第二种方式:使用缓冲流使用字符数组
```java
package com.itheima_04;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

/*
 * 需求：
 * 		把项目路径下的FileWriterDemo.java中的内容复制到项目路径下的Copy.java中
 * 
 * 数据源：
 * 		FileWriterDemo.java -- 读数据 -- FileReader -- 高效的读数据 -- BufferedReader
 * 目的地：
 * 		Copy.java -- 写数据 -- FileWriter -- 高效的写数据 -- BufferedWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader("FileWriterDemo.java"));
		//创建输出缓冲流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("Copy.java"));
		
		//读写数据
		/*
		//一次读写一个字符
		int ch;
		while((ch=br.read())!=-1) {
			bw.write(ch);
		}
		*/
		
		
//一次读写一个字符数组
		char[] chs = new char[1024];
		int len;
		while((len=br.read(chs))!=-1) {
			bw.write(chs,0,len);
		}
		
		//释放资源
		bw.close();
		br.close();
	}
}

```
## 缓冲流的特有方法复制文件
```java
package com.itheima_05;
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
/*
 * 需求：
 * 		把项目路径下的FileWriterDemo.java中的内容复制到项目路径下的Copy.java中
 * 
 * 数据源：
 * 		FileWriterDemo.java -- 读数据 -- FileReader -- 高效的读数据 -- BufferedReader
 * 目的地：
 * 		Copy.java -- 写数据 -- FileWriter -- 高效的写数据 -- BufferedWriter
 */
public class CopyFileDemo {
	public static void main(String[] args) throws IOException {
		//创建输入缓冲流对象
		BufferedReader br = new BufferedReader(new FileReader("FileWriterDemo.java"));
		//创建输出缓冲流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("Copy.java"));
		
		//读写数据
		String line;
		while((line=br.readLine())!=null) {
			bw.write(line);
			bw.newLine();
			bw.flush();
		}
		//释放资源
		bw.close();
		br.close();
	}
}

```


