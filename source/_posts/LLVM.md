---
title: 编译器(LLVM)
copyright: true

date: 2018-10-31 16:33:59
tags: [编译器,LLVM,Clang]
categories: [编译器]
password:
keywords: 编译器,LLVM
description: LLVM是构架编译器(compiler)的框架系统，以C++编写而成，用于优化以任意程序语言编写的程序的编译时间(compile-time)、链接时间(link-time)、运行时间(run-time)以及空闲时间(idle-time)，对开发者保持开放，并兼容已有脚本。
author: XQ
---



## 概述

```
1.广义LLVM指代LLVM架构（包括Clang前端与LLVM后端）,狭义的LLVM指代LLVm后端，以下先以狭义LLVM理解。
2.为什么苹果的Xcode会使用Clang+LLVM取代GCC？这里面有些历史原因。毕竟GCC是第三方开源的，不属于苹果维护也不能完全掌控其开发进程，Apple为Objective-C增加许多新特性，但GCC开发者对这些支持却不友好；Apple需要做模块化，GCC开发者却拖着迟迟不实现。随着Apple对其IDE(也就是Xcode)性能的要求越来越高，最终还是从零开发了一个Clang前端加LLVM后端的编译器，这个编译器的作者是大名鼎鼎的Swift之父Chris Lattner。
Clang比GCC优秀在哪些方面？传说新的Clang编译器编译Objective-C代码速度比GCC快3倍，并且提供了友好的代码提示。
3.对于LLVM来说，其前端是Clang，由于GCC对LLVM的支持不好，LLVM项目改换用Clang作为前端，编译类C源代码文件的时候使用的编译工具就是Clang。而生成中间IR代码后需要对IR代码进行一些操作，例如添加一些代码混淆功能（将计算机程序的代码，转换成一种功能上等价，但是难于阅读和理解的形式的行为，防止逆向分析）。LLVM的做法是通过编写Pass(其实就是对应的一个个类，每个类实现不同的功能)来实现混淆的功能，实现混淆，即编写功能性的Pass(如何编写Pass是个要点)。
4.编译器是如何设计的？经典的三段式设计(three phase design):前端(Frontend)-->优化器(Optimizer)-->后端(Backend)。
```

<!-- more -->

> 经典的三段式设计（three phase design）:

![img][image-1]



```
1).其中前端负责分析源代码，可以检查词法&语法级错误，并构建针对该语言的抽象语法树（AST）。
2).抽象语法树可以进一步转换优化，优化的目的是为了保证功能不变的情况下提高性能，最终转为新的)表示方式（IR），然后再交给让优化器和后端处理。
3).最终由后端生成可执行的机器码。

4.为什么要使用三段式设计？优势在哪？
1).首先解决了一个很大的问题：假如有N种语言（C、OC、C++、Swift...）的前端，同时也有M个架构（模拟器、arm64、x86...）的Target，是否就需要 N × M 个编译器？
2).三段式架构的价值就体现出来了，通过共享优化器的中转，很好的解决了共性的问题。
3).假如需要增加一种语言，只需要增加一种前端；假如需要增加一种处理器架构，也只需要增加一种后端，而其他的地方都不需要改动。
其实就是优化器的复用：
```

> 

![img][image-2]

## LLVM前端

```
前端:LLVM架构的后端一般变动是不大的，只有在有新架构出现才需要添加相应的代码，所以如果想实现编译器，可以基于LLVM做开发，基于LLVM的编译器中，前端的作用是解析、验证和诊断代码错误，将解析后的代码翻译为LLVM IR（通常是这么做，通过生成AST然后将AST转为LLVM IR）。翻译后的IR代码经过一系列的优化过程与分析后，代码得到改善，并将其送到后端（代码生成器）去产生原生的机器码。
```

## LLVM优化器

