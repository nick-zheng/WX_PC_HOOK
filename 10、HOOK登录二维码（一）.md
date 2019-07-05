**HOOK**登录二维码（一）

方法一





**一、分析&找基址**



**工具：**CE、OD

**突破口：**二维码未被初始化，以及被初始化赋值，以及重新赋值之后 的在内存的值都是会变化的。

**总体流程：**需要使用CE首次扫码未被初始化的内存数据，再切换账号（改变二维码内容）CE再次扫描变化的值，再用手机微信扫码（不登录）CE再次扫码变化的值，再点击返回二维码登录（改变二维码内容）CE再次扫描变化的值，…，一直重复多次，直至找到剩下比较少量的绿色的基址，从中分析（二维码不变动的情况下，把那些基址经常变化的给剔除掉，剩下不常变的作为分析的数据）



**1、一定要在这个页面附加CE:**

![Pasted Graphic.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic.png)



**2、首次搜索：**

![Pasted Graphic 1.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%201.png)



首次搜索，搜索出548万个结果

![Pasted Graphic 2.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%202.png)

这个要一遍一遍地筛选，大概要五六遍。。。





**3、切换账号（出现二维码页面）：**

![Pasted Graphic 3.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%203.png)

![Pasted Graphic 4.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%204.png)

![Pasted Graphic 5.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%205.png)

还有26万个搜索结果

![Pasted Graphic 6.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%206.png)



**4、用手机微信扫一下二维码（千万不要登录）：**

再次搜索改变的值

![Pasted Graphic 8.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%208.png)

还在9万多个结果

![Pasted Graphic 9.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%209.png)



**5、再多次重复以上的这些操作：**

返回登录，再搜索改变的值

![Pasted Graphic 10.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2010.png)

再手机扫码，再搜索改变的值

![Pasted Graphic 8.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%208.png)

就这样重复几遍



**6、一直到搜索结果一直都是几千不会降下来了为止：**

![Pasted Graphic 11.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2011.png)

列表拉到下面绿色的那些项，这里面就有二维码的基址，需要分析这些找到二维码的基址

![Pasted Graphic 12.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2012.png)

![Pasted Graphic 13.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2013.png)



**7、观测变化：**

![Pasted Graphic 14.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2014.png)



微信没动的话，把那些值经常变的给删除掉，剩下的那些不变的就很有可能是我们要找的基址

![Pasted Graphic 15.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2015.png)

**8、打开OD，查看剩下的这些地址的值：**

dd xxxxx

打一个内存写入断点

切换微信，把二维码搞出来，使之进入内存断点



只有出现这种情况的，才开始分析，其他的就放过（不要取消断点，点击微信切换账号要快点点击，不然很快就会进断点）

![Pasted Graphic 16.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2016.png)



然后看堆栈里，找WeChatWin.dll相关的函数，进行分析

![Pasted Graphic 18.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2018.png)



![Pasted Graphic 19.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2019.png)



![Pasted Graphic 20.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2020.png)



往下找外层的，连续找几个，找到的都下一个CPU断点，来测试

![Pasted Graphic 21.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2021.png)



全部过掉，再扫一下二维码，进入断点，一直放过去，点击返回二维码（还没点击之前进入断点的就不是我们要找的，要移除掉这些断点），

只有点击了 又未画出二维码的情况下的断点，才是我们需要的

![Pasted Graphic 22.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2022.png)

查看这里装的是什么内容 dd [ecx]回车，看不出来是什么内容的话就用 dc [ecx]回车

如果有89 50 4E 47之类的特征，就很有可能了，特别是内容有 PNG之类的



可以使用打印数据的命令：

dm 数据的地址,数据的长度,”保存文件的名称(如QrCode.png)”

ret



举例：

dm 05617508,F00,”QrCode.png”

ret



保存这个文件为xxx.txt



在CPU窗口中任意位置右键，运行脚本，打开刚才保存的xxx.txt

![Pasted Graphic 23.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2023.png)

然后到微信的安装目录下找保存的图片QrCode.png

这个查看一下，是不是二维码，微信扫码一下，也能登录微信



如果找到了，就在CPU窗口中那个call位置添加备注：

![Pasted Graphic 24.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2024.png)

为了确定一下，可以再跑一遍扫码》返回，进入断点，dm保存图片查看~



![Pasted Graphic 25.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic%2025.png)

接下来就算一下偏移：

call的地址：FEE4638

WeChatWin.dll的地址：FD000000

call的偏移地址：FEE4638-FD000000=1E4638

下一个执行代码（返回）地址：FEE463D

下一个执行代码（返回）的偏移地址：FEE463D-FD000000=12EE463D











**二、代码实现**

将

E8 D34D2900 call WeChatWi.10179410 //这个call修改成:

E9 xxxxxxxxxx jmp //这个jmp到自己的代码块进行处理

​	//处理，处理完后再

​	//jmp FEE463D //jmp到原来call的下一行代码地址



说明：E8 D34D2900 里的这个D34D2900，或E9 xxxxxxxx 里的xxxxxxxx 并不是要执行的函数的地址，而是根据公式计算出来的地址，

