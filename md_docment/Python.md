# Python

标签（空格分隔）： python

[TOC]

## 一、基本数据类型:
- int 
- long
- bool
- float  
  与Java中非常接近.可以近似认为一致.bool的值是True和False,或者0(False),非0就是True.  

### 1、变量作用域

本地变量，全局变量



## 二、操作符
> 算术运算符: +(加), -(减), *(乘), /(除), %(求余), `**`(指数运算)
>
> 比较运算符: == ,  != ,  >  ,  >= , < , <=
>
> 赋值运算符: =, += , -= , *= , /= , %= , **= , //=
>
> 逻辑运算符: and , or  , not
>
> 成员运算符: in  ,  not in 
>
> 身份运算符: is / is not


## 三、注释与文档
一行当中,从#开始地方就是注释.不会影响下一行.  
""引号放在文件的开头,函数的开头或者一个类的开头,就是文档注释,与Java中的/** ... */作用和目的是一样的.

折行
:   如果一行太长了,写不下了,就需要在下一行接着写,这时可以使用`\`来告诉Python,下一行继续.  
Python是一个语句放在一行,行尾可以选择性的加上;但如果想在一行放多个语句,就需要用;来分隔语句:  
a = 1; b = 2; c = 3;  
虽然这在语法上可行,但不是一个好习惯,绝大多数的编程规范都是要一行写一个语句.

## 四、数组、集合
### List和Tuple
**List**

这就是Java或C中的数组.它是一个容器,能用来顺序的,以整数索引方式检索, 存储一组对象. **List** 用 **[]** 来表示,如 **[1, 2, 3]** 就是一个List；而 **Tuple** 用 **()** 来表示,如 **(3, 4, 5)** 就是一个 **Tuple** .  
它们的区别在于List是可变的；而Tuple是不可变的.也就是说不可以增,删和改.  
索引方式除了与Java一样的以一个整数下标方式外,还可以指定开始,结束和步长,和使用负索引来分割List:  
通用语法格式是: **list[start: end: step]**  

```python
list[index] --- 返回第(index+1)个元素,受C语言影响,下标亦是从0开始  
list[start:end] --- 返回从start开始,到end-1,也就是list[start], list[start+1].....list[end-1]  
list[start:end:step] --- 与上面类似,只不过每隔step取一个  
list[:end]  ---- 缺省的开端是0  
list[start:] ---- 缺省的结尾是len(list),或者-1  
负数索引更是方便,它与正数的对应关系为:  
正数索引   0    1      2      3
数组元素  [1]   [3]    [5]    [7]
负数索引  -4    -3      -2    -1
```
实例：

```python
>>> a = [1, 3, 5, 7];  
>>> a[0]  
1  
>>> a[3]  
7  
>>> a[-1]  
7  
>>> a[-2]  
5  
>>> a[0:3]  
[1, 3, 5]  
>>> a[1:3:2]  
[3]  
>>> a[0:3:2]  
[1, 5]  
>>> a[0:-1:2]  
[1, 5]  
>>>
```
list增删查改：
```python
//list是一个可变的有序表，所以，可以往list中追加元素到末尾：
list.append('Adam')
//也可以把元素插入到指定的位置，比如索引号为1的位置：
list.insert(1, 'Jack')
//要删除list末尾的元素，用pop()方法：
list.pop()
//要删除指定位置的元素，用pop(i)方法，其中i是索引位置：
list.pop(i)

```
list里面的元素的数据类型也可以不同  
list里面也可以包含list

list的遍历：
```python
for item in sequence:
        process(item)
        
#如果要取到某个item的位置，可以这样写：
for index in range(len(sequence)):
        process(sequence[index])

for index,item in enumerate(sequence):
        print index,item
```

**tuple**
tuple和list非常类似，但是tuple一旦初始化就不能修改，比如同样是列出同学的名字：
```python
classmates = ('Michael', 'Bob', 'Tracy')
```
现在，classmates这个tuple不能变了，它也没有append()，insert()这样的方法。其他获取元素的方法和list是一样的，你可以正常地使用classmates[0]，classmates[-1]，但不能赋值成另外的元素。  

tuple里面如果包含有一个list，则这个list里面的元素可以被改变；
### dict
dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。  
例：
```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
>>> d['Michael'] = 90
>>> d['Michael']
90

