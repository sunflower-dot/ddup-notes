[TOC]

## 概述
Python 提供了`open()`函数，可以打开一个文件，得到一个文件`file`对象，而`file`对象提供相关的方法对文件内容进行读写等操作。

`open()`函数有若干个参数，比较重要的是以下三个参数：
- 文件路径：指定需要打开的文件的文件路径
- 打开模式：针对不同文件（二进制文件、文本文件）以及不同操作（读操作、写操作），会有不同的打开模式
- 编码：设定打开文件的默认编码

常见的打开模式有以下几种：
|模式|描述|
|---|---|
|t|文本模式（默认）|
|x|写模式，新建一个文件|
|b|二进制模式，打开二进制文件|
|+|更新一个文件，可读可写|
|r|以只读模式打开一个文件|
|rb|以二进制模式打开一个文件，只读|
|w|文件写入，原内容会清除|
|wb|以二进制打开文件写入，会清除原内容|
|a|打开一个文件并在内容末尾追加内容|
|ab|以二进制格式打开一个文件，在末尾追加内容|
|w+|打开一个文件进行读写，如果文件内容已存在，会清除原有的内容|
|a+|打开一个文件并使用追加进行读写|

注意，为了安全操作文件，文件使用完毕后，需要使用`close()`函数正确关闭。
```python
f = open('hello.txt','r')
print(type(f)) # <class '_io.TextIOWrapper'>
f.close() # 关闭文件
```
## 读取文件内容
使用open()函数打开文件之后，就可以读取文件的内容，文件对象提供多种读取文件内容的方法。

测试文件hello.txt
```txt
Hello World.
Hello Python.
Hello Java.
Hello C++.
```

### 读取若干字符
文件对象提供read()方法，可以读取文件中的若干个字符，它提供一个参数size，可以指定读取字符的数量。
```python
s = f.read(5)
print(s) # ==> Hello
```
当read()之后，访问文件的游标就会移动到第六个字符前面，此时，继续read，将得到Hello后面的结果。
```python
s = f.read(6)
print(s) # ==> ' World'
```

### 读取一行
文件对象提供`readline()`方法，和`read()`方法类似，可以读取文件中的若干个字符，它也提供一个参数`size`，可以指定读取字符的数量，不过和`read()`方法不同的是，`readline()`方法遇到一行结束的时候，就会返回。
```python
f = open('hello.txt','r')
s = f.read(3)
print(s) #Hel

s = f.readline(3)
print(s) #lo 

s = f.readline()
print(s) #World.\n

s = f.readline()
print(s) #Hello Python.\n

s = f.readline()
print(s) #Hello Java.\n
```

### 读取多行
文件对象提供`readlines()`方法，可以读取多行字符，返回一个列表。它提供一个`hint`参数，表示返回总和大约为hint字节的行，没有指定则默认以列表的形式返回文件所有的字符串。
```python
f = open('hello.txt','r')
s = f.readlines(30)
print(s)
# ['Hello World.\n', 'Hello Python.\n', 'Hello Java.\n', 'Hello C++.']
```

## 把字符串写入文件（覆盖）
要把字符串内容写入文件，需要使用w的模式打开文件。

|模式|描述|
|--|--|
|w|打开一个文件进行写入，如果文件内容已存在，会清除原有的内容|
|wb|以二进制格式只写模式打开一个文件，会清除原有的内容|
|w+|打开一个文件进行读写，如果文件内容已存在，会清除原有的内容|

```python
​f = open('test.txt', 'w')
```
### 写入若干字符
文件对象提供write方法向文件内写入若干字符，它接受一个字符串参数，表示需要写入的字符串。
```python
f = open('test.txt', 'w')
f.write('Hello World\n')
f.close()
```
### 写入若干行
文件对象提供writelines()方法向文件内容写入多行数据，它接受一个列表，表示需要写入的字符串列表。
```python
lines = ['Hello World\n', 'Hello Python\n', 'Hello Imooc\n']
f = open('test.txt', 'w')

f.writelines(lines)
f.close()
```
## 把字符串写入文件（追加）
通过`w`的打开方式打开文件，会清空文件的内容，这在很多场景下是不合适的，比如写系统日志的时候，需要累积随时间推移的所有数据。

Python提供文件追加内容的打开模式，可以往文件尾部添加内容，又不清空文件原有的内容。

|模式|描述|
|--|--|
|a|打开一个文件并追加内容，会往文件尾部添加内容|
|ab|以二进制格式打开一个文件并追加内容，会往文件尾部添加内容|
|a+|打开一个文件并使用追加进行读写|
```python
f = open('test.txt', 'a')
f.write('Hello Everyone\n')
f.close()
```
使用`a`的打开方式打开文件，文件游标默认是在文件的尾部，因此，可以便捷的往文件尾部添加内容。
除此以外，文件对象还提供`seek()`方法，可以移动文件的游标位置，它接受一个参数，表示文件的位置：
- 0：文件首部
- 1：当前位置
- 2：文件尾部
通过seek()可以把文件游标移动到文件首部但不删除文件的内容。
```python
​f = open('test.txt', 'a+')
content = f.readlines()
print(content) # ==> []
f.seek(0)
content = f.readlines()
print(content) # ==> ['Hello World\n', 'Hello Python\n', 'Hello Imooc\n']
```
第一次`print(content)`的时候，由于文件游标在文件的尾部，所以`readlines()`读取不到任何数据，打印了空的结果，第二次`print(content)`的时候，由于通过`seek(0)`，文件游标移动到了文件的首部，因此`readlines()`就返回了文件所有的内容。

在进行文件操作的时候，正确关闭一个文件非常重要，如果在文件读写后，没有正确关闭一个文件的话，则有可能导致文件损坏，文件内容丢失等问题。

在一般情况下，我们使用文件对象的close()方法，来关闭一个文件。
但是，使用close()方法，也不是100%安全的，如果在close()文件之前，程序异常退出了，那么文件也得不到正确的关闭。比如：

## 正确关闭文件
```python
f = open('test.txt', 'a+')
exit(-1) # ==> 模拟程序异常退出
f.close() # ==> close语句永远的不到执行
```
在实际工程中，`close()`文件之前，为了正确关闭文件，需要考虑各种异常情况，这是非常麻烦的一件事，Python提供`with`关键字，可以免除这类后顾之忧。

`with`关键字对资源进行访问的场合，会确保不管在使用过程中是否发生异常，都会执行必要的“清理”的操作，释放资源，比如文件使用后自动关闭等等。
with的使用方法如下：
```python
with open('test.txt', 'r') as f:
    content = f.readlines()
    for line in content:
        print(line)

```
当文件使用结束后，不需要显式的调用`f.close()`关闭文件。