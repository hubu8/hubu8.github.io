# 开发板用采集卡连接笔记本屏幕


<!--more-->

### 1.安装系统

注：有的树莓派自带系统（如笔者的4B，说明书一般会注明）

先通过读卡器读写配套的**sd卡**。如果装好系统会有一个**有东西的boot和空的G盘**（不一定是G盘），**系统位于boot盘**（几百MB）。

1. **自带系统**：直接进入下一步。
2. **不带系统**：暂略，安装可见网上教程

### 2.根据自己手头有的设备，选择方法

树莓派通常配有HDMI线（公头），只有有HDMI口（母头）的屏幕能直接连。( ･᷄д･᷅ )一般的显示器都是HDMI线（公头），笔记本有HDMI口（母头）但是没法输入信号当显示器。

1. **有屏幕**：直接连上即可。
2. **没有屏幕**：

- **有笔记本+采集卡**：参见下一部分。
- **只有电脑+网络，没有采集卡**：暂略，搜索“树莓派连笔记本”

### 采集卡+笔记本屏幕

**下载PotPlayer**，官网：http: //potplayer. tv/? lang=zh_CN

插入采集卡，打开potplayer，打开树莓派（没有画面试试重启）

1.左上角PotPlayer-打开-摄像头/其他设备

![image-20240222082025610.png](./images/image-20240222082025610.png)

正常就会显示桌面，异常是显示彩条

如果画面异常，菜单-打开-设备设置，确认一下是USB Video之类的。没问题的话，重启树莓派。开机不快，会黑屏/彩条一会，中间会出现一次树莓标，正常就是上图↑的桌面。

![image-20240222190659938.png](./images/image-20240222190659938.png)

备注：potplayer只能看到开发板桌面信息，不能通过系统操作桌面，可以在开发板插入鼠标和键盘来控制开发板。





开发板网站集锦：

Banana Pi ： https://wiki.banana-pi.org/

Banana Pi BPI-M4 Zero： [Banana Pi BPI-M4 Zero - Banana Pi Wiki (banana-pi.org)](https://wiki.banana-pi.org/Banana_Pi_BPI-M4_Zero)

raspberrypi：https://www.raspberrypi.org/

