---
title: 『Python 工匠』是什么？
toc: true
tags:
  - 编程
  - Python
categories:
  - "\U0001F4BB 工作"
  - "\U0001F40DPython"
  - Python 工匠系列
date: 2020-08-13 18:20:46
---

我一直觉得编程某种意义上是一门『手艺』，因为优雅而高效的代码，就如同完美的手工艺品一样让人赏心悦目。

在雕琢代码的过程中，有大工程：比如应该用什么架构、哪种设计模式。也有更多的小细节，比如何时使用异常（Exceptions）、或怎么给变量起名。那些真正优秀的代码，正是由无数优秀的细节造就的。

『Python 工匠』这个系列文章，是我的一次小小尝试。它专注于分享 Python 编程中的一些偏 **『小』** 的东西。希望能够帮到每一位编程路上的匠人。

本文系列来自此处：[piglei/one-python-craftsman: 来自一位 Pythonista 的编程经验分享，内容涵盖编码技巧、最佳实践与思维模式等方面。](https://github.com/piglei/one-python-craftsman)

## 详细内容

### 1. [善用变量改善代码质量](../1-using-variables-well)

* 如何为变量起名
    * 1 - 变量名要有描述性，不能太宽泛
    * 2 - 变量名最好让人能猜出类型
        * 『什么样的名字会被当成 bool 类型？』
        * 『什么样的名字会被当成 int/float 类型？』
        * 其他类型
    * 3 - 适当使用『匈牙利命名法』
    * 4 - 变量名尽量短，但是绝对不要太短
        * 使用短名字的例外情况
    * 5 - 其他注意事项
* 更好的使用变量
    * 1 - 保持一致性
    * 2 - 尽量不要用 globals()/locals()
    * 3 - 变量定义尽量靠近使用
    * 4 - 合理使用 namedtuple/dict 来让函数返回多个值
    * 5 - 控制单个函数内的变量数量
    * 6 - 及时删掉那些没用的变量
    * 7 - 能不定义变量就不定义
* 结语

### 2. [编写条件分支代码的技巧](../2-if-else-block-secrets)

* 最佳实践
    * 1 - 避免多层分支嵌套
    * 2 - 封装那些过于复杂的逻辑判断
    * 3 - 留意不同分支下的重复代码
    * 4 - 谨慎使用三元表达式
* 常见技巧
    * 1 - 使用“德摩根定律”
    * 2 - 自定义对象的“布尔真假”
    * 3 - 在条件判断中使用 all() / any()
    * 4 - 使用 try/while/for 中 else 分支
* 常见陷阱
    * 1 - 与 None 值的比较
    * 2 - 留意 and 和 or 的运算优先级
* 结语
* 注解

### 3. [使用数字与字符串的技巧](../3-tips-on-numbers-and-strings)

* 最佳实践
    * 1 - 少写数字字面量
        * 使用 enum 枚举类型改善代码
    * 2 - 别在裸字符串处理上走太远
    * 3 - 不必预计算字面量表达式
* 实用技巧
    * 1 - 布尔值其实也是“数字”
    * 2 - 改善超长字符串的可读性
        * 当多级缩进里出现多行字符串时
    * 3 - 别忘了那些 “r” 开头的内建字符串函数
    * 4 - 使用“无穷大” float("inf")
* 常见误区
    * 1 - “value += 1” 并非线程安全
    * 2 - 字符串拼接并不慢
* 结语

### 4. [容器的门道](../4-mastering-container-types)

* 底层看容器
    * 写更快的代码
        * 1 - 避免频繁扩充列表/创建新列表
        * 2 - 在列表头部操作多的场景使用 deque 模块
        * 3 - 使用集合/字典来判断成员是否存在
* 高层看容器
    * 写扩展性更好的代码
        * 面向容器接口编程
* 常用技巧
    * 1 - 使用元组改善分支代码
    * 2 - 在更多地方使用动态解包
    * 3 - 使用 next() 函数
    * 4 - 使用有序字典来去重
* 常见误区
    * 1 - 当心那些已经枯竭的迭代器
    * 2 - 别在循环体内修改被迭代对象
* 总结
* 系列其他文章
* 注解

### 5. [让函数返回结果的技巧](../5-function-returning-tips)

* 编程建议
   * 1 - 单个函数不要返回多种类型
   * 2 - 使用 partial 构造新函数
   * 3 - 抛出异常，而不是返回结果与错误
   * 4 - 谨慎使用 None 返回值
      * 1 - 作为操作类函数的默认返回值
      * 2 - 作为某些“意料之中”的可能没有的值
      * 3 - 作为调用失败时代表“错误结果”的值
   * 5 - 合理使用“空对象模式”
   * 6 - 使用生成器函数代替返回列表
   * 7 - 限制递归的使用
* 总结
* 附录

### 6. [异常处理的三个好习惯](../6-three-rituals-of-exceptions-handling)

* 前言
* 三个好习惯
   * 1 - 只做最精确的异常捕获
   * 2 - 别让异常破坏抽象一致性
   * 3 - 异常处理不应该喧宾夺主
* 总结
* 附录

### 7. [编写地道循环的两个建议](../7-two-tips-on-loop-writing)

* 前言
* 什么是“地道”的循环？
   * enumerate() 所代表的编程思路
* 建议 1：使用函数修饰被迭代对象来优化循环
   * 1 - 使用 product 扁平化多层嵌套循环
   * 2 - 使用 islice 实现循环内隔行处理
   * 3 - 使用 takewhile 替代 break 语句
   * 4 - 使用生成器编写自己的修饰函数
* 建议 2：按职责拆解循环体内复杂代码块
   * 复杂循环体如何应对新需求
   * 使用生成器函数解耦循环体
* 总结
* 附录

### 8. [使用装饰器的技巧](../8-tips-on-decorators)

* 前言
* 最佳实践
   * 1 - 尝试用类来实现装饰器
   * 2 - 使用 wrapt 模块编写更扁平的装饰器
* 常见错误
   * 1 - “装饰器”并不是“装饰器模式”
   * 2 - 记得用 functools.wraps() 装饰内层函数
   * 3 - 修改外层变量时记得使用 nonlocal
* 总结
* 附录

### 9. [一个关于模块的小故事](../9-a-story-on-cyclic-imports)

* 前言
* 一个关于模块的小故事
   * 需求变更
   * 解决环形依赖问题
   * 小 C 的疑问
* 总结
* 附录

### 10. [做一个精通规则的玩家](../10-a-good-player-know-the-rules)

* 前言
   * Python 里的规则
* 案例：从两份旅游数据中获取人员名单
   * 第一次蛮力尝试
   * 尝试使用集合优化函数
   * 对问题的重新思考
   * 利用集合的游戏规则
   * 使用 dataclass 简化代码
   * 案例总结
* 其他规则如何影响我们
   * 使用 `__format__` 做对象字符串格式化
   * 使用 `__getitem__` 定义对象切片操作
* 总结
* 附录

### 11. [高效操作文件的三个建议](../11-three-tips-on-writing-file-related-codes)

* 前言
* 建议一：使用 pathlib 模块
   * 使用 pathlib 模块改写代码
   * 其他用法
* 建议二：掌握如何流式读取大文件
   * 标准做法的缺点
   * 使用 read 方法分块读取
   * 利用生成器解耦代码
* 建议三：设计接受文件对象的函数
   * 如何编写兼容二者的函数
* 总结
* 附录
* 注解

### 12. [写好面向对象代码的原则（上）](../12-write-solid-python-codes-part-1)

* 前言
   * Python 对 OOP 的支持
   * SOLID 设计原则
* SOLID 原则与 Python
* S：单一职责原则
   * 违反“单一职责原则”的坏处
   * 拆分大类为多个小类
   * 另一种方案：使用函数
* O：开放-关闭原则
   * 如何违反“开放-关闭原则”
   * 使用类继承来改造代码
   * 使用组合与依赖注入来改造代码
   * 使用数据驱动思想来改造代码
* 总结
* 附录

### 13. [写好面向对象代码的原则（中）](../13-write-solid-python-codes-part-2)

* 前言
* 里氏替换原则与继承
* L：里氏替换原则
* 一个违反 L 原则的样例
   * 不当继承关系如何违反 L 原则
   * 一个简单但错误的解决办法
   * 正确的修改办法
* 另一种违反方式：子类修改方法返回值法返回值)
   * 分析类方法返回结果
   * 如何修改代码
   * 方法参数与 L 原则
* 总结
* 附录

### 14. [写好面向对象代码的原则（下）](../14-write-solid-python-codes-part-3)

* 前言
* D：依赖倒置原则
   * 需求：按域名分组统计 HN 新闻数量
   * 为 SiteSourceGrouper 编写单元测试
      * 使用 mock 模块
   * 实现依赖倒置原则
   * 依赖倒置后的单元测试
   * 问题：一定要使用抽象类 abc 吗？
   * 问题：抽象一定是好东西吗？
* I：接口隔离原则
   * 例子：开发页面归档功能
   * 问题：实体类不符合 HNWebPage 接口规范
   * 成功违反 I 协议
   * 如何分拆接口
   * 一些不容易发现的违反情况
   * 现实世界中的接口隔离
* 总结
* 附录

### 15. [在边界处思考](../15-thinking-in-edge-cases)

* 前言
* 第一课：使用分支还是异常？
    * 获取原谅比许可简单(EAFP)
* 当容器内容不存在时
    * 使用 defaultdict 改写示例
    * 使用 setdefault 取值并修改
    * 使用 dict.pop 删除不存在的键
    * 当列表切片越界时
* 好用又危险的 “or” 操作符
* 不要手动去做数据校验
* 不要忘记做数学计算
* 总结
* 附录
