# Matebook_13/14_2020_Hackintosh_OpenCore
 A hackintosh OpenCore EFI for Huawei Matebook 13/14 2020 Intel Edition(Comet Lake)
 Matebook 13/14 2020款 黑苹果
## 配置信息
|  | 详细                                                                                                         |
|:--------------:|:------------------------------------------------------------------------------------------------------------------:|
|型号 | 华为 Matebook 13/14 **2020款**|
| CPU |    Intel Core i5 10210U/i7 10510U (Comet Lake) |
| GPU | Intel UHD620 </br> Nvidia MX250 (使用`SSDT-DDGPU`屏蔽) |
|RAM  |     8GB / 16GB LPDDR3|
| 网卡  | Intel 9462AC/9560AC CNVio <br>(无法驱动，使用`SSDT-Disable-CNVI`屏蔽)|
| 触摸板/触摸屏|  ELAN962C / SYNA7813 (MB14) / ELAN224A (MB13)</br> （使用修改的`VoodooI2C`驱动） |
| 声卡 |  ALC256 |
|SSD | SAMSUNG PM981（无法正常安装，建议更换）<br> WDC PC SN730 </br>Toshiba XG6 |
| LCD | 2160*1440|
|SMBIOS | MacBook 15,4|
--------
## 目前状态
### 由于我并没有机器，制作此配置仅仅出于兴趣。本人处于跑路状态，希望手上有机器的大佬能接手本仓库

