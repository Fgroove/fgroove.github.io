---
layout: post
title:   "Java Overview"
date:   2019-06-17 8:55:01 +0800
categories: "Job Hunting"
tag: "leetcode"

---
* content
{:toc}


# Java Overview

## Java语言

JAVA语言编程范例基于OOP概念，是C语言的一种衍生语言，语法规则和C语言非常相似。

在结构上，Java以包开头，包是JAVA语言的名称空间机制。包中包含类，类中包含方法、变量和常量等。

## Java编译器

Java编译器编译`.java`文件，将字节码写入`.class`文件，字节码是在JVM上运行的指令。通过这一抽象级别，Java编译器会编写适合用来运行程序的 CPU 芯片集的指令。

## JVM

运行中，JVM读取并解释`.class`文件，JVM像CPU解释汇编指令一样解释字节码。JVM是Java语言"编写一次，随处运行"原则的核心。

## GC

隐式内存管理。

Java应用程序创建对象实例是，JVM自动从堆分配内存。JVM GC会自动回收不再需要对象占用的内存。

## JDK

除了编译器和其他工具外，JDK还有一个包含预构建的实用程序完整类库。相关JDK包和库见[JDK API](https://docs.oracle.com/javase/8/docs/api/).

## JRE

Java运行时环境，Java程序运行时需要的JVM、代码库和相关组件。JRE多平台，包含在JDK中。





# 设置开发环境

JDK 和 Eclipse IDE 

## 安装JDK

macOS查看Mac 上的 JDK 8 位置。显示的路径类似

```shell
$ /usr/libexec/java_home -1.8 
/Library/Java/JavaVirtualMachines/jdk1.8.0_92.jdk/Contents/Home。
```



# OOP语言概念

C语言：结构化编程语言，高度面向数据；拥有数据结构，然后程序指令会处理该数据。

OOP语言：对象，结合数据和程序逻辑，只有属性和行为。

## OOP特点

### 封装

对象是离散的，封装的原理。

`public/private`，封装的实现。

### 继承

子类继承父类所有属性，并且可以添加修改属性。

结构化编程中需要复制数据结构，产生大量重复代码。

### 多态性





# Java语言入门

## 保留字

保留字不能用来命名Java构造。

```plain
abstract
assert
boolean
break
byte
case
catch
char
class
const
continue
default
do
double
else
enum
extends
final
finally
float
for
goto
if
implements
import
instanceof
int
interface
long
native
new
package
private
protected
public
return
short
static
strictfp
super
switch
synchronized
this
throw
throws
transient
try
void
volatile
while
```

严格意义上，`true`,`false`和`null`也不行.

## Java类的构造

类是包含属性和行为的离散实体（对象）的蓝图。

**类定义：**

```java
package packageName;
import ClassNameToImport; 
accessSpecifier class ClassName {
  accessSpecifier dataType variableName [= initialValue];
  accessSpecifier ClassName([argumentList]) {
    constructorStatement(s)
  }
  accessSpecifier returnType methodName ([argumentList]) {
    methodStatement(s)
  }
  // This is a comment
  /* This is a comment too */
  /* This is a
     multiline
     comment */
}
```



### 打包类

解决class重名产生的名称冲突。

Java 包是一种提供`名称空间`的机制,`名称空间`是一个区域，名称在该区域内是唯一的，但在其外部可能不是唯一的。

**包定义：**

```java
`package  orgType.orgName.appName.compName;`
```

这个包定义可分解为不同的部分：

- `orgType` 是组织类型，比如 `com`、`org` 或 `net`。
- `orgName` 是组织的域名称，比如 `makotojava`、`oracle` 或 `ibm`。
- `appName` 是应用程序的缩写名称。
- `compName` 是组件的名称。

### 导入语句

导入语句告诉 Java 编译器在何处查找您在代码中引用的类。

```java
`import ClassNameToImport;`
```

类名应是*完全限定的*，这意味着类名中应包含它的包。

导入一个包中的所有类，可以将 `.*` 放在包名称后。例如，这条语句导入 `com.makotojava` 包中的每个类：

```
`import com.makotojava.*;`
```

### 类的声明

要在 Java 语言中定义一个对象，必须声明一个类。可以将类看作是对象的模板。

```java
accessSpecifier class ClassName {
  accessSpecifier dataType variableName [= initialValue];
    accessSpecifier ClassName([argumentList]) {
    constructorStatement(s)
  }
  accessSpecifier returnType methodName([argumentList]) {
    methodStatement(s)
  }
}
```

类的命名，`驼峰式大小写`：以一个大写字母开头，将串联的每个单词的首字母大写，将其他所有字母小写。类名应仅包含字母和数字。

### 变量和方法

类的变量值可以区分该类的每个实例并定义它的状态。

类的方法定义了它的行为。



# 第一个Java类

## 原语数据类型

| 类型      | 大小   | 默认值     | 值范围                                      |
| :-------- | :----- | :--------- | :------------------------------------------ |
| `boolean` | 不适用 | false      | true 或 false                               |
| `byte`    | 8 位   | 0          | -128 到 127                                 |
| `char`    | 16 位  | （无符号） | \u0000' \u0000' 到 \uffff' 或 0 到 65535    |
| `short`   | 16 位  | 0          | -32768 到 32767                             |
| `int`     | 32 位  | 0          | -2147483648 到 2147483647                   |
| `long`    | 64 位  | 0          | -9223372036854775808 到 9223372036854775807 |
| `float`   | 32 位  | 0.0        | 1.17549435e-38 到 3.4028235e+38             |
| `double`  | 64 位  | 0.0        | 4.9e-324 到 1.7976931348623157e+308         |

## 内置记录

Java 平台包含 `java.util.logging` 包，这是一种以可读形式收集程序信息的内置记录机制。Logger 是您通过对 `Logger` 类执行静态方法调用所创建的命名实体：

```java
import java.util.logging.Logger;
//...
Logger l = Logger.getLogger(getClass().getName());
```

在任何常规（即非静态）方法中，前面的代码始终引用该类的名称并将它传递给`Logger`。

