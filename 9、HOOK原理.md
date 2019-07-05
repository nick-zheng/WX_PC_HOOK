**HOOK**原理

Inline Hook





Inline Hook 是使用最广泛的也是自由度在r3（系统3环里自由度最大的），这种hook可以对任何地址进行hook

其他hook有：apihook，消息hook等（有局限性，如apihook只能hook windows的api，消息同理）





**HOOK流程：**

![Pasted Graphic 33.png](https://github.com/nick-zheng/WX_PC_HOOK/blob/master/_images/9/Pasted%20Graphic%2033.png)



要先找到目标函数的基址：xxxxxx

定位到call的地址

call上面的是参数，如果需要修改参数，则可以自己手写一段代码，修改原来的参数（要注意参数的位数大小要一致，不然会覆盖后面的代码，程序就会出错崩溃）



或者

nop掉参数和call的代码，写自己的jmp xxxxx 函数，xxxxx函数里处理完后一定要jmp回来原来的call的下一行代码



**如果做复杂的操作，一定要保存一下原来的寄存器和堆栈的数据，处理完后再恢复回来，不然程序也是会出错崩溃的**