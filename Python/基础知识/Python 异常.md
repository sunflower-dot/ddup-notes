[TOC]

异常可能是错误（如试图除以零），也可能是通常不会发生的事情。

## 触发异常

```python
>>> raise Exception
Traceback (most recent call last):
File "<stdin>", line 1, in ?
Exception
>>> raise Exception('hyperdrive overload')
Traceback (most recent call last):
File "<stdin>", line 1, in ?
Exception: hyperdrive overload
```



## 自定义异常

那么如何创建异常类呢？就像创建其他类一样，但务必直接或间接地继承 `Exception` （这意味着从任何内置异常类派生都可以）。因此，自定义异常类的代码类似于下面这样：

```python
class SomeCustomException(Exception): 
    pass
```



## 捕获异常

### try/except

可以使用`try...except`语句来捕获异常。

示例：

```python
try:
    x = int(input('Enter the first number: '))
    y = int(input('Enter the second number: '))
	print(x / y)
except ZeroDivisionError:
	print("The second number can't be zero!")
```



> 说明：异常的传递。异常从函数向外传播到调用函数的地方。如果在这里也没有被捕获，异常将向程序的最
> 顶层传播。这意味着你可使用 try / except 来捕获他人所编写函数引发的异常



### 捕获多个异常

在实际程序中，难免存在多个异常的情况，可以在可在 try / except 语句中再添加一个 except 子句，使用多个except捕获多个异常：

```python
try:
    x = int(input('Enter the first number: '))
    y = int(input('Enter the second number: '))
	print(x / y)
except ZeroDivisionError:
	print("The second number can't be zero!")
except TypeError:
	print("That wasn't a number, was it?")
```



如果要使用一个 except 子句捕获多种异常，也可在一个元组中指定这些异常：

```python
try:
    x = int(input('Enter the first number: '))
    y = int(input('Enter the second number: '))
	print(x / y)
except (ZeroDivisionError, TypeError, NameError):
	print('Your numbers were bogus ...')
```

注意事项：

1. 捕获是从上到下依次比较，如果匹配，则执行匹配的except的语句块；
2. 异常只能被一个except语句捕获，其它except语句就不会再次捕获了，谁在前谁捕获；
3. 如果没有任何一个except语句捕获到这个异常，则该异常向外抛出；
4. 编写异常时：应该越具体的异常，越往上写；宽泛的，往下写；尽量不要抑制异常
   

### 获取异常对象 as

可以使用 `as` 子句获取异常对象：

```python
try:
    x = int(input('Enter the first number: '))
    y = int(input('Enter the second number: '))
	print(x / y)
except ZeroDivisionError as e:
	print("The second number can't be zero!")
except TypeError as e:
	print("That wasn't a number, was it?")
```



### 一网打尽，捕获漏网之鱼

即使程序处理了好几种异常，还是可能有一些漏网之鱼。如果你就是要使用一段代码捕获所有的异常，只需在 except 语句中不指定任何异常类即可。

```python
try:
    x = int(input('Enter the first number: '))
    y = int(input('Enter the second number: '))
	print(x / y)
except ZeroDivisionError as e:
	print("The second number can't be zero!")
except TypeError as e:
	print("That wasn't a number, was it?")
except:
    print("Other Exception!")
```



### 万事大吉，`else`子句

`else`子句只有没有任何异常发生，则执行。在有些情况下，在没有出现异常时执行一个代码块很有用。为此，可像条件语句和循环一样，给 try / except 语句添加一个 else 子句。

```python
while True:
    try:
        x = int(input('Enter the first number: '))
        y = int(input('Enter the second number: '))
        value = x / y
        print('x / y is', value)
    except:
    	print('Invalid input. Please try again.')
    else:
    	break
```

仅当没有引发异常时，才会跳出循环。换而言之，只要出现错误，程序就会要求用户提供新的输入。

### 清理工作  finally 子句

在`try...finally`语句块中，不管是否发生了异常，最后都要执行`finally`的部分。

```python
try:
	1 / 0
except NameError:
	print("Unknown variable")
else:
	print("That went well!")
finally:
	print("Cleaning up.")
```



### 总结：异常捕获流程

1. 如果try中语句执行时发生异常，搜索except子句，并执行第一个匹配该异常的except子句；
2. 如果try中语句执行时发生异常，却没有匹配的except的子句，异常将被递交到外层的try，如果外层不处理这个异常，异常将继续向外层传递。如果都不处理该异常，则会传递到最外层，如果还没有处理，就终止异常所在的线程。
3. 如果在try执行时没有发生异常，如有else子句，可执行else子句中的语句；
4. 无论try中是否发生异常，finally子句最终都会执行

