---
title: "Python中的self用法"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "python 编程笔记"
lightgallery: true
tags: [python]
categories: [python]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


<!-- TOC -->

- [1. python 中的类](#1-python-%E4%B8%AD%E7%9A%84%E7%B1%BB)
- [2. 类 class 中为啥用使用 self](#2-%E7%B1%BB-class-%E4%B8%AD%E4%B8%BA%E5%95%A5%E7%94%A8%E4%BD%BF%E7%94%A8-self)
- [3. 在Python中类的定义](#3-%E5%9C%A8python%E4%B8%AD%E7%B1%BB%E7%9A%84%E5%AE%9A%E4%B9%89)
- [4. def __init__self 方法](#4-def-__init__self-%E6%96%B9%E6%B3%95)
- [5. 为啥要使用 self](#5-%E4%B8%BA%E5%95%A5%E8%A6%81%E4%BD%BF%E7%94%A8-self)
- [6. self 到底是什么](#6-self-%E5%88%B0%E5%BA%95%E6%98%AF%E4%BB%80%E4%B9%88)

<!-- /TOC -->
## 0.1. python 中的类


在Python类中规定，函数的第一个参数是实例对象本身，并且约定俗成，把其名字写为self。其作用相当于java中的this，表示当前类的对象，可以调用当前类中的属性和方法。

class是面向对象的设计思想，instance（也即是 object，对象）是根据 class 创建的。一个类（class）应该包含数据和操作数据的方法，通俗来讲就是属性和函数（即调用方法）。

## 0.2. 类 class 中为啥用使用 self

在类的代码（函数）中，需要访问当前的实例中的变量和函数，即访问Instance中的：

对应的变量（property)：Instance.ProperyNam，去读取之前的值和写入新的值。

调用对应函数（function）：Instance.function()，即执行对应的动作。

- 需要访问实例的变量和调用实例的函数，当然需要对应的实例Instance对象本身。
- Python中就规定好了，函数的第一个参数，就必须是实例对象本身，并且建议，约定俗成，把其名字写为self。
- 所以，我们需要self（需要用到self）。

## 0.3. 在Python中类的定义

在python中，类是通过关键字 class 定义的：

class 后面紧跟类名，即 Person，类名通常大写字母开头，紧接着是(object),表示该类是从哪个类继承下来的，通常，如果没有合适的 继承类，就使用 object 类，这是所有类最终都会继承的类。

```python
class Person（object）:
	pass
```

将 Person类实例化，创建实例化是通过 类名+() 实现的。

```python
class Person(object):
	pass

student = Person()  # 创建类的实例化
print(student)
print(Person)

---------------------------------
输出：
<__main__.Person object at 0x7f8a4bb14e20>
<class '__main__.Person'>
```

可以看到，变量 student 指向的就是一个 Person的 object，后面的 0x7f8a4bb14e20 是内存地址，每个 object 的地址都不一样，而 Person 本身则是一个类。

也可以给实例变量绑定属性，比如：为 student 绑定 name 和 score 属性

```python
class Person(object):
    pass
student = Person()
# print(student)
# print(Person)
student.name = "Gavin"   # 为实例变量
# student 绑定 name 属性  类似于 赋值 操作
student.score = 100    # 为 其绑定
# score 属性
print(student.name)
print(student.score)

---------------------------------
输出：
Gavin
100
```



## 0.4. def __init__(self) 方法

上述的方法虽然可以为类的实例变量绑定属性，但是不够方便和elegant , 由于类 可以起到模板的作用，故在创建实例的时候，可以将我们认为必须绑定 属性 强制填写进去，在python中，是通过 类中通常都会使用的一个方法，即def __init__(self) 方法，在创建实例变量的时候，就把 name 和 score 等属性绑上去。

```python
class Person(object):
    def __init__(self,name,score):
        self.name = name
        self.score = score

student = Person('Gavin',100)  # 传入 __init__ 方法中需要的参数
print(student.name)
print(student.score)

---------------------------------
输出：
Gavin
100
```

传入空参数的情况，会报错：

```python
class Person(object):
    def __init__(self,name,score):
        self.name = name
        self.score = score

student = Person()
print(student.name)
print(student.score)

---------------------------------
输出：
  File "/mnt/DATA/code/COMMON/Program/python/self/self2.py", line 6, in <module>
    student = Person()
TypeError: __init__() missing 2 required positional arguments: 'name' and 'score'
```

注意：

1、__init__ 方法的第一个参数永远是 self ，表示创建的实例本身，因此，在 __init__ 方法的内部，就可以把各种属性绑定到 self，因为 self 就指向创建的实例本身。

2、使用了 __init__ 方法，在创建实例的时候就不能传入 空的参数了，必须传入与 __init__ 方法匹配的参数，但是 self 不需要传，python解释器会自己把实例变量传进去。

在类中定义多个函数相互调用

```python
class Person(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def add(self):
        sum = self.x + self.y
        return sum

    def square(self):
        squr = pow(self.x, 2) + pow(self.y, 2)
        return squr

    def add_square(self):
        c = self.add() + self.square()
        return c


student = Person(3, 4)
print(student.add())
print(student.square())
print("--------- 我是可爱的分割线-----------")
print(student.add_square())

---------------------------------
输出：
7
25
--------- 我是可爱的分割线-----------
32
```

通过上述的例子可以看出，与普通的函数相比，在类中定义的函数只有两点不同：

1、第一个参数永远是 self ，并且调用时不用传递该参数

2、在类中函数相互调用要加 self ，如上例中： c = self.add()+self.square(), 不加 self ，会报错： 函数未定义。

除此之外，类的方法和普通函数没甚区别，当然也可以使用 默认参数、可变参数和关键字参数，例子如下：

```python
class Person(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def add(self, z=16):  # 设置 默认变量 z =16,这只是个普通的局部变量，非实例变量，实例变量需要 self.z = z,这样定义
        sum = self.x + self.y + z
        return sum

    def square(self):
        squr = pow(self.x, 2) + pow(self.y, 2)
        return squr

    def add_square(self, z):  # 调用时传入变量，这也是个普通的局部变量，非实例变量
        c = self.add() + self.square() + z
        return c


student = Person(3, 4)
print(student.add())
print(student.square())
print("--------- 我是可爱的分割线-----------")
print(student.add_square(16))

---------------------------------
输出：
23
25
--------- 我是可爱的分割线-----------
64
```

## 0.5. 为啥要使用 self

看了上述的例子可能还是不明白 self 到底是个什么鬼，为啥要使用 self 这鬼东西？没关系，往下看：

其实 self 这家伙简单的说就是把 class 中 定义的 **变量和函数** 变成 **实例变量**和**实例函数**，作为类 class 的成员，使得成员间能互相调用，而不需要从外部调用 数据（变量）和 方法（函数），以实现数据的封装，以上面的 Person 类为例：

创建实例的时候需要给出实例变量 x,y, 调用函数时给出 z ，调用很容易，却不知道内部实现的细节。

总之，类是创建实例的模板，而实例则是一个一个具体的对象，各个实例拥有的数据都相互独立、互不影响；方法是与实例绑定的函数，和普通的函数不同，方法可以直接访问实例的数据。

其实 self 中存储的是实例变量和实例函数的属性，可以理解为一个字典（ dict ），如：`{'name':'zhang','age':'18'}`就是这些。

注意只有数据属性，并没有创建新的类的方法。 类----->通过实例化生成----对象---->（对象只是一串类似于字典的数据，没有把类的里的方法复制给你，python没有new这个方法！）

```python
class Person(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def add(self, z=16):  # 设置 z 为实例变量，即
        self.z = z, z  # 是 class 的一个成员了，而非普通局部变量
        self.z = z
        sum = self.x + self.y + z  # z虽然已被实例化，但是依然可以当作 普通变量来用    r
        return sum

    def square(self):
        squr = pow(self.x, 2) + pow(self.y, 2)
        return squr

    def add_square(self):
        c = self.add() + self.square() + self.z  # 调用实例变量 z
        return c


student = Person(3, 4)
print(student.add())
print(student.square())
print("--------- 我是可爱的分割线-----------")
print(student.add_square())
print(student.z)  # 函数add 中的 z 被实例化以后，就可以利用实例化的方法访问它

---------------------------------
输出：
23
25
--------- 我是可爱的分割线-----------
64
16
```

通过这个例子可以看出， z 本来是 add() 函数的默认形参，通过将其实例化，就可以在其他函数体内调用实例变量z

被实例化以后，就可以利用实例化的方法访问它。

## 0.6. self 到底是什么

```python
class Box(object):
    def __init__(self, boxname, size, color):
        self.boxname = boxname
        self.size = size
        self.color = color  # self就是用于存储对象属性的集合，就算没有属性self也是必备的

    def open(self, myself):
        print(
            "-->用自己的myself，打开那个%s,%s的%s" % (myself.color, myself.size, myself.boxname)
        )
        print("-->用类自己的self，打开那个%s,%s的%s" % (self.color, self.size, self.boxname))

    def close(self):
        print("-->关闭%s，谢谢" % self.boxname)


b = Box("魔盒", "14m", "红色")
b.close()
b.open(b)  # 本来就会自动传一个self，现在传入b，就会让open多得到一个实例对象本身，print看看是什么。
print(b.__dict__)  # 这里返回的就是self本身，self存储属性，没有动作。


---------------------------------
输出：
-->关闭魔盒，谢谢
-->用自己的myself，打开那个红色,14m的魔盒
-->用类自己的self，打开那个红色,14m的魔盒
{'boxname': '魔盒', 'size': '14m', 'color': '红色'}
```

- self代表类的实例，而非类；self 就是 对象/实例 属性集合

- Box 是个类-----》self 实例化------》 b对象/ 实例

- class 抽象体------》实例化------》对象/实例，含有属性：{'boxname':'魔盒', ‘size'：‘14m', 'color':'red'}，即 self

- self 看似是整个对象，实际上清楚地描述了类就是产生对象的过程，描述了 self 就是得到了 对象，所以 self 内的键值可以直接使用

正如自然界中一个有效的对象，必须包括：

1. 描述对象的属性；

2. 对象的方法

所以 self是必须的，也是对象中重要的特性。

看下面的代码，感觉就更神奇了：

```python
class Box(object):
    def myInit(mySelf, boxname, size, color):
        print(mySelf.__dict__)  # 显示为{}空字典
        mySelf.boxname = boxname
        mySelf.__dict__["aa"] = "w"  # 甚至可以像字典一样操作
        mySelf.size = size
        mySelf.color = color  # 自己写一个初始化函数，一样奏效,甚至不用self命名。其它函数当中用标准self
        return mySelf  # 返回给实例化过程一个对象！神奇！并且含有对象属性/字典

    # def __init__(self, boxname, size, color):
    #   self.boxname = boxname
    #   self.size = size
    #   self.color = color #注释掉原来标准的初始化

    def open(self, myself):
        print(self)
        print(
            "-->用自己的myself，打开那个%s,%s的%s" % (myself.color, myself.size, myself.boxname)
        )
        print("-->用类自己的self，打开那个%s,%s的%s" % (myself.color, myself.size, myself.boxname))

    def close(self):
        print("-->关闭%s，谢谢" % self.boxname)


# 经过改造，运行结果和标准初始化没区别
b = Box().myInit("魔盒", "14m", "红色")
# b = Box('魔盒', '14m', '红色')#注释掉原来标准的初始化方法
b.close()
b.open(b)  # 本来就会自动传一个self，现在传入b，就会让open多得到一个实例对象本身，print看看是什么。
print(b.__dict__)  # 这里返回的就是self本身，self存储属性，没有动作。


---------------------------------
输出：
-->关闭魔盒，谢谢
<__main__.Box object at 0x7f0c536c3e20>
-->用自己的myself，打开那个红色,14m的魔盒
-->用类自己的self，打开那个红色,14m的魔盒
{'boxname': '魔盒', 'aa': 'w', 'size': '14m', 'color': '红色'}
```

换个角度来讲，对类的操作有：

1. 定义属性 ；

2. 调用方法

对类的反馈有：

1. 得到属性 ；

2. 执行方法

在 class 类的函数中，为什么 self是必要的，因为 self 是对象的载体，可以理解成一个字典，看下面代码：

```python
class Box(object):
    def myInit(mySelf, boxname, size, color):
        print(mySelf.__dict__)  # 显示为{}空字典
        mySelf.boxname = boxname
        mySelf.__dict__["aa"] = "w"  # 甚至可以像字典一样操作
        mySelf.size = size
        mySelf.color = color  # 自己写一个初始化函数，一样奏效,甚至不用self命名。其它函数当中用标准self
        return mySelf  # 返回给实例化过程一个对象！神奇！并且含有对象属性/字典

    # def __init__(self, boxname, size, color):
    #   self.boxname = boxname
    #   self.size = size
    #   self.color = color #注释掉原来标准的初始化

    def open(self, myself):
        print(self)
        print(
            "-->用自己的myself，打开那个%s,%s的%s" % (myself.color, myself.size, myself.boxname)
        )
        print("-->用类自己的self，打开那个%s,%s的%s" % (myself.color, myself.size, myself.boxname))

    def close(self):
        print("-->关闭%s，谢谢" % self.boxname)


# 经过改造，运行结果和标准初始化没区别
b = Box().myInit("魔盒", "14m", "红色")
# b = Box('魔盒', '14m', '红色')#注释掉原来标准的初始化方法
b.close()
b.open(b)  # 本来就会自动传一个self，现在传入b，就会让open多得到一个实例对象本身，print看看是什么。
print(b.__dict__)  # 这里返回的就是self本身，self存储属性，没有动作。

---------------------------------
输出：
-->关闭魔盒，谢谢
<__main__.Box object at 0x7fe655060e20>
-->用自己的myself，打开那个红色,14m的魔盒
-->用类自己的self，打开那个红色,14m的魔盒
{'boxname': '魔盒', 'aa': 'w', 'size': '14m', 'color': '红色'}
```

注意此处的： mySelf.__dict__['aa'] = 'w' #甚至可以像字典一样操作； 在 b.__dict__ 的结果中显示为：'aa':'w'

故可以把 self 理解成存储 实例化对象属性的字典(dict), self 存储属性，而没有动作执行。

self总是指调用时的类的实例。

python 中一些特殊的实例变量：

1. 私有变量(private),只有内部可以访问，外部不能访问，私有变量是在名称前以两个下划线开头，如：__name，其实私有变量也不是完全不能被外部访问，不能直接访问是因为python解释器对外把 __name 变量改成了 _类名__name,所仍然可以通过 _类名__name 来访问 __name。

2. 在Python中，变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。

3. 以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

