[TOC]

## python文件操作


### 一、读文件
要以读文件的模式打开一个文件对象，使用Python内置的open()函数，传入文件名和标示符：

```
>>> f = open('/Users/michael/test.txt', 'r')
```
标示符'r'表示读，这样，我们就成功地打开了一个文件。


如果文件打开成功，接下来，调用read()方法可以一次读取文件的全部内容，Python把内容读到内存，用一个str对象表示：

```
>>> f.read()
'Hello, world!'
```
最后一步是调用close()方法关闭文件。文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的：

```
>>> f.close()
```
由于文件读写时都有可能产生IOError，一旦出错，后面的f.close()就不会调用。所以，为了保证无论是否出错都能正确地关闭文件，我们可以使用try ... finally来实现：

```
try:
    f = open('/path/to/file', 'r')
    print f.read()
finally:
    if f:
        f.close()
```

但是每次都这么写实在太繁琐，所以，Python引入了with语句来自动帮我们调用close()方法：

```
with open('/path/to/file', 'r') as f:
    print f.read()
```

这和前面的try ... finally是一样的，但是代码更佳简洁，并且不必调用f.close()方法。


要读取二进制文件，比如图片、视频等等，用'rb'模式打开文件即可

### 二、写文件
写文件和读文件是一样的，唯一区别是调用open()函数时，传入标识符'w'或者'wb'表示写文本文件或写二进制文件：

```
>>> f = open('/Users/michael/test.txt', 'w')
>>> f.write('Hello, world!')
>>> f.close()
```
但是只有调用close()方法时，操作系统才保证把没有写入的数据全部写入磁盘。忘记调用close()的后果是数据可能只写了一部分到磁盘，剩下的丢失了。所以，还是用with语句来得保险：

```
with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```
在Python中，文件读写是通过open()函数打开的文件对象完成的。使用with语句操作文件IO是个好习惯。



python文件读写的参数解释如下

> w：以写方式打开
>
> a：以追加模式打开 (从 EOF 开始, 必要时创建新文件)
>
> r+：以读写模式打开
>
> w+：以读写模式打开 (参见 w )
>
> a+：以读写模式打开 (参见 a )
>
> rb：以二进制读模式打开
>
> wb：以二进制写模式打开 (参见 w )
>
> ab：以二进制追加模式打开 (参见 a )
>
> rb+：以二进制读写模式打开 (参见 r+ )
>
> wb+：以二进制读写模式打开 (参见 w+ )
>
> ab+：以二进制读写模式打开 (参见 a+ )

另外对于大文件我们还可以分段读写

> - f.read([size]) #size为读取的长度，以byte为单位
> - f.readline([size]) #读一行，如果定义了size，有可能返回的只是一行的一部分
> - f.readlines([size]) #把文件每一行作为一个list的一个成员，并返回这个list。其实它的内部是通过循环调用readline()来实现的。如果提供size参数，size是表示读取内容的总长，也就是说可能只读到文件的一部分。 
> - f.write(str) #把str写到文件中，write()并不会在str后加上一个换行符
> - f.writelines(seq) #把seq的内容全部写到文件中(多行一次性写入)。这个函数也只是忠实地写入，不会在每行后面加上任何东西。
> - f.close() #关闭文件。python会在一个文件不用后自动关闭文件，不过这一功能没有保证，最好还是养成自己关闭的习惯。 如果一个文件在关闭后还对其进行操作会产生ValueError fp.flush() #把缓冲区的内容写入硬盘 



### 三、文件夹操作

文件夹操作一般使用python的os模块。

- 1.得到当前工作目录，即当前Python脚本工作的目录路径:` os.getcwd()`

- 2.返回指定目录下的所有文件和目录名:`os.listdir()`

- 3.函数用来删除一个文件:`os.remove()`

- 4.检验给出的路径是否是一个文件：`os.path.isfile()`

- 5.检验给出的路径是否是一个目录：`os.path.isdir()`

- 6.判断是否是绝对路径：os.path.isabs()

- 7.检验给出的路径是否真地存:os.path.exists()

- 8.返回一个路径的目录名和文件名:os.path.split()  
    ```python
    >>> os.path.split('D:\workspace\PythonWork\logger.py')
    ('D:\\workspace\\PythonWork', 'logger.py')
    ```
