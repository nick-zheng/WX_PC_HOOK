微信**HOOK**基址

windows PC版



windows 

微信2.6.8.52（微信2.6.8.51也可以使用）



​									Address		Value		   基址+偏移（偏移：OD中找到的地址xxx-dll基址（xxx-6BC20000))

dll基址：					6BC20000	00905A4D		 WeChatWin.dll

微信名称基址：        WeChatWin.dll+126D91C     6CE8D91C	捣鼓一下IT		WeChatWin.dll + 0x0126D91C

微信手机码：			6CE8D950	189xxxx2615	WeChatWin.dll + 0x0126D950

微信地址-国家：	   6CE8DAF8	CN 				     WeChatWin.dll + 0x0126DAF8

微信地址-省：		   6CE8DA08	Guangdong	  WeChatWin.dll + 0x0126DA08

微信地址-市：		  6CE8DA20	Guangzhou		WeChatWin.dll + 0x0126DA20

微信号（wxid）：   6CE8DA80	zheng_guoli2	 WeChatWin.dll + 0x0126DA80

登录设备：			   6CE8DD48	android			  WeChatWin.dll + 0x0126DD48

微信头像指针：	   6CE8DBE4	0C91EA58		  WeChatWin.dll + 0x0126DBE4

微信头像 				 0C91EA58    	http://wx.ql..	



微信登录二维码变化内容：057FDC40	0aGaay....//根据057FDC40在OD中dd查看，在内存中下断点，再找基址

微信登录二维码基址：		6F20F618	4 Bytes(指针)		WeChatWin.dll + 0x0127F618
