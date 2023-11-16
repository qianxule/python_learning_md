@[TOC]
参考我的别的帖子，此篇博客只进行补充：
[链接](https://blog.csdn.net/micoreal/category_12130085.html)
# 面向对象
[链接](https://blog.csdn.net/Micoreal/article/details/128552968)

==is和\=\=的差别：==
1. is指的是两个变量引用的对象是否是同一个
2. ==指的是引用变量的值是否是同一个

而关于实现多态实际上就是通过实现重写功能来实现的多态功能。

![[image/07f8ecb5fe4847e0a9cfbaac90c7645e_MD5.png]]
![[image/08d6f1a0cb290d42b6f9b8d0da085ee7_MD5.png]]
补充：
## 类属性和类方法
1. 创建出来的对象叫做类的实例
2. 创建对象的动作叫做实例化
3. 对象的属性叫做实例属性
4. 对象调用的方法叫做实例方法

在程序执行时：
1. 对象各自拥有自己的 实例属性
2. 调用对象方法，可以通过 self.
– 访问自己的属性
– 调用自己的方法

结论
1. 每一个对象 都有自己 独立的内存空间，保存各自不同的属性
2. 多个对象的方法，在内存中只有一份，在调用方法时，需要把对象的引用传递到方法内部（知道为啥要搞 self 了吧）

![[image/beb0946700bf0710fcefc75da091fdd9_MD5.png]]
### 类属性
定义：
类属性就是给类对象中定义的属性，通常用来记录与这个类相关的特征，类属性不会用于记录具体对象的特征。

例子：

```python
class Tool(object):
	# 使用赋值语句，定义类属性，记录创建工具对象的总数
	count = 0
	def __init__(self, name):
		self.name = name
		# 针对类属性做一个计数+1
		Tool.count += 1

# 创建工具对象
tool1 = Tool("斧头")
tool2 = Tool("榔头")
tool3 = Tool("铁锹")
# 知道使用 Tool 类到底创建了多少个对象?
print("现在创建了 %d 个工具" % Tool.count)
```
对于count来说就是对应的类属性，而对于count属性来说对应的就是对应的对象属性或者叫实例属性。

而在访问类属性的时候有两种方法：
1. 类名.类属性
2. 对象.类属性 （不推荐）  ==别用==

值得关注的是：
如果使用对象.类属性 = 值 赋值语句，只会给对象添加一个属性，而不会影响到 类属性的值

```python
# 拼接上面的代码
tool3.count = 99
print("工具对象总数 %d" % tool3.count)
print("===> %d" % Tool.count)
# 运行完会发现实际上tool3.count变成tool3的一个新定义的属性了，不是原本的tool的属性。
```

### 类方法
类方法就是针对类对象定义的方法
语法如下：

> @classmethod 
> def 类方法名(cls): 
>      pass

1. 类方法需要用修饰器 @classmethod 来标识，告诉解释器这是一个类方法
2. 类方法的第一个参数应该是cls
– 由哪一个类调用的方法，方法内的cls就是哪一个类的引用
– 这个参数和实例方法的第一个参数是和self类似
3. 通过类名. 调用类方法，调用方法时，不需要传递 cls 参数
4. 在方法内部
– 可以通过 cls. 访问类的属性
– 也可以通过 cls. 调用其他的类方法

例子：

```python
class Tool(object):
	# 使用赋值语句定义类属性，记录所有工具对象的数量
	count = 0
	# 定义类方法
	@classmethod
	def show_tool_count(cls):
		print("工具对象的数量 %d" % cls.count)
		
	def __init__(self, name):
		self.name = name
		# 让类属性的值+1
		Tool.count += 1

# 创建工具对象
tool1 = Tool("斧头")
tool2 = Tool("榔头")
# 调用类方法
Tool.show_tool_count()
```

### 静态方法
在开发时，如果需要在类中封装一个方法，这个方法：==既不需要访问实例属性 或者调用实例方法，也不需要访问类属性或者调用类方法==，这个时候，可以把这个方法封装成一个静态方法，例如打印一些帮助。
语法：

```python
@staticmethod
def 静态方法名():
	pass
```

例子：

```python
class Dog(object):
	@staticmethod
	def run():
	# 不访问实例属性/类属性
	print("小狗要跑...")

# 通过类名.调用静态方法 - 不需要创建对象
Dog.run()
```

总结：
1. 实例方法 —— 方法内部需要访问实例属性
– 实例方法 内部可以使用类名. 访问类属性
2. 类方法 —— 方法内部只需要访问类属性
3. 静态方法 —— 方法内部，不需要访问实例属性和类属性

![[image/d1e74d0c7b401c5a6946709a7a85cfb6_MD5.png]]



# 单例模式
[链接](https://blog.csdn.net/Micoreal/article/details/128690502)

## __new__ 方法
1. 使用类名()创建对象时，Python的解释器首先会调用__new__ 方法为对象
分配空间
2.  __new__ 是一个由object基类提供的内置的静态方法，主要作用有两个：
– 1) 在内存中为对象分配空间
– 2) 返回对象的引用
3. Python的解释器获得对象的引用后，将引用作为第一个参数，传递给
__init__ 方法
重写 __new__ 方法 的代码非常固定！
4. 重写 __new__ 方法 一定要 return super().__new__(cls)
5. 否则Python的解释器得不到分配了空间的对象引用，就不会调用对象的初
始化方法
6. 注意：__new__ 是一个静态方法，在调用时需要主动传递cls参数

## 类实现单例模式

```python
class Single(object):
    _instance = None
    def __new__(cls, *args, **kw):
        if cls._instance is None:
            cls._instance = object.__new__(cls, *args, **kw)
        return cls._instance
    def __init__(self):
        pass

single1 = Single()
single2 = Single()
print(id(single1) == id(single2))
```

补充，但是个人建议使用导包法，或者装饰器方法进行实现，不然实际上这个单例模式会进行覆盖，实际上就是每次进行一次类型的创建，就相当于对于这个地址的相对创建，其中的所有的内容都会进行一次初始化。

![[image/fd9c3dc2f38a875c55b495054f0a8215_MD5.png]]


# 异常 、模块和包
[链接](https://blog.csdn.net/Micoreal/article/details/128454712)

## 异常
==使用异常可以提高程序鲁棒性（稳定性）==

比较好用的模板补充：
Python 如何捕获异常发生的文件和具体行数

```python
try:
	print(a)
except Exception as e:
	print(e)
	print(e.__traceback__.tb_frame.f_globals["__file__"]) # 发生异常所在的文件
	print(e.__traceback__.tb_lineno) # 发生异常所在的行数
```
### 自定义异常
示例：
这个一般用于只应用一俩次的异常
```python
def input_password():
	# 1. 提示用户输入密码
	pwd = input("请输入密码：")
	# 2. 判断密码长度 >= 8，返回用户输入的密码
	if len(pwd) >= 8:
		return pwd
	# 3. 如果 < 8 主动抛出异常
	print("主动抛出异常")
	# 1> 创建异常对象 - 可以使用错误信息字符串作为参数
	ex = Exception("密码长度不够")
	# 2> 主动抛出异常
	raise ex

# 提示用户输入密码
try:
	print(input_password())
except Exception as result:
	print(result)
```

方法二：自定义异常类，不去使用exception：

创建新的异常很简单——定义新的类，让它继承自 Exception （或者是任何一个已存在的异常类型）。 例如，如果你编写网络相关的程序，你可能会定义一些类似如下的异常：

```python
class NetworkError(Exception):
    pass

class HostnameError(NetworkError):
    pass

class TimeoutError(NetworkError):
    pass

class ProtocolError(NetworkError):
    pass
```

然后用户就可以像通常那样使用这些异常了，例如：

```python
try:
    msg = s.recv()
except TimeoutError as e:
    ...
except ProtocolError as e:
    ...
```

![[image/b69295208d389041ec07ac75759780d1_MD5.png]]

## 模块和包
补充：

### 模块的搜索顺序
Python 的解释器在 导入模块 时，会：
1. 搜索当前目录指定模块名的文件，如果有就直接导入
2. 如果没有，再搜索系统目录
在开发时，给文件起名，不要和系统的模块文件重名，还有导入的函数也会满足后导入的覆盖前导入的，所以==这边对于from ··· import *是很不推荐的，原因就是后面迟早会发生名字覆盖的问题==。
3. Python 中每一个模块都有一个内置属性 __file__ 可以 查看模块 的 完整路径
示例

```python
import random
print(random.__file__)
rand = random.randint(0, 10)
print(rand)
```

注意：如果当前目录下，存在一个 random.py 的文件，程序就无法正常执行了！这个时候，Python 的解释器会 加载当前目录 下的 random.py 而不会加载 系统的 random 模块。

### 包的init文件
__init__.py
• 要在外界使用包中的模块，需要在 __init__.py 中指定对外界提供的模块列
表

```python
# 从当前目录导入模块列表
# .就是当前文件夹，这里要写的就是文件的相对路径
from . import send_message
from . import receive_messag
```


### 发布模块（了解）
如果希望自己开发的模块，分享给其他人，可以按照以下步骤操作：

1. 制作发布压缩包步骤
新建发布模块的文件夹，然后将 setup.py 放入(setup.py 和要发布的模块在同级目录)
（1） 创建 setup.py

```python
from distutils.core import setup
setup(name="", # 包名
	version="", # 版本
	description="", # 描述信息
	long_description="", # 完整描述信息
	author="", # 作者
	author_email="", # 作者邮箱
	url="", # 主页
	py_modules=["wd_message.send_message",
	"wd_message.receive_message"])
```
（2) 构建模块（ubuntu 下，windows 都可以）

```python
$ python3 setup.py build
```

（3) 生成发布压缩包

