---
layout: post
title:   "Using S2E to generate PoVs"
date:   2019-05-29 09:55:01 +0800
categories: 沙箱逃逸
tag: S2E

---
* content
{:toc}


# Using S2E to generate PoVs

Windows和linux类似，但是`dbg`无法执行`.exe`，无法验证漏洞,所以用linux演示。

S2E自带演示文本`vulnerabilities`,源文件[vulnerabilities](https://github.com/S2E/guest-tools/blob/master/common/demos/vulnerabilities.c).

### 1.创建`guest-tools`

`~/s2e/build/s2e/`目录下有Windows/linux、32/64位工具,使用32位的linux版本

```shell
$ cd ~/s2e/build/s2e/guest-tools32
$ make
```

### 2.创建项目

```shell
$ cd ~/s2e/env
$ s2e new_project -i debian-9.2.1-i386 -n vuln-lin32-32 --enable-pov-generation \
  build/s2e/guest-tools32/common/demos/vulnerabilities @@
```

* `-n`项目名：默认为分析程序的名字，这里自定义为`vuln-lin32-32`
* `-i`指定镜像：这里指定`debian-9.2.1-i386`
* `--enable-pov-generation`:生成PoVs
* `@@`:标志，表明该程序已文件作为输入，运行时会替换为包含符号数据的实际文件。

### 3.启动S2E等待分析完成

```shell
$ cd ~/s2e/env/projects/vuln-lin32-32
$ ./launch-s2e.sh
```

目录`~/s2e/projects/vuln-lin32-32/s2e-last`如下，

```shell
$ ls ~/s2e/env/projects/vuln-lin32-32/s2e-last
_tmp_input-crash@0x0-pov-unknown-0
_tmp_input-crash@0x0-pov-unknown-3
_tmp_input-recipe-type1_i386_generic_reg_ebp.rcp@0x8048760-pov-type1-0
_tmp_input-recipe-type1_i386_generic_reg_edi.rcp@0x8048760-pov-type1-0
_tmp_input-recipe-type1_i386_generic_shellcode_eax.rcp@0x804883e-pov-type1-3
...
```

这些具体输入文件可以证实漏洞，用这些文件做输入在GDB下运行该程序，

### 4.证实漏洞

```shell
$ gdb --args ./vulnerabilities s2e-last/_tmp_input-recipe-type1_i386_generic_shellcode_eax.rcp@0x804883e-pov-type1-3
(gdb) r
Starting program: /home/user/s2e/env/projects/vuln-lin32-32/vulnerabilities
     s2e-last/_tmp_input-recipe-type1_i386_generic_shellcode_eax.rcp@0x804883e-pov-type1-3

Demoing function pointer overwrite

Program received signal SIGSEGV, Segmentation fault.
0x44556677 in ?? ()
(gdb) info registers
eax            0xccddeeff       -857870593
ecx            0x44556677       1146447479
edx            0x40     64
ebx            0x0      0
esp            0xffffcd1c       0xffffcd1c
ebp            0xffffcd68       0xffffcd68
esi            0x804b008        134524936
edi            0xf7fa2000       -134602752
eip            0x44556677       0x44556677
eflags         0x10246  [ PF ZF IF RF ]
cs             0x23     35
ss             0x2b     43
ds             0x2b     43
es             0x2b     43
fs             0x0      0
gs             0x63     99

```

程序在`PC=0x44556677`是崩溃，此时`eax=0xccddeeff`

* EAX 是"累加器"(**A**ccumulator), 它是很多加法乘法指令的缺省寄存器。

* EBX 是"基地址"(**B**ase)寄存器, 在内存寻址时存放基地址。

* ECX 是计数器(**C**ounter), 是重复(REP)前缀指令和LOOP指令的内定计数器。

* EDX 则总是被用来放整数除法产生的余数。