- 9.分离扩展名：os.path.splitext()
    ```python
    >>> os.path.splitext('D:\workspace\PythonWork\middleclient\logger.py')
    ('D:\\workspace\\PythonWork\\middleclient\\logger', '.py')
    ```

- 10.获取路径名：os.path.dirname()
    ```python
    >>> os.path.dirname('D:\workspace\PythonWork\middleclient\logger.py')
    'D:\\workspace\\PythonWork\\middleclient'
    ```

- 11.获取文件名：os.path.basename()

    ```python
    >>> os.path.basename('D:\workspace\PythonWork\middleclient\logger.py')
    'logger.py'
    ```

- 12.重命名：os.rename（old， new）

- 13.创建多级目录：os.makedirs（r"c：\python\test"）

- 14.创建单个目录：os.mkdir（"test"）

- 15.获取文件大小：os.path.getsize（filename）

---

## python线程

### 一、线程

Python的标准库提供了两个模块：`thread`和`threading`，`thread`是低级模块，`threading`是高级模块，对`thread`进行了封装。绝大多数情况下，我们只需要使用`threading`这个高级模块。

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

> Thread(group=None, target=None, name=None, args=(), kwargs={}) 
>     　　group: 线程组，目前还没有实现，库引用中提示必须是None； 
>     　　target: 要执行的方法； 
>     　　name: 线程名； 
>     　　args/kwargs: 要传入方法的参数。

实例方法： 

- **isAlive()**: 返回线程是否在运行。正在运行指启动后、终止前。 
- **get/setName(name)**: 获取/设置线程名。 
- **start()**:  线程准备就绪，等待CPU调度
- **is/setDaemon(bool)**: 获取/设置是后台线程（默认前台线程（False））。（在start之前设置）
  　　如果是后台线程，主线程执行过程中，后台线程也在进行，主线程执行完毕后，后台线程不论成功与否，主线程和后台线程均停止，如果是前台线程，主线程执行过程中，前台线程也在进行，主线程执行完毕后，等待前台线程也执行完成后，程序停止
- **start()**: 启动线程。 
- **join([timeout])**: 阻塞当前上下文环境的线程，直到调用此方法的线程终止或到达指定的timeout（可选参数）。

### 二、线程同步

线程经常会有同步问题，python中同步锁是通过`threading.Lock()`来创建的。

```python
import time,threading

count = 0

lock = threading.Lock()

def add(n):
    global count
    count += n
    count -= n

def run_thread(n):
    for i in range(1000):
        # 先要获取锁
        lock.acquire()
        try:
            add(n)
        finaly:
        	lock.release() #改完一定要释放锁
    
t1 = threading.Thread(target=run_thread, args=(3,))
t2 = threading.Thread(target=run_thread, args=(2,))
t1.start()
t2.start()
t1.join()
t2.join()
print count
```

如以上例子，如果你不加锁，那么count有可能不会是你想要的结果。

### 三、线程池

python自带的线程池是threadpool，我们来看看它的用法：

```python
import threadpool

def func(a):
    a = a+1

params = [2,4,6,8]
    
pool = threadpool.ThreadPool(10)
reqs = threadpool.makeRequests(func, params)
[pool.putRequest(req) for req in reqs]
```
params可以是一个参数，也可以是一个数组，如果是一个数组就对应一组请求。

如上代码所示，params有4个数据，就会对应创建四个请求，线程池会有对应线程去执行这4个请求。



当然我们也可以自定义线程池
```python

class ThreadPoolManger():
    """线程池管理器"""

    def __init__(self, thread_num):
        # 初始化参数
        self.work_queue = Queue()
        self.thread_num = thread_num
        self.__init_threading_pool(self.thread_num)

    def __init_threading_pool(self, thread_num):
        # 初始化线程池，创建指定数量的线程池
        for i in range(thread_num):
            thread = ThreadManger(self.work_queue)
            thread.start()

    def add_job(self, func, *args):
        # 将任务放入队列，等待线程池阻塞读取，参数是被执行的函数和函数的参数
        self.work_queue.put((func, args))


class ThreadManger(threading.Thread):
    """定义线程类，继承threading.Thread"""

    def __init__(self, work_queue):
        threading.Thread.__init__(self)
        self.work_queue = work_queue
        self.daemon = True

    def run(self):
        # 启动线程
        while True:
            target, args = self.work_queue.get()
            target(*args)
            self.work_queue.task_done()

```

