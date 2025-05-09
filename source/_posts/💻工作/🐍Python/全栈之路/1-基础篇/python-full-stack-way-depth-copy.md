---
title: Python 全栈之路系列之深浅拷贝
toc: true
tags:
  - 编码
  - 拷贝
top: 9
categories:
  - "\U0001F4BB 工作"
  - "\U0001F40DPython"
  - 全栈之路
  - 1-基础篇
date: 2020-05-23 18:21:46
---

## 深浅拷贝

深浅拷贝分为两部分，一部分是数字和字符串，另一部分是列表、元组、字典等其他数据类型。

### 数字和字符串

对于`数字`和`字符串`而言，赋值、浅拷贝和深拷贝无意义，因为他们的值永远都会指向同一个内存地址。

```python
# 导入copy模块
>>> import copy
# 定义一个变量var1
>>> var1 = 123
# 输出var1的内存地址
>>> id(var1)
1347747440
>>> var2 = var1
# var2的内存地址和var1相同
>>> id(var2)
1347747440
# 浅拷贝
>>> var3 = copy.copy(var1)
# var3的内存地址和var1相同
>>> id(var3)
1347747440
# 深拷贝
>>> var4 = copy.deepcopy(var1)
# var4的内存地址和var1相同
>>> id(var4)
1347747440
```

### 其他数据类型

对于字典、元祖、列表 而言，进行赋值、浅拷贝和深拷贝时，其内存地址的变化是不同的。

#### 创建一个字典`var1`

```python
var1 = {"k1": "1", "k2": 2, "k3": ["abc", 456]}
```

#### 赋值

赋值，只是创建一个变量，该变量指向原来内存地址，如：

```python
>>> var1 = {"k1": "1", "k2": 2, "k3": ["abc", 456]}
>>> var2 = var1
>>> id(var1)
1937003361288
>>> id(var2)
1937003361288
```

如图所示：

![Python-Day04-01](https://ansheng.me/wp-content/uploads/2016/12/1483017038.png)

#### 浅拷贝

浅拷贝，在内存中只额外创建第一层数据

```python
# 导入拷贝模块
>>> import copy
>>> var1 = {"k1": "1", "k2": 2, "k3": ["abc", 456]}
# 使用浅拷贝的方式
>>> var2 = copy.copy(var1)
# 两个变量的内存地址是不一样的
>>> id(var1)
2084726354952
>>> id(var2)
2084730248008
# 但是他们的元素内存地址是一样的
>>> id(var1["k1"])
2084726207464
>>> id(var2["k1"])
2084726207464
```

如图所示：

![Python-Day04-02](https://ansheng.me/wp-content/uploads/2016/12/1483017066.png)

#### 深拷贝

深拷贝，在内存中将所有的数据重新创建一份（排除最后一层，即：python 内部对字符串和数字的优化）

```python
# 导入拷贝模块
>>> import copy
>>> var1 = {"k1": "1", "k2": 2, "k3": ["abc", 456]}
# 使用深拷贝的方式把var1的内容拷贝给var2
>>> var2 = copy.deepcopy(var1)
# var1和var2的内存地址是不相同的
>>> id(var1)
1706383946760
>>> id(var2)
1706389852744
# var1和var2的元素"k3"内存地址是不相同的
>>> id(var1["k3"])
1706389853576
>>> id(var2["k3"])
1706389740744
# var1和var2的"k3"元素的内存地址是相同的
>>> id(var1["k3"][1])
1706383265744
>>> id(var2["k3"][1])
1706383265744
```

如图所示：

![Python-Day04-03](https://ansheng.me/wp-content/uploads/2016/12/1483017092.png)

## 推荐阅读
[Python 学习系列之值类型与引用类型_answer3lin 的博客-CSDN 博客](https://blog.csdn.net/answer3lin/article/details/86430074)
