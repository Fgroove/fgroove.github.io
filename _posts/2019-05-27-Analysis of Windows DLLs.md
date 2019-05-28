---
layout: post
title:   "Analysis of Windows DLLs"
date:   2019-05-27 09:55:01 +0800
categories: 沙箱逃逸
tag: S2E

---
* content
{:toc}


# Analysis of Windows DLLs

---

利用S2E分析DLL文件。作为示例，分析`kernel32.dll`中的[Beep](https://docs.microsoft.com/en-us/windows/desktop/api/utilapiset/nf-utilapiset-beep)函数。

## 准备测试环境

创建Windows镜像，详细见[521-s2e](https://fgroove.github.io/2019/05/21/s2e/).

拥有镜像后，创建DLL项目，

```shell
s2e new_project /path/to/kernel32.dll Beep 5000 1000
```

DLL项目的目标文件扩展名必须为`.dll`,打开`bootstrap.sh`会发现`rundll32`程序会用来执行DLL文件，并且`Beep`函数为主函式(程序开始执行地方)。5000和1000是`Beep`函数的频率和持续时间。

`rundll32`启动后不会结束，必须修改`s2e-config.lua`确保翻译块覆盖率被记录；启用周期性覆盖率更新插件，通过修改`TranslationBlockCoverage`,

```c
pluginsConfig.TranslationBlockCoverage = {
    writeCoverageOnStateKill = true,
    writeCoveragePeriod = 60,
}
```

用`launch-s2e.sh`开始分析后，运行两分钟后中断，通过`killall -9 qemu-system-x86_64`.

## 生成行覆盖率

```shell
s2e coverage lcov --html kernel32
```

在`projects/kernel/s2e-last` xia将生成

* `Kernel32.dll.info`包含覆盖率信息
* `Kernel32.dll_lcov`HTML报告

## 生成基本块覆盖率

利用`s2e-env`生成基本块覆盖，验证`Beep`函数是否执行。

```shell
s2e coverage basic_block kernel32
```

将生成`projects/kernel32/s2e-last/basic_block_coverage.json`.利用`install/bin/ida_highlight_basic_blocks.py`来高亮代码覆盖。

安装IDA Pro之后，指定路径到`s2e.yaml`,还是找不到IDA Pro

```shell
ERROR:[coverage] IDA Pro not found at /path/to/ida
```

利用IDA Pro可以显示S2E执行的代码块(绿色标记)，图形界面更直观；但是始终找不到IDA Pro，**止损放弃。**



