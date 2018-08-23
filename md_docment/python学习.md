## python基础之数据类型和变量

### 一、**整数**

Python可以处理任意大小的整数，当然包括负整数，在程序中的表示方法和数学上的写法一模一样，例如：1，100，-8080，0，等等。

### 二、**浮点数**

浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，比如，1.23x10^9和12.3x10^8是相等的。浮点数可以用数学写法，如1.23，3.14，-9.01，等等。但是对于很大或很小的浮点数，就必须用科学计数法表示，把10用e替代，1.23x109就是1.23e9，或者12.3e8，0.000012可以写成1.2e-5，等等。

整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（除法难道也是精确的？是的！），而浮点数运算则可能会有四舍五入的误差。

### 三、**字符串**

字符串是以单引号`''`或双引号`""` 括起来的任意文本，比如'abc'，"xyz"等等。

`'''...'''`三引号可以用来显示多行字符串内容，如：

```python
>>> print '''line1
... line2
... line3'''
line1
line2
line3
```

如涉及到中文字符，尽量在字符串前面加u，如`u'张三'`

%s表示用字符串替换，%d表示用整数替换，有几个%?占位符，后面就跟几个变量或者值，顺序要对应好。

```python
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

**format()**

另一种格式化字符串的方法是使用字符串的`format()`方法，它会用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……，不过这种方式写起来比%要麻烦得多：

```
'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
```



字符串拼接直接对两字符串相加即可

```python
>>> 'hello' + ' world!'
'hello world!'
```



注意：

由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```



### 四、布尔值

布尔值只有`True`、`False`两种值。

### 五、空值

空值是Python里一个特殊的值，用`None`表示。`None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值

### 六、操作符

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

### 七、变量

变量在程序中就是用一个变量名表示了，变量名必须是大小写英文、数字和`_`的组合，且不能用数字开头。

如：

```python
a = 1
```

变量`a`是一个整数。

```python
t_007 = 'T007'
```

变量`t_007`是一个字符串。

```python
Answer = True
```

变量`Answer`是一个布尔值`True`。

这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言，赋值语句如下（// 表示注释）：

```
int a = 123; // a是整数类型变量
a = "ABC"; // 错误：不能把字符串赋给整型变量
```

和静态语言相比，动态语言更灵活，就是这个原因。



也可以把一个变量`a`赋值给另一个变量`b`，这个操作实际上是把变量`b`指向变量`a`所指向的数据，例如下面的代码：

```python
a = 'ABC'
b = a
a = 'XYZ'
print b
```

最后打印的b即是：`'ABC'`



**常量**

所谓常量就是不能变的变量，比如常用的数学常数π就是一个常量。在Python中，通常用全部大写的变量名表示常量：

```
PI = 3.14159265359
```

但事实上`PI`仍然是一个变量，Python根本没有任何机制保证`PI`不会被改变，所以，用全部大写的变量名表示常量只是一个习惯上的用法，如果你一定要改变变量`PI`的值，也没人能拦住你。

在计算机内部，可以把任何数据都看成一个“对象”，而变量就是在程序中用来指向这些数据对象的，对变量赋值就是把数据和变量给关联起来。

### 八、List和tuple

**List**

这就是Java或C中的数组.它是一个容器,能用来顺序的,以整数索引方式检索, 存储一组对象. **List** 用 **[]** 来表示,如 **[1, 2, 3]** 就是一个List；而 **Tuple** 用 **()** 来表示,如 **(3, 4, 5)** 就是一个 **Tuple** .  

它们的区别在于List是可变的；而Tuple是不可变的.也就是说不可以增,删和改.  

索引方式除了与Java一样的以一个整数下标方式外,还可以指定开始,结束和步长,和使用负索引来分割List:  

通用语法格式是: **list[start: end: step]**  

```python
list[index] --- 返回第(index+1)个元素,下标从0开始  
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

```
classmates = ('Michael', 'Bob', 'Tracy')
```

现在，classmates这个tuple不能变了，它也没有append()，insert()这样的方法。其他获取元素的方法和list是一样的，你可以正常地使用classmates[0]，classmates[-1]，但不能赋值成另外的元素。

tuple里面的元素如果是一个可变对象，那么这个对象的子属性是可以被改变的，对象本身的索引是不可改变的。

如tuple里面包含一个listA，我们可以往listA里面append，insert，但是你不能对listA赋值，不能删掉listA。



### 九、dict和set

**dict**

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


**set**

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


### 十、分支语句

格式为:

```python
if expression:  
      blocks;  
elif expression2:  
      blocks;  
else:  
      blocks; 
```
### 十一、循环

 **for循环**

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

**while循环**

格式：  

```python
while expression:
      blocks
```