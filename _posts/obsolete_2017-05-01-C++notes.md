---
layout: post
title: "一些C++笔记"
image: ''
date:  2017-05-01 04:29:30
tags:
- 笔记
- C++
description: ''
categories:
- 学习总结
---

---
本文记录一些c++的笔记，缓慢更新中。  
## 目录
### [1. windows数据类型 (2017-05-08)](#1)
### [2. 监视某个exe是否在运行 (2017-05-01)](#2)
### [3. 启动外部程序 (2017-05-08)](#3)
### [4. Windows下的简易socket程序 (2017-05-24)](#4)

---
<h4 id="1">windows数据类型</h4>
在Windows编程/MFC中，常常遇见一些基础类型都是大写字母，而且与c/c++中的基础类型不一致的情况，
其实这些类型仍然是c/c++中的基础类型，只是微软为了解决兼容问题，自己所定义的一些类型，下表给出了windows中常见的基础类型。

|类型名称  | 定义|类型名称  | 定义|
|: ---  :  |:----:|: ---  :  |:----:|
|BOOL      | typedef int BOOL|PBOOL|	typedef BOOL  \*PBOOL
|BOOLEAN   | typedef BYTE BOOLEAN|PBOOLEAN	|typedef BOOLEAN \*PBOOLEAN
|BYTE      | typedef unsigned char BYTE|PBYTE|	typedef BYTE \*PBYTE
|CHAR      | typedef char CHAR|PCHAR|	typedef CHAR \*PCHAR
|UCHAR     | typedef unsigned char UCHAR|PUCHAR|	typedef UCHAR \*PUCHAR
|SHORT     | typedef short SHORT|PSHORT|	typedef SHORT \*PSHORT
|WORD      | typedef unsigned short WORD|PUSHORT	|typedef USHORT \*PUSHORT
|DWORD     | typedef unsigned long DWORD|PWORD|	typedef WORD \*PWORD
|DWORD32   | typedef unsigned int DWORD32|PDWORD|	typedef DWORD \*PDWORD
|DWORD64   | typedef unsigned \_int64 DOWRD64|PDWORD32	|typedef DOWORD32 \*PDWORD32
|INT       | typedef int INT|PDWORD64|	typedef DWORD64 \*PDWORD64
|INT32     | typedef signed int INT32|PINT	|typedef INT \*PINT
|INT64     | typedef signed \_int64 INT64|PINT32|	typedef INT32 \*PNT32
|UINT      | typedef unsigned int UINT|PINT64	|typedef INT64 \*PINT64
|UINT32    | typedef unsigned int UINT32|PUINT	|typedef UINT \*PUINT
|UINT64    | typedef unsigned \_int64 UINT64|PUINT32|	typedef UINT32 \*PUINT32
|LONG      | typedef long LONG|PUINT64|	typedef UINT64 \*PUINT64
|LONG32    | typedef signed int LONG32|PLONG|	typedef LONG \*PLONG
|LONG64    | typedef \_int64 LONG64|PLONG32|	typedef LONG32 \*PLONG32
|DOWRDLONG | typedef ULONGLONG DWORDLONG|PLONG64|	typedef LOGN64 \*PLONG64
|LONGLONG  | typedef \_int64 LONGLONG|PDWORDLONG|	typedef DWORDLONG \*PDWORDLONG
|ULONG     | typedef unsigned long ULONG|PLONGLONG|	typedef LONGLONG \*PLONGLONG
|ULONG32   | typedef unsigned int ULONG32|PULONG|	typedef ULONG \*PULONG
|ULONGLONG | typedef unsigned \_int64 ULONGLONG|PULONG32	|typedef ULONG32 \*PULONG32
|ULONG64   | typedef unsigned \_int64 ULONG64|PULONGLONG	|typedef ULONGLONG \*PULONGLONG
|FLOAT     | typedef float FLOAT |PULONG64|	typedef ULONG64 \*PULONG64
|PFLOAT	|typedef FLOAT \*PFLOAT |LPBOOL	|typedef BOOL far \*LPBOOL
|LPWORD	|typedef WORD \*LPWORD |LPDWORD|	typedef DWORD \*LPDWORD
|LPINT|	typedef int \*LPINT |LPLONG	|typedef long \*LPLON
|PWCHAR|	typedef CHAR \*PWCHAR|PWSTR	|typedef \_\_nullterminated WCHAR \*PSTR
|PCWSTR|	typedef \_\_nullterminated CONST WCHAR \*PCSTR | LPWSTR	|typedef \_\_nullterminated WCHAR \*LPSTR

该表未包含所有windows数据类型，具体可参考 [[windows数据类型 - mouseOS 技术小站](http://www.mouseos.com/win64/data_type.html)]

---

<h4 id="2">监视某个exe是否在运行</h4>
在解决这个问题之前，先得弄清楚c++中是否有一个数据结构来保存系统中运行的进程，查阅相关api知在c++中有一个名为PROCESSENTRE32的结构体，用于存放进程快照信息，其结构体源码如下：   
```cpp
typedef struct tagPROCESSENTRY32
{
    DWORD dwSize; //结构大小
    DWORD cntUsage; //引用计数，已弃用
    DWORD th32ProcessID;  //pid
    ULONG_PTR th32DefaultHeapID;  //默认进程堆，已弃用
    DWORD th32ModuleID; //进程模块，已弃用
    DWORD cntThreads; //此进程开启的进程计数器
    DWORD th32ParentProcessID;  //父进程pid
    LONG pcPriClassBase;  //线程优先级
    DWORD dwFlags;  //已弃用
    TCHAR szExeFile[MAX_PATH];  //进程全名(\*.exe)
} PROCESSENTRY32, *PPROCESSENTRY32;
```
得知这一结构体的存在后，需要找到如何去获取这样的一个结构体数组或者其他包含这个结构体的数据结构，在c++的库函数中，存在一个CreateToolhelp32Snapshot函数，这个函数的原型是`HANDLE WINAPI CreateToolhelp32Snapshot(DWORD dwFlags, DWORD th32ProcessID)`，头文件为`TlHelp32.h`,其中的参数含义如下：  
dwFlags:指定快照中包含的系统内容，可以使用下列数值的一个或多个：
>TH32CS_INHERIT - 声明快照句柄是可继承的。     
TH32CS_SNAPALL - 在快照中包含系统中所有的进程和线程。  
TH32CS_SNAPHEAPLIST - 在快照中包含在th32ProcessID中指定的进程的所有的堆。  
TH32CS_SNAPMODULE - 在快照中包含在th32ProcessID中指定的进程的所有的模块。  
TH32CS_SNAPPROCESS - 在快照中包含系统中所有的进程。  
TH32CS_SNAPTHREAD - 在快照中包含系统中所有的线程。  
Const TH32CS_SNAPHEAPLIST = &H1  
Const TH32CS_SNAPPROCESS = &H2  
Const TH32CS_SNAPTHREAD = &H4  
Const TH32CS_SNAPMODULE = &H8  
Const TH32CS_SNAPALL = (TH32CS_SNAPHEAPLIST | TH32CS_SNAPPROCESS | TH32CS_SNAPTHREAD | TH32CS_SNAPMODULE)  
Const TH32CS_INHERIT = &H80000000   

th32ProcessID:指定将要快照的进程ID，参数为0表示当前进程。注意该进程只有在设置了TH32CS_SNAPHEAPLIST或者TH32CS_SNAPMODULE后才会生效。
返回值为快照的句柄，如果调用失败，返回`INVALID_HANDLE_VALUE`。

通过查阅到该函数，便可以开始编写代码实现监视进程的功能了，这个功能的基本思路是遍历系统中 已经运行的进程列表，如果在该列表中能找到需要监视的进程，就可以说明该进程在运行，反之则说明没有运行该exe。

代码如下：  
```cpp
bool hasProcessRunning(string exename)
{
  map<string, int> processMap;
  PROCESSENTRY32 pe32;
  pe32.dwSize = sizeof(pe32);

  HANDLE processSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
  if (processSnap == INVALID_HANDLE_VALUE)
  {
     cout<<"CreateToolhelp32Snapshot Error!"<<endl;
     return false;
  }
  bool result = Process32First(processSnap, &pe32);
  int num(0);

  while (result)
  {
    string name = pe32.szExeFile;
    int id = pe32.th32ProcessID;

    processMap.insert(pair<string, int>(name, id));
    result = Process32Next(processSnap, &pe32);
  }

  CloseHandle(processSnap);

  map<string, int>::iterator it = processMap.begin();
  for (; it != processMap.end(); ++it)
    if (it->first == exename) return true;

  return false;
}
```

测试：  
首先编写简单的c++与c#测试程序（或者使用其他测试可运行文件），该程序为控制台程序，效果为在控制台输出一行“xxx is running”。
然后开启监视程序，分别传入测试程序的名称，查看输出结果，然后关闭测试程序再次在监视程序中查找测试程序，查看输出结果。
```cpp
// CppTest.cpp : Defines the entry point for the console application.
//
#include "stdafx.h"
#include <conio.h>

int main()
{
	puts("CppTest.exe is running...");
	puts("press any key to exit...");
	while (true)
	{
		if (_getch())
			break;
	}
	return 0;
}
```
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CSharpTest
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("CSharpTest.exe is running...");
            Console.WriteLine("press any key to exit...");

            Console.ReadKey(true);
        }
    }
}

```

运行结果如下：
![](..\assets\img\cpp\running.PNG)
![](..\assets\img\cpp\close.PNG)

---

<h4 id="3">启动外部程序</h4>
在c++程序中，有时候一个程序需要另外一个程序的配合才能正常完成操作，这时候会有一个启动外部程序的过程。  
这一过程的实现并不复杂，在win32API中，存在一个函数可以实现这一功能，它就是`CreateProcess`，该函数的头文件为`windows.h`，函数原型较为复杂，如下：
```cpp
BOOL CreateProcess
(
LPCTSTR lpApplicationName,
LPTSTR lpCommandLine,
LPSECURITY_ATTRIBUTES lpProcessAttributes,
LPSECURITY_ATTRIBUTES lpThreadAttributes,
BOOL bInheritHandles,
DWORD dwCreationFlags,
LPVOID lpEnvironment,
LPCTSTR lpCurrentDirectory,
LPSTARTUPINFO lpStartupInfo,
LPPROCESS_INFORMATIONlpProcessInformation
);
```
其中各个参数含义如下：
> lpApplicationName  
指向一个NULL结尾的、用来指定可执行模块的字符串。  
这个字符串可以是可执行模块的绝对路径，也可以是相对路径，在后一种情况下，函数使用当前驱动器和目录建立可执行模块的路径。  
这个参数可以被设为NULL，在这种情况下，可执行模块的名字必须处于 lpCommandLine 参数最前面并由空格符与后面的字符分开。  

>lpCommandLine  
指向一个以NULL结尾的字符串，该字符串指定要执行的命令行。  
这个参数可以为空，那么函数将使用lpApplicationName参数指定的字符串当做要运行的程序的命令行。  
如果lpApplicationName和lpCommandLine参数都不为空，那么lpApplicationName参数指定将要被运行的模块，lpCommandLine参数指定将被运行的模块的命令行。新运行的进程可以使用GetCommandLine函数获得整个命令行。C语言程序可以使用argc和argv参数。  

>lpProcessAttributes  
指向一个SECURITY_ATTRIBUTES结构体，这个结构体决定是否返回的句柄可以被子进程继承。如果lpProcessAttributes参数为空（NULL），那么句柄不能被继承。  
在Windows NT中：SECURITY_ATTRIBUTES结构的lpSecurityDescriptor成员指定了新进程的安全描述符，如果参数为空，新进程使用默认的安全描述符。  

>lpThreadAttributes  
同lpProcessAttribute,不过这个参数决定的是线程是否被继承.通常置为NULL.  
bInheritHandles
指示新进程是否从调用进程处继承了句柄。  
如果参数的值为真，调用进程中的每一个可继承的打开句柄都将被子进程继承。被继承的句柄与原进程拥有完全相同的值和访问权限。  

>dwCreationFlags  
指定附加的、用来控制优先类和进程的创建的标志。以下的创建标志可以以除下面列出的方式外的任何方式组合后指定。  

>lpEnvironment  
指向一个新进程的环境块。如果此参数为空，新进程使用调用进程的环境。  
一个环境块存在于一个由以NULL结尾的字符串组成的块中，这个块也是以NULL结尾的。每个字符串都是name=value的形式。  
因为相等标志被当做分隔符，所以它不能被环境变量当做变量名。  
与其使用应用程序提供的环境块，不如直接把这个参数设为空，系统驱动器上的当前目录信息不会被自动传递给新创建的进程。对于这个情况的探讨和如何处理，请参见注释一节。  
环境块可以包含Unicode或ANSI字符。如果lpEnvironment指向的环境块包含Unicode字符，那么dwCreationFlags字段的CREATE_UNICODE_ENⅥRONMENT标志将被设置。如果块包含ANSI字符，该标志将被清空。  
请注意一个ANSI环境块是由两个零字节结束的：一个是字符串的结尾，另一个用来结束这个快。一个Unicode环境块是由四个零字节结束的：两个代表字符串结束，另两个用来结束块。  

>lpCurrentDirectory  
指向一个以NULL结尾的字符串，这个字符串用来指定子进程的工作路径。这个字符串必须是一个包含驱动器名的绝对路径。如果这个参数为空，新进程将使用与调用进程相同的驱动器和目录。这个选项是一个需要启动应用程序并指定它们的驱动器和工作目录的外壳程序的主要条件。  

>lpStartupInfo  
指向一个用于决定新进程的主窗体如何显示的STARTUPINFO结构体。  

>lpProcessInformation  
指向一个用来接收新进程的识别信息的PROCESS_INFORMATION结构体。

测试：  
使用该函数打开一个外部程序，参数为该程序完整路径的代码如下：

```cpp
BOOL processStart(string path)
{
  STARTUPINFO si;
  memset(&si,0,sizeof(STARTUPINFO));
  si.cb=sizeof(STARTUPINFO);
  si.dwFlags=STARTF_USESHOWWINDOW;
  si.wShowWindow=SW_SHOW;
  PROCESS_INFORMATION pi;

  return CreateProcess(TEXT(path),
                      NULL,
                      NULL,
                      NULL,
                      FALSE,
                      0,
                      NULL,
                      NULL,
                      &si,
                      &pi
                      );
```

---
<h4 id="4">Windows下的简易socket程序</h4>
在这个例子中使用的IDE是 DevCpp 4.9.2版本。  
> 注意：在Dev中使用socket API需要在 "Project -> Project Options -> Parameters -> Linker"中添加一行"-lwsock32"，然后在预编译语句下方添加"#pragma comment(lib,"WS2_32")"才可以正常使用socket相关API。  

在c++程序中对TCP与UDP的操作几乎是通过socket来完成的。
socket通信流程图如下：  
![](..\assets\img\cpp\socketflow.gif)  
图片来自：http://blog.csdn.net/heyutao007/article/details/6588302

Windows下的Socket API在使用之间需要通过WSAStartup函数进行初始化来指明WinSocket规范的版本，其函数原型为  
> int WSAStartup(WORD wVersionRequested, LPWSADATA lpWSAData);    

WSAStartup函数执行成功后，会将与 ws2_32.dll 有关的信息写入 WSAData 结构体变量。WSAData 的定义如下：

```cpp
typedef struct WSAData {
    WORD           wVersion;  //ws2_32.dll 建议我们使用的版本号
    WORD           wHighVersion;  //ws2_32.dll 支持的最高版本号
    //一个以 null 结尾的字符串，用来说明 ws2_32.dll 的实现以及厂商信息
    char           szDescription[WSADESCRIPTION_LEN+1];
    //一个以 null 结尾的字符串，用来说明 ws2_32.dll 的状态以及配置信息
    char           szSystemStatus[WSASYS_STATUS_LEN+1];
    unsigned short iMaxSockets;  //2.0以后不再使用
    unsigned short iMaxUdpDg;  //2.0以后不再使用
    char FAR       *lpVendorInfo;  //2.0以后不再使用
} WSADATA, *LPWSADATA;
```

知道了以上内容，可以参考下面的代码来对socket编程有更深一步的了解。
```cpp
//服务端代码
#include <stdio.h>
#include <winsock2.h>
#pragma comment (lib, "ws2_32.lib")  //加载 ws2_32.dll
int main()
{
    //初始化 DLL
    WSADATA wsaData;
    WSAStartup( MAKEWORD(2, 2), &wsaData);
    //创建套接字
    SOCKET servSock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);
    //绑定套接字
    sockaddr_in sockAddr;
    memset(&sockAddr, 0, sizeof(sockAddr));  //每个字节都用0填充
    sockAddr.sin_family = PF_INET;  //使用IPv4地址
    sockAddr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
    sockAddr.sin_port = htons(1234);  //端口
    bind(servSock, (SOCKADDR*)&sockAddr, sizeof(SOCKADDR));
    //进入监听状态
    listen(servSock, 20);
    //接收客户端请求
    SOCKADDR clntAddr;
    int nSize = sizeof(SOCKADDR);
    SOCKET clntSock = accept(servSock, (SOCKADDR*)&clntAddr, &nSize);
    //向客户端发送数据
    char *str = "Hello World!";
    send(clntSock, str, strlen(str)+sizeof(char), NULL);
    //关闭套接字
    closesocket(clntSock);
    closesocket(servSock);
    //终止 DLL 的使用
    WSACleanup();
    return 0;
}
```

```cpp
//客户端代码
#include <stdio.h>
#include <stdlib.h>
#include <WinSock2.h>
#pragma comment(lib, "ws2_32.lib")  //加载 ws2_32.dll
int main()
{
    //初始化DLL
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    //创建套接字
    SOCKET sock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);
    //向服务器发起请求
    sockaddr_in sockAddr;
    memset(&sockAddr, 0, sizeof(sockAddr));  //每个字节都用0填充
    sockAddr.sin_family = PF_INET;
    sockAddr.sin_addr.s_addr = inet_addr("127.0.0.1");
    sockAddr.sin_port = htons(1234);
    connect(sock, (SOCKADDR*)&sockAddr, sizeof(SOCKADDR));
    //接收服务器传回的数据
    char szBuffer[MAXBYTE] = {0};
    recv(sock, szBuffer, MAXBYTE, NULL);
    //输出接收到的数据
    printf("Message form server: %s\n", szBuffer);
    //关闭套接字
    closesocket(sock);
    //终止使用 DLL
    WSACleanup();
    system("pause");
    return 0;
}
```
参考资料：[C/C++ socket编程教程:1天玩转socket通信技术_C语言中文网](http://c.biancheng.net/cpp/html/3029.html)

---