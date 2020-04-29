---
title: try-catch-finally总结
categories: 每天一道面试题
toc: true
abbrlink: 2774241439
date: 2020-03-24 09:42:10
tags:
---
在日常编译运行程序出错的时候，编译器就会抛出异常，抛出异常要比终止程序灵活的多，这是因为Java提供了一个"捕获"异常的处理器对异常情况进行处理，如果没有提供处理器机制，程序就会终止，try-catch-finally可以保证程序发生错误的时候继续执行下去。下面就谈一下使用的总结，和注意事项。
<!--more-->

## 总结

- try，catch，finally语句中，如果try语句有return语句，此后做任何修改，都不影响try中return的返回值。
- 如果finally块中有return语句，则try或catch中中的返回语句忽略。
- 如果finally块中抛出异常，则整个try，catch，finally块中抛出异常。

## 注意

- 在使用try- catch-finally的时候，要注意以下问题：
①尽量在try或者catch中使用return语句。通过finally块中达到对try或者catch返回值修改是不可行的。
②finally块中避免使用return语句，因为finally块中如果使用return语句，会显式的消化掉try，catch块中的异常信息，屏蔽错误的发生。
③finally块中避免再次抛出异常，否则整个包含try语句块的方法会抛出异常，并且会消化掉try，catch块中的异常。


