---
title: Objective-C语法
categories:
  - 语言
tags:
  - Objective-C
  - C++
  - Java
copyright: true
comments: true
top: false
date: 2018-11-04 11:56:47
updated: 2018-11-04 11:56:47
keywords: Objective-C，C++，Java
description: Objective-C与C++、Java比较
author: XQ
---

#### 1.函数方法

- C++:

```c++
函数返回类型 函数名称(参数类型 参数名称){
    函数体;
}
比如:
auto add(auto a,auto b){
    return a + b;
}
```

- Java:

```java
访问权限 函数返回类型 函数名称(参数类型 参数名称){
    函数体;
}
比如:
public int add(int a,int b){
    return a + b;
}
```

- Objective-C:

```objective-c
+/- (函数返回类型) 函数名称:(参数类型) 参数名称{
    函数体;
}
比如:
//实例方法
-(int) add:(int) a,(int) b{
    return a + b;
}
//静态方法 static
+(int) add:(int) a,(int) b{
    return a + b;
}
```

#### 2.消息

- C++:

```c++
object.function();
object.function(param···);
string * str = new string("");
```

- Java:

```java
object.function();//方法
object.attributes;//属性
object.function(param···);
string * str = new string("");
```

- Objective-C:

```objective-c
[object function];//方法
object.attaibutes;//属性
[object function:param···];//方法
NSString * string = [[NSString alloc] initWithString:@""];
```

#### 3.导入

- C++:

```c++
#include ""
#include <>
#include </>
```

- Java:

```java
import ""
import <>
import </>
```

- Objective-C:

```objective-c
import ""
import <>
import </>
```

#### 4. setter与getter

- C++:

```c++
自定义😝
```

- Java:

```java
自定义😝
kotlin支持一行代码解决
```

- Objective-C:

```objective-c
Property定义：@property 声明用于自动创建property属性变量的getter和setter
Synthesize定义：@Synthesize声明实现了property属性变量的getter和setter。
```

#### 5.头文件中的方法

- C++:

```c++
returnType method(param···)
```

- Java:

```java
returnType method(param···)
```

- Objective-C:

```objective-c
-(returnType)method
-(returnType)method:(dataType)param1
-(returnType)method:(dataType)param1 withParam:(dataType)param2
```

#### 6.self/this

- C++:

```c++
this.method()
```

- Java:

```java
this.method()
```

- Objective-C:

```objective-c
[self method]
```

#### 7.继承

- C++:

```c++
class:public parent/class:protected parent/classname:private parent
class:public parent1,protected parent2,private parent3
```

- Java:

```java
Class extends Parent
Class extends Parent implements interface
Class implements interface
```

- Objective-C:

```objective-c
ClassA:ParentA
ClassA:ParentA<Protocol>
ClassA <Protocol>
```

#### 



#### 参考:

[十分钟让你明白Objective-C的语法（和Java、C++的对比）](https://blog.csdn.net/totogo2010/article/details/7632384)