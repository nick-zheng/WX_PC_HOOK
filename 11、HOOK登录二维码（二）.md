**HOOK**登录二维码（二）

方法二





**一、分析&找基址**



**前提：**二维码里面的数据是文本内容，如一个链接

**工具：**https://cli.im/tools、CE、OD

**突破口：**通过二维码的文本内容来找到装这个二维码文本内容的基址

**总体流程：**CE里搜索二维码里http://weixin.qq.com/x/后面的内容（这部分内容才是从服务器获取的动态更新的，只有这部分内容才能在CE中找得到），搜索结果出来后，等二维码变更，把那些没变更的，或者格式不太对的给剔除掉，最终能找到一个二维码内容的地址，





**1、CE中查找二维码变更内容：**

建议最好在几分钟内查找（微信会隔一段时间就更新二维码的）

![Pasted Graphic 26.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/11/Pasted%20Graphic%2026.png)

![Pasted Graphic 27.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/11/Pasted%20Graphic%2027.png)

使用https://cli.im/tools 工具上传微信登录二维码图片，拿到内容，与CE里这个地址显示的文本内容是否一样，一样则是正确的了

0802D718就是二维码文本内容的地址，需要找到它的基址（使用OD）



**2、OD中查找二维码文本内容的基址：**

dd 0802D718

打一下内存写入断点（要快一点，可能微信二维码会刷新）

![Pasted Graphic 29.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/11/Pasted%20Graphic%2029.png)



等待二维码刷新，进入断点

![Pasted Graphic 30.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/11/Pasted%20Graphic%2030.png)

![Pasted Graphic 31.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/11/Pasted%20Graphic%2031.png)



**3、计算二维码文本内容偏移地址：**

![Pasted Graphic 32.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/11/Pasted%20Graphic%2032.png)

二维码文本内容基址：5901658C (这是一个指针，指向一个地址，地址装的内容是二维码文本内容）

WeChatWin.dll基址：57ED0000

偏移地址：5901658C - 57ED0000 = 114658C

**至此，找到了二维码文本内容基址：WeChatWin.dll + 0x114658C** （要注意：这是一个指针）









**二、代码实现**