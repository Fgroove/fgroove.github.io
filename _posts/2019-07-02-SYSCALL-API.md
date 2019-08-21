---
layout: post
title:   "Syscall & API"
date:   2019-07-02 09:55:01 +0800
categories: "research proposal"
tag: "技术思路"

---

* content
{:toc}




# SYSCALL & API

## 常见API

### 时间系列

#### 结构体

1.`FILETIME`包含一个64位值，表示自1601年1月1日（UTC）以来100纳秒间隔的数量。

```c++
typedef struct _FILETIME {
  DWORD dwLowDateTime;
  DWORD dwHighDateTime;
} FILETIME, *PFILETIME, *LPFILETIME;
```

2.`SYSTEMTIME`,熟悉的日期（年月日）和时间（时分秒）。

```C++
typedef struct _SYSTEMTIME {
  WORD wYear;
  WORD wMonth;
  WORD wDayOfWeek;
  WORD wDay;
  WORD wHour;
  WORD wMinute;
  WORD wSecond;
  WORD wMilliseconds;
} SYSTEMTIME, *PSYSTEMTIME, *LPSYSTEMTIME;
```

相关的6个函数 `GetSystemTime`、 `GetLocalTime`、 `SystemTimeToFileTime`、 `FileTimeToSystemTime`、 `LocalFileTimeToFileTime`、 `FileTimeToLocalFileTime`。

### API

 `GetSystemTime`获取当前UTC时间。

```C++
void GetSystemTime(LPSYSTEMTIME lpSystemTime);
```

---

`GetLocalTime`获取当地时间。

```C++
void GetLocalTime(LPSYSTEMTIME lpSystemTime);
```

---

`GetSystemTimeAsFileTime`获取系统当前日期和时间。`lpSystemTimeAsFileTime`指向`FILETIME`的指针，接收当前系统日期和时间。`Kernel32.dll`。

```c++
void GetSystemTimeAsFileTime(
  LPFILETIME lpSystemTimeAsFileTime
);
```

---

`SystemTimeToFileTime`,转换系统时间到文件时间格式。

```C++
BOOL SystemTimeToFileTime(
  const SYSTEMTIME *lpSystemTime,
  LPFILETIME       lpFileTime
);
```





---

系统调用：系统调用实际上是指底层的一个调用，就是**内核**提供的、功能十分强大的一系列的函数。这些系统调用是**在内核中实现的**。是操作系统为**用户态运行的进程和硬件设备（如CPU、磁盘、打印机等）进行交互**提供的一组接口，即就是设置在应用程序和硬件设备之间的一个接口层。可以说是操作系统留给用户程序的一个接口。

API：win32API也就是MicrosoftWindows32位平台的应用程序编程接口。windows规定一切的应用程序优先级为3，为windows自己的为0（0最高，3最低）。**windows不允许应用程序直接访问硬件，但是会提供API函数让用户间接地访问**，这样就会**调用系统级API**。

* **用户级API：**替用户写好函数，方便调用，使程序员可以将焦点放在设计程序逻辑上面，而不必再编写繁琐、重复的程序，不必关注技术的细节。例如VC++，MFC，VB等都是类库和各种控件，它代替了API的神秘功能。
* **系统级API：**如果应用程序想要和硬件交互，必须调用此类函数。**程序员调用的是API（api函数**），然后通过与系统调用共同完成函数的功能。因此，**API是**一个提供给应用程序的接口，一组函数，是**与程序猿直接进行交互的**。系统调用则不与程序员进行交互，它是根据API函数，通过一个软件中断机制向内核提交请求（trap指令类似于一个系统中断，系统也会有一个特殊的中断处理函数（interrupt handler）来处理用户的请求），以获得内核服务的接口。并不是所有的API函数都一一对应一个系统的调用，有时，**一个API函数会需要几个系统调用共同来完成函数的功能**，甚至**一些API函数不需要调用相应的系统调用**，不需要内核提供的服务。

## Windows API

摘选自《恶意代码实战分析》第7章 分析恶意Windows程序

Windows API管理着恶意代码与微软程序库之间的交互方式。

### 类型和匈牙利表达法

WindowsAPI是用自己的名字，来表示C语言类型。如`DWORD`和`WORD`分别标识32位和16位无符号整数。

Windows使用匈牙利表达法，作为API函数标识符。

*匈牙利表达法：变量名以一个或多个小写字母开始，代表变量的类型。后面附以变量的名字，变量名以意义明确的大小写混合字母序列（驼峰式表达）所构成。*如`dw`,表示`DWORD`、32位无符号整数变量。

| 类型和前缀      | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| WORD(w)         | 一个16位的无符号数值                                         |
| DWORD(dw)       | 一个双字节、32位的无符号数值                                 |
| Handles(H)      | 一个对象索引，句柄中存储的信息并没有文档化，且这一句柄只被Windows API操作，如HModule、HInstance、HKey。 |
| Long Pointer(L) | 一个指向另一类型的指针，LPByte是指向字节的指针，LPCSTR是一个指向字符串的指针，字符串通常是由LP作为前缀，因为它们实际上是指针。Pointer(P)在32位系统里面，和LP一样。 |
| Callback        | 一个会被WIndows API调用的函数。例如，InternetSetStatusCallback函数传递一个函数指针，当系统有Internet连接状态的更新时将会调用这个函数。 |

### 句柄

句柄是打开或创建窗口、进程、模块、菜单、文件等。和指针类似，不同的是，句柄不能用来进行数学操作。能对句柄做的唯一的事情，就是保存它，并在后续函数调用中使用它来引用同一个对象。

`CreateWindowEx`函数返回一个`HWND`，窗口句柄。对该窗口的任何操作，比如调用`DestroyWindow`函数时，需要使用这个句柄。

### 文件系统函数

恶意代码与系统交互的一个最常用的方式就是创建或修改文件，而且独特文件名或修改为既有的文件名是明显的基于主机的感染迹象。

微软提供了多个函数来访问文件系统。

`CreateFile`：

`ReadFile`和`WriteFile`

`CreateFileMapping`和`MapViewOfFile`



### 特殊文件

