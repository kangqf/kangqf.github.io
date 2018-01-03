---
title: cplusplus notes
categories:
  - notes
tags:
  - cplusplus
date: 2016-09-22 16:30:54
---

## 一些关于C++的笔记


1. Lambdas 表达式
  1. `尾置返回类型`-一般用于返回类型比较复杂和难理解（例如 数组的指针或是数组的引用）的函数中来声明返回类型。
    * 尾置返回类型的一般格式为：`auto fun(parameter list) -> trailing type`
    * 返回类型跟在形参列表的后面，并且以一个` -> ` 符号开头。
    * 为了表示真正的返回类型跟在形参列表之后，在本该出现返回类型的地方放置一个`auto`。
    * 实例：申明一个返回指向一个维度为10的int数组的指针
      * 原生的方式： `int (*function(int i))[10]`
      * 使用尾置返回： `auto function(int i) -> int (*)[10]`
      * 使用类型别名： `typedef int arrT[10]` or `using arrT = int[10]` then `arrT* function(int i)`
      * 使用`decltype`：首先`int arr[] = {1,2,3,4}` then `decltype(arr) *function(int i)`
<!-- more-->
  1. `调用运算符`-调用运算符是一对圆括号，里面放置实参列表。
  2. `可调用的(对象/表达式)`-我们可以对一个(对象/表达式)使用调用运算符，则称这样的对象或表达式是可调用的。
  3. 四种可调用的对象- `函数` `函数指针` `重载了函数调用运算符的类` `Lambda表达式`
  4. lambda expression represent a callable unit of code. It can be thought of as unnamed,inline function, but may be defined inside a function.
  5. A lambda expression has the form : `[capture list](parameter list) -> return type { function body } `
    * capture list 称为捕获列表，是在enclosing function 中的局部变量.
    * lambda 必须使用的是尾置返回(trailing return)来指定它的返回类型.
    * 可以忽略参数列表和返回类型，但是必须包含函数体和捕获列表:`auto f = [] { return 0;}`
  6. 向lambda传递参数及使用捕获列表。
    1. lambda不能含有默认参数，因此lambda的形参的个数和实参的个数一定是一样的。
    2. 空的捕获列表表明lambda表达式不使用它所在函数中的任何局部变量。
    3. lambda 表达式可以使用定义在当前函数之外的名字 比如 cout.
    4. 捕获列表只适用于局部的非静态变量，它可以直接使用局部的静态变量和在函数外部的名字.
  7. lambda 捕获和返回
    1. 定义一个lambda 表达式的时候，编译器会生成一个与lambda对应的未命名的类类型。
    2. 变量的捕获方式可以是值或是引用。如果使用的是值捕获前提是采用值捕获的变量可以被复制。
    3. 与参数的传递不同，被捕获的变量的值是在lambda创建的时候发生拷贝，而不是在调用的时候发生拷贝的。因此对捕获的变量的后续的修改将不会影响到lambda里面对应的值。
    4. 使用引用捕获必须确定被引用的对象在lambda执行的时候是存在的。
    5. 引用值捕获在有的时候显得十分必要，比如我们需要使用输出流，因为ostream是不能复制的，所以唯一的方式就是使用引用捕获（或指针）。
    6. 我们可以返回lambda表达式，但是不能返回局部变量的引用，也就是说lambda不能包含引用捕获。
    7. 隐式捕获，就是不显式地指明我们需要捕获的变量而是在捕获列表里使用`&`或`=`。其中`&`表示要使用引用捕获，而`=`表示要使用值捕获。如果我们混合使用显式捕获和隐式捕获，那么捕获列表的第一项必须是`&`或`=`。
    8. 如果混合使用显式捕获和隐式捕获，那么如果隐式捕获使用的是引用模式那么显式捕获只能使用值捕获模式（如果使用引用直接用就行不用再次声明），反之亦然。
  8. 可变的lambda.
    1. 一般情况下lambda不会改变使用值捕获的变量的值，如果我们想改变这个值，那么我们必须在参数列表后面跟上关键词`mutable`
    2. 一个引用变量能否修改，取决于引用指向的是一个const类型变量还是一个非const类型的变量。
  9. 指定lambda的返回类型.
    1. 如果一个lambda表达式的函数体包含return之外的任何语句，则编译器假定此lambda返回void，被推断返回void的lambda不能返回值。
    2. 如果想在函数体中使用除了return之外的其他语句，并且还要返回非void的值，则要使用尾置返回来指定返回的类型。
  10. 参数绑定
    1. 若果一个lambda的捕获列表为空则我们可以使用一个函数来代替它。
    2. 对于一个捕获局部变量的lambda，我们可以使用`bind`-定义在`functional`中，这个标准库函数。它接收一个可调用对象，生成一个新的可调用对象来适应原对象的参数列表。
    3. bind 调用的一般形式是 `suto newCallable = bind(callable, arg_list)`arg_list中包含_n（`_1 _2 _3`）之类的占位符。来表示这是第几个参数。
    4. 绑定引用参数。如果我们希望传递给bind一个对象而又不拷贝它，就必须使用ref函数。ref返回一个对象，包含给定的引用，此对象是可以拷贝的。另外还有一个cref函数，生成一个保存const引用的类。

2. 作用域和命名空间
  1. 第三方


## 参考文章
[C++11的语法糖](http://imzlp.me/2016/05/12/cpp11-new-features/)
