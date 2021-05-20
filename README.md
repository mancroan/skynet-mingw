# 关于skynet-mingw-w64 [![Build status](https://ci.appveyor.com/api/projects/status/9j45lldyxmfdau3r?svg=true)](https://ci.appveyor.com/project/dpull/skynet-mingw)

[skynet-mingw](https://github.com/dpull/skynet-mingw) 是[skynet](https://github.com/cloudwu/skynet)的windows平台的实现。其主要特点是：

1. skynet 以submodule链接，方便升级，**确保不改**。
1. 仅扩展了700行代码，方便维护。
1. 自动更新skynet，自动构建，自动化测试，确保质量。

## 编译
亲测在mingw-w64编译通过
1.安装msys2,根据官方文档提示安装
2.运行./prepare.sh
3.make

### 常见问题
1. 当提示缺少类似`dlfcn.h`文件时，安装mingw-w64_x86_64-dlfcn, 当找不到函数的实现是注意makefile的MACHINE_ARCH_INFO="`ls /mingw64/lib/gcc`"路劲是否正确
2. 当提示缺少`netdb.h`文件时, mingw-w64不提供某些头文件(坑爹)，只能修改原文件
#ifdef _WIN32
#include <winsock2.h>
#include <windows.h> 
#else
#include <netdb.h>
#endif


## 测试

```bash
./skynet.exe examples\\config    # Launch first skynet node  (Gate server) and a skynet-master (see config for standalone option)
./3rd/lua/lua examples/client.lua   # Launch a client, and try to input hello.
```

## 已知问题
1.错误，不知道原因，没解决
ua:330: ./lualib/skynet\socket.lua:357: assertion failed!
stack traceback:
        [C]: in function 'assert'
        ./lualib/skynet\socket.lua:357: in function 'skynet.socket.readline'
        ./service/console.lua:16: in upvalue 'f'
        ./lualib/skynet.lua:253: in function <./lualib/skynet.lua:252>
stack traceback:
        [C]: in function 'assert'
        ./lualib/skynet.lua:858: in function 'skynet.dispatch_message'



2. console服务不可用(无法对stdin进行select)， 会提示如下出错信息，暂时没有解决方案。

```bash
stack traceback:
        [C]: in function 'assert'
        ./lualib/socket.lua:361: in function 'socket.lock'
        ./service/console.lua:15: in upvalue 'func'
        ./lualib/skynet.lua:452: in upvalue 'f'
        ./lualib/skynet.lua:105: in function <./lualib/skynet.lua:104>
```

3. 使用`skynet.abort`无法退出，看堆栈卡在了系统中，暂时没有解决方案。（替代方案`os.exit(true)`）

```bash
#0  0x77bd718c in ntdll!ZwWaitForMultipleObjects () from C:\WINDOWS\SYSTEM32\ntdll.dll
#1  0x74c0a4fa in WaitForMultipleObjectsEx () from C:\WINDOWS\SYSTEM32\KernelBase.dll
#2  0x74c0a3d8 in WaitForMultipleObjects () from C:\WINDOWS\SYSTEM32\KernelBase.dll
#3  0x6085be1c in pause () from D:\MinGW\msys\1.0\bin\msys-1.0.dll
#4  0x6085ccf1 in msys-1.0!cwait () from D:\MinGW\msys\1.0\bin\msys-1.0.dll
#5  0x6080dff4 in msys-1.0!cygwin_stackdump () from D:\MinGW\msys\1.0\bin\msys-1.0.dll
#6  0x00413fe5 in ?? ()
#7  0x00413e8f in ?? ()
#8  0x00412a1b in ?? ()
#9  0x0040f77b in ?? ()
#10 0x0040f151 in ?? ()
#11 0x00403869 in __mingw_opendir ()
#12 0x0000000a in ?? ()
#13 0x0069fe30 in ?? ()
#14 0x00000000 in ?? ()
```

## 相关文档
[开发笔记](http://www.dpull.com/blog/2015-11-08-skynet_mingw) 