公式：要跳转的地址 - hook的地址 - 5

所以要按公式算出xxxxxxxx的地址





创建一个dll工程；

创建一个对话框界面，一个图片框，两个按钮（一个是开始hook，一个是卸载hook），一个文本展示hook的状态；





hook.cpp里实现：

\#include <Windows.h>

\#include <stdio.h>

\#include <atlimage.h>



\#define HOOK_LEN =5

DWORD backupCode[HOOK_LEN];

HWND hDlg = 0;



获取”weChatWin.dll”模块基址：

​	DWORD getWeChatWin() { 

​		return (DWORD)LoadLibrary(“WeChatWin.dll”); 

​	}

开始hook: 

​	VOID StartHook(DWORD hookPosAddress, DWORD hookFunc, HWND hwndDlg) {

​		hDlg = hwndDlg;

​		

​		//hook地址 = 根据模块基址 + hook函数的偏移地址;

​		//跳转地址 = 要跳转的地址 - hook地址 - 5；//jmpAddress = (DWORD)hookFunc - hookAddress - HOOK_LEN;

​		//组装数据byte 

​		BYTE jmpCode[HOOK_LEN] = {0};

​		jmpCode[0] = 0xE9; //jmp

​		*(DWORD *)&jmpCode[1] = jmpAddress;

​		//备份寄存器里的数据

​		HANDLE hWHND = OpenProcess(PROCESS_ALL_ACCESS, NULL, GetCurrentProcessId());

​		//备份

​		if (ReadProcessMemory(hWHND, (LPCVOID)hookAddress, backupCode, HOOK_LEN, NULL) == 0) {

​			return;

​		}

​		//写入组装好的数据

​		if (WriteProcessMemory(hWHND, (LPVOID)jmpAddress, jmpCode, HOOK_LEN, NULL) == 0 ) {

​			return;

​		}

​	}

卸载hook: 

​	VOID StopHook(DWORD hookPosAddress) {

​		//	

​	}

显示图片：

​	VOID SaveImg(DWORD qrCode) {

​		DWORD picLen = qrCode + 0x4; //图片长度

​		char picData[0xFFF] = {0}; //图片的数据

​		size_t cpyLen = (size_t)*(LPVOID *)picLen);

​		memcpy(picData, *((LPVOID *)qrCode), cpyLen);

​		FILE *pFile;

​		fopen_s(&pFile, “qrCode.png”, “wb”);

​		fwrite(picData, sizeof(char), sizeof(picData), pFile);

​		fclose(pFile);

​	}



​	VOID ShowImageToDialog() {

​		//显示图片到dialog上

​		CImage *img;

​		CRect rect;

​		HWND hPic = GetDlgItem(hDlg, ID_QR);

​		GetClientRect(hPic, &rect);

​		//载入图片

​		img.Load(“qrCode.png”);

​		img.Draw(GetDC(hPic), rect);

​	}

​	

​	DWORD pEAX = 0;

​	DWORD pECX = 0;

​	DWORD pEDX = 0;

​	DWORD pEBX = 0;

​	DWORD pEBP = 0;

​	DWORD pESP = 0;

​	DWORD pESI = 0;

​	DWORD pEDI = 0;

​	DWORD baseAddress = getWechatWin(); //模块基址

​	DWORD backAddress = baseAddress + 0x1E463D; //返回的地址

​	自己的方法一定要定义为裸函数，且变量都在外面定义好，这样告诉编译器不做其他多余的操作

​	VOID __declspec(naked) ShowPic() {

​		//备份寄存器

​		__asm {

​			mov pEAX, eax

​			mov pECX, ecx

​			mov pEDX, edx

​			mov pEBX, ebx

​			mov pEBP, ebp

​			mov pESP, esp

​			mov pESI, esi

​			mov pEDI, edi

​		}

​		

​		//二维码数据在ecx中

​		SaveImg(pECX);

​		//显示图片到dialog上

​		ShowImageToDialog();

​		

​		//返回的地址backAddress

​		//恢复寄存器

​		__asm {

​			mov eax, pEAX

​			mov ecx, pECX

​			mov edx, pEDX

​			mov ebx, pEBX

​			mov ebp, pEBP

​			mov esp, pESP

​			mov esi, pESI

​			mov edi, pEDI

​			jmp backAddress

​		}

​	}







也可以参考：

https://www.52pojie.cn/thread-977238-1-1.html







![Pasted Graphic_1.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/10/Pasted%20Graphic_1.png)





7306916E    8D4D C4         lea ecx,dword ptr ss:[ebp-0x3C]

73069171    E8 1A050000     call WeChatWi.73069690                   ; 疑似1

73069176    83C4 10         add esp,0x10





0381F1EC  |08C11010  ASCII "/cgi-bin/micromsg-bin/getloginqrcode"



731A6CCA    8D4D AC         lea ecx,dword ptr ss:[ebp-0x54]

731A6CCD    E8 5EBEFFFF     call WeChatWi.731A2B30                   ; 疑似8

731A6CD2    8B4D F4         mov ecx,dword ptr ss:[ebp-0xC]