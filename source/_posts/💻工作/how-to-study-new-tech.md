---
title: 如何学习一门新技术
toc: true
tags:
  - 学习
  - 方法论
categories:
  - "\U0001F4BB 工作"
date: 2020-05-26 12:27:56
---

## 定义技术

首先来定义一下 **技术** 的概念。技术是 **知识** 的一种，如果按照弗里茨-马克卢普的分类法，技术应该属于 **实用知识**。如果按照 Towers 的分类法，技术应该属于 **实践性知识**。如果按照波兰尼的分类法，技术应该属于 **关于技能和诀窍方面的知识。**

> 弗里茨-马克卢普根据知识的实用价值把知识分为五类:实用知识、学术知识、闲谈与消遣知识、精神知识、不需要的知识。
>
> 一九六六年,美国俄亥俄州立大学教授陶尔士 (Towers)、陆克斯 (Lux)和雷(Ray)等人,受 Maccia 系统化知识思辨理论的影响,将人类知识分类成「描述性的知识」、「规范性的知识」、「实践性的知识」、与「形式性的知识」四大领域.
>
> 经济合作与发展组织(OECD)在 1996 年发表的《以知识为基础的经济》报告中,以波兰尼的知识分类为基础,进而把人类的知识分为四大类:关于事实和现实的知识、关于自然规律和原理方面的知识、关于技能和诀窍方面的知识、关于人力资源方面的知识。前两类为可编码的显性知识,后两类为不能明言的意会知识。

