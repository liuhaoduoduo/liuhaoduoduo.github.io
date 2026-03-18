---
layout: post
title:  "Java的异常（Exception）、错误（Error）体系"
date:   2025-03-30 18:00:00 +0800
categories: Java
---
说来惭愧，从事 Java 开发近 10 年，我竟从未系统梳理过 Java 错误和异常类体系。前几天面试，面对相关问题，我一时语塞。反思过后，今天我就梳理一下。

![](https://github.com/liuhaoduoduo/liuhaoduoduo.github.io/raw/main/images/250330193006.jpg)

上图是日常开发中“常见”的错误（异常）类的层级关系图。

在 Java 体系里，java.lang.Throwable类作为所有错误和异常的超类，有着特殊地位。只有Throwable类及其子类的实例，能够被 Java 虚拟机抛出，或是通过throw语句手动抛出；同样，catch语句也仅能捕获Throwable类及其子类的实例。

在实际开发过程中，直接使用Throwable类的场景较为少见。在不确定原因的场景中，比如每当碰到一段代码可能因抛出异常而执行失败时。对于这类代码，我一般会在外层嵌套try...catch语句，借此拦截异常。在这一情形下，catch块能够捕获Throwable类所涵盖的所有异常类型。

Throwable有两个直接子类，分别为Error和Exception，它们代表了不同类型的程序异常情况。

Error类及其子类所表示的是严重问题，这些问题无法简单地通过在代码中捕获异常，再采用重试等手段来恢复正常。以 ThreadDeath 为例，尽管它在某种程度上属于线程生命周期里的 “正常” 情况，但仍被归为错误类。这或许是因为，现在已不推荐在代码里使用stop方法使线程抛出ThreadDeath异常来停止线程。所以，即便它是线程正常结束流程的一部分，在此处仍被视为异常状况。

在日常开发中，常见的错误类型有OutOfMemoryError和StackOverflowError。OutOfMemoryError通常是在内存空间分配失败，且垃圾回收器无法提供更多可用内存时抛出；而StackOverflowError往往是由于递归调用层级过深所致。

Exception 类及其子类（RuntimeException 除外）代表的是相对不那么严重、可预见的问题。对于这类问题，应当在代码中使用 try...catch 语句进行捕获，并尝试采取适当的处理措施，使程序恢复正常运行。而 RuntimeException 类及其子类表示的是运行时异常，其严重程度低于 Error，并且也难以通过简单的异常捕获和重试机制来解决。
在日常开发中，需要在代码里捕获的常见异常有 TimeoutException。例如，当调用 future.get(timeout, unit) 方法时，如果异步任务未能在预期时间内完成，就会抛出该异常。而像 NullPointerException 和 IndexOutOfBoundsException 这类异常则无需强制捕获。NullPointerException 通常是因为尝试使用一个不存在的对象实例而引发；IndexOutOfBoundsException 则是在操作集合类数据（如数组、字符串）时，因访问越界所导致。
那么，如何判断一个错误究竟属于 Error 还是 Exception 呢？我总结出可以依据初步的处理方式来判断：
1. 如果问题能够通过调整虚拟机参数来解决，那么大概率属于 Error。例如 OutOfMemoryError 和 StackOverflowError，在不存在内存泄漏和死循环的情况下，确实无需修改代码，只需增加堆和栈的内存空间即可解决。
2. 如果问题只能通过调整代码来解决，那么大概率属于 Exception。例如 TimeoutException、NullPointerException 和 IndexOutOfBoundsException，这些异常无法通过调整虚拟机参数来处理，只能在代码中添加重试逻辑或检查语句以避免异常发生。