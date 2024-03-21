---
layout: post
title:  "HttpServletRequest输入流被框架先读走导致无法读取到数据"
date:   2024-03-21 10:00:00 +0800
categories: Spring
---
#### 现象：
从HttpServletRequest中调用getInputStream获取的输入流中无法读取出数据。
#### 排查：
* 通过在调用InputStream的read方法前加日志观察InputStream的isReady和isFinished状态发现isReady:false和isFinished:true
* 通过讯问chatGPT后的得到如下疑似线索：
![图1](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/240321100100.jpg)
* 此前通过观察日志发现过如下的日志信息：
![图2](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/240321100200.jpg)
* 此时想到上述两幅图中的Parameters是否有些许的关联。通过观察第二幅图的日志级别发现是DEBUG级。此前为了调试问题方便确实在全局开启了DEBUG级日志：
![图3](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/240321100300.jpg)
* 尝试修改配置文件，将日志级别配置删除后问题解决。
  
#### 结论
由于在全局开启了DEBUG级日志后，导致底层框架先于应用从输入流中读取了数据（具体做什么不清楚，但从日志可以看出确实读取了并输出了日志）

#### 解决办法
* 不要开启过低级别的日志，如果为了方便调试问题，可以仅调整应用级的日志界别
* 通过其他方式拿body中的数据，例如用@RequestBody注解