#### 欢迎加入 华为Matebook系列黑苹果QQ交流群： 527454189  
#### **不要为了装macOS买这个机器！** 由于BIOS和网卡的限制，connectivity功能无法正常使用。需要笔记本整黑苹果请左转隔壁~~美帝良心想~~


 #### 2020-Mar.-2   全家桶更新
 1. 基于OpenCore 0.5.6，顺带[Acidanthera](https://github.com/acidanthera)全家桶更新  </br>

 2. 基于 `Clover`的测试得出结论：导致`kernel_task`起飞的原因在于触摸屏（感谢群友**Joker**）。</br>无论是走轮询还是走GPIO，都会导致大量的资源消耗，具体是`VoodooI2C`的bug还是其他原因，有待探究。

 3. 加入`CPUFriend`实现更好的电源管理</br>

 4. 加入`config-DVMT64.plist`供**解锁BIOS隐藏设置**后使用，可以达到更好的HiDPI与外接4k效果</br></br>
 
 
 
 
 #### 2020-Feb.-28    触摸板更新
 1. 弃用`SSDT-OC-XOSI`,使用“预置变量法”的方式，启用触摸设备的GPIO中断，感谢 **@宪武**</br> 参见[OC-little](https://github.com/daliansky/OC-little)--《二进制更名与预置变量》、《I2C专用部件》
 * 触摸板`ELAN962C`默认走GPIO中断，`GPIO Pin`由系统固件决定，无需指定
 * 触摸屏`SYNA7813` (MateBook 14) /`ELAN224A` (MateBook 13) 的`GPIO Pin`为`0x42`,强制走`GPIO中断`。VoodooI2C的日志看不出问题，但据报告称，触摸屏只能在开机后“划一下”有反应，然后就没有然后了</br>
* 在MateBook 13上，触摸屏仍然会引起CPU满载的问题，现默认屏蔽。~~**管它呢反正是个鸡肋玩意**~~
</br>
 2. 删除了造成莫名其妙导致机器满载的`CodecCommander.kext`,如果发现其他导致**负载异常**的情况，欢迎提出  </br>
    另外，`FakePCIID`  有一定概率导致CPU满载，但是目前不得不使用以达到驱动声卡的目的，有待进一步观察。</br></br>
 
 #### 2020-Feb.-25   声卡更新，感谢 [黑果小兵Daliansky](https://github.com/daliansky)
 1. **声卡（ALC256）** 使用AppleALC驱动，`Layout-ID`=~~`56`~~ `21`
      * 在[黑果小兵Daliansky](https://github.com/daliansky) 的指导下，添加声卡`device-id`仿冒，以及`FakePCIID`等kexts
      * 如果**耳机孔麦克风输入不可用**，或者**耳机杂音多**可以尝试运行小兵制作的[ALCPlugFix](https://github.com/Zero-zer0/Matebook_14_2020_Hackintosh_OpenCore/tree/master/AlcPlugFix) ,下载整个文件夹后，双击运行 “`install双击自动安装.command`”,强制输入走机身自带麦克风。
      * ~~**如果内置麦克风输入无声音**，还可以尝试`Layout-ID`=`21`~~</br></br>
 
 2. 在部分机器上有莫名其妙的`kernel_task`占用起飞的问题，原因之一来自于走轮询模式触摸屏，还不知道怎么从SSDT的角度禁用它，不过你可以从`VoodooI2C`的`info.plist`中删除`pci8086,2e9`的NameMatch
     * ~~在debug文件夹内有我尝试过用`预置变量法`来启用触摸板GPIO中断的SSDT，但是存在一些问题</br></br>~~
 
 
 
 
 #### 2020-Feb.-22  鬼知道还有没有下一次更新的更新
 1. ~~**声卡（ALC256）** 使用`VoodooHDA`驱动~~
    * 使用VoodooHDA提取到的有效路径中，缺少`耳机口MIC输入`的路径  
    * ~~使用`AppleALC`节点路径几乎完全一致的`Layout-ID=21`只能做到内置麦克风输入，猜测是`ConfigData`的问题~~
    * ~~声卡定制仍在学习，欢迎有能力的大佬继续挖掘，已经上传Codec和VoodooHDA的dump文件</br><br>~~
 2. ~~**触摸板与触摸屏**  暂时使用十分dirty的`SSDT-XOSI`实现驱动。~~  </br>
    * 触摸板(`_SB.PCI0.I2C0.TPD0`)可以在`SSDT-XOSI`的作用下默认走GPIO中断    
    * 触摸屏(`_SB.PCI0.I2C1.TPL1`)的`APIC Pin`为`0x6e`，转换出来的`GPIO Pin` ~~是`0x6e` 或~~ `0x42`,能否走GPIO中断还有待测试，目前走轮询。  (Matebook 14可以正常使用。Matebook 13不一定，原因未知)
    * 据群友反映，触摸板有一定概率会在睡眠唤醒后失效，由于未提供日志，原因未知。  </br>
    * ~~已经为触摸屏走GPIO中断写了三个SSDT，具体ACPI需要重命名什么打开看就知道了，count skip懒得数（~~
    * ~~同样可以根据这样的SSDT，为触摸板开启macOS下的GPIO中断，以抛弃`SSDT-XOSI`</br></br>~~
 3. **核显**
    * 目前的`platform-id`为`0xa53e0005`，可以正常驱动，HiDPI正常(貌似)
    * 自带HDMI似乎无法正常工作（没有定制端口）
    * 外接扩展坞的HDMI没有问题
    * 受限于DVMT，只能做到外接2K屏幕，外接4K需要解锁BIOS隐藏项目
    * 有一定的概率会出现关机花屏的情况
    * 有更加合适的`platform-id`欢迎提出
  
 
 
 #### 2020-Feb.-17 首次更新（OpenCore 0.5.5正常开机使用）
 1. 触摸屏/触摸板使用 [bat.bat](https://github.com/williambj1) 编译的修改版VoodooI2C进行驱动,在此表示感谢  
 2. ~~声卡ALC256，目前**无法驱动**~~
 3. 由于没搞清楚独显的具体PCI地址，使用了一个较为通用的`SSDT-DDGPU`屏蔽独显，可能会屏蔽掉潜在的PCIe通道（可能是给网卡用的，我猜）  
 4. CNVi网卡**无法驱动**，使用`SSDT-Disable-CNVW`屏蔽，感谢 [laozhiang](https://github.com/laozhiang)    
 5. Intel蓝牙使用 [IntelBluetoothFirmware](https://github.com/zxystd/IntelBluetoothFirmware)  驱动，感谢 [zxystd](https://github.com/zxystd)  
 6. 摄像头**无法驱动** （仿冒`FaceTime HD Camera`似乎无效）   
 7. USB端口未定制，有需求请使用 `Hackintool` 自行定制   
 8. 已内置Realtek USB网卡驱动，`Wireless Ultility`请下载 [Wireless-USB-Adapter](https://github.com/chris1111/Wireless-USB-Adapter-Clover/releases)  
 9. 其他的想到了再加</br></br>
____________
 ## Credits
 1. [Acidanthera](https://github.com/acidanthera) for OpenCore (and related documents) , Lilu, AppleALC and other awesome projects.

2. [OC-little](https://github.com/daliansky/OC-little) for their handy ACPI Hotpatches for OpenCore.

3. [VoodooI2C](https://github.com/alexandred/VoodooI2C) for their magnificent work on I2C trackpads.  

4. [zxystd](https://github.com/zxystd)  for awesome works on Intel wireless cards.  

5. [laozhiang](https://github.com/laozhiang) for handy ACPI Hotpatch on Huawei Matebooks.

6. [chris111](https://github.com/chris1111) for maintaing supports on Realtek USB wireless card.  

7. [Daliansky](https://github.com/daliansky) for instructions on AppleALC and ALCPlugFix.

8. All the friends in QQ Group who support my work😊.