```
LLVM Optimize:优化器输入是LLVM IR，经过优化后产生新的LLVM IR，优化是为了提高性能，在未优化前，容易被黑客逆向，加入优化器代码混淆功能，防止逆向工程。在LLVM中，优化器有很多种不同的优化通道(optimization passes)，根据输入的不同，我们可以针对性地进行一些改变(编写自定义的pass)，
每一个pass都被写成了一个C++类，都由Pass类继承来的。大多数的pass都是写在一个单独的类文件(.cpp)中的。Pass类的子类被定义在一个匿名空间中（保证完全私有），为了使得pass可用，文件外的代码必须可以访问到此pass类文件，因此设置public函数实现接口功能。

例如，小型的pass：

namespace {
  class LittlePass : public FunctionPass {
  public:
    // Print out the names of functions in the LLVM IR being optimized.
    virtual bool runOnFunction(Function &F) {
      cerr << "Hello: " << F.getName() << "\\n";
      return false;
    }
  };
}

FunctionPass *createHelloPass() { return new Hello(); }
LLVM的优化器提供了非常多不同的pass，每种pass的写法都是类似的。这些pass文件都被编译成为.o文件，接着会被链接打包成为一系列库文件(.a文件)，这些库文件提供了很多的分析与翻译的功能。pass之间都尽可能高聚低耦，相互之间尽可能保持独立，或者时明确地定义pass之间的依赖关系。如果给了许多的pass文件，那么pass管理器就可以通过pass之间的文件依赖关系正确地执行这些pass。LLVM优化器允许挑选指定的pass文件并将它们链接到一起执行，以此来完成指定的一些功能在这样只会由指定的pass完成优化，而不是整个优化器。
```

> pass优化:

![img][image-3]

## LLVM后端

```
后端（代码生成器）的任务是将LLVM IR翻译为机器可识别的代码。每个后端（代码生成器）都要针对不同的任务定制不同的机器代码，另外，后端（代码生成器）所做的工作又有很多类似的，例如将变量分配到寄存器。
与优化器采用的方式类似，LLVM的后端（代码生成器）将代码生成的问题分离成独立的pass，例如指令选择，寄存器分配，建表，代码布局优化以及提供默认的内建pass等等。通过选用内建的默认pass，重写这些默认pass实现针对特定设备的自定制pass。
```

## 使用LLVM开发

```
1.使用LLVM现有的库与源代码，可以自己实现前端的功能，如词法分析，语法分析，生成AST，中间代码(IR)生成，这样在前端可以自定义自己的编程语言。
2.在现有的前端基础上，可以自定义LLVM优化器，如编写自己的pass，测试pass优化性能，优化代码编译的性能。
3.与实现前两种相比,难度大，后端需要针对各种CPU架构，针对硬件底层的编程，多使用汇编编写pass，涉及的操作如：操作寄存器，指令选择，建表等，也可重写默认pass实现针对特定设备的自定制pass。
```

> 由以上分析，开发优先顺序为前端-\>优化-\>后端，在实际开发中，利用LLVM现有模块与源代码可以实现基于LLVM的编译器。 如果需要自己实现或改进前端，需要阅读并理解Clang源码，为改进优化器与后端性能，需要阅读LLVM源码与pass，同时自己动手编写自定义的pass。

## 结论

```
LLVM技术上的（最大）优势就在于它的模块化设计。
在LLVM中，IR的解析，优化，汇编码的生成，寄存器分配，汇编码优化以及机器码生成，各种类型的二进制文件生成全部都是接口定义清晰的模块完成的，很容易分别改进或者添加定制功能。
而且由于LLVM的C++实现，很多模块理解和使用比较容易。这些特性使得LLVM可以很容易地被用在科研和生产实践当中。
反观GCC，模块化做得不如LLVM好，这使得它定制或者改进比较不方便。
因为Clang与LLVM的配合，也许LLVM编译器的地位逐步上升为主流，又由于LLVM框架开源，自定义程度高，所有比较容易实现自己的编译器功能。
```

----

## 参考

[LLVM每日谈博客][1]

[简书][2]

[1]:	https://blog.csdn.net/snsn1984
[2]:	https://www.jianshu.com/p/513a9bd35a7d

[image-1]:	https://upload-images.jianshu.io/upload_images/620754-5b01af04f7db7596.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/474
[image-2]:	https://upload-images.jianshu.io/upload_images/620754-fa67e2861a77eccd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/557
[image-3]:	https://upload-images.jianshu.io/upload_images/1460317-742b3eb88b3922ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/948