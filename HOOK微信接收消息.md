**HOOK**微信接收消息

分析和实现





**一、分析&基址**



**关键词：**与消息最直接相关的东西（入口点：**消息本身**）

**工具：**CE、OD



![Pasted Graphic 14.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2014.png)

再发送一个内容，再点Next Scan。。。

![Pasted Graphic 15.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2015.png)

修改Type大小，内容里有<msgsource>之类的才是我们要找的地址，这是比较完整的数据





用这个地址：0B10483E

在OD中下**内存写入**断点，再用另一个微信号发一个消息，进入断点后先把内存断点删除，再进行分析：

![Pasted Graphic 17.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2017.png)

再往下找比较外层的，

![Pasted Graphic 18.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2018.png)

遇到这类型的，再继续往下找一下，

![Pasted Graphic 19.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2019.png)

遇到这类型的，就在附近总会有call的：

![Pasted Graphic 20.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2020.png)

回车，观察CPU窗口：

![Pasted Graphic 23.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2023.png)



**（要学会看地址里面的内容来判断，来找call，不一定要死记硬背这个”lass SyncMgr”字符串来找call)**



**再用另一个微信发送一条消息，进入断点，**

**dd [[esp]]，查看内存中的内容**

![Pasted Graphic 24.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2024.png)

![Pasted Graphic 25.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2025.png)



![Pasted Graphic 26.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2026.png)

![Pasted Graphic 27.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2027.png)

![Pasted Graphic 28.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2028.png)



[[esp]]的地址是：       06C11F38

wxid的地址是：          06C11F78

消息内容的地址是：   06C11FA0

未知一串码的地址是：06C12060

**[[esp]] + 0x40 如果是群消息则是群wxid，如果是个人消息则是个人wxid**

**[[esp]] + 0x68 是消息内容文本xml格式**

**[[esp]] + 0x114 如果是群消息则是个人wxid，如果是个人消息则是0x0（可以用这个来判断是否是个人消息还是群消息）**

**[[esp]] + 0x128 未知的一串码**

**[[esp]] + 0x168 是<msgsource>….</msgsource>结构**



这里没有找到群名称 或 好友名称，这个等后续学会获取群列表 或 联系人列表，通过wxid来获取群名称 或 联系人名称即可实现



![Pasted Graphic 29.png](/var/folders/_8/n5n2trzn1tqc8pw803z2wtzm0000gn/T/abnerworks.Typora/6FF351BA-A963-4CC6-94E6-D3DC72B097F2/Pasted%20Graphic%2029.png)

WeChatWin.dll基址：737E0000

接收消息call地址：    73AF5E98  

**接收消息call的HOOK地址：WeChatWin.dll + 0x315E98**

**接收消息call的返回地址：WeChatWin.dll + 0x315E9B**







**二、代码实现**