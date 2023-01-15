---
title: "Python中的字典"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "python 编程笔记"
lightgallery: true
tags: [python]
categories: [python]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


<!-- TOC -->

- [简介](#%E7%AE%80%E4%BB%8B)
- [字典排序](#%E5%AD%97%E5%85%B8%E6%8E%92%E5%BA%8F)
    - [1. 字典排序](#1-%E5%AD%97%E5%85%B8%E6%8E%92%E5%BA%8F)
    - [2. 字典列表排序](#2-%E5%AD%97%E5%85%B8%E5%88%97%E8%A1%A8%E6%8E%92%E5%BA%8F)
- [计算字典值之和](#%E8%AE%A1%E7%AE%97%E5%AD%97%E5%85%B8%E5%80%BC%E4%B9%8B%E5%92%8C)
- [移除字典点键值](#%E7%A7%BB%E9%99%A4%E5%AD%97%E5%85%B8%E7%82%B9%E9%94%AE%E5%80%BC)
- [合并字典](#%E5%90%88%E5%B9%B6%E5%AD%97%E5%85%B8)
- [使用字典格式化字符串](#%E4%BD%BF%E7%94%A8%E5%AD%97%E5%85%B8%E6%A0%BC%E5%BC%8F%E5%8C%96%E5%AD%97%E7%AC%A6%E4%B8%B2)

<!-- /TOC -->

# 1. 简介

字典是另一种可变容器模型，且可存储任意类型对象。

字典的每个键值 **key=>value** 对用冒号 **:** 分割，每个对之间用逗号(**,**)分割，整个字典包括在花括号 **{}** 中 ,格式如下所示：

```python
d = {key1 : value1, key2 : value2, key3 : value3 }
```

键必须是唯一的，但值则不必。

值可以取任何数据类型，但键必须是不可变的，如字符串，数字。



# 1.1. 字典排序

## 1.2. 字典排序

```python
# coding:utf-8
from random import randint


def key_sort(dict):
    print("按键(key)排序:")
    # sorted(key_value) 返回重新排序的列表
    # 字典按键排序
    for i in sorted(dict):
        print((i, dict[i]), end=" ")
    print("\r")


def value_sort(dict):
    print("按键(value)排序:")
    # sorted(key_value) 返回重新排序的列表
    # 字典按键排序
    for i in sorted(dict.items(), key=lambda kv: (kv[1])):
        print(i)
    print("\r")


def main():
    d = {x: randint(60, 100) for x in "xyzabc"}
    key_sort(d)
    value_sort(d)

# 主函数
if __name__ == "__main__":
    main()

```



## 1.3. 字典列表排序

```python
# coding:utf-8

def main():
    lis = [
        {"name": "Taobao", "age": 100},
        {"name": "Runoob", "age": 7},
        {"name": "Google", "age": 100},
        {"name": "Wiki", "age": 200},
    ]
    print("\r")

    # 通过 age 升序排序
    print("列表通过 age 升序排序: ")
    print(sorted(lis, key=lambda i: i["age"]))

    print("\r")

    # 先按 age 排序，再按 name 排序
    print("列表通过 age 和 name 排序: ")
    print(sorted(lis, key=lambda i: (i["age"], i["name"])))

    print("\r")

    # 按 age 降序排序
    print("列表通过 age 降序排序: ")
    print(sorted(lis, key=lambda i: i["age"], reverse=True))


# 主函数
if __name__ == "__main__":
    main()
```



# 2. 计算字典值之和

```python
# coding:utf-8
from random import randint

def returnSum(Dict):
    sum = 0
    for i in Dict:
        sum = sum + Dict[i]

    return sum


def main():
    d = {x: randint(60, 100) for x in "xyzabc"}
    print(returnSum(d))


# 主函数
if __name__ == "__main__":
    main()

```



# 3. 移除字典点键值

```python
# coding:utf-8
from random import randint


def main():
    test_dict = {x: randint(0, 50) for x in "abcdefg"}
    # 输出原始的字典
    print ("字典移除前 : " + str(test_dict))

    # 使用 del 移除 Zhihu
    del test_dict['a']

    # 使用 pop 移除 Zhihu
    removed_value = test_dict.pop('b')
    print("removed_value : " + str(removed_value))

    # 使用 pop 移除 Zhihu
    new_dict = {key:val for key, val in test_dict.items() if key != 'c'}

    # 输出移除后的字典
    print ("字典移除后 : " + str(test_dict))
    print ("字典移除后 : " + str(new_dict))


# 主函数
if __name__ == "__main__":
    main()
```



# 4. 合并字典

```python
# coding:utf-8
from random import randint

def Merge1(dict1, dict2):
    return(dict2.update(dict1))

def Merge2(dict1, dict2):
    res = {**dict1, **dict2}
    return res

def main():
    d1 = {x: randint(0, 50) for x in "abc"}
    d2 = {x: randint(50, 100) for x in "xyz"}

    Merge1(d1, d2)
    print(d2)

    dict2 = Merge2(d1, d2)
    print(dict2)

# 主函数
if __name__ == "__main__":
    main()
```



# 5. 使用字典格式化字符串

```python
# coding:utf-8
from random import randint


def main():
    # 字符串模板中使用key
    temp = '教程是:%(name)s, 价格是:%(price)010.2f, 出版社是:%(publish)s'
    book = {'name':'Python基础教程', 'price': 99, 'publish': 'C语言中文网'}
    # 使用字典为字符串模板中的key传入值
    print(temp % book)
    book = {'name':'C语言小白变怪兽', 'price':159, 'publish': 'C语言中文网'}
    # 使用字典为字符串模板中的key传入值
    print(temp % book)


# 主函数
if __name__ == "__main__":
    main()
```

