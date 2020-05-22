# 未来人类T500 10.15.4 黑苹果配置

让你的杂牌机器吃上黑苹果

## 电脑配置
| 规格|详细信息|不能驱动|
| -------- | ---------------------------------------- |-------- |
| 电脑型号 | Terrans Torce T500 笔记本电脑 |
| 主板芯片组 | 100 Series/C230 Series 芯片组 Family - A152 |
| BIOS版本|	E16M3IT8.313  /  BIOS程序发布日期: 03/29/2017|
| 处理器 | 英特尔 core i7-7700HQ @2.80GHz 四核|
| 内存 | 16gb 英睿达 crucial DDR4 8GB 2400MHz+8GB 2667MHz|
| 硬盘 | Lenovo SSD SL700 M.2 128G + 日立 HGST HTS721010A9E630机械硬盘|
| 核显 | 英特尔 HD Graphics 630|
| 独显 | Nvidia GeForce GTX 1050 Ti| × |
| 显示器 | 内置 15.5 英寸 京东方 BOE0632 外接 HDMI 转vga 冠捷 AOC1936 936W|
| 声卡 | 瑞昱 ALC899 @ 英特尔 High Definition Audio 控制器 |
| 无线网卡 | 英特尔 Dual Band Wireless-AC 8265 | × |
| 网卡 |	鈺硕 Killer E2500 Gigabit Ethernet Controller |
| 摄像头 | USB 2.0 Webcam Device |

## 哪些不能在Clover正常工作

- 冷启动启动HDMI接口
  - 启动后两次插入hdmi正常
- minidp接口无法驱动
- n卡独显在10.15catalinna中无法驱动
  - 10.13.6中没有尝试
- 读卡器
  - 没有设备测试,未驱动
- 有关intel无线网卡无法驱动
- 其他都工作正常



## 安装

使用黑果小兵[macOS Catalina 10.15.4 19E287 正式版 with Clover 5112](https://blog.daliansky.net/macOS-Catalina-10.15.4-19E266-Release-version-with-Clover-5107-original-image-Double-EFI-Version-UEFI-and-MBR.html#more)的镜像替换我的efi进行安装.系统安装完成后使用Hackintool驱动了HD630核显.

后来只能用voodoohda驱动声卡，然后在最后写了定制applealc过程，用上原生态声卡。



##可正常启动机型记录

1. MacBookPro14,3 (当前使用)

## 常见问题解答

### 插入hdmi接口外置显示器开机启动黑屏怎么办?

使用kext unility 写入 [AppleGraphicsControl.kext.zip](https://pan.baidu.com/s/1uI0B-bkmFKMF8IiJkq7Sng) 提取码: xmnf

###  主题问题
默认是1080p因为我是1080p屏幕,需要4k的我还没更新


## 更新日志
2020-4-21

- 抛弃voodoohda改用applehda原生声卡
- 自己定制的applealc

2020-4-12

- 加入notouchid和acpiACPIBatteryManager两个驱动

2020-4-8

- 修复主题中显示不正常的问题


## 声卡

- Lilu+appleALC 无法驱动 尝试很多layoutid都不管用
- voodooHDA 可以驱动但会出现音质差爆音状况
- 尝试定制appleALC驱动

### 定制applealc驱动

#### **1、提取codec**

通过CLOVER提取codec
操作方法：
• CLOVER v2.4k r4886或者以上
• CLOVER里要有AudioDex-64.efi
• CLOVER引导界面，按F8快捷键，它会在EFI/CLOVER/misc目录下生成以HdaCodec开头的文件，其中的HdaCodec#x (Realtek ALCxxx).txt就是你需要提取的codec
通过linux提取codec
操作方法：任意一个linux或口袋LINUX启动后，进入路劲proc/asound/card1，将文件夹里面的所有名为codec#开头的文件都复制出来，其中一个就是你的声卡codec，千万不要把HDMI音频的文件复制出来了（如果card1没有就一定在card0文件夹，笔记本一般都在card1中）

#### **2、找出有效的节点,通过WINDOWS HDU TOOLS获取更容易**

- HDUT获取节点信息

| 名字&endint at pin | Audio output - AudioMixer - Pin Complex  | 转换为10进制 | Misc | Color | Connection type | Directional Location | Default Device | Location | Port Connectivity |
| :--------------------- | ----------------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
| Headphone(外置耳机)0x14 | 0x2 - 0xC - 0x14  | 2-12-14 | 0x0 | Green | 1/8" stereo/mono | Rear | HP Out | External | Jack |
| Speaker(内置喇叭) 0x15  | 0x3 - 0xD - 0x15  | 3-13-15 | 0x1 | Unknow | Other Analog | N/A | Speaker | Internal | Fixed Function Device |
| Speaker(内置喇叭) 0x1B  | 0x4 - 0xE - 0x1B  | 4-14-27 | 0x1 | Unknown | Other Analog | N/A | Speaker | Internal | Fixed Function Device |
| SpdifOut(数字接口) 0x1E | 0x6 - 0x1E        | 6-30    | 0x1 | Green | Optical | Rear | SPDIF | External | Jack |
| MicIn(外插麦克）0x18    | 0x8 - 0x23 - 0x18 | 8-35-24 | 0x0 | Pink | 1/8" stereo/mono | Rear | Mic In |  External | Jack |
| MicIn(外插麦克）0x12    | 0x7 - 0x24 - 0x12 | 7-36-18 | 0x1 | Unknown | Other Diagital | N/A |Mic In | Internal | Fixed Function Device |


    Vendor ID: 283904153

- 整理一下
  - Headphone(外置耳机)		0x14		0x2 - 0xC - 0x14	     转为10进制		20-12-2		输出设备从后向前顺序
  - Speaker(内置喇叭) 		    0x15		0x3 - 0xD - 0x15		 转为10进制		21-13-3		输出设备从后向前顺序
  - Speaker(内置喇叭) 		    0x1B		0x4 - 0xE - 0x1B		 转为10进制		27-14-4		输出设备从后向前顺序
  - SpdifOut(数字接口) 		   0x1E		0x6 - 0x1E				   转为10进制		30-6			 输出设备从后向前顺序
  - MicIn(外插麦克）			   0x18		0x8 - 0x23 - 0x18		转为10进制		8-35-24		输入设备从前往后顺序
  - MicIn(内置麦克）	   		0x12		0x7 - 0x24 - 0x12		转为10进制		7-36-18		输入设备从前往后顺序

#### **3、整理ConfigData,通过Pin Configurtor软件进行修改获取**

##### 	获取到的pinconfig数据

01471C10 01471D40 01471E21 01471F01 01571C20 01571D00 01571E17 01571F99 01B71C30 01B71D01 01B71E17 01B71F99 01B70C02 01E71C40 01E71D40 01E71E45 01E71F01 01871C50 01871D90 01871E81 01871F01 01271C60 01271D01 01271EA6 01271F99

​	企鹅大佬说两个EAPD保留一个 我把HP OUT 的删了 就是 01470C02删了 具体：

	有些输出设备具有EAPD，软件会自动显示出来，简单判断EAPD节点的方法:那就是它通常会位于Speaker Out和HP Out这两个输出节点上.至于其它教程提到过的关于01470c02是组神奇的代码,可以让外放发声的说法是错误的,它可能刚好声卡的Speaker Out的输出节点是0x14而已.如果您的Speaker Out输出节点是0x16,那么就需要把它修改为01670c02,当然要遵守这个公式:Address+节点+71c+02
	
	插口位置
	1/8" stereo/mono 通常接口
	Optical SPDIF光纤口
	Rear（后）笔记本选这个，意义不大
	Front（前）
	ATAPI （內建）笔记本一般选这个
	• Fixed 内部设备
	• Jack 是通过插孔进行连接的外部设备
	• N/A 是其它未知设备
	• 内置麦克风 —— Mic In
	• 内置扬声器 ——Speaker
	• 线路输出 —— line out
	• 外置麦克风 —— Mic at Ext
	• 线路输入 —— Line In 笔记本外置麦克要选这个
	• 耳机 —— HP Out
#### **4、修改AppleALC源码配置文件**

• 最后确认一共需要修改和定制的为4个文件：
• 1.Applealc——resources——alc898—>Platforms65.xml.zlib
• 2.Applealc——resources——alc898—>layout65.xml.zlib
• 3.Applealc—>resources—>alc898—>info.Plist
• 4.Applealc—>resources—>pinconfigs.kext—>contents—>info.Plist

[^我并不清楚为什么我的alc899声卡没有，但很多地方都把alc898和alc899写在一起，所以我直接用alc898]: 
[^这里选择layout65，这个的选择好像并不影响后续使用，因为是定制]: 

外置mic需要修改电压控制值来实现外置mic驱动。
搜索codec中外置mic下的vref值，vref含义为初始电压基础上增加的百分比，如图为vref为50。当vref不为Hiz时，muteGPIO={（vref转换为16进制）+"0100"+node id}转换为10进制，codec中vref表示的是十进制，计算时转为16进制。如：在节点 0x18发现vref_80，80转换为16进制=50，则muteGPIO=（50010018）转换为10进制=838926360；如果vref为Hiz，则muteGPIO=0

#### **5、编译AppleALC为AppleALC.KEXT**

打开源码里的xcode项目进行编译，生成驱动文件，注意别忘记了要放lilu.kext在源码根目录，要不然会出错的~

#### **6、放入驱动，注入layout-id即可**

> 企鹅大佬：教程相关软件下载：https://github.com/btwise/TOOLS/AppleALC
> **企鹅大佬：视频教程观看地址：https://www.bilibili.com/video/av50809670/**
#### **6、经过一番折腾成功驱动**