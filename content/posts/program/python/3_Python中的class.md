---
title: "Python中的class"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "python 编程笔记"
lightgallery: true
tags: [python]
categories: [python]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


<!-- TOC -->

- [类的成员](#%E7%B1%BB%E7%9A%84%E6%88%90%E5%91%98)
    - [1. 类的属性](#1-%E7%B1%BB%E7%9A%84%E5%B1%9E%E6%80%A7)
        - [1.1. 静态属性的创建方式](#11-%E9%9D%99%E6%80%81%E5%B1%9E%E6%80%A7%E7%9A%84%E5%88%9B%E5%BB%BA%E6%96%B9%E5%BC%8F)
        - [1.2. 实例属性](#12-%E5%AE%9E%E4%BE%8B%E5%B1%9E%E6%80%A7)
    - [2. 类的方法](#2-%E7%B1%BB%E7%9A%84%E6%96%B9%E6%B3%95)
- [类成员的修饰符](#%E7%B1%BB%E6%88%90%E5%91%98%E7%9A%84%E4%BF%AE%E9%A5%B0%E7%AC%A6)
- [类的特殊成员](#%E7%B1%BB%E7%9A%84%E7%89%B9%E6%AE%8A%E6%88%90%E5%91%98)
- [类的专有方法：](#%E7%B1%BB%E7%9A%84%E4%B8%93%E6%9C%89%E6%96%B9%E6%B3%95)
- [继承](#%E7%BB%A7%E6%89%BF)
    - [1. 单继承](#1-%E5%8D%95%E7%BB%A7%E6%89%BF)
    - [2. 多继承](#2-%E5%A4%9A%E7%BB%A7%E6%89%BF)
    - [3. 方法重写](#3-%E6%96%B9%E6%B3%95%E9%87%8D%E5%86%99)
- [运算符重载](#%E8%BF%90%E7%AE%97%E7%AC%A6%E9%87%8D%E8%BD%BD)

<!-- /TOC -->



```python
class Document():
    def __init__(self, title, author, context):
        print('init function called')
        self.title = title
        self.author = author
        self.__context = context # __ 开头的属性是私有属性

    def get_context_length(self):
        return len(self.__context)

    def intercept_context(self, length):
        self.__context = self.__context[:length]

harry_potter_book = Document('Harry Potter', 'J. K. Rowling', '... Forever Do not believe any thing is capable of thinking independently ...')

print(harry_potter_book.title)
print(harry_potter_book.author)
print(harry_potter_book.get_context_length())

harry_potter_book.intercept_context(10)

print(harry_potter_book.get_context_length())

print(harry_potter_book.__context)
```

- 类：一群有着相似性的事物的集合，这里对应 Python 的 class。
- 对象：集合中的一个事物，这里对应由 class 生成的某一个 object，比如代码中的 harry_potter_book。
- 属性：对象的某个静态特征，比如上述代码中的 title、author 和 __context。
- 函数：对象的某个动态能力，比如上述代码中的 intercept_context () 函数。



# 1. 类的成员

类的成员包括：属性和方法。

属性可以分为：静态属性和实例属性

方法可以分为：普通方法、类方法和静态方法。

```python
#!/usr/bin/python3

class MyClass:
    """一个简单的类实例"""
    i = 12345
    def f(self):
        return 'hello world'

# 实例化类
x = MyClass()

# 访问类的属性和方法
print("MyClass 类的属性 i 为：", x.i)
print("MyClass 类的方法 f 输出为：", x.f())
```

## 1.1. 类的属性
属性可以分为：静态属性和实例属性。

实例属性属于对象，而静态属性属于类。

通过类创建对象时，如果每个对象都具有相同的属性，那么就使用静态属性。


### 1.1.1. 静态属性的创建方式
静态属性是属于类的，所以不用创建对象访问。
```python
#!/usr/bin/python3

class Province:

    # 静态字段
    country ＝ '中国'


# 直接访问静态字段
Province.country
```

### 1.1.2. 实例属性

```python
lass Goods:

    def __init__(self):
        # 原价
        self.original_price = 100
        # 折扣
        self.discount = 0.8

    @property
    def price(self):
        # 实际价格 = 原价 * 折扣
        new_price = self.original_price * self.discount
        return new_price

    @price.setter
    def price(self, value):
        self.original_price = value

    @price.deleter
    def price(self):
        del self.original_price
```

实例属性可以在构造方法中进行初始化。@property装饰器可以把一个实例方法变成其同名属性，以支持.号访问。我们可以根据属性的访问特点，分别将三个方法定义为对同一个属性：获取、修改、删除。

扩展：

对于静态属性还可以使用property函数的形式定义一个属性。与@property实现原理类似。

`property(fget=None, fset=None, fdel=None, doc=None)`

```python
class Foo:

    def get_bar(self):
        return 'get_bar'

    # *必须两个参数
    def set_bar(self, value):
        return 'set value' + value

    def del_bar(self):
        return 'del_bar'

    age = property(fget=get_bar,fset=set_bar,fdel=del_bar,doc='description...')
```



## 1.2. 类的方法

方法包括：普通方法、类方法和静态方法。

- 普通方法：由**对象**调用；至少一个**self**参数；执行普通方法时，自动将调用该方法的**对象**赋值给**self**；

- 类方法：由**类**调用； 至少一个**cls**参数；执行类方法时，自动将调用该方法的**类**复制给**cls**；

- 静态方法：由**类**调用；无默认参数；

如果Python中没有属性，方法完全可以代替其功能。

```python
class Foo:

    def __init__(self, name):
        self.name = name

    def ord_func(self):
        """ 定义普通方法，至少有一个self参数 """
        # print self.name
        print('普通方法')

    @classmethod
    def class_func(cls):
        """ 定义类方法，至少有一个cls参数 """
        print('类方法')

    @staticmethod
    def static_func():
        """ 定义静态方法 ，无默认参数"""
        print('静态方法')
```



# 2. 类成员的修饰符

对于每一个类的成员而言都有两种形式：

公有成员，在任何地方都能访问。

私有成员，只有在类的内部才能方法。

**私有成员和公有成员的定义不同**：私有成员命名时，前两个字符是下划线。（特殊成员除外，例如：__init__、__call__、__dict__等）

ps：如果想要强制访问私有字段，可以通过 【对象._类名__私有字段明 】访问（如：obj._C__foo），不建议强制访问私有成员。



```python
class C:
	name = '公有静态字段'
	__sname ='私有静态字段'

	def pub_func(self):
		print(C.name)

	def pra_func(self):
		print(C._sname)

class D(C):

	def pub_show(self):
		print(C.name)

	def pra_show(self):
		print(C._sname)
```

注：不建议强制访问私有成员。



# 3. 类的特殊成员

- \_\_doc\_\_ : 表示类的描述信息。

- \_\_module\_\_ : 表示当前操作的对象在哪个模块

- \_\_class\_\_ : 表示当前操作的对象的类是什么

- \_\_init\_\_ : 构造方法，通过类创建对象时，自动触发执行。

```python
#!/usr/bin/python3

class Complex:
    def __init__(self, realpart, imagpart):
        self.r = realpart
        self.i = imagpart
x = Complex(3.0, -4.5)
print(x.r, x.i)   # 输出结果：3.0 -4.5
```



- \_\_del\_\_ : 当对象在内存中被释放时，自动触发执行。

- \_\_call\_\_ : 对象后面加括号，触发执行。

- \_\_dict\_\_ : 类或对象中的所有成员。

- \_\_str\_\_ : 如果一个类中定义了\_\_str\_\_方法，那么在打印 对象 时，默认输出该方法的返回值。有点像java中的toString方法。

- \_\_getitem\_\_、\_\_setitem\_\_、\_\_delitem\_\_ : 用于索引操作，如字典。以上分别表示获取、设置、删除数据。

- \_\_getslice\_\_、\_\_setslice\_\_、\_\_delslice\_\_ : 三个方法用于分片操作。

-  \_\_iter\_\_ : 用于迭代器，之所以列表、字典、元组可以进行for循环，是因为类型内部定义了 \_\_iter\_\_。


# 4. 类的专有方法：

- \_\_init\_\_ : 构造函数，在生成对象时调用
- \_\_del\_\_ : 析构函数，释放对象时使用
- \_\_repr\_\_ : 打印，转换
- \_\_setitem\_\_ : 按照索引赋值
- \_\_getitem\_\_: 按照索引获取值
- \_\_len\_\_: 获得长度
- \_\_cmp\_\_: 比较运算
- \_\_call\_\_: 函数调用
- \_\_add\_\_: 加运算
- \_\_sub\_\_: 减运算
- \_\_mul\_\_: 乘运算
- \_\_truediv\_\_: 除运算
- \_\_mod\_\_: 求余运算
- \_\_pow\_\_: 乘方

# 5. 继承

## 5.1. 单继承

Python 同样支持类的继承，如果一种语言不支持继承，类就没有什么意义。派生类的定义如下所示:

```python
class DerivedClassName(BaseClassName):
    <statement-1>
    .
    .
    .
    <statement-N>
```

子类（派生类 DerivedClassName）会继承父类（基类 BaseClassName）的**属性和方法**。

BaseClassName（实例中的基类名）必须与派生类定义在一个**作用域内**。除了类，还可以用表达式，基类定义在另一个模块中时这一点非常有用:

```python
class DerivedClassName(modname.BaseClassName):
```



```python
#!/usr/bin/python3

#类定义
class people:
    #定义基本属性
    name = ''
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问
    __weight = 0
    #定义构造方法
    def __init__(self,n,a,w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name,self.age))

#单继承示例
class student(people):
    grade = ''
    def __init__(self,n,a,w,g):
        #调用父类的构函
        people.__init__(self,n,a,w)
        self.grade = g
    #覆写父类的方法
    def speak(self):
        print("%s 说: 我 %d 岁了，我在读 %d 年级"%(self.name,self.age,self.grade))



s = student('ken',10,60,3)
s.speak()
```



## 5.2. 多继承

```python
class DerivedClassName(Base1, Base2, Base3):
    <statement-1>
    .
    .
    .
    <statement-N>
```

需要注意圆括号中父类的顺序，若是父类中有相同的方法名，而在子类使用时未指定，python从左至右搜索 即方法在子类中未找到时，从左到右查找父类中是否包含方法。

```python
#!/usr/bin/python3

#类定义
class people:
    #定义基本属性
    name = ''
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问
    __weight = 0
    #定义构造方法
    def __init__(self,n,a,w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name,self.age))

#单继承示例
class student(people):
    grade = ''
    def __init__(self,n,a,w,g):
        #调用父类的构函
        people.__init__(self,n,a,w)
        self.grade = g
    #覆写父类的方法
    def speak(self):
        print("%s 说: 我 %d 岁了，我在读 %d 年级"%(self.name,self.age,self.grade))

#另一个类，多重继承之前的准备
class speaker():
    topic = ''
    name = ''
    def __init__(self,n,t):
        self.name = n
        self.topic = t
    def speak(self):
        print("我叫 %s，我是一个演说家，我演讲的主题是 %s"%(self.name,self.topic))

#多重继承
class sample(speaker,student):
    a =''
    def __init__(self,n,a,w,g,t):
        student.__init__(self,n,a,w,g)
        speaker.__init__(self,n,t)

test = sample("Tim",25,80,4,"Python")
test.speak()   #方法名同，默认调用的是在括号中参数位置排前父类的方法
```



## 5.3. 方法重写

如果你的父类方法的功能不能满足你的需求，你可以在子类重写你父类的方法，实例如下：

```python
#!/usr/bin/python3

class Parent:        # 定义父类
   def myMethod(self):
      print ('调用父类方法')

class Child(Parent): # 定义子类
   def myMethod(self):
      print ('调用子类方法')

c = Child()          # 子类实例
c.myMethod()         # 子类调用重写方法
super(Child,c).myMethod() #用子类对象调用父类已被覆盖的方法
```

super() 函数是用于调用父类(超类)的一个方法。

执行以上程序输出结果为：



# 6. 运算符重载

Python同样支持运算符重载，我们可以对类的专有方法进行重载，实例如下：

```python
#!/usr/bin/python3

class Vector:
   def __init__(self, a, b):
      self.a = a
      self.b = b

   def __str__(self):
      return 'Vector (%d, %d)' % (self.a, self.b)

   def __add__(self,other):
      return Vector(self.a + other.a, self.b + other.b)

v1 = Vector(2,10)
v2 = Vector(5,-2)
print (v1 + v2)
```