2 月份 [SAGI 读书会](https://blog.zengrong.net/tag/sagibookclub/) 选书 [《俞军产品方法论》](https://blog.zengrong.net/post/reading-notes-yujun/) 中也提到了关于企业知识传承的问题，在这段话中可以看出，技术属于可以用语言文字符号传递的 **显性知识。**

> 生产效率高于市场平均水平的企业才能得以延续。企业使用 权威 的力量来提高效率。利用各岗位的专家表现出超出市场平均水准的更优决策来战胜市场。企业的核心竞争力是专业知识，专业知识中的 显性知识 可用语言文字符号传递，但 默会知识 只可意会不可言传。企业管理者通过设计内部惯例和制度，将一部分专有 默会知识 转换成 企业共同知识，从而提升提升组织效率。

在这篇文章中，我把 **技术** 定义为： **可以通过现有资料进行全面掌握的解决实际问题的知识与能力。**

从上面的定义可以看出，由于技术解决的是 **实际问题**，不可避免地，技术会根据现状的变化出现过时的情况。由于可以 **通过现有资料全面掌握**，时效性就显得尤为重要。

##  锚

找到正确的资料，才能准确掌握技术。当今的互联网上，优质和劣质的资料混杂在一起，我们必须寻找到特定的锚才能判断资料的质量。

对于程序开发技术，我主要用下面几个锚：

1.  该技术的官方网站/官方文档。
2.  社区。
3.  问答网站。
4.  经典书籍。
5.  相关联的技术。

下面展开讲一下。

### 官方文档

官方网站/官方文档永远是你学习这门技术时首先阅读的。一个值得学习的技术，应该是相对优秀的。如果它的官方文档不能让你满意，那就只能说明两件事：

1.  该技术还不够有名，可能属于项目早期。
2.  该技术由于维护人员太少而有维护风险。

在技术百花齐放的今天，对于一门技术，你往往能找到大量的替代者。此时需要睁大眼睛挑选，如果在选择上出了问题，可能会浪费大量的学习时间。如果官方文档很不完善，可以观察项目是否处于早期阶段。早期阶段的技术需要谨慎使用，如果学有余力，可以为早期技术贡献代码，当然这就是另一个故事了。

在文档不完善的情况下，你可能需要大量阅读源码才能完成对资料的理解，孰优孰劣，要看个人。

下面是我之前做技术选型时候的一些选择，供参考：

*   [桌面软件 GUI 开发框架](https://blog.zengrong.net/post/choose-a-gui-framework/)
*   [SaltStack/Ansible/Fabric 的选择](https://blog.zengrong.net/post/choose-in-salt-ansible-fabric/)
*   [Python web 框架的选择](https://blog.zengrong.net/post/python_web_frameworks/)
*   [Golang Resources](https://blog.zengrong.net/post/golang-resources/)

### 技术社区

Github 是目前最大的社区，大量的开源项目会在 Github 上托管自己的仓库，并建立互动社区。你需要关注该技术的 Issue 和版本发布信息。有些技术依然在使用古老但有效的邮件列表，例如 [uWSGI](http://lists.unbit.it/cgi-bin/mailman/listinfo/uwsgi)。入乡随俗使用它们，观察参与社区讨论的热度和话题的回复速度，能得到正确的锚。

对一些热门技术，社区提供了集合性的资源，例如针对 Vue 的 [Awesome Vue.js](https://github.com/vuejs/awesome-vue) 。

### 问答网站

全球知名的 [Stackoverflow](https://stackoverflow.com/) 是程序员必上的网站。你可以查看一个话题的热度和回复速度，这能够体现该技术的热门程度。热门的技术总是相对更好的选择，这意味着你可以更容易找到一起讨论的人，你的问题能更容易得到解决。

国内的 [V2EX](https://www.v2ex.com/) 和 [SegmentFault](https://segmentfault.com/) 也是相对靠谱的社区。而对于 [知(B)乎](https://www.zhihu.com/)，需要谨慎分辨。

总的来说，不建议在知乎提问技术问题。不要使用百度搜索技术问题，尽量少看 [简书](https://www.jianshu.com/) 和 [CSDN 博客](https://blog.csdn.net/) 的文章。并非前面的社区不好（都有不少隐藏大牛），而是因为你碰到质量很差的文章的概率更高。在不知道如何快速分辨这些文章质量之前，你会在这些平台上浪费大量时间。

重要的事情说三遍：

**不要用百度搜索技术问题！**
**不要用百度搜索技术问题！**
**不要用百度搜索技术问题！**

### 经典书籍

成熟技术都有经典书籍，例如著名的 [《UNIX 环境高级编程》](https://book.douban.com/subject/1788421/)，[《JavaScript 高级程序设计》](https://book.douban.com/subject/10546125/) 等等。 最简单的找到经典书籍的办法是在 [豆瓣图书](https://book.douban.com/) 搜索后查看评分。如果一本技术书籍（非小众技术）有 500 人以上的评价 **和** 7.5 以上的评分，我们就可以认为这本书是经典之作。

挑选出版社也是个好办法。 **O’REILLY** 策划了一系列优秀的技术书籍，它的书籍装帧使用相同的风格，是的，这个风格你肯定见过。

![HTTP 权威指南](https://blog.zengrong.net/uploads/2020/05/http.jpg)

在国内，**电子工业出版社、人民邮电出版社、清华大学出版社** 在技术书籍方面的质量是比较有保证的，买书时可以优先选择它们。

### 相关联的技术

要完全掌握一门技术，需要学习其相关联的技术。以 [Vue](https://vuejs.org/) 为例，相关的基础架构有这么多：

1.  HTML4/HTML5
2.  CSS3
3.  Node
4.  Javascript/Typescript/ES2015/ES2016
5.  NPM/Yarn

前端生态系统依赖：

1.  Babel
2.  webpack
3.  axios

相关插件和工具链：

1.  Vue Router
2.  Vuex
3.  Vue Loader
4.  Vue Cli

这么多相关联的技术，一次性全部掌握是不可能的。最好的方法是保证学习的主线，在主线周边逐渐展开技术广度，不要追求单个技术的深度，一切以实践中能满足需求为目标。

在需要了解某一个关联技术的时候，也可以继续采用上面的 5 个方法找到该技术的锚，基于优质资料持续深入学习。

## 我是怎么学习 Golang 的

去年底，我花了 1 个月的时间学习 Golang，并写了一个在生产中使用的小项目。学习的过程如下：

1.  找到官方和民间最主流的入门资料，阅读完毕。
2.  选择一个 Web 框架，选择的方法可以参考 [SaltStack/Ansible/Fabric 的选择](https://blog.zengrong.net/post/choose-in-salt-ansible-fabric/)。
3.  找一个即将投入生产的项目，使用 Golang 将其实现出来并部署上线。
4.  强制输出：把整个学习过程记录在 [Golang Resources](https://blog.zengrong.net/post/golang-resources/) 和 [从 Flask 到 Gin](https://blog.zengrong.net/post/flask-to-gin-index/) 这一系列文章中。

下面是学习输出文章的一段摘录，现在看来，描写得很准确：

> 我学习新技术的时候有个不好的习惯，就是除了新技术本身，还会第一时间找一堆与这个技术相关的信息，了解一下其主流应用，把今后可能用到的库啊开源项目啊都找出来。这就导致我在学习 Golang 的同时又跑去看了大量的开源库和 Golang 主流应用的资料。另一个不太好的习惯就是，我很有点选择困难综合症，总执着于在一堆技术里面选择出来一个自己看起来顺眼的，相对主流，用起来不错，源码足够优雅的那个。这也就直接导致我足不出户的这两天阅读量巨大，眼都快瞎了。除了选择起来很痛苦，寻找佐证和写垃圾代码测试的过程也是难以描述啊……

## 我是怎么学习 OKR 的

今年 2 月，为了启动团队 OKR，我全面学习了 OKR 知识，并在新冠疫情期间团队在线办公时，在部分项目中进行了试验，效果还不错。学习过程如下：

![OKR书籍搜索](https://blog.zengrong.net/uploads/2020/05/okr.jpg)

1.  在豆瓣读书中找到评分最高的关于 OKR 的数据，从图中可以看出，《OKR 工作法》与《这就是 OKR》是最好的书（综合评分和评价数量）。
2.  深度阅读，做读书笔记。 [读书笔记：《这就是 OKR》](https://blog.zengrong.net/post/reading-notes-measure-what-matters/)， [读书笔记《OKR 工作法》](https://blog.zengrong.net/post/reading-notes-radical-focus/)。
3.  阅读一本包含多种绩效管理方法的书，深度阅读，比较所有绩效管理方法与 OKR 的区别。我选择的这本书评价不佳，读起来也的确很乏味。但它对我要达到的目的来说，是一个合适的选择。[读书笔记：《绩效管理全流程实战方案》](https://blog.zengrong.net/post/reading-notes-jxglqlc/)
4.  在团队中实践。选择一个项目，在制作人完善了解 OKR 的前提下（可以通过读书笔记），与制作人逐条全面讨论目标和关键结果的制订。讨论期间，不断重新理解书籍中的内容，学习书籍中的实例，直至制订出大家都认同的 OKR。然后每周对 OKR 进行同步、调整、打分和反馈。在 OKR 结束时进行总结，并制定下一个阶段的 OKR。

## 形成默会知识

**授人以渔** 的重点，是形成 **默会知识**。有着不同经验的人，在学习时花费的时间是不同的。经验更丰富的学习者在学习新知识的时候，会从之前形成的知识网络节点中找到类似的经验，与新知识关联验证。如果找到相似的关联，这部分的知识就会立刻被学习者的知识网络接受，从而节省大量的时间。

形成的默会知识越多，**个人知识网络** 越清晰，经验关联越丰富，学习新技能和知识的速度就会越快。我在学习 Vue 的时候，已经有大约 18 年没有写 Web 前端，个人知识网络中的前端知识点还是 XHTML/CSS2/Dreamweaver，那时 jQuery 还没有被发明，Node.js 还没有出现，Div 布局还没有成为标准，Web 前端还在使用各种奇技淫巧来解决老不死的 IE 兼容问题。但即使如此，十几年前积累下来的关于浏览器渲染、HTTP 协议以及 HTML 技术栈知识依然快速与新的 Web 标准形成了关联，极大缩短了我的学习时间。

对于技术人员来说，需要不断拓宽个人知识网络的边界，大量阅读和编码，正所谓：

**学得越多，就学得越快。**

这套方法论并非只能用于程序员。我们在工作中需要掌握的大部分技能，都属于“技术”的范畴。希望读到文章的同学能触类旁通，举一反三。

学习之路，没有尽头。与君共勉。

## 参考链接

[如何学习一门新技术 | zrong's Blog](https://blog.zengrong.net/post/how-to-study-a-new-technology/)
[学习一个新领域的知识的最佳方法和最快时间各是什么？ - 知乎](https://www.zhihu.com/question/19550362)