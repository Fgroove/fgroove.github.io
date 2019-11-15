---
 Javalayout: post
title:   "nitro小总结"
date:   2019-08-30 08:55:01 +0800
categories: "meeting"
tag: "nitro"

---

* content
{:toc}




# groupMeeting

### Kvmi

根据已有的接口，编写监控程序

* 方便移植，没有太多应用

### nitro

应用libvmi，绑定`qemu2.8.0`

* 参考多，不好移植
* 未配置网络，`linux 4.9.0`下没有自动生成`virbr0`,virsh文件未配置网络。

进程名，进程PID，系统调用名字

```shell
{'event': {'cr3': '0x8633f000',
           'direction': 'enter',
           'rax': '0x1005',
           'time': '2019-09-02T10:22:56.414270',
           'type': 'syscall',
           'vcpu': 0},
 'full_name': 'win32k!NtUserCallNoParam',
 'name': 'NtUserCallNoParam',
 'process': {'command_line': '"C:\\Windows\\system32\\SearchProtocolHost.exe" '
                             'Global\\UsGthrFltPipeMssGthrPipe_S-1-5-21-3134082484-3337425862-525414299-10001_ '
                             'Global\\UsGthrCtrlFltPipeMssGthrPipe_S-1-5-21-3134082484-3337425862-525414299-10001 '
                             '1 '
                             '-2147483646 '
                             '"Software\\Microsoft\\Windows '
                             'Search" '
                             '"Mozilla/4.0 '
                             '(compatible; '
                             'MSIE '
                             '6.0; '
                             'Windows '
                             'NT; '
                             'MS '
                             'Search '
                             '4.0 '
                             'Robot)" '
                             '"C:\\ProgramData\\Microsoft\\Search\\Data\\Temp\\usgthrsvc" '
                             '"DownLevelDaemon"  '
                             '"1"',
             'create_time': '2019-09-02 '
                            '10:20:07',
             'iswow64': False,
             'name': 'SearchProtocol',
             'parent_pid': 1808,
             'path': '\\Device\\HarddiskVolume2\\Windows\\System32\\SearchProtocolHost.exe',
             'pid': 1900}}
```



`vmi_request_page_fault`python binding里面有，nitro/libvmi里面没有

* 添加到libvmi，解决
* 回滚python binding

### rekall

[TypeError:XXX takes exactly 3 arguments (2 given)](https://github.com/google/rekall/issues/495)

rekall由python2.7引用，需要使用python2.7安装，运行时用python3.

[maximum recursion depth exceeded](https://github.com/google/rekall/issues/428),升级解决bug

```shell
pip2 install --upgrade rekall==1.7.2.rc1
```