```python
$ python3 setup.py sdist
```

注意：要制作哪个版本的模块，就使用哪个版本的解释器执行！
2.  安装模块

```python
$ tar xf wd_message-1.0.tar.gz
$ sudo python3 setup.py install
```

卸载模块
直接从安装目录下，把安装模块的 目录 删除就可以

```python
$ cd /usr/local/lib/python3.6/dist-packages/
$ sudo rm -r wd_message*
```

![[image/9c61e19612685b5bc66eab06489cb8e8_MD5.png]]



# 文件
[链接](https://blog.csdn.net/Micoreal/article/details/128448744)

访问模式扩展：
|访问方式| 说明|
|-|-|
|r| 以只读方式打开文件。文件的指针将会放在文件的开头，这是默认模式。如果文件不存在，抛出异常|
|w| 以只写方式打开文件。如果文件存在会被覆盖。如果文件不存在，创建新文件|
|a| 以追加方式打开文件。如果该文件已存在，文件指针将会放在文件的结尾。如果文件不存在，创建新文件进行写入r+ 以读写方式打开文件。文件的指针将会放在文件的开头。如果文件不存在，抛出异常.|
|w+| 以读写方式打开文件。如果文件存在会被覆盖。如果文件不存在，创建新文件|
|a+| 以读写方式打开文件。如果该文件已存在，文件指针将会放在文件的结尾。如果文件不存在，创建新文件进行写入|

值得关注的是：
==文本模式下：\n 写入磁盘时，会保存为\r\n，当遇到\r\n 时，读到的是\n==，所以对于一个文件流来说，我们打开都尽量以二进制打开，而不要使用文本模式打开。

## seek
seek(offset, from)
offset ：文件指针偏移量(offset 定义为指针偏移量）
from ： 0-文件开头 1-当前位置 2-文件末尾(这里的 0,1,2 只是代表了文件位
置，而不是说 0,1,2 可以参与指针偏移的计算。from 是可选项，默认为 0.)
二进制模式下才可以往前偏


## 文件/目录的常用管理操作
1. 在终端 / 文件浏览器中可以执行常规的 文件 / 目录 管理操作，例如：
– 创建、重命名、删除、改变路径、查看目录内容、……
2. 在 Python 中，如果希望通过程序实现上述功能，需要导入 os 模块

| 方法名 |说明| 示例|
|-|-|-|
|rename| 重命名文件| os.rename(源文件名, 目标文件名)|
|remove| 删除文件,不能删除文件夹|os.remove(文件名）|
|listdir| 目录列表| os.listdir(目录名)|
| mkdir| 创建目录| os.mkdir(目录名)|
| rmdir| 删除目录 |os.rmdir(目录名)|
|getcwd| 获取当前目录| os.getcwd()|
| chdir| 修改工作目录| os.chdir(目标目录)|
| path.isdir| 判断是否是文件夹| os.path.isdir(文件路径)|

![[image/7d77a04fc853c281baa02aa4b50e4f4d_MD5.png]]

## eval函数
eval() 函数十分强大 —— 将字符串当成有效的表达式来求值并返回计算结果，实际上就是==去引号化的最简式==。

例子：
```python
# 基本的数学计算
In [1]: eval("1 + 1")
Out[1]: 2
# 字符串重复
In [2]: eval("'*' * 10")
Out[2]: '**********'
# 将字符串转换成列表
In [3]: type(eval("[1, 2, 3, 4, 5]"))
Out[3]: list
# 将字符串转换成字典
In [4]: type(eval("{'name': 'xiaoming', 'age': 18}"))
Out[4]: dict
```

对于eval的警惕：
==切记不能将eval和前端传回的消息相沟通，只能用之与自己的本地的文件相连，不然很可能出现问题。==


# 补充性知识
## 位运算小技巧

```python
# 找101个数中的出现1次的1个数
def find_list101_one():
    list1 = [8, 3, 2, 6, 3, 8, 2]  # 所有的数都异或起来，就得到了出现1次的那个数
    result = 0
    for i in list1:
        result ^= i
    print(result)
    
```
实际上就是通过异或具有交换律进行了解的，相同的异或成0，不同的异或成非零数，得到，我们只需要将这每一个数都异或起来即可得到唯一一个只出现过一次的一个数。


```python
# 找102个数中的出现1次的两个数，如何把出现1次的两个数分到两堆
def find_list102_one():
    list1 = [8, 3, 2, 6, 3, 8, 2, 11]
    result = 0
    for i in list1:
        result ^= i
    split_flag = result & -result
    result1 = 0
    result2 = 0
    for i in list1:
        if split_flag & i:
            result1 ^= i
        else:
            result2 ^= i
    print('出现1次的两个数分别是 %d %d' % (result1,result2))
```

此处实现全部异或得到一个数，这个数实际上就是那两个数的异或值，比如此题就是6和11的异或值，然后我么将==这个数据和他的负数相异或==，==可以得到最低的不同位不同的最小数（通过补码自己计算一遍）==，然后把这个数去和这个数组在异或一遍，就会将原本的数分成两堆，一堆有6，一堆有11，实际上就是分开的过程，然后再用第一题的方法就可以实现求出两个数。


