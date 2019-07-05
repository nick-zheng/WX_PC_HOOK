**WeChat PC Hook** 笔记



**知识点：**

**注入DLL**

​	createToolhelp32Snapshot //获取进程快照取得进程PID

​	VirtualAllocEx //申请内存

​	WriteProcessMemory //用来写入注入DLL的路径

​	GetModuleHandle //获取Kernel32基址

​	GetProcAddress //获取加载DLL函数的地址

​	CreateRemoteThread //在别人的进程里执行 加载DLL函数，从而加载我们的DLL实现注入



​	Kernel32.dll > LoadLibrary



​	原理、过程：

![Pasted Graphic 1.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/8/Pasted%20Graphic%201.png)











**知识点：**

**应用双开：**

微信采用互斥体来实现防止程序双开的：

CreateMutex

只要HOOK这个函数，修改name，就可以双开微信











**知识点：**

**获取微信登录二维码：**

图片数据在内存的表现形式（如微信登录二维码）

用到工具有：CE、OD

NG……IHDR       //这就是PNG图片的前缀



解析二维码内容，在CE中搜索，等待二维码变化，看看搜索出来的变化了的

记录下地址，在OD中dd xxxxx查看，在内存中下断点，等待二维码刷新变化进入断点，

在右下点击value，在内存中查看基址

![Pasted Graphic.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/8/Pasted%20Graphic.png)

获取下一行代码的地址



复杂的HOOK要注意保存寄存器和堆栈，处理完HOOK后再复原寄存器和堆栈







找到二维码图片地址，在CUP中运行脚本：

dm xxxx,xx,”code.png”   //dm 内存地址，大小，“保存图片名称”

ret









**知识点：**

**Inlink HOOK的原理：**

![Pasted Graphic 1.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/8/Pasted%20Graphic%201.png)



**一、手动在OD修改：**

1.找到入口处，在：E8 xxxxx //call WeChatWin.xxxxx ;HOOK这里

2.修改为：E9 xxxxx //jmp xxxxx自己写的汇编代码地址（在OD空白区域写的代码）

3.编写hook代码

   xxxxx //修改变量的值之类的

   jmp xxxxx //跳回到原来逻辑要执行的地址：原来入口地址的下一行的地址



**二、硬编码实现：**

1.找到入口处，在：E8 xxxxxx //call WeChatWin.xxxxx  ;HOOK这里

2.修改为：E9 xxxxx //jmp xxxxx自己写的汇编代码地址（是按公式计算出来的地址，不是代码所在内存地址，公式：要跳转的地址 - hook的地址 - 5)

3.编写hook写内存代码:

​    pic = ecx ;//上一行中的参数

​    show

​    ….

​    jmp xxxxxx //跳回到原来逻辑要执行的地址：原来入口地址的下一行的地址