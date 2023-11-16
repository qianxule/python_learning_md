@[toc]
# 前情知识补充
## hasattr 函数

```python
class ObjectCreator(object):
    pass

my_object = ObjectCreator()

# hasattr用来判断一个对象是否有某个属性，有就是True，没有就是false
print(hasattr(ObjectCreator, 'new_attribute'))

ObjectCreator.new_attribute = 'foo'  # 你可以为类增加属性

print(hasattr(ObjectCreator, 'new_attribute'))
```
输出：
![[image/6aff8bc58e25fa22de495e135bb7c4d0_MD5.png]]
## setattr函数
这个函数是用来给对象赋属性以及对应的值的

使用的方式：
```python
setattr(object 对象, name 字符串 对象属性, value 属性的值)
```

```python
>>>class A(object):
...     bar = 1
... 
>>> a = A()
>>> getattr(a, 'bar')          # 获取属性 bar 值
1
>>> setattr(a, 'bar', 5)       # 设置属性 bar 值
>>> a.bar
5
```

## getattr函数
参考[链接](https://blog.csdn.net/ZauberC/article/details/123176696)

基本使用方式
```python
getattr(object 对象, name 对象属性, default 如果不存在则返回的值)
```
一般用于由用户或者程序自动决定想要访问的属性名时，采用这种方法，并且省去try的异常检测，直接自定义返回值。

样例：

```python
from typing import NamedTuple
 
class Person(NamedTuple):
    '''人类'''
    name: str
    age: int
    job: str
    weight: float
    height: float
 
alex = Person('Alex', 32, 'actor', 60, 178)
 
# 把用户输入的字符串赋值给变量attribute_name
attribute_name = input('''What do you want to know about Alex? 
Enter an attribute name>>>''')
# 注意，上述字符串被传进了这个函数作为第二个参数
# 第三个参数是属性不存在时返回的字符串
print(getattr(alex,attribute_name, 'Sorry, this attribute does not exist.'))

```

## join 函数

参考链接[链接](https://blog.csdn.net/weixin_50853979/article/details/125119368)

这边只介绍元组：

```python

tuple1 = ('a','b','c')  #定义元组tuple1
'、'.join(tuple1)
输出： a、b、c
 
tuple2 = ('hello','peace','world')  #定义元组tuple2
' '.join(tuple2)
输出：hello peace world
```

# 元类技术
先介绍方法，再去介绍相关的概念，感觉比较好理解
## 使用type创建类
先是关于type的基础介绍：

```python
创建出来的类名称 = type("创建出来的类名称", (元组内部放想要继承的类,), {"print_b": print_b, 字典放类函数})
```
样例见下：

```python
class A(object):
    num = 100


def print_b(self):
    print(self.num)


@staticmethod
def print_static():
    print("----haha-----")


@classmethod
def print_class(cls):
    print(cls.num)


B = type("B", (A,), {"print_b": print_b, "print_static": print_static,
                     "print_class": print_class})
b = B()
b.print_b()
b.print_static()
b.print_class()
```

## 什么是元类（概念总结）
这是因为函数 type 实际上是一个元类。type 就是 Python 在背后用来创建所有类的元类。跟int 一样之所以type全是小写，就是根据于此，int是用来创建整形的变量，type就是用来创建类的类。

## \_\_metaclass\_\_属性
python在创建类的时候会经过下面的步骤：
1. 类 中有\_\_metaclass\_\_这个属性吗？如果是，Python 会通过\_\_metaclass\_\_创建
2. 如果 Python 没有找到\_\_metaclass\_\_，它会继续在 父类中寻找
\_\_metaclass\_\_属性，并尝试做和前面同样的操作。
3. 如果 Python 在任何父类中都找不到\_\_metaclass\_\_，它就会在模块层次中去寻找\_\_metaclass\_\_，并尝试做同样的操作。
5. 如果还是找不到\_\_metaclass\_\_,Python 就会用内置的 type 来创建这个类对象

### 使用metaclass 的函数方式进行创建类

```python
def upper_class(class_name, class_parent, class_function: dict):
    """
    这边实现的就是将类元素全部变成大写的
    """
    new_dict = dict()
    for key, function in class_function.items():
        if not key.startswith('__'):
            new_dict[key.upper()] = function

    return type(class_name, class_parent, new_dict)


class Test(object, metaclass=upper_class):
    a = 1


print(Test.a)
print(Test.A)
```

![[image/c42bbff6c19dc19f7886ac36da4b6efb_MD5.png]]
输出的结果：
![[image/a8894d68401aaa5ed157cd3e472d4026_MD5.png]]


### 使用metaclass 的类方式进行创建类

```python
class UpperAttrMetaClass(type):
    # __new__ 是在__init__之前被调用的特殊方法
    # __new__是用来创建对象并返回之的方法
    # 而__init__只是用来将传入的参数初始化给对象
    # 你很少用到__new__，除非你希望能够控制对象的创建
    # 这里，创建的对象是类，我们希望能够自定义它，所以我们这里改写__new__
    # 如果你希望的话，你也可以在__init__中做些事情
    # 还有一些高级的用法会涉及到改写__call__特殊方法，但是我们这里不用
    def __new__(cls, class_name, class_parents, class_attr):
        # 遍历属性字典，把不是__开头的属性名字变为大写
        new_attr = {}
        for name, value in class_attr.items():
            if not name.startswith("__"):
                new_attr[name.upper()] = value
		# 值得关注的是需要用这种形式type.__new__而不是使用 type()
        return type.__new__(cls, class_name, class_parents, new_attr)


# python3的用法
class Foo(object, metaclass=UpperAttrMetaClass):
    bar = 'bip'


print(Foo.BAR)
```

使用type.\_\_new\_\_而不使用type的原因：[链接](https://blog.csdn.net/weixin_44425934/article/details/125263398)

## 自定义元类  
正常人不需要，只有在想要看懂框架，特别是深度学习的时候需要搞懂这门技术，自己也不需要这么写OxO

“元类就是深度的魔法，99%的用户应该根本不必为此操心。如果你想搞清楚究竟是否需要用到元类，那么你就不需要它。那些实际用到元类的人都非常清楚地知道他们需要做什么，而且根本不需要解释为什么要用元类。” —— Python 界的领袖 Tim Peter”

# 元类实现ORM

```python
class ModelMetaclass(type):
    def __new__(cls, name, bases, attrs):
        mappings = dict()
        # 判断是否需要保存
        for k, v in attrs.items():
            # 判断是否是指定的StringField或者IntegerField的实例对象
            if isinstance(v, tuple):
                print('Found mapping: %s ==> %s' % (k, v))
                mappings[k] = v

        # 删除这些已经在字典中存储的属性
        for k in mappings.keys():
            attrs.pop(k)

        # if name == 'User':
        #     attrs.pop('hello')
        
        # 将之前的uid/name/email/password以及对应的对象引用、类名字
        attrs['__mappings__'] = mappings  # 保存属性和列的映射关系
        attrs['__table__'] = name  # 假设表名和类名一致
        return super().__new__(cls, name, bases, attrs)


class Model(object, metaclass=ModelMetaclass):
    def __init__(self, **kwargs):
        for name, value in kwargs.items():
            setattr(self, name, value)

    def save(self):
        fields = []
        args = []
        for k, v in self.__mappings__.items():
            fields.append(v[0])
            args.append(getattr(self, k, None))

        args_temp = list()
        for temp in args:
            # 判断入如果是数字类型
            if isinstance(temp, int):
                args_temp.append(str(temp))
            elif isinstance(temp, str):
                args_temp.append("""'%s'""" % temp)
        sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join(args_temp))
        print('SQL: %s' % sql)


class User(Model):
    uid = ('uid', "int unsigned")
    name = ('username', "varchar(30)")
    email = ('email', "varchar(30)")
    password = ('password', "varchar(30)")


u = User(uid=12345, name='Michael', email='test@orm.org', password='my-pwd')
print(u.__dict__)
u.save()
```

建议一步一步调试看看函数的执行流程。

# 接口类与抽象类

抽象类的定义就是：作为微信支付宝的父类，定义了一个具有支付功能的设计类，但是又没有具体实现，需要子类去实现具体的功能的一种类。

接口类的定义就是：一个接口就是一个类，在这个类当中，我们尽量希望有少 或者 仅有一个方法，也即方法即类，在后面使用的时候，我们使用一个子类去继承一个个接口类，并且都将其实例化到各个类代码当中。

二者使用的场景不同：抽象类希望的是本身具有越多的属性越好，然后子类继承，实例化即可，而接口类希望的是本身方法越少越好，最好自己就是一个行为。

## 抽象类
同样也需要用到元类技术metaclass，以及需要额外导入一个包：
"from abc import abstractmethod, ABCMeta" 使用内部的装饰器@abstractmethod即可创建出一个抽象属性（意思就是子类需要将其定义其功能，而自己只需要写个名字）

```python
from abc import abstractmethod, ABCMeta


# 使用了抽象方法的类，就是抽象类，Payment就是一个抽象类
class Payment(metaclass=ABCMeta):
    @abstractmethod
    def pay(self, money):
        pass


class Alipay(Payment):
    def pay(self, money):  # 这里类的方法不是一致的pay,导致后面调用的时候找不到pay
        print('支付宝支付了')


# 如果子类中没有实现抽象方法，实例化对象时，就会报错
p = Alipay()
```



## 接口类

```python
from abc import abstractmethod, ABCMeta


class WalkAnimal(metaclass=ABCMeta):
    @abstractmethod
    def walk(self):
        pass


class SwimAnimal(metaclass=ABCMeta):
    @abstractmethod
    def swim(self):
        pass


class FlyAnimal(metaclass=ABCMeta):
    @abstractmethod
    def fly(self):
        pass


# 如果正常一个老虎有跑和跑的方法的话，我们会这么做
class Tiger(WalkAnimal, SwimAnimal):
    def walk(self): pass
    def swim(self): pass


t = Tiger()
```


如上，就不进行解释了。


## 注意点
注意点：
1. 抽象类是一个介于类和接口之间的一个概念，同时具备类和接口的部分特性，可以用来实现归一化设计。
2. 在继承抽象类的过程中，我们应该尽量避免多继承；
3. 而在继承接口的时候，我们反而鼓励你来多继承接口， 一般情况下 单继承 能实现的功能都是一样的，所以在父类中可以有一些简单的基础实现，多继承的情况 由于功能比较复杂，所以不容易抽象出相同的功能的具体实现写在父类中。























