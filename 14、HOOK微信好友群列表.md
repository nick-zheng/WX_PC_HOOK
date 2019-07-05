**HOOK**微信好友群列表

分析和实现



**一、分析&基址**

获取好友列表目前有五种方法：

**第一种：**二叉树遍历，一层一层的往下读取，类似递归算法**（关于算法方面的方法）**；

**第二种：（关于内存方面的方法）**【课程介绍的主要方法，比较容易掌握，有个不好的地方就是数据人有重复（这个可以在后期代码里处理掉重复的数据即可，不难）】；

**第三种：**类似找登录二维码图片地址一直的方法，先找未初始化的值，再切换到联系人，再搜索值改变的…该方法比较麻烦**（关于内存方面的方法）**；

**第四种：**直接select * from 查微信数据库来获取列表**（关于数据库方面的方法）**；

**第五种：**根据好友列表条数来查找，删除一个再查找，再删除再查找。。。；





基于第二种内存方法来分析：

![Pasted Graphic 1.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%201.png)

思路：点击左侧联系人，右侧就会显示该联系人的信息，分析微信应该是这样实现的：点击左侧联系人时，根据这个联系人的wxid调用一个方法来获取联系人的信息 并 显示出来。这就是突破口。



突破口：在点击左侧联系人时，我们要找到微信去获取联系人信息的函数。微信登录的时候会加载所有联系人信息，也是会调用这个函数的。



开始分析：

![Pasted Graphic 2.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%202.png)

在CE中搜索这个wxid:

![Pasted Graphic 3.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%203.png)

切换几个联系人，就能找到几个结果出来，一个一个地在OD中试：

![Pasted Graphic 4.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%204.png)

下一个内存写入断点，再切换联系人，进入断点，再堆栈中分析（堆栈中会显示这个联系人的一些信息）：

![Pasted Graphic 5.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%205.png)

在这个函数之前，就已经拿到联系人信息数据了的，这里看看这个的CPU中分析：

![Pasted Graphic 7.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%207.png)

![Pasted Graphic 8.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%208.png)

![Pasted Graphic 9.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%209.png)

到这里找到了一个疑似call，可以删除一个好友来让好友列表刷新，来验证一下微信加载联系人是否也是调用这一个call，如果删除好友后联系人列表刷新了，但是没有进入这个call，那么这个call就不是我们需要的，需要再继续找call**（我们需要的call是，当微信登录后加载联系人自动进入的这个call，以及联系人变化后 或 每次进来加载的时候 也会自动进入这个call，这个才是我们需要的）**;



再回到CE中，复制下一个找到的地址，在OD中继续按上面的步骤进行操作和分析；

![Pasted Graphic 10.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2010.png)



往前找到call入口 ebp 下断点：

![Pasted Graphic 11.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2011.png)

如果每次刷新列表（删除一个联系人）的时候都经过这里的话，那这个就是我们想要的；

![Pasted Graphic 12.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2012.png)



![Pasted Graphic 13.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2013.png)

这个call执行完后，再观察寄存器的数据，在edi里找到的个人信息



在这个call的前面再看看，有一个swift的分支处理，每一个状态都一个分支处理的call：

![Pasted Graphic 14.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2014.png)

有时间可以分析研究一下这里有什么用；



当然这里不是我们要找的call，我们要找的call是，经过这个call，我们能拿到所有联系人的数据的那个call;

![Pasted Graphic 15.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2015.png)



![Pasted Graphic 16.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2016.png)

在这个call下断，再分析

![Pasted Graphic 17.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2017.png)

按一下这个重新来，看看微信登录的时候会不会自动进入这个call

![Pasted Graphic 18.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/14/Pasted%20Graphic%2018.png)

自动进入了这个call，这个就是我们要找的call。

每进来多次，查看eax 每一个联系人信息都有，也会有重复的联系人信息。

call地址：   5D090994

下一行地址: 5D090999



eax + 0x10   wxid 群

eax + 0x30   wxid 群

eax + 0x44  微信号

eax + 0x58  V1数据

eax + 0x8C  昵称

eax + 0x11C 小头像

eax + 0x130 大头像

eax + 0x144 未知md5数据

eax + 0x1C8 国籍

eax + 0x1DC 省份

eax + 0x1F0 城市

eax + 0x204 添加来源

eax + 0x294 朋友圈壁纸









**二、代码实现**