```
- 多次对一个key放入value，后面的值会把前面的值冲掉  
- 如果key不存在，dict就会报错
  要避免key不存在的错误，有两种办法，一是通过in判断key是否存在：

```python
>>> 'Thomas' in d
False
```
二是通过dict提供的get方法，如果key不存在，可以返回None，或者自己指定的value：

```python
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```
- pop(key)  删除并返回该条目值（键值对）
- del d[key] 也可以删除指定元素

遍历dict
```python
# 方法一
for key in d: 
     n = d[key]
     
# 方法二：最慢
for k,v in d.items(): 
    n = v

# 方法三: 最快，推荐方法
for k,v in d.iteritems(): 
    n = v 

# 方法四
for k,v in zip(d.iterkeys(),d.itervalues()): 
    n = v 
```


### set

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。  
要创建一个set，需要提供一个list作为输入集合：

```python
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```
重复元素在set中自动被过滤：

```python
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```
通过add(key)方法可以添加元素到set中，可以重复添加，但不会有效果   

```python
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

通过remove(key)方法可以删除元素：

```python
>>> s.remove(4)
>>> s
{1, 2, 3}
```
set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：


```python
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```
---

### 列表生成式（强大）：
举个例子，要生成list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]可以用list(range(1, 11))：

```python
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

但如果要生成[1x1, 2x2, 3x3, ..., 10x10]怎么做？方法一是循环：

```python
>>> L = []
>>> for x in range(1, 11):
...    L.append(x * x)
...
>>> L
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
但是循环太繁琐，而列表生成式则可以用一行语句代替循环生成上面的list：
```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

写列表生成式时，把要生成的元素x * x放到前面，后面跟for循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：

```python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```






## 五、字符串String
字符串就是一个字符的数组,List的操作都可以对String直接使用.
### 1、字串格式化符%
这是一个类似C语言printf和Java中的String.format()的操作符,它能格式化字串,整数,浮点等类型:语句是:  
formats % (var1, var2,....)  
它返回的是一个String.
```python
>>> "Int %d, Float %d, String '%s'" % (5, 2.3, 'hello')  
"Int 5, Float 2, String 'hello'"  
>>> 
```
### json和python
:    json字符串转python对象

```python
    json.loads()
```

:    python对象转json字符串

```python
    json.dumps()
```

## 六、分支语句
格式为:
```python
if expression:  
      blocks;  
elif expression2:  
      blocks;  
else:  
      blocks; 
```
## 七、循环
### for循环：
格式：  

```python
for x in list:
    break;
# 取出下标和值
for index,value in enumerate(A):
     print index
```
例：

```python
names = ['Michael', 'Bob', 'Tracy']
for name in names:
    print(name)
//或者
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    print(x)
```

### while循环
格式：  

```python
while expression:
      blocks
```
## 八、函数
ython内置了很多有用的函数，我们可以直接调用。

要调用一个函数，需要知道函数的名称和参数，比如求绝对值的函数abs，只有一个参数。可以直接从Python的官方网站查看文档：

http://docs.python.org/3/library/functions.html#abs

也可以在交互式命令行通过help(abs)查看abs函数的帮助信息。

> - Python中函数也是一个对象,可以赋值,可以拷贝,可以像普通变量那样使用.  
>   函数名其实就是指向函数的变量！对于abs()这个函数，完全可以把函数名abs看成变量，它指向一个可以计算绝对值的函数！
- python函数是一个对象，那么他也就有属性，函数对象有一个`__name__`属性，可以拿到函数名字


### 定义函数

```python
def function_name(args):
      function_body;
```
### 调用函数
调用函数的方式function_name(formal_args):

```python
>>> def power(x):  
... return x*x;  
...   
>>> power(4)  
16  
>>>
```

### 高阶函数

能够接收函数作为参数的函数称之为高阶函数

map()
:   map()函数接收两个参数，一个是函数，一个是序列，map将传入的函数依次作用到序列的每个元素，并把结果作为新的list返回。

```python
    >>> def f(x):
    ...     return x * x
    ...
    >>> map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
    [1, 4, 9, 16, 25, 36, 49, 64, 81]
```

reduce()
:   reduce把一个函数作用在一个序列[x1, x2, x3...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算,效果如下：

```python
    reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
    
    # 例如求和计算
    >>> def add(x, y):
    ...     return x + y
    ...
    >>> reduce(add, [1, 3, 5, 7, 9])
    25
```

filter()
:   和map()类似，filter()也接收一个函数和一个序列。和map()不同的时，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。

```python
    def is_odd(n):
        return n % 2 == 1

    filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15])
    # 结果: [1, 5, 9, 15]
```

### 匿名函数
关键字lambda表示匿名函数，冒号前面的x表示函数参数。它没有名字,只有参数和表达式:
lambda  args: expression
```python
>>> d = lambda x: x*x;  
>>> d(2)  
4
```


如果想定义一个什么事也不做的空函数，可以用pass语句：

def nop():
    pass

---

## 九、模块
使用import导入，

    import sys

新建一个包：新建一个文件夹，然后在该文件夹下创建`__init__.py`文件即可。

---

## 十、面向对象

### 类和实例

用class关键字定义类：
```python
class ClassName(object):
    ...

```
在类中：`__init__()`可以被当成构造器，不过不像其他语言中的构造器，**它并不创建实例——它仅仅是你的对象创建后执行的第一个方法**。它的目的是执行一些该对象的必要的初始化工作。因此，可以在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去。

- 可以自由地给一个实例变量绑定属性
  对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同

- 和普通的函数相比，在类中定义的函数只有一点不同，就是第一个参数永远是实例变量self，并且，调用时，不用传递该参数。除此之外，类的方法和普通函数没有什么区别，所以，你仍然可以用默认参数、可变参数和关键字参数。

- 如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，在Python中，实例的变量名如果以`__`开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。
  （双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问`__name`是因为Python解释器对外把`__name`变量改成了`_Student__name`，所以，仍然可以通过`_Student__name`来访问`__name`变量）

- 有些时候，你会看到以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

### 多态
和java的多态差不多，子类继承父类，重写父类的方法，实现多态。

### 获取对象信息

    type(): 获取对象类型
    isinstance():  判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上
    dir():  获得一个对象的所有属性和方法，返回一个包含字符串的list
    getattr():  getattr(obj, 'y') # 获取属性'y'  'y'可以是类的属性也可以是类的方法
    setattr():  setattr(obj, 'y', 19) # 设置一个属性'y'
    hasattr():  hasattr(obj, 'x') # 有属性'x'吗？

如果试图获取不存在的属性，会抛出AttributeError的错误
可以传入一个default参数，如果属性不存在，就返回默认值：
```python
>>> getattr(obj, 'z', 404) # 获取属性'z'，如果不存在，返回默认值404
404
```


## 十、错误和异常
使用print

使用logging
参考：https://www.cnblogs.com/CJOKER/p/8295272.html


## 十一、IO操作
### 文件读写
使用open(path,mode)函数，mode为以什么模式操作文件，读，写等；

    mode
        r  读取
        w  写入    文件若存在则首先清空，然后（重新）创建
        a  追加    如果文件不存在，将被自动创建
        b  代表二进制模式访问(在unix类似系统中可有可无,不能单独使用)
        r+ 以读写模式打开
        w+ 以读写模式打开

file.read()    最多读取给定数目个字节，否则文件将被读取直至末尾
file.readline()
file.write()
file.close()

### 操作文件和目录
使用系统变量os

    import os
    
    os.mkdir(path)    新建目录
    os.rmdir(path)    删除目录
    os.rename(oldname,newname)    重新命名
    os.remove(path)   删除文件
    
    os.path.isdir(path)    是否为目录
    os.path.isfile(path)
    cwd = os.getcwd()
    os.listdir(cwd)

## 十二、线程
python的线程有两种方式，直接传入要运行的方法或从Thread继承并覆盖run()
```python
# coding:utf-8
import threading
import time
#方法一：将要执行的方法作为参数传给Thread的构造方法
def action(arg):
    time.sleep(1)
    print 'the arg is:%s\r' %arg

for i in xrange(4):
    t =threading.Thread(target=action,args=(i,))
    t.start()

print 'main thread end!'

#方法二：从Thread继承，并重写run()
class MyThread(threading.Thread):
    def __init__(self,arg):
        super(MyThread, self).__init__()#注意：一定要显式的调用父类的初始化函数。
        self.arg=arg
    def run(self):#定义每个线程要运行的函数
        time.sleep(1)
        print threading.current_thread().getName()

for i in xrange(4):
    t =MyThread(i)
    t.start()

print 'main thread end!'

创建线程的两种方法
```

构造方法： 
:   Thread(group=None, target=None, name=None, args=(), kwargs={}) 
    　　group: 线程组，目前还没有实现，库引用中提示必须是None； 
    　　target: 要执行的方法； 
    　　name: 线程名； 
    　　args/kwargs: 要传入方法的参数。

实例方法： 
:   isAlive(): 返回线程是否在运行。正在运行指启动后、终止前。 
get/setName(name): 获取/设置线程名。 
start():  线程准备就绪，等待CPU调度
is/setDaemon(bool): 获取/设置是后台线程（默认前台线程（False））。（在start之前设置）
　　如果是后台线程，主线程执行过程中，后台线程也在进行，主线程执行完毕后，后台线程不论成功与否，主线程和后台线程均停止，如果是前台线程，主线程执行过程中，前台线程也在进行，主线程执行完毕后，等待前台线程也执行完成后，程序停止
start(): 启动线程。 
join([timeout]): 阻塞当前上下文环境的线程，直到调用此方法的线程终止或到达指定的timeout（可选参数）。

参考：https://www.cnblogs.com/tkqasn/p/5700281.html

## 十三、网络编程
socket套接字

    socket(socket_family, socket_type, protocol=0)
socket_family 是 `AF_UNIX` 或 `AF_INET` ,socket_type 是 SOCK_STREAM 或 SOCK_DGRAM。protocol 通常省略,默认为 0。


### SocketServer

```python
# coding:utf-8

import SocketServer
import threading
import time

client_addr = []
client_socket = []

class ThreadTCPRequestHandler(SocketServer.BaseRequestHandler):
    ip = ""
    port = 0
    timeOut = 30  # 设置超时时间变量

    def setup(self):
        """在handle方法执行之前执行"""
        self.ip = self.client_address[0].strip()  # 获取客户端的ip
        self.port = self.client_address[1]  # 获取客户端的port
        self.request.settimeout(self.timeOut)  # 对socket设置超时时间
        print(self.ip + ":" + str(self.port) + "连接到服务器！")
        client_addr.append(self.client_address)  # 保存到队列中
        client_socket.append(self.request)  # 保存套接字socket

    def handle(self):
        while True:
            try:
                data = self.request.recv(1024)
            except Exception as e:
                print(self.ip + ":" + str(self.port) + "接收超时！即将断开连接！")
                break
            if data:
                time.sleep(0.1)
                cur_thread = threading.current_thread()
                response = "my name is {0} you said:{1}".format(cur_thread.name, data)
                self.request.sendall(response)

    def finish(self):
        print(self.ip + ":" + str(self.port) + "断开连接！")
        client_addr.remove(self.client_address)
        client_socket.remove(self.request)

def startServer():
    HOST, PORT = "127.0.0.1", 21356
    server = SocketServer.ThreadingTCPServer((HOST, PORT), ThreadTCPRequestHandler)
    server_thread = threading.Thread(target=server.serve_forever)
    server_thread.daemon = True
    server_thread.start()
    print 'started server'

if __name__ == "__main__":
    startServer()
```
这即一个简单的多线程的tcp服务器；
上面的代码最简化：
```python
import SocketServer
  
class MyTCPHandler(SocketServer.BaseRequestHandler):
    #继承BaseRequestHandler基类，然后必须重写handle方法，并且在handle方法里实现与客户端的所有交互
    def handle(self):
        while  True:
            data = self.request.recv(1024) #接收1024字节数据
            if not data: break     
            self.request.sendall(data.upper())
  
if __name__ == "__main__":
    HOST, PORT = "localhost", 50007
    # 把刚才写的类当作一个参数传给ThreadingTCPServer这个类，下面的代码就创建了一个多线程socket server
    server = SocketServer.ThreadingTCPServer((HOST, PORT), MyTCPHandler)
    server.serve_forever()
```
SocketServer.ThreadingTCPServer继承了ThreadingMixIn，所以可以实现多线程


