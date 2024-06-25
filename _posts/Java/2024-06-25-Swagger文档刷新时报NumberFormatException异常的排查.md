---
layout: post
title:  "Swagger文档刷新时报NumberFormatException异常的排查"
date:   2024-06-25 15:00:00 +0800
categories: Java
---
### 问题现象
最近开发的一个java项目使用的Swagger2作为文档生成框架。在使用期间总会发现日志中打印出“Illegal DefaultValue null for parameter type integer java.lang.NumberFormatException: For input string: ""”的异常信息。命令行中的截图如下:
![错误截图](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/add-doc/images/240625150228.jpg)


### 排查过程
* 阅读日志

排查的第一步一定是从错误日志入手。可以很明确的看出问题的原因是将一个空字符串转为一个数值时发生了错误。但是整个错误日志中，完全没有提及是项目中的哪个文件的哪行代码引发的问题。仅知道问题原因对于解决问题来说帮助还是比较有限的。

* Debug源码

通过错误日志中展示的堆栈信息，可以定位到错误发生时执行的代码。在此处打一个断点。
![错误发生时执行的代码](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/240625153346.jpg)

此时，选择以Dbug模式启动项目，然后打开Swagger文档页面。正常来说代码执行到刚刚添加断点的位置停住。利用Idea的debug能力可以得到一个动态的堆栈列表。
![动态的堆栈列表](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/240625154128.jpg)

逐层的点击堆栈中每一行，观察在执行到该行时右侧所展示的数据信息，看是否存在对排查问题有帮助的内容。我在点击到了第5层fromProperty方法时发现了有价值的信息。
![有价值的内容](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/240625154746.jpg)

以“用户ID”为关键字，在项目中搜索看哪个满足这个搜索条件的字段在使用Swagger时没有注解没有配置正确即可解决问题。

### 结论
经过排查以后发现一个注释为“用户ID”的字段是int型，但Swagger的文档配置中没有使用example来给文档提供展示时的示例值，Swagger提供的注解中这个属性默认是一个空字符串，所以在将其转为数值型时发生了异常。

整个排查过程中存在一个小小的遗憾，本来我期望通过运行时的堆栈能够准确定位出具体解析哪个字段时存在的问题，但未能实现。