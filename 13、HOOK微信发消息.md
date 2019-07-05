**HOOK**微信发消息

分析和实现





**一、分析 & 找基址：**



**错误思路：**

​	下断点不正确，如下发包断点：ws2_332.send 这样就会进处死循环（确实能断下来，但是会进入死循环跳不出去），所以很难找到call；

**正确思路：**

​	一个发送消息的函数，至少是需要两个参数：

​	发给谁（wxid），

​	发送的内容是什么（发送内容）；

​	所以找call至少要有两个参数以上的才能被我们拿去分析的，不满足的根本不用看。



**工具：**OD、CE



**步骤：**

1、用OD附加微信、CE也附加微信：



有两个突破口：

一个是从发送的内容入手（追溯从哪里调用的）；

一个是从我发给谁入手（如发给文件助手filehelpser，从wxid来找，这种比较容易）；



从wxid入手来找，CE中：

![Pasted Graphic.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic.png)

找到几百个结果，个人的微信id都是wxid_开头的，我们切换成个人聊天窗口，再CE中搜索wxid_

![Pasted Graphic 2.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%202.png)

现在只有8个结果，再找切回filehelper

![Pasted Graphic 1.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%201.png)

将上面的结果都拿下来观察：

切来切去，最后确定下一个基址，在OD中下内存访问断点来分析，往前找到push edp，前面有好多int3什么的就是参数定义，在push edp这里下个断，再在堆栈中定位真正call入口（在堆栈里找到返回WeChatVi.xxx的，回车，在CUP中查看往前找到push多个参数的）

![Pasted Graphic 6.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%206.png)



内容：

![Pasted Graphic 7.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%207.png)

wxid：

![Pasted Graphic 8.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%208.png)

第二行是内容的长度，

第三行是内容的长度的两倍就可以了。

![Pasted Graphic 10.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%2010.png)



我们可以手动构造一个这样的内存结构，来更改要发送的内容；

发送内容：

![Pasted Graphic 11.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%2011.png)

微信id也是和发送内容一样的结构体；



发送内容的结构体：

address1 发送内容的地址（addressText)

address2 发送内容的长度

address3 发送内容的长度的两倍



addressText 发送的内容文本(text, unicode)







dll基址：		         737E0000

**发送消息的call:	 738ABD08** 	

**发送消息call的HOOK地址：WeChatWin.dll + 0xCBD08**





**edx: wxid**

**push 0x1**

**eax:** 可以是**0x0**，如果是**@**人 那么就是被**@**的那个人的**wxid**指针

**ebx:** 发送的消息内容

**ecx:** 缓冲区

**esp:** **平衡堆栈，不平栈就会崩溃**











过程记录：



738ABCF8    8B55 CC         mov edx,dword ptr ss:[ebp-0x34]          ; wxid

738ABCFB    8D43 14         lea eax,dword ptr ds:[ebx+0x14]          ; 分配一块空间，代码实现时这行可以不用写

738ABCFE    6A 01           push 0x1                                 ; 0x1

738ABD00    50              push eax                                 ; 可以直接传零：0x0，如果是@人 那么就是被@那个人的wxid指针

738ABD01    53              push ebx                                 ; 发送的消息内容

738ABD02    8D8D E4F7FFFF   lea ecx,dword ptr ss:[ebp-0x81C]         ; 0x81C大小的缓冲区

738ABD08    E8 D3F72100     call WeChatWi.73ACB4E0                   ; 疑似2：发消息的call

738ABD0D    83C4 0C         add esp,0xC                              ; 外平栈

738ABD10    50              push eax                                 ; WeChatWi.745E0E29

738ABD11    8D8D A4FBFFFF   lea ecx,dword ptr ss:[ebp-0x45C]







dll基址：		         737E0000

发送消息的call:	738ABD08 	

**发送消息call的HOOK地址：WeChatWin.dll + 0xCBD08**





原始发送内容：

06B06CC4  06DECA48  UNICODE "测试4"    ;内容

06B06CC8  00000003					  ;内容大小

06B06CCC  00000004					  ;内容大小的整数倍，建议是填内容大小的2倍即可



06DECA48   测试4







我人有的和主产不为这

0xA

0x14





找一片空白的内存区域构造发送内容的结构体：

06DB0120  06DB0130  UNICODE "我人有的和主产不为这"

06DB0124  0000000A

06DB0128  00000014

06DB012C  00000000

06DB0130  4EBA6211

06DB0134  76846709  advapi32.76846709

06DB0138  4E3B548C

06DB013C  4E0D4EA7

06DB0140  8FD94E3A







06DB0120 发送内容的结构体地址（06DB0130）

06DB0124 发送内容的长度（0xA）

06DB0128 发送内容的长度的两倍（0x14）



06DB0130 发送内容文本（我人有的和主产不为这）







![Pasted Graphic 12.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/13/Pasted%20Graphic%2012.png)

继续执行，会发送了新构造的内容







**二、代码实现：**