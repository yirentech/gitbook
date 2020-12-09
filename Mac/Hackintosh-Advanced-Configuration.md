# 资料

## 论坛和博客

```
https://blog.daliansky.net/
https://osxlatitude.com/forums/
https://www.tonymacx86.com/forums/
https://www.macos86.it/
https://www.hackintosh-forum.de/
https://www.reddit.com/r/hackintosh/
https://www.insanelymac.com/forum/
https://blog.neroxps.cn/
https://www.hackintosh-montreal.com/
https://www.applelife.ru/
https://www.misonsky.cn/
https://www.bugprogrammer.me/
https://fangf.cc/
https://blog.xjn819.com/
http://bbs.pcbeta.com/
https://www.itpwd.com/
https://www.penghubingzhou.cn/#blog
https://blog.zuiyu1818.cn/
https://change-y.github.io/
https://www.macrumors.com/
https://www.kancloud.cn/chandler/mac_os/480611
https://www.firewolf.science/
https://www.osx86.net/
http://yqp7js.coding-pages.com/
https://xstar-dev.github.io/
https://heipg.cn/
```

## AMD安装黑苹果

```
https://vanilla.amd-osx.com/
https://kb.hackintoshisfun.ml/clover/
https://kb.amd-osx.com/guides/HS/
https://blog.amd-osx.com/
```

## 硬件选择

```
https://4pda.ru/forum/index.php?showtopic=72333
http://wiki.osx86project.org/wiki/index.php/Main_Page
```

# 升级macOS Catalina

升级Catalina需要对在Mojave下的配置进行适当调整。

## 配置EC

EC设备主要为电池管理。由于Catalina中名为AppleACPIEC.kext的插件依赖于名为`EC`的ACPI设备，故升级Catalina需要将EC控制器名称改为`EC`，否则系统启动崩溃。

### 笔记本用户

打开系统DSDT，搜索`PNP0C09`，查看对应的EC名称。若不止一个，则需要判断此EC是否被屏蔽，主要看是否存在`Return (Zero)`，若存在，则说明此设备被禁用。如本机对应两个EC名称H_EC和EC0，但只有EC0未被屏蔽，则只需对EC0进行改名。更名补丁如下，在Clover的config.plist添加即可。

```
Comment: Rename EC0 to EC 
Find: 4543305F 
Replace: 45435F5F
```

### 台式机用户

如果为台式机，可直接禁用EC。

#### 阻止AppleACPIEC.kext法

在OpenCore的config.pilst中进入Kernel/Block，添加以下内容即可。

| 选项        | 内容                         |
| ----------- | ---------------------------- |
| Enabled     | True                         |
| Indentifier | com.apple.driver.AppleACPIEC |

#### 自动添加SSDT法

打开以下链接下载压缩包，解压后运行SSDTTime.command，选择Fake EC并拖拽DSDT文件以自动生成SSDT，放于Clover的ACPI/patched即可。

```
https://github.com/corpnewt/SSDTTime
```

#### 手动添加SSDT法

将以下代码复制到MaciASL中，将EC0改为对应的EC名称，另存为`SSDT-no-EC.aml`，放于Clover的ACPI/patched即可。

```
DefinitionBlock ("", "SSDT", 2, "ACDT", "SsdtnoEC", 0x00001000)
{
    External (_SB.PCI0.LPCB, DeviceObj)
    External (_SB.PCI0.LPCB.EC0, DeviceObj)

    Scope (\_SB.PCI0.LPCB.EC0)
    {
        Method (_STA, 0, NotSerialized)  // _STA: Status
        {
            If (_OSI ("Darwin"))
            {
                Return (0)
            }
            Else
            {
                Return (0x0F)
            }
        }
    }
}
```

## 禁用ApplelIntelLpssI2CController和ApplelIntelLpssI2C

可选，在不禁用的情况下也可能可以直接进入。

## 更新kexts

将要用到的kexts更新到最新版本。本机在未更新Lilu和Whatevergreen的情况下显卡出现GFX Firmware问题。 

## 解锁系统分区

Catalina的系统分区被锁定，无法直接修改系统盘内的文件。进行SLE路径的驱动安装等操作需要解锁。

### 通过终端

```
sudo su
sudo mount -uw / && killall Finder
```

### 通过自动执行脚本

打开系统的`自动操作`程序，选择`应用程序`，类型选择`运行Shell脚本`，修改Shell为`/bin/bash`并将以下脚本粘贴进去，保存为`unlockSystem.app`。

```
#! /bin/bash
echo '你的电脑密码' | sudo -S mount -uw / && killall Finder
```

打开`偏好设置`，选择`用户与群组`，在登录项中将unlockSystem.app添加到启动项并勾选`隐藏`即可。

### 通过plist文件

下载`CatalinaAutoMountWritable.plist`并放在系统盘的`Library/LaunchDaemons`即可。

## 解锁Sidebar功能

部分机型不支持Sidebar，可通过修改机型或在终端输入以下命令解锁。

```
// 绕过SMBIOS限制
defaults write http://com.apple .sidecar.display allowAllDevices -bool YES

// 解锁系统首选项中「Sidebar」页
defaults write com.apple.sidecar.display hasShownPref -bool true
```

## Catalina下AR9565的驱动方法

需将`corecapture.kext`、`CoreCaptureResponder.kext`和`IO80211Family.kext`放入SLE下，且将SLE下的IO80211Family.kext与IO80211FamilyV2.kext更改扩展名以使其失效。完成替换后，需修复权限并重建缓存。

# 升级macOS Big Sur

```
首先需要更新所有的Kexts！！！


如果显示安装包过期或损坏，不能安装macos请退出到恢复界面，并保持断网（只要不插着网线），左上角实用工具————终端————输入以下代码：
date 032208102015.20
然后再左上角退出进入安装，安装位置选择刚抹掉的磁盘，切记别选错，然后等待，等它中间会自动重启2-3次

注意：
不能将IO80211Family放到Clover/OC的kexts文件夹，否则启动就panic

不用电池修补hotpatch也可以正常工作（好奇怪）

各系统镜像下载链接：
https://zhuanlan.zhihu.com/p/143960664

资料：
https://github.com/khronokernel/Legacy-Kexts/tree/master/Injectors/Zip



无法通过AppleIntelInfo.kext获取当前CPU状态
所以没办法进行下面的超频：
https://zhuanlan.zhihu.com/p/49183461
https://www.tonymacx86.com/threads/skylake-hwp-enable.214915/


```

```
https://zhuanlan.zhihu.com/p/158770898

安装或升级后的影响（部分由远景摘抄总结，侵权请联系删除）：

Unix内核更新了，Darwin更新到了20.0.0，以下是uname得到的信息：

Darwin VincentdeMacBook-Pro.local 20.0.0 Darwin Kernel Version 20.0.0: Sat Jun 13 17:58:16 PDT 2020; root:xnu-7090.0.0.111.5~1/RELEASE_X86_64 x86_64

1.即使关闭SIP，root用户依然无法挂载根目录为读写，这意味着直接修改/System里面的文件已经不可能了（要到Recovery模式用命令行修改）

影响范围（这些方法可能已失效）：

single-user mode（单用户模式 -s）不能再加载launchd服务

在之前的macOS中，我们可以在单用户模式运行launchctl load /S*/L*/LaunchDaemons/com.apple.opendirectory.plist来加载OpenDirectory服务来修改root密码和管理员密码。现在我测试的结果是，这个方法已经失效。launchctl在单用户模式加载的服务会自动停止运行，同时屏幕显示service only ran for 5 seconds，无法再在单用户模式找回管理员密码了。同时，单用户模式中加载kext的kextload命令也失去了效果。

1.旧版修改/S/L/E加载kext的方法

2.修改/System/Library开启HiDPI的方法

3.修改/System/Frameworks/CoreDisplay.framework和添加旧版kext至S/L/E来开启Intel HD3000核芯显卡和老旧Nvidia（Tesla和Fermi架构GPU）的方法

4.kext的加载受到严格限制，旧版本的kext几乎全军覆没

5.Lilu不能被加载，kextutil kextload这些命令都被/usr/bin/kmutil接替，现在运行kextutil kextload会自动跳到kmutil，不允许绕过系统安全性设置来加载kext。这意味着root用户也不能通过Terminal来加载kext了。现在只能靠OpenCore的注入功能直接把kext注入到kernelcache里面才有效，是的，黑果要升级这个系统，还有很多驱动的问题需要解决。比如opencore要更新适配新的mach内核，驱动要找到绕过苹果限制的方式加载了。目前来看现有版本的lilu不能被正确加载。

之前opencore的开发组已经料想到macOS10.16（当然现在是叫macOS 11.0）会禁止修改系统的kext了，所以他们做出了一套可以不修改系统，直接把kext注入到kernelcache的方法，这个方法可能还管用，具体要等opencore的更新。

黑果要升级这个系统，还有很多驱动的问题需要解决。比如opencore要更新适配新的mach内核，驱动要找到绕过苹果限制的方式加载了。目前来看现有版本的lilu不能被正确加载。

总结：这次系统更新大幅削弱了root的权限，修改系统将变得更加困难和麻烦
```


## 通过Clover安装

### 修改Clover

Clover 5120后，Clover移植了OpenCore的OpenRuntime.efi以替换原有的内存修复程序（如 AptioMemoryFix）。因此由旧版本升级时，需要替换以上的驱动，并更新Clover Configurator至5.16及以上以适配新增的OcQuirks选项。

完成后应当保留的驱动如下。

```
// 必选
ApfsDriverLoader.efi
HFSPlus.efi
OpenRuntime.efi
VirtualSmc.efi

// 可选
AudioDxe.efi
DataHubDxe.efi
FSInject.efi
```


```
用原版clover（>5120）就可以了，
最小作业单元(Clover)：
SSDT：
GPRW：0D6D补丁
PLUG：X86补丁
drivers：
Apfs，HFSPlus，OpenRuntime，VirtualSmc
Kexts：
Lilu,WEG,VirtualSMC+Sensor,USBInjectAll,Voodoo
ACPI更名：
change GFX0 to IGPU (Intel display)
change GPRW to XPRW (0D6D)
change _DSM to XDSM (Unused)
change EC0 to EC (EC Patch)
设备：
只加显卡

过程：
首先引导进去（不要用usb里面的clover）
参数：plugin-type=1 vsmcgen=1 -lilubetaall -vsmcbeta
DW1820A / DW1560 / DW1830等博通网卡可能还需要添加：brcmfx-aspm

卡AppleACPICPU/x86Platform plugin是可以继续跑的
但是要等很久
解决方法：OC-little包X86注入，加SSDT-PLUG（同时要删掉SSDT-XCPM）
卡disk1s1 is locked也是可以继续跑的

安装重启后的第二阶段，如果直接从安装盘启动
会一直循环com.apple.xpc.launchd[1]
这时需要CLOVER主引导界面按f3，选择Preboot继续完成安装
一直跑preboot直至出语言界面
在完成SATA修补之前都要preboot进去

http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1876638&page=1#pid50769208
http://i.pcbeta.com/home.php?mod=space&uid=233039&do=thread&type=reply&view=me&from=space


安装完了，但是preboot引导卡waiting for root devices：
原因：没有放VoodooI2C，其实卡的是IOUSBDevice
解决：最新VoodooI2C，禁用里面的VoodooInput.kext.dYSM和VoodooInput.kext
顺便解决了X86问题,SATA-Unsupported.kext也不需要了
根本原因——VoodooPS2和VoodooI2C的VoodooInput冲突了

https://github.com/CloverHackyColor/CloverBootloader/issues/297


可在问题库找答案：
https://github.com/CloverHackyColor/CloverBootloader/issues
（OC同理）




对于Clover而言，千万不要改系统快照，因为快照broken后，Clover 没有设置SecretBootModel的选项，会panic：...release build
只要不改，Clover是可以引导的
```

## 通过OpenCore安装

OpenCore相关：

```
https://www.zdynb.cn/2020/opencore-jie-gou-jie-shao-ji-zhu-ti-an-zhuang.html
https://www.zdynb.cn/2020/yi-jian-sheng-cheng-opencore-yin-dao.html
```

指南：

```
https://dortania.github.io/OpenCore-Install-Guide/
https://dortania.github.io/OpenCore-Post-Install/
http://blog.daliansky.net/Lenovo-Tianyi-510s-Mini-and-macOS-BigSur-Installation-Tutorial.html

// 常见问题
https://blog.daliansky.net/Common-problems-and-solutions-in-macOS-BigSur-11.0-installation.html
```

```
最小作业单元；
SSDT、ACPI、设备同Clover
Driver：
Apfs，HFSPlus，OpenCanopy，OpenRuntime，OpenUsbKbDxe
（不要放VirtualSMC.efi，已经内置了
Kexts：
Lilu,WEG,VirtualSMC+Sensor,USBInjectAll,Voodoo，SATA Patcher



配置文件要注意的地方：
SecureBootModel=disabled
一定Lilu第一加载
csr配置


OC主界面空格是显示全部内容


卡waiting for root device：
引导的是USB安装盘还是SATA上的Mac？
USB上的出问题——USB没有被识别，
临时方法：OC打开RealeasUSBOwnership、XhciPortlimit，加入USBInjecctAll
长期方法：USBMap

SATA上的出问题——
用CtlnaAHCIPort.kext


https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/extended/kernel-issues.html#waiting-for-root-device-or-prohibited-sign-error


内核恐慌: rooting from the live fs of a sealed volume is not allowed on a release build

将config.plist的Misc-Security-SecureBootModel=Disabled

注意：Snapshot没有被破坏时，SecureBootModel是可以设为Default的
查看方法：

# List all APFS volumes
diskutil apfs list

# Look for your system volume
Volume disk1s8 A604D636-3C54-4CAA-9A31-5E1A460DC5C0
        ---------------------------------------------------
        APFS Volume Disk (Role):   disk1s8 (System)
        Name:                      Big Sur HD (Case-insensitive)
        Mount Point:               Not Mounted
        Capacity Consumed:         15113809920 B (15.1 GB)
        Sealed:                    Broken
        FileVault:                 No
        |
        Snapshot:                  4202EBE5-288B-4701-BA1E-B6EC8AD6397D
        Snapshot Disk:             disk1s8s1
        Snapshot Mount Point:      /
        Snapshot Sealed:           Yes

如果返回Snapshot Sealed: Broken，则需要进行以下操作：

更新到OpenCore 0.6.3或更高版本，回滚APFS快照（恢复模式-时间恢复机器回滚）


https://www.tonymacx86.com/threads/solved-macos-11-0-big-sur-public-beta-10-boot-kernel-panic.305007/
https://github.com/acidanthera/bugtracker/issues/1235





解决OC下触控板不能驱动的问题：
按照以下顺序加载，放到最后：
VoodooPS2
VoodooPS2/VoodooInput
VoodooPS2/VoodooPS2Keyboard
VoodooPS2/...Mouse
VoodooPS2/...Trackpad
VoodooI2C/VoodooGPIO
VoodooI2C/...I2CServices
VoodooI2C/...Input   <----- 需禁用该项，防止与PS2/Input冲突
VoodooI2C
VoodooI2CHID


不需要电池hotpatch了
```

### 使用

#### 驱动AR9565

Wifi还没成功。蓝牙看基础配置一文。

```
:Clover不能加载已经在SLE里的kexts


// 尝试过，配合下面教程实现，但用OpenCore引导后Wifi不能用
https://www.longzc.cn/index.php/archives/306

// 其它
https://www.reddit.com/r/hackintosh/comments/f1hpn9/ar9565_wireless_bluetooth/
https://github.com/tj320442/AR9565-DW1707-macOS-10.15-Catalina
https://gitee.com/i320442/AR9565_DW1707/tree/master
```

#### 禁用SIP

```
https://github.com/Mother-FKR/RazerBlade15-Base-Model-Hackintosh_macOS_Big_Sur#6-7%E4%BF%AE%E6%94%B9%E7%B3%BB%E7%BB%9F%E5%BF%AB%E7%85%A7


打开 config.plist
* NVRAM
    * add / 添加
        * 7C436110-AB2A-4BBB-A880-FE41995C9F82
            * csr-active-config
                * FF0F0000 ｜ 数据类型：DATA
    * block / 删除
        * 7C436110-AB2A-4BBB-A880-FE41995C9F82
            * csr-active-config


进入恢复模式，输
csrutil disable
csrutil authenticated-root disable
```

#### 将Kexts安装到SLE

##### 失效

```
// 法一
https://www.longzc.cn/index.php/archives/304

// 法二（通过关闭快照）
https://forums.macrumors.com/threads/macos-11-big-sur-on-unsupported-macs-thread.2242172/page-28?post=28603788#post-28603788

* 关SIP
* 进入系统，打开终端，执行
* diskutil list
挂载系统盘
* diskutil mount disk1s5
* ls -l /Volumes/
sudo mount -uw /Volumes/big\ sur\ 1
进入su，关闭快照
* sudo su
* /System/Library/Filesystems/apfs.fs/Contents/Resources/apfs_systemsnapshot -v /Volumes/Samsung\ 1 -r ""
```



##### 可行

Big Sur从快照启动。通过挂载系统分区，修改分区内容后新建系统分区快照，并制定启动时使用该快照达到目的。

```
需要SecureBootModel=Disabled，否则会启动到恢复模式

法一：
https://blog.juliensatti.com/install-macos-big-sur-mac-obsolete/
https://andv.medium.com/making-wifi-on-big-sur-unsupported-macs-with-failed-with-66-error-36c98e3f7965
https://egpu.io/forums/mac-setup/macos-up-to-11/

法二：
http://bbs.pcbeta.com/viewthread-1873750-1-1.html
https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/extended/post-issues.html#writing-to-the-macos-system-partition
https://zhangzi.life/articles/1605255382000

第一步：解锁SIP
NVRAM-Add-7C436110-AB2A-4BBB-A880-FE41995C9F82-csr-active-config改为FF0F0000
NVRAM-Block-7C436110-AB2A-4BBB-A880-FE41995C9F82增加csr-active-config
完成后重启

第二步：创建挂载目录
mkdir ~/livemount

第三步：挂载到目录
使用diskutil list查找自己的系统分区，disk1s5为我的系统分区
sudo mount -o nobrowse -t apfs /dev/disk1s5 ~/livemount

第四步：进行你想要的操作
在访达中前往用户根目录（~/）即可看到挂载的新的系统分区，所有操作均需要在此目录中进行操作

第五步（可选）：如修改s/l/e下的驱动需要重建缓存
sudo chown -R root:wheel ~/livemount/System/Library/Extensions/
sudo chmod -R 755 ~/livemount/System/Library/Extensions/
sudo kmutil install --volume-root ~/livemount --update-all
sudo kcditto

第六步：创建新的快照
sudo bless --folder ~/livemount/System/Library/CoreServices --bootefi --create-snapshot
完成后重启

注意：此法会使snapshot变为broken：



```

##### 未尝试

其余可尝试的教程如下。

```
https://iosre.com/t/big-sur/18051
```

##### OC-little包

```
值得注意的：
02-GPIO补丁
19-I2C

要做的操作：
仿冒EC：本机起作用的是EC0
    ->笔记本是不可以仿冒EC的，一定要更名
02-3:ALSD
05-1:X86,PR-PR00
05-2:PNLF
05-3:SBUS
06:DMAC/MCHC/MEM2/IMEI/PMCR
07:PS2Controller有了BrightnessKey.kext用于控制亮度快捷键
12:0D6D,GPRW
18:ASUS专用更名和补丁



GFXO->IGPU是可以不要的
_DSM->XDSM也可以不要

AppleALC要配上CodecCommander才能睡眠后继续有声音
github.com/Sniki/EAPD-Codec-Commander

Clover不需要CtlnaAHCIPort，OC需要





curl提示443的解决方法：
https://github.com/hawtim/blog/issues/10

Switchhosts添加以上写的内容后打开该配置文件，还要打开backup配置文件
要开全局翻墙
弹SSL_ERROR_SYSCCALL时，打开backup又关掉backup配置文件就可以了

不要用Karbiner
不要开启Airdrop有线传输

```

##### OpenCore Post Install

```
需要注意的：
修复DRM（暂时不可用
修复电源管理-使用CPUFriend
    ->不要放，放了更慢
睡眠修复
添加GUI和启动提示音




要用NoTouchID，系统速度要快很多（尽管指南不推荐）


https://dortania.github.io/Getting-Started-With-ACPI/

```

#### 触控板GPIO中断补丁

```
预备知识：在ACPI中
1）_STA用于控制设备的开关(_STA: Status)，返回非零为开，返回零为关
所以通过预置变量法，将_STA中某些变量设为特定值，使它进入/不进入If语句，返回零/非零
就可以禁用/启用设备
（详见OC-little包中预置变量法，
https://github.com/daliansky/OC-little/tree/master/02-%E9%A2%84%E7%BD%AE%E5%8F%98%E9%87%8F）

2）Field(xxx,xxx){
    A,...
}
外部引用时为External(A,FieldUnitObj)，如果Field在Scope(_SB)，那么就不用给A写路径(不需要_SB.A)
不要看penghubinzhou的方法，那个复杂了

3）以下两者等价：
Scope(_SB.XXX){
    Device(ABC){
        ...
    }
}
等同于：
Device(_SB.XXX.ABC){
    ...
}
添加外部引用时，都是写External(_SB.XXX,DeviceObj)，不用写ABC的

```

```
一个很奇怪的点：
实际起作用的是ETPD
但是GPIO的模版都在TPD0，不是一个真实的设备
应当修补ETPD，不要理TPD0

https://gitter.im/alexandred/VoodooI2C/archives/2018/08/30
搜索@Piero512
```

```
http://yqp7js.coding-pages.com/2020/05/17/%E7%94%A8%E7%83%AD%E8%A1%A5%E4%B8%81%E4%BF%AE%E7%90%86%E4%BD%A0%E7%9A%84%E8%A7%A6%E6%91%B8%E6%9D%BF%EF%BC%88TouchPad-Hotpatch%EF%BC%89/
https://github.com/daliansky/OC-little/blob/master/19-I2C%E4%B8%93%E7%94%A8%E9%83%A8%E4%BB%B6/README.md
https://www.penghubingzhou.cn/2019/01/06/VoodooI2C%20DSDT%20Edit/
```

##### 确定设备路径

进入Windows系统，打开设备管理器，找到人体输入学设备，确认自己是不是I2C触摸板（有可能是I2C USB），如果找不到I2C HID设备，下面的鼠标又没有PS2的时候，把每个都打开看看有没有Bios Device Name 里有 I2C 的。

然后右键查看属性，在详细信息里找到触摸板的位置路径

```
ACPI(_SB_)#ACPI(PCI0)#ACPI(I2C1)#ACPI(ETPD)
```

##### 确定APIC PIn

继续在Windows下，设备管理器中确认一下你的APIC Pin，属性-资源-IRQ右边数字的最后两位和括号。如果这里括号里的值大于等于 1024（10进制），直接去做热补丁，删掉所有有关操作系统的判断就好了


你也可以在Mac下查看APIC Pin

到Mac系统下，使用IORegistryExplorer查看APIC Pin
在右上角搜索自己的触摸板名称，比如根据我们之前照出来的路径，触摸板设备的名称就为ETPD


```
0x0000006D (109)
```

```
Pin < 2F 或者 无IOInterruptSpecifiers，为APIC中断
Pin > 2F 需要制作热补丁使触摸板在GPIO模式下正常工作

APIC中断：上文有提到，也就是最好的一个工作模式，当然只有极少数人有这个运气是APIC中断
GPIO中断：Windows系统下用的也是这个模式，是仅次于APIC中断的，比较高效，也是我们首先推荐的模式。触摸板热补丁也是主要针对GPIO中断有比较多的修改。
轮询：最低效的一个模式，但相比GPIO中断，这个模式的适用范围更广，大部分机子都能适用这个，虽然有时候可能有些问题，如，指针漂移或者不灵敏等，但却更容易驱动。GPIO中断无法使用的情况下，我们就选择使用轮询模式。
总结归纳：
效率：APIC中断 > GPIO中断 > 轮询
驱动难易成度：GPIO中断 > 轮询 ，这里APIC中断不作讨论，看你运气。
```


##### 禁用原设备

###### 预置变量法（当前使用）

DSDT查找触控板设备ETPD，查看其_STA方法：

```
            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If (((TPIF != One) || (DSYN && One)))
                {
                    Return (Zero)
                }

                Return (0x0F)
            }
```

若TPIF不等于1，或DSYN不等于0，就可以Return Zero，禁用该设备。

搜索TPIF，仅在此处被引用；DSYN，有在别处被引用，所以设置TPIF是最好的，设TPIF=0就可以了。

搜索TPIF，其定义如下：

```
    Scope (_SB)
    {
        OperationRegion (ASIO, SystemMemory, ASIB, 0x09)
        Field (ASIO, AnyAcc, NoLock, Preserve)
        {
            ASIS,   32, 
            TPIF,   8, 
            TPDI,   8, 
            TPLP,   8, 
            TPLI,   8, 
            TPHI,   8
        }
    }
```

【第一个补丁】所以补丁如下(SSDT-DPAD.dsl，禁用原设备)，`\`代表的就是根目录_SB：

```
DefinitionBlock ("", "SSDT", 2, "_ASUS_", "Notebook", 0x01072009)
{
    External (TPIF, FieldUnitObj)
    
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            TPIF = Zero
        }
    }
}
```

###### `_STA`修改法（未使用）

新建一个SSDT，然后我们先写入根作用域（把DSDT中的DefinitionBlock复制到SSDT中就行，然后将里面的DSDT改为SSDT）

```
DefinitionBlock ("", "SSDT", 2, "_ASUS_", "Notebook", 0x01072009)
{
    
}
```

然后我们再添加Scope作用域，路径就是原设备（ETPD）的路径

```
Scope (_SB.PCI0.I2C1.ETPD)
    {
    }
```

把我下面给出的_STA复制到这个Scope作用域中

```
Method (_STA, 0, NotSerialized)  // _STA: Status
{
    If (_OSI ("Darwin"))
    {
        Return (Zero)
    }
    Else
    {
        Return (XSTA())
    }
}
```

...（后面还有，自行查看上面的网址）

##### 启用GPI0

```
https://github.com/daliansky/OC-little/tree/master/02-%E9%A2%84%E7%BD%AE%E5%8F%98%E9%87%8F/03-1-OCI2C-GPIO%E8%A1%A5%E4%B8%81
```

DSDT搜索Device (GPI0，查看其_STA方法：

```
            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If ((SBRG == Zero))
                {
                    Return (Zero)
                }

                If ((GPEN == Zero))
                {
                    Return (Zero)
                }

                Return (0x0F)
            }
```

启用GPI0要SBRG和GPEN都不等于0。搜索SBRG，被多处引用；GPEN，仅本处被引用，所以先设GPEN试一下，SBRG可能本来就不等于0

SBRG/GPEN定义如下：

```
// 这里就是根目录，没有Scope()

    OperationRegion (PNVA, SystemMemory, PNVB, PNVL)
    Field (PNVA, AnyAcc, Lock, Preserve)
    {
        ...
        SBRG,   32, 
        ...
        GPEN,   8, 
        ...
    }

```

【第二个补丁】SSDT-OCGPI0-GPEN.dsl

```
// GPI0 enable
DefinitionBlock("", "SSDT", 2, "OCLT", "GPI0", 0)
{
    External(GPEN, FieldUnitObj)
    
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            GPEN = 1
        }
    }
}
//EOF
```

##### 触摸板热补丁制作

首先我们用Maciasl打开DSDT，并新建一个文件。然后我们将DSDT的DefinitionBlock部分复制到新建的文件中,并将其中的DSDT的字符串改为SSDT，如图

```
DefinitionBlock ("", "SSDT", 2, "_ASUS_", "Notebook", 0x01072009)
{
    
}
```

然后我们根据之前确定的触摸板设备路径(`_SB.PCI0.I2C1.ETPD`)找到我们需要修改的设备(Device)。很简单，我们按Option + F在搜索框中输入自己触摸板的名称，我们这里的是ETPD，然后回车搜索，找到位于_SB.PCI0.I2C1下的ETPD设备

然后我们在新建的SSDT中，先添加路径Scope(`_SB.PCI0.I2C1`)，然后将整个ETPD设备复制进去(一定要注意对应括号，不要复制错了)，如图：


```
DefinitionBlock ("", "SSDT", 2, "_ASUS_", "Notebook", 0x01072009)
{
    Scope (_SB.PCI0.I2C1)
    {
        Device (ETPD)
        {
            Name (_ADR, One)  // _ADR: Address
            Name (ETPH, Package (0x16)
            {
                "ELAN1200", 
                "ELAN1201", 
                "ELAN1203", 
                "ELAN1200", 
                "ELAN1201", 
                "ELAN1300", 
                "ELAN1301", 
                "ELAN1300", 
                "ELAN1301", 
                "ELAN1000", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1203", 
                "ELAN1203", 
                "ELAN1201", 
                "ELAN1300", 
                "ELAN1300", 
                "ELAN1200", 
                "ELAN1300"
            })
            Name (FTPH, Package (0x05)
            {
                "FTE1001", 
                "FTE1200", 
                "FTE1200", 
                "FTE1300", 
                "FTE1300"
            })
            Method (_HID, 0, NotSerialized)  // _HID: Hardware ID
            {
                If ((TPDI & 0x04))
                {
                    Return (DerefOf (ETPH [TPHI]))
                }

                If ((TPDI & 0x10))
                {
                    Return (DerefOf (FTPH [TPHI]))
                }

                Return ("ELAN1010")
            }

            Name (_CID, "PNP0C50" /* HID Protocol Device (I2C bus) */)  // _CID: Compatible ID
            Name (_UID, One)  // _UID: Unique ID
            Name (_S0W, 0x03)  // _S0W: S0 Device Wake State
            Method (_DSM, 4, NotSerialized)  // _DSM: Device-Specific Method
            {
                If ((Arg0 == ToUUID ("3cdff6f7-4267-4555-ad05-b30a3d8938de") /* HID I2C Device */))
                {
                    If ((Arg2 == Zero))
                    {
                        If ((Arg1 == One))
                        {
                            Return (Buffer (One)
                            {
                                 0x03                                             // .
                            })
                        }
                        Else
                        {
                            Return (Buffer (One)
                            {
                                 0x00                                             // .
                            })
                        }
                    }

                    If ((Arg2 == One))
                    {
                        Return (One)
                    }
                }
                Else
                {
                    Return (Buffer (One)
                    {
                         0x00                                             // .
                    })
                }
            }

            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If (((TPIF != One) || (DSYN && One)))
                {
                    Return (Zero)
                }

                Return (0x0F)
            }

            Method (_CRS, 0, Serialized)  // _CRS: Current Resource Settings
            {
                Name (SBFI, ResourceTemplate ()
                {
                    I2cSerialBusV2 (0x0015, ControllerInitiated, 0x00061A80,
                        AddressingMode7Bit, "\\_SB.PCI0.I2C1",
                        0x00, ResourceConsumer, , Exclusive,
                        )
                    Interrupt (ResourceConsumer, Level, ActiveLow, Exclusive, ,, )
                    {
                        0x0000006D,
                    }
                })
                Return (SBFI) /* \_SB_.PCI0.I2C1.ETPD._CRS.SBFI */
            }
        }
    }
}
```

然后我们将ETPD进行改名，改成TPXX或者其他的什么都可以，只要不与DSDT中其他的设备名称相同有冲突。我们在新建的SSDT中查找ETPD，并将所有找到的ETPD，都改成TPXX(或者自己改的名称)。

补丁中 `_STA` 部分替换为：

```
Method (_STA, 0, NotSerialized)
{
    If (_OSI ("Darwin"))
    {
        Return (0x0F)
    }
    Else
    {
        Return (Zero)
    }
}
```

（本机没有）查找 TPIF (禁止ETPD时预置变量法用到的变量)，如果有If (TPIF...) 语句，修改为 If (one)。

（本机没发现）查找 OSYS，删除（注释掉）以下内容：

```
//If (LLess (OSYS, 0x07DC))
//{
//    SRXO (GPDI, One)
//}
```

##### GPIO中断实现

```
原理：
首先，我们先来讲解一些触摸板的相关内容。我们可以在触摸板设备中找到如下，GpioInt（GPIO中断）、Interrupt（APIC中断） 

（在复制出来的SSDT中看，千万不要跳到其他的设备，其他设备根本就没有关系，也不能调用其他设备中的方法）

(本机有）先看SBFI，这个用来实现APIC中断的，当这个值无效的时候（也就是APIC Pin大于2F的时候），走的是轮询
```

```
            Name (SBFI, ResourceTemplate ()
            {
                Interrupt (ResourceConsumer, Level, ActiveLow, ExclusiveAndWake, ,, _Y25)
                {
                    0x00000000,
                }
            })
```

```
(本机没有）再来看SBFB，这个轮询和中断都可以调用。当然也有的_CSR里面不返回这个，而是返回了>I2CM，其实两者的用处是一样的，但I2CM里面包括了更多的东西。
我们可以这样理解吧，I2CM>=SBFB
```

```
            Name (SBFB, ResourceTemplate ()
            {
                I2cSerialBusV2 (0x004C, ControllerInitiated, 0x00061A80,
                    AddressingMode7Bit, "\\_SB.PCI0.I2C1",
                    0x00, ResourceConsumer, _Y2A, Exclusive,
                    )
            })
```

```
（本机没有）这里有一个重点，那就是SBFG，其实我们看的不是SBFG这个名字，而是里面的GpioInt，这是中断所需要的。
```

```
            Name (SBFG, ResourceTemplate ()
            {
                GpioInt (Level, ActiveLow, ExclusiveAndWake, PullDefault, 0x0000,
                    "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,
                    )
                    {   // Pin list
                        0x0000
                    }
            })
```

先找到_CRS方法：

```
           Method (_CRS, 0, Serialized)  // _CRS: Current Resource Settings
            {
                Name (SBFI, ResourceTemplate ()
                {
                    I2cSerialBusV2 (0x0015, ControllerInitiated, 0x00061A80,
                        AddressingMode7Bit, "\\_SB.PCI0.I2C1",
                        0x00, ResourceConsumer, , Exclusive,
                        )
                    Interrupt (ResourceConsumer, Level, ActiveLow, Exclusive, ,, )
                    {
                        0x0000006D,
                    }
                })
                Return (SBFI) /* \_SB_.PCI0.I2C1.ETPD._CRS.SBFI */
            }
```

将Name (SBFI改为Name (SBFB，并删除整个Interrupt()函数，变成如下：

```
           Method (_CRS, 0, Serialized)  // _CRS: Current Resource Settings
            {
                Name (SBFB, ResourceTemplate ()
                {
                    I2cSerialBusV2 (0x0015, ControllerInitiated, 0x00061A80,
                        AddressingMode7Bit, "\\_SB.PCI0.I2C1",
                        0x00, ResourceConsumer, , Exclusive,
                        )
                })
                Return (SBFI) /* \_SB_.PCI0.I2C1.ETPD._CRS.SBFI */
            }
```

(本机没有，有的话教程看连接：https://www.penghubingzhou.cn/2019/01/06/VoodooI2C%20DSDT%20Edit/)

找类似以下的代码有没有，没有就将它复制到Device (TPXX){}的大括号（也就是TPXX的根目录）：

没有的人不仅需要补上，而且需要计算GPIO Pin；有这个GpioInt的，是不需要去算GPIO Pin的，它自己会自动注入的，当然你想去算一个Gpio Pin也没事，不影响。

```
            Name (SBFG, ResourceTemplate ()
            {
                GpioInt (Level, ActiveLow, ExclusiveAndWake, PullDefault, 0x0000,
                    "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,
                    )
                    {   // Pin list
                        0x0000
                    }
            })
```

GPIO Pin的计算：

```
此处摘录https://blog.gzxiaobai.cn/2020/05/17/%E7%94%A8%E7%83%AD%E8%A1%A5%E4%B8%81%E4%BF%AE%E7%90%86%E4%BD%A0%E7%9A%84%E8%A7%A6%E6%91%B8%E6%9D%BF%EF%BC%88TouchPad-Hotpatch%EF%BC%89/

也可以用https://www.penghubingzhou.cn/2019/01/06/VoodooI2C%20DSDT%20Edit/
```

本机0x6D，化为十进制109，带进去算，算完了再化回16进制：

（本机适用，算出来是85=0x55）Skylake：

```
If APICPIN > 47 And APICPIN <= 79 Then     
    GPIOPIN = APICPIN - 24   
    GPIOPIN2 = APICPIN + 72  
ElseIf APICPIN > 79 And APICPIN <= 119 Then
    GPIOPIN = APICPIN - 24
End If
```

CoffeeLake-H：

```
If APICPIN > 47 And APICPIN <= 71 Then   
    GPIOPIN = APICPIN - 16   
    GPIOPIN2 = APICPIN + 240 
    If APICPIN > 47 And APICPIN <= 59 Then GPIOPIN3 = APICPIN + 304  
ElseIf APICPIN > 71 And APICPIN <= 95 Then 
    GPIOPIN = APICPIN - 8    
    GPIOPIN3 = APICPIN + 152
    GPIOPIN2 = APICPIN + 120 
ElseIf APICPIN > 95 And APICPIN <= 119 Then 
    GPIOPIN = APICPIN        
    If APICPIN > 108 And APICPIN <= 115 Then GPIOPIN2 = APICPIN + 20 
 End If
```

CoffeeLake-LF和Whiskylake：

```
If APICPIN > 47 And APICPIN <= 71 Then      
    GPIOPIN = APICPIN - 16   
    GPIOPIN2 = APICPIN + 80  
ElseIf APICPIN > 71 And APICPIN <= 95 Then  
    GPIOPIN2 = APICPIN + 184 
    GPIOPIN = APICPIN + 88   
ElseIf APICPIN > 95 And APICPIN <= 119 Then 
    GPIOPIN = APICPIN        
    If APICPIN > 108 And APICPIN <= 115 Then GPIOPIN2 = APICPIN - 44 
End If
```

不过在某些极端状况下，你找到的值都不起作用的话。那么此时，你只能去尝试些比较常见的数值了，如0x17、0x1b、0x34和0x55。（仅限 SunrisePoint）

计算好了就把GPIO Pin填入到SBFG的Pin list，改完变成如下：

```
            Name (SBFG, ResourceTemplate ()
            {
                GpioInt (Level, ActiveLow, ExclusiveAndWake, PullDefault, 0x0000,
                    "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,
                    )
                    {   // Pin list
                        0x55
                    }
            })
```

去 _CRS 方法下，确保所有返回语句都改成以下内容：

```
// 如果没有I2CM，选这个
Return (ConcatenateResTemplate (SBFB, SBFG))

// 有I2CM，选这个
Return (ConcatenateResTemplate (I2CM (I2CX, BADR, SPED), SBFG))
```

所以整个_CRS变成了这样：

```
            Method (_CRS, 0, Serialized)  // _CRS: Current Resource Settings
            {
                Name (SBFB, ResourceTemplate ()
                {
                    I2cSerialBusV2 (0x0015, ControllerInitiated, 0x00061A80,
                        AddressingMode7Bit, "\\_SB.PCI0.I2C1",
                        0x00, ResourceConsumer, , Exclusive,
                        )
                })
                Return (ConcatenateResTemplate (SBFB, SBFG))
            }
```

然后就做完了，点击编译，对出错的变量添加外部引用，其中各变量类型的解释参照如下连接：

```
http://bbs.pcbeta.com/viewthread-1866928-1-1.html
```

【第三个补丁】SSDT-ETPD.dsl

```
DefinitionBlock ("", "SSDT", 2, "_ASUS_", "Notebook", 0x01072009)
{
    External(TPDI, FieldUnitObj)
    External(TPHI, FieldUnitObj)
    External (_SB.PCI0.I2C1,DeviceObj)
    
    
    Scope (_SB.PCI0.I2C1)
    {
        Device (TPXX)
        {
            Name (SBFG, ResourceTemplate ()
               {
                    GpioInt (Level, ActiveLow, Exclusive, PullUp, 0x0000,
                            "\\_SB.PCI0.GPI0", 0x00, ResourceConsumer, ,
                            )
                            {   // Pin list
                                0x55
                            }
               })
            
            Name (_ADR, One)  // _ADR: Address
            Name (ETPH, Package (0x16)
            {
                "ELAN1200", 
                "ELAN1201", 
                "ELAN1203", 
                "ELAN1200", 
                "ELAN1201", 
                "ELAN1300", 
                "ELAN1301", 
                "ELAN1300", 
                "ELAN1301", 
                "ELAN1000", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1200", 
                "ELAN1203", 
                "ELAN1203", 
                "ELAN1201", 
                "ELAN1300", 
                "ELAN1300", 
                "ELAN1200", 
                "ELAN1300"
            })
            Name (FTPH, Package (0x05)
            {
                "FTE1001", 
                "FTE1200", 
                "FTE1200", 
                "FTE1300", 
                "FTE1300"
            })
            Method (_HID, 0, NotSerialized)  // _HID: Hardware ID
            {
                If ((TPDI & 0x04))
                {
                    Return (DerefOf (ETPH [TPHI]))
                }

                If ((TPDI & 0x10))
                {
                    Return (DerefOf (FTPH [TPHI]))
                }

                Return ("ELAN1010")
            }

            Name (_CID, "PNP0C50" /* HID Protocol Device (I2C bus) */)  // _CID: Compatible ID
            Name (_UID, One)  // _UID: Unique ID
            Name (_S0W, 0x03)  // _S0W: S0 Device Wake State
            Method (_DSM, 4, NotSerialized)  // _DSM: Device-Specific Method
            {
                If ((Arg0 == ToUUID ("3cdff6f7-4267-4555-ad05-b30a3d8938de") /* HID I2C Device */))
                {
                    If ((Arg2 == Zero))
                    {
                        If ((Arg1 == One))
                        {
                            Return (Buffer (One)
                            {
                                 0x03                                             // .
                            })
                        }
                        Else
                        {
                            Return (Buffer (One)
                            {
                                 0x00                                             // .
                            })
                        }
                    }

                    If ((Arg2 == One))
                    {
                        Return (One)
                    }
                }
                Else
                {
                    Return (Buffer (One)
                    {
                         0x00                                             // .
                    })
                }
            }

            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                        If (_OSI ("Darwin"))
        {
            Return (0x0F)
        }
        Else
        {
            Return (Zero)
        }
            }

            Method (_CRS, 0, Serialized)  // _CRS: Current Resource Settings
            {
                Name (SBFB, ResourceTemplate ()
                {
                    I2cSerialBusV2 (0x0015, ControllerInitiated, 0x00061A80,
                        AddressingMode7Bit, "\\_SB.PCI0.I2C1",
                        0x00, ResourceConsumer, , Exclusive,
                        )
                })
                Return (ConcatenateResTemplate (SBFB, SBFG))
            }
        }
    }
}
```

注意：全程不涉及ACPI重命名！不需要将ETPD改成TPXX

检验效果：重启后IORegistryExplorer查找ETPD，没有该设备就是被禁用了；查找TPXX，有就是生效了

结果：Clover完美（只有TPXX），OC两个设备都在（不知道为什么）


# 旧款主板安装旧版Mac系统

如果主板或CPU较旧，则只能安装旧版系统。可先查看CPU支持的指令集，如果CPU不支持SSE4.1指令集，则不能运行10.12及以上的系统。10.11支持SSE3指令集。

## UEFI引导

如果主板为BIOS Legacy模式，可以通过模拟UEFI实现，具体做法可参照有关引导的文章。向EFI分区安装好Clover后即可开始。

### U盘安装

在Mac系统下下载好后缀为app的安装包拖到`应用程序`文件夹。用磁盘工具格式化U盘，选择GUID分区图，格式化为Mac日志式。然后打开终端并输入以下命令，下以Sierra安装包为例，其中/Volumes/Sierra为U盘的盘符。

```
sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/Sierra --applicationpath /Applications/Install\ macOS\ Sierra.app --nointeraction
```

制作完成后，插入到需要安装的电脑即可引导启动。

### 硬盘安装

用Diskgenius划分一块10G左右的磁盘作为安装盘，注意分配盘符但不要分区。通过硬盘安装助手，可以把镜像写到硬盘中。

如果镜像为cdr格式，则需要以管理员模式打开硬盘安装助手，选择安装镜像和安装磁盘，只勾选`写入`，点击开始即可。如果不以管理员身份运行，则会出现`not a HFS partition`的错误提示。

完成写入后需要手动设置安装盘的参数。打开Diskgenius，在安装盘分区上右键选择修改参数，修改系统类型为`48465300-0000-11AA-AA11-00306543ECAC`即可。

如果是以app为后缀的安装包，则需要从app包制作cdr镜像。打开终端并输入以下命令即可。

```
// 创建dmg
// -size 8300m是指镜像大小8300MB，这里的8300改为app包大小的基础上增加500MB左右
hdiutil create -o /tmp/Install\ macOS\ Catalina -size 8500m -layout SPUD -fs HFS+J

// 挂载dmg
hdiutil attach /tmp/Install\ macOS\ Catalina.dmg -noverify -mountpoint /Volumes/Install\ macOS\ Catalina

// 将app写入dmg
echo Y | sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/Install\ macOS\ Catalina

// 卸载挂载的dmg
hdiutil detach /Volumes/Install\ macOS\ Catalina

// 将dmg转换为cdr
hdiutil convert /tmp/Install\ macOS\ Catalina.dmg -format UDTO -o ~/Desktop/Install\ macOS\ Catalina

// 删除dmg
rm /tmp/Install\ macOS\ Catalina.dmg
```

## BIOS引导

如果不希望使用模拟UEFI，也可以通过MBR+BIOS的模式。注意，由于​Mac不支持MBR启动，因此需要通过MBR补丁的方式实现，且应当使用Ecoch即变色龙进行引导，通过Clover引导时可能会出现`X64 Exception`的错误。

注意，对于变色龙而言，Extra/Extensions目录相当于Clover的kexts/Other目录。

### 安装引导器

#### U盘安装

在Mac系统下下载Ecoch的pkg安装包，安装时安装路径选择U盘即可。U盘可为MBR分区表。

#### 硬盘安装

下载Chameleon Install安装器并打开，按照提示完成安装。安装好的镜像文件wowpc.iso位于系统分区根目录，下载最新的wowpc.iso并替换即可。

### 准备安装介质

下载Catalina MBR HFS Firmware Check Patch，打开dmg文件，进入`Old-macOS-10+11+12`，运行想安装的系统的脚本。注意需要使用一个U盘作为写入盘，并按照提示格式化。

安装盘完成后，需要将Enoch-r2922里的文件拷贝到引导器。对于U盘安装的变色龙而言，直接替换文件即可。而对于硬盘安装，需要编辑iso文件。

## 启动排错

启动时需添加`-v`参数以进入啰嗦模式。

### 用Clover引导选择完启动项后即黑屏

更换旧版Clover，如r3333。注意，旧版Clover需要在启动项上空格，并选择`Boot with injected kexts`才能载入kexts。

### 卡AppleEHCI/AppleUHCI

可尝试添加引导参数`USBBusFix=Yes`，若无效用Clover Configurator打开ACPI中的FixHPET和FixIPIC，若无效则改用硬盘模式安装。

### 显示panic(CPU 1 caller 0xffffff800e7ce6fa):Kernel trap at 0xffffff7f9294d4c6

如果用Clover引导，可勾选FixHPET和FixIPIC。

### 卡Missing bluetooth controller transport!后禁行

如果用Clover引导，则可勾选FixHPEC，FixIPIC，FixFirewire，FixUSB，DropOEM，并关闭所有USB注入。

# 修改InstallESD.dmg

InstallESD.dmg位于macOS系统的安装包内，修改其内容（如删除部分kexts）可能会使安装过程更加顺利。

打开磁盘工具，点击菜单栏上的映像-转换，选择要转换的镜像，映像格式选择读/写。打开转换好的dmg，修改完成后再次转换，映像格式选择压缩即可。

# 常用KextsToPatch

## 为非苹果SSD打开Trim功能

```
<dict>
	<key>Name</key>
	<string>IOAHCIBlockStorage</string>
	<key>Find</key>
	<data>QVBQTEUgU1NEAA==</data>
	<key>Replace</key>
	<data>AAAAAAAAAAAAAA==</data>
</dict>
```

## 修复外置硬盘识别为内置硬盘问题

`RXh0ZXJuYWw=`指External，`SW50ZXJuYWw=`指Internal。

```
<dict>
	<key>Name</key>
	<string>AppleAHCIPort</string>
	<key>Find</key>
	<data>RXh0ZXJuYWw=</data>
	<key>Replace</key>
	<data>SW50ZXJuYWw=</data>
</dict>
```

## 允许使用MacPro4,1或MacPro5,1的SMBIOS时不带ECC内存引导

```
<dict>
	<key>Name</key>
	<string>AppleTyMCEDriver</string>
	<key>Find</key>
	<data>cgoATWFjUHJvNCwxAE1hY1BybzUsMQBY</data>
	<key>Replace</key>
	<data>cgoAAAAAAAAAAAAAAAAAAAAAAAAAAABY</data>
</dict>
```

# 常用KernelToPatch

## 去除Lilu内核崩溃时的kexts输出

```
// 根据MatchOS的版本号选用更名补丁
Comment: Disable panic kext logging on 10.15.4 Release kernel
Find: 008a0284c074
Replace: 008a0284c0eb
MatchOS: 10.14.4,10.14.5

Comment: Disable panic kext logging on 10.13 Release kernel
Find: 8A0284C07444
Replace: 8A0284C0EB44
MatchOS: 10.13

Comment: Disable panic kext logging on 10.14-10.14.3  Release kernel
Find: 8A0284C07442
Replace: 8A0284C0EB42 
MatchOS: 10.14-10.14.3

Comment: Disable panic kext logging on 10.14.4  Release kernel
Find: 008A0284C074
Replace: 008A0284C0EB 
MatchOS: 10.14.4
```

或用文本编辑器打开，在对应位置添加以下段落内容。

```
<key>KernelToPatch</key>
<array>
	<dict>
		<key>Comment</key>
		<string>Disable panic kext logging on Release kernel</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>
		AIoChMB0
		</data>
		<key>MatchOS</key>
		<string>10.14.4,10.14.5</string>
		<key>Replace</key>
		<data>
		AIoChMDr
		</data>
	</dict>
	</dict>
</array>
```

# 华硕笔记本的键盘修复

## 按键驱动

利用AsusSMC.kext，可修复华硕笔记本键盘与Fn有关的快捷键不工作的问题。将下载好的kext放到Clover的kexts文件夹中，若键盘有睡眠和飞行模式按键则还需运行`install_daemon.sh`，然后重启。逐个测试按键是否工作，若不工作，可通过SSDT或DSDT进行修补。

## 按键修复

### 通过修改DSDT

用MaciASL打开DSDT添加以下源，然后点击Patch打相应补丁即可。

```
http://raw.github.com/hieplpvip/AsusSMC/master
```

### 通过SSDT

选取以下所需代码复制到MaciASL中，创建一个SSDT并保存为SSDT-ASUS.aml，放入Clover的ACPI/patched，并按照注释在config.plist中添加更名补丁即可。

```
DefinitionBlock ("", "SSDT", 2, "hack", "EC", 0x00000000)
{
    External (_SB.ATKD.IANE, MethodObj)
    External (_SB.ATKP, IntObj)
    External (_SB.PCI0.LPCB.EC, DeviceObj)

    Scope (_SB.PCI0.LPCB.EC)
    {
        // F1
        // change _Q0A to XQ0A
        // Find: 5F513041
        // Replace: 58513041
        Method (_Q0A, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x5E)
            }
        }
            
        // F2
        // change _Q0B to XQ0B
        // Find: 5F513042
        // Replace: 58513042
        Method (_Q0B, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x7D)
            }
        }
        
        // F3
        // change _Q0C to XQ0C
        // Find: 5F513043
        // Replace: 58513043
        Method (_Q0C, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0xC5)
            }
        }
        
        // F4
        // change _Q0D to XQ0D
        // Find: 5F513044
        // Replace: 58513044
        Method (_Q0D, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0xC4)
            }
        }

        // F5
        // change _Q0E to XQ0E
        // Find: 5F513045
        // Replace: 58513045
        Method (_Q0E, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x20)
            }
        }

        // F6
        // change _Q0F to XQ0F
        // Find: 5F513046
        // Replace: 58513046
        Method (_Q0F, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x10)
            }
        }
        
        // F7
        // change _Q10 to XQ10
        // Find: 5F513130
        // Replace: 58513130
        Method (_Q10, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x35)
            }
        }
        
        // F8
        // change _Q10 to XQ10
        // Find: 5F513131
        // Replace: 58513131
        Method (_Q11, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x61)
            }
        }
        
        // F9
        // change _Q12 to XQ12
        // Find: 5F513132
        // Replace: 58513132
        Method (_Q12, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x6B)
            }
        }
        
        // F10
        // change _Q13 to XQ13
        // Find: 5F513133
        // Replace: 58513133
        Method (_Q13, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x32)
            }
        }
        
        // F11
        // change _Q14 to XQ14
        // Find: 5F513134
        // Replace: 58513134
        Method (_Q14, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x31)
            }
        }
        
        // F12
        // change _Q15 to XQ15
        // Find: 5F513135
        // Replace: 58513135
        Method (_Q15, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x30)
            }
        }

        // media left arrow key
        // change _Q6C to XQ6C
        // Find: 5F513643
        // Replace: 58513643
        Method (_Q6C, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x40)
            }
        }
        
        // media right arrow key
        // change _Q6D to XQ6D
        // Find: 5F513644
        // Replace: 58513644
        Method (_Q6D, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x41)
            }
        }

        // media up arrow key
        // change _Q6E to XQ6E
        // Find: 5F513645
        // Replace: 58513645
        Method (_Q6E, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x43)
            }
        }
        
        // media down arrow key
        // change _Q6F to XQ6F
        // Find: 5F513646
        // Replace: 58513646
        Method (_Q6F, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x45)
            }
        }

        // space key
        // change _Q71 to XQ71
        // Find: 5F513731
        // Replace: 58513731
        Method (_Q71, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x5C)
            }
        }

        // V key
        // change _Q72 to XQ72
        // Find: 5F513732
        // Replace: 58513732
        Method (_Q72, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x8A)
            }
        }


        // C key
        // change _Q73 to XQ73
        // Find: 5F513733
        // Replace: 58513733
        Method (_Q73, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x82)
            }
        }

        // ALS toggle A key
        // change _Q6F to XQ6F
        // Find: 5F513646
        // Replace: 58513646
        Method (_Q6F, 0, NotSerialized)
        {
            If (^^^^ATKP)
            {
                ^^^^ATKD.IANE (0x7A)
            }
        }
    }
}
```

# SIP配置

SIP是macOS的安全保护机制，包括文件系统保护、运行时保护和内核扩展签名保护。

## csr-active-config参数

| BiT位 | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| 0     | [kext]允许加载不受信任的kext（与kext-dev-mode=1等效）        |
| 1     | [fs]解锁文件系统限制                                         |
| 2     | [debug]允许task_for_pid()调用                                |
| 3     | [N/A]允许内核调试 （官方的csrutil工具无法设置此位）          |
| 4     | [internal]Apple内部保留位（csrutil默认会设置此位，实际不会起作用。设置与否均可） |
| 5     | [dtrace]解锁dtrace限制                                       |
| 6     | [nvram]解锁NVRAM限制                                         |
| 7     | [N/A]允许设备配置，用于Recovery/安装环境                     |

## 修改SIP配置

SIP可以防止将未签名的程序包注入内核，并可以防止应用程序覆盖nvram值。通过Clover注入kext时不要求SIP禁用。

### 通过csrutil命令

在正常系统环境下，csrutil只能查询SIP状态，故需进入恢复模式更改。白苹果在启动时按command+R进入，而黑苹果在Clover的引导菜单进入。

#### 查询SIP状态

```
csrutil status
```

#### 完全启用SIP

```
csrutil enable
// 或
csrutil clear
```

#### 开关各项技术

```
csrutil enable [--without kext|fs|debug|dtrace|nvram] [--no-internal]
```

#### 禁用SIP

```
csrutil disable
// 或
csrutil enable --without kext --without fs --without debug --without dtrace --without nvram
```

### 通过Clover

通过修改Clover的Rt Variables选项卡中的CsrActiveConfig，可修改SIP状态。使用0x67或0x3E7以禁用。两者参数比较如下表。

| 参数  | 有效值                         |
| ----- | ------------------------------ |
| 0x67  | CSR_ALLOW_UNRESTRICTED_NVRAM   |
|       | CSR_ALLOW_UNRESTRICTED_NVRAM   |
|       | CSR_ALLOW_TASK_FOR_PID         |
|       | CSR_ALLOW_UNRESTRICTED_FS      |
|       | CSR_ALLOW_UNTRUSTED_KEXTS      |
| 0x3E7 | CSR_ALLOW_UNAPPROVED_KEXTS     |
|       | CSR_ALLOW_ANY_RECOVERY_OS      |
|       | CSR_ALLOW_DEVICE_CONFIGURATION |
|       | CSR_ALLOW_UNRESTRICTED_NVRAM   |
|       | CSR_ALLOW_UNRESTRICTED_DTRACE  |
|       | CSR_ALLOW_UNRESTRICTED_DTRACE  |
|       | CSR_ALLOW_UNTRUSTED_KEXTS      |

### 通过直接写入NVRAM

```
sudo nvram 7C436110-AB2A-4BBB-A880-FE41995C9F82:csr-active-config=%13%00%00%00
```

# Clover的使用

## base64与ASCII说明

在plist的文本中，data是以base64的格式存储的，而在用Xcode等编辑器打开后，base64将被显示为十六进制ASCII码。以下示例均在终端进行。

```
// base64->ASCII
echo RXh0ZXJuYWw= | python -m base64 -d && echo

// ASCII->base64
echo -n External | base64
```

## 常用快捷键

| 快捷键 | 含义                                      |
| ------ | ----------------------------------------- |
| F1     | 帮助                                      |
| F2     | 保存启动日志preboot.log到EFI/CLOVER/misc/ |
| F3     | 显示被隐藏的入口                          |
| F4     | 提取DSDT到EFI/CLOVER/ACPI/origin/         |
| F5     | 提取修正过的DSDT到EFI/CLOVER/ACPI/origin/ |
| F6     | 提取显卡ROM的VideoBios到EFI/CLOVER/misc/  |
| F10    | 截屏到EFI/CLOVER/misc/                    |
| F11    | 重置NVRAM                                 |
| 空格   | 制定菜单项的详细信息                      |
| A      | 关于Clover                                |
| O      | Clover选项                                |
| U      | 退出                                      |

## Options界面说明

### ACPI patching

| 选项                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| Tables dropping     | 等同于Clover Configurator中ACPI选项卡的Drop Tables           |
| Drop OEM _DSM       | 丢弃_DSM                                                     |
| DSDT fix mask       | DSDT修复遮盖，等同于Clover Configurator中ACPI选项卡的各种Fix选项 |
| Custom DSDT patches | 自定义的ACPI重命名                                           |

### System Parameters

| 选项               | 说明               |
| ------------------ | ------------------ |
| Booter Cfg Command | 自定义的ACPI重命名 |

## 目录说明

### Kexts目录

Kexts/10.x下的驱动，只有在Clover引导使用boot with injected kexts时才会加载。

### Driver目录

新版Clover驱动位置在`drivers/UEFI`以及`drivers/BIOS`中。但只要新目录中没有文件，旧路径`drivers64UEFI`和`drivers64`仍可正常使用。

### OEM目录

OEM目录下可存储包含特定机型的配置文件，如EFI/CLOVER/OEM/Inspiron1525/config.plist（kexts/FakeSMC.kext）或EFI/CLOVER/OEM/H61M-S1/UEFI/config.plist外加自定义DSDT.aml。其中有UEFI目录的为UEFI启动的配置文件。

OEM目录下的子目录名称是根据SMBIOS得到的，可以在preboot.log日志文件中查到。在Clover启动界面按下F2时，日志文件preboot.log就会保存到/EFI/Clover/misc目录下，在日志里搜索Cloverrevision即可。

## 启动参数

|            参数            |                             含义                             |
| -------------------------- | ------------------------------------------------------------ |
|             -v             |               啰嗦模式（跑代码模式，用于排错）               |
|             -x             |                           安全模式                           |
|             -s             |                   单用户模式（命令行模式）                   |
|          -liluoff          |                        跳过Lilu.kext                         |
|          -alcoff           |                      跳过AppleALC.kext                       |
|      -uia_exclude_hs       | 从USB3.0端口中禁用HS端口（将无法在这些端口上使用USB2.0设备，使用USB鼠标/键盘切勿使用） |
|           -xcpm            | 在Ivy Bridge系统里强制xcpm使用系统内核以实现CPU原生电源管理  |
|      -gux_defer_usb2       |   修复使用GenericUSBXHCI.kext导致的睡眠问题（i7系列适用）    |
|        -gux_no_idle        | 终止英特尔芯片上的一些空闲模式（idle-mode）功能（i7系列适用） |
|        -gux_noslee         |                强迫睡眠使用shutdown/reset方法                |
|         -gux_nomsi         |              强迫使用中断（interrupt）而不是msi              |
|     -amd_no_dgpu_accel     |                     关闭AMD显卡硬件加速                      |
|      -no_compat_check      |                        关闭兼容性检查                        |
|          slide=#           |                   手动设置KASLR slide值为#                   |
|           cpus=#           |                        启用#个CPU核心                        |
|     MountEFI=yes/diskX     |                     挂在所有/某个EFI分区                     |
|       LogLineCount=0       |              设置日志文件最大行数（0为无限制）               |
|      LogLocation=PATH      |                       日志文件保存路径                       |
|    LogEveryBoot=yes/no     |                     每次启动保存日志文件                     |
|           dart=0           |                修复因开启VT-d导致系统启动错误                |
| darkwake=no/0/1/2/3/4/8/10 |             禁止系统休眠/设定休眠模式为固定数值              |
|      kext-dev-mode=1       |                        启用第三方驱动                        |
|        nv_disable=1        |                    禁止加载Nvidia独显驱动                    |
|         nvda_drv=1         |     让系统安装Nvidia官方WebDriver显卡驱动（10.12+失效）      |
|         arch=i386          |                      以32位内核模式启动                      |
|        arch=x86_64         |                      以64位内核模式启动                      |
|        npci=0x2000         | 在旧设备上禁用 kIOPCIConfiguratorPFM64，解决独立显卡PCIconfiguration begin卡住不动的问题 |
|        npci=0x3000         |          解决独立显卡PCIconfiguration begin卡住不动          |
|           cpus=1           |                         使用单核模式                         |
|        debug=0x100         |                    防止内核崩溃时重新启动                    |
|         keepsyms=1         |     配合debug=0x100使用，使操作系统在内核崩溃时打印符号      |
|     Kernel=mach_kernel     |                        从系统内核启动                        |

## 隐藏启动界面

确保系统正常运作后，打开Clover的config.plist，进行下列操作。

```
Boot选项卡Verbose（-v）取消勾选
Hide Volume选项添加Preboot和Recovery（在引导界面按F3可重新显示）
Timeout设置为0以跳过倒计时（-1为停止倒计时），在启动时点击空格即可看到被跳过的引导界面
Default Boot Volume填写Mac系统盘名称或GUID号或设备路径，如HD(1,GPT,57272A5A-7EFE-4404-9CDA-C33761D0DB3C,0x800,0xFF000)
```

打开Mac系统盘下的Library/Preferences/SystemConfiguration，找到`com.apple.Boot.plist`。复制到桌面后用文本编辑器打开，删除里面的`-v`参数，保存后拷贝回原来的位置，替换原始文件后重启，正常情况下开机不会进入Clover界面和啰嗦模式。

下载`ApfsDriverLoader.efi`，打开Clover下的`Drivers64UEFI`文件夹，删掉apfs.efi并把ApfsDriverLoader.efi复制进去，重启后进入Clover的过程中没有log代码。

## 配置主题

将下载好的主题文件夹以主题本身名字命名，并复制到`EFI/CLOVER/themes`下，在`Gui`选项卡的右侧`Theme`下填写主题名称即可。

## 常用ACPI更名补丁

| 更名补丁                                                     | 作用                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| change EHC1 to EH01                                          | 避免与内置USB注入器冲突                                      |
| change EHC2 to EH02                                          | 避免与内置USB注入器冲突                                      |
| change XHC1 to XHC                                           | 避免与内置USB注入器冲突                                      |
| change XHCI to XHC                                           | 避免与内置USB注入器冲突                                      |
| change SAT0 to SATA                                          | 实现潜在的SATA兼容性                                         |
| change _OSI to XOSI                                          | 配合SSDT-XOSI.dsl启用系统操作补丁                            |
| change APSS to APXX                                          | 可能导致AppleIntelCPUPowerManagement出现紧急情况，如果ACPI中不存在APSS，则无需启用 |
| change _DSM to XDSM                                          | 对于VoodooI2C可能是必需的                                    |
| change LPC to LPCB                                           | 为macOS修补低引脚数总线                                      |
| change _REG to XREG in EC0                                   | 对于电池状态可能是必需的                                     |
| ALS: change Method(RALS,0,S) XALS                            | 对于环境光传感器可能是必需的                                 |
| Fix 300-series RTC Bug(Find A00A935354415301, Replace A00A910AFF0BFFFF) | 修复300系主板的RTC问题                                       |

## 选项配置

### 配置器条目说明

| 选项                         | 说明                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| **ACPI**                     |                                                              |
| **/ DSDT**                   |                                                              |
| ResetAddress                 | 修复重启，默认为0x64（通过PS2控制器重新启动），0x0CF9为通过PCI导轨重新启动，0x0为使用默认的FACP值（可在Clover启动界面按F2后打开日志文件查找Address得到本值，如0000000000000CF9即为0xCF9） |
| ResetValue                   | 修复重启，默认为0xFE（通过PS2控制器重新启动），0x06为通过PCI导轨重新启动，0x0为使用默认的FACP值（可在Clover启动界面按F2后打开日志文件查找Value to cause reset得到本值，如06即为0x06） |
| DsdtName                     | dsdt.aml的名称                                               |
| AddMCHC                      | 在DSDT中添加Device ID=0X0044的设备（匹配 Intel Clarkdale平台，有些芯片需要这个装置来解决 PCI 的电源管理问题，一般不启用） |
| AddIMEI                      | 用于intelHDxxx集成显卡，解决完美注入（需要开启fakeid-IMEI）  |
| AddPNLF                      | 添加PNLF设置代码，实现系统良好睡眠/唤醒                      |
| AddHDMI                      | 修复HDMI音频输出                                             |
| FixDarwin                    | 使macOS系统（Darwin）被标识为Windows                         |
| FixDisplay                   | 修复显卡代码（增加GFX0以及HDMI音频设置HADU）                 |
| FakeLPC                      | 仿冒LPC以加载AppleLPC.kext（Intel and NForce 芯片建议勾选，特别是ICH7/ICH9等老芯片组） |
| **->**                       | **以下四条修复IRQ，消除可能导致触摸板和音频设备无法正常工作的任何潜在IRQ冲突** |
| FixIPIC                      | 从decice IPIC移除中断语句(IRQ(2))，有利于某些笔记本电源按钮的工作 |
| FixRTC                       | 从RTC装置中删除IRQ(0)，作用与patch里的防RTC重置类似          |
| FixTMR                       | 从TMR装置中删除IRQ(8)，适用于较早的dos设备                   |
| FixHPET                      | 添加IRQ(0,8,11)代码，加载原生电源管理（10.9+无需勾选）       |
| **<-**                       |                                                              |
| FixIDE                       | 修复在10.6时五国出现的AppleIntelPIIXATA错误（一般不启用）    |
| FixSBUS                      | 增加SMBusControlle到设备树种，可修复因缺失SBUS控制而在系统log中出现的警告 |
| FixDisplay                   | 此补丁将影响所有视频卡，包括嵌入式英特尔GFX                  |
| FixIntelGfx                  | 开启对IntelGFX显卡的新补丁设置（需配合imei）                 |
| FixWAK                       | 消除警告                                                     |
| FixADP1                      | 将设备AC0重命名为ADP1，效果与直接用ACPI更名等效              |
| FixS3D                       | 修正_S3Dmethods函数，解决睡眠/唤醒问题                       |
| FixACST                      | 重命名ACST，常用于华硕主板以避免冲突（ACST项对于华硕是AC适配器状态，对于苹果是CPU闲置休眠状态的功能） |
| FixRegions                   | 修正BIOS中的浮动区域（每次BIOS设置更改时OperatingRegions的地址会被更改的区域） |
| FixHeaders                   | MACH重启修复，使用不会带来冲突                               |
| FixHDA                       | 修复声卡代码（修正AZAL为HDEF/HDAU，且增加layout -id和pinconfig/MaximumBootBeepVolume属性） |
| FixLAN                       | 修复网卡代码（注入网卡属性，修改网卡为内建）                 |
| FixAirport                   | 修复无线网卡代码（为支持Airport的无线网卡注入属性，以开启Airport功能） |
| Fixshutdown                  | 修复关机断电代码（主要是添加_PTS函数，判断寄存器arg0值是否为5并防止设置SLP_SMI_EN=1，华硕主板建议勾选），等效于应用\_PTS到ZPTS补丁并使用FixShutdown-USB-SSDT.dsl |
| FixUSB                       | 修复USB代码（包含EHCI代码，注入USB属性，帮助内建USB，是完成睡眠的关键） |
| FixFirewire                  | 修复1394火线代码（在火线控制装置中增加fwhub属性，一般不启用） |
| FixSATA                      | 内建磁盘，修复SATA代码（用ICH6的ID匹配，可修复硬盘图标显示为橙黄色问题） |
| PatchAPIC                    | 修复MADT表                                                   |
| HaltEnabler                  | 在OSX系统启动时设置SLP_SMI_EN=0，断电重启补丁，与OpenHaltRestart.kext作用相同 |
| UseSystemIO                  | 在生成的_CST表中对`Register (FFixedHW, `与`Register (SystemIO,`进行选择 |
| SlpSmiAtWake                 | 在_WAK方法添加SLP_SMI_EN=0，可能有助于解决UEFI引导时的睡眠和关机问题 |
| SuspendOverride              | 将睡眠模式扩展为状态3、4、5                                  |
| ReuseFFFF                    | 将诸如Name (_ADR, 0xFFFF)的设备转换为(ADR，0)并重新用于注入（如FakeID） |
| Rtc8Allowed                  | 设置RTC长度为8字节                                           |
| Debug                        | 打开后会在EFI/CLOVER/ACPI/origin看到未修补的DSDT-or.aml、经过第一次修补尝试后的DSDT-pa0.aml、经过第二次修补尝试后的DSDT-pa1.aml，一直生成，直至系统能够启动为止 |
| **/ SSDT**                   |                                                              |
| DeleteUnuse                  | 从DSDT中删除无用设备                                         |
| Double First State           | CPU功耗相关（Ivy Bridge专用）                                |
| Drop OEM                     | 获取内建SSDT表单，删除与OEM CPU相关的SSDT以避免在为处理器生成包含P State和C State的SSDT时发生冲突，使用自定义DSDT则默认值为false，使用BIOS.aml则默认值为true（对于Sandy Bridge及较旧硬件使用，非必须勿用） |
| Generate CStates             | 开启C-State功能，可以修复启动时ACPI_SMC_PlatformPlugin错误   |
| Generate PStates             | 开启P-State功能                                              |
| Plugin Type                  | 值为0时用ACPI_SMC_PlatformPlugin，为1时用X86PlatformPlugin（Ivy Bridge专用） |
| PLimit Dict                  | 限制CPU频率，0为不开启，1为降低一级，2为降低两级             |
| UnderVolt Step               | 限制CPU功率，只针对core 2，对i系列CPU无效，0为不开启，设为2可能死机 |
| Min Multiplier               | 最小变频数                                                   |
| Max Multiplier               | 最大变频数                                                   |
| smartUPS                     | 若打开，在系统概述的电源项中UPS已安装显示为是（只起修饰作用） |
| PatchAPIC                    | 在SSDT中添加CPUS=1，解决ACPI表单中NMI丢失时macOS启动可能五国的问题 |
| EnableC2/C4/C6/C7            | 在SSDT中添加_CST函数以启用C2/C4/C6/C7休眠                    |
| **/ RenameDevices**          | （以将\_SB.PCI0.RP02.PSXS->_SB.PCI0.RP02.ARPT为例）          |
| Find Device                  | _SB.PCI0.RP02.PSXS                                           |
| Rename Device                | ARPT                                                         |
| **/ DropTables**             | 通过签名和/或表标识符删除OEM ACPI表（Signature、TableID和Length都可以在原SSDT表头找到，注意Length需转换为十进制） |
| （SSDT）CpuPm / Cpu0Ist      | 与CPU电源管理有关                                            |
| DMAR                         | 防止Vt-d出现问题，修复10.8.2中的AppleACPIPlatform.kext启动问题 |
| MATS                         | 此表可能包含无法打印的字符以导致内核崩溃，打开该选项可修复ACPI_SMC_PlatformPlugin错误 |
| BGRT                         | 删除BIOS内置的启动图片                                       |
| HPET / MCFG                  | 获取MCFG表单                                                 |
| **BOOT**                     |                                                              |
| DefaultVolume                | 指定默认启动项，可以为卷名（Macintosh）、分区UUID（ABCDEFGH-ZXCV-ASDF-QWER-ASDFGHJKLPOIU）或设备路径（HD(1,GPT,ABCDEFGH-ZXCV-ASDF-QWER-ASDFGHJKLPOIU,0xAAA,0xBBBBB)） |
| DefaultLoader                | 指定加载器路径（如BOOTX64.efi）                              |
| XMPDetection                 | 当检测到XMP内存时读取最佳预设参数或禁止检测XMP内存           |
| Debug                        | 生成调试输出/EFI/CLOVER/misc/debug.log（大大增加加载时间）   |
| IgnoreNVRAMBoot              | 忽略启动磁盘面板的设置并允许始终启动到同一操作系统           |
| Secure                       | 在启用安全启动时使用Clover（需要签署Clover的二进制文件并加载证书） |
| Legacy                       | 加载旧版本Windows/Linux                                      |
| LegacyBiosDefaultEntry       | 指定以Legacy模式启动的硬盘（在Legacy选项选中后才会显示）     |
| Timeout                      | 0为直接跳过引导界面，-1为停止倒计时                          |
| RtcHibernateAware            | 在从休眠状态唤醒时启用RTC内存擦除，等同于启用AppleRTC补丁并使用HibernationFixup.kext（至少在Ivy Bridge和更高版本上使用，非必要请勿使用） |
| SignatureFixup               | 为True时签名值来自休眠映像，为False时签名值为零，内核将其复制到映像中。 |
| **Boot Graphics**            |                                                              |
| UI Scale                     | 高分辨率显示器可设置为2                                      |
| **CPU**                      |                                                              |
| Frequency MHz                | CPU频率（如3300-3.3GHz，建议不要使用）                       |
| BusSpeed kHz                 | 主频（如133330-133MHz）                                      |
| Type                         | CPU类型（如0x0901-i3，具体数值可百度）                       |
| QPI                          | CPU的总线速度（如5000-i3 2120，具体数值可百度，装饰作用）    |
| C2/C4/C6                     | 启用CPU的Turbo功能                                           |
| Type                         | CPU类型（修饰功能）                                          |
| Latency                      | 进入C3状态时发出的C3进入等待时间，最大为0x3E8（1000），0x3E9表示SpeedStep未打开，笔记本用户可用0x00FA以启用电源管理 |
| **Devices**                  |                                                              |
| FakeID                       | 更改PCI属性（只有AddMCHC启用，IMEI修复程序才会起作用）       |
| Audio / Inject               | 883（0x373）指Realtek ALC883                                 |
| ResetHDA                     | 音频控制器初始化（在冷启动后无法正常工作，但在重启或唤醒后仍可以工作时使用），等效于使用CodecCommander.kext |
| AFGLowPowerState             | 消除空闲模式后音频输出上的刺耳声音，等效于DeviceProperties-Add-PCIRoot...-AFGLowPowerState-<01000000> |
| USB / FixOwnership           | 使USB注入保持启用状态而仅关闭所有权修复，解决卡Enabling Legacy Matching问题（也可在BIOS启用XHCI Hand Off解决） |
| USB / AddClockID             | 启用良好的深度睡眠（无法通过键盘或鼠标输入/退出），需要配合USBInjection启用，等效于DeviceProperties-Add-PCIRoot...-AAPL,clock-id |
| USB / HighCurrent            | 为iPad充电等提供更多电源                                     |
| UseIntelHDMI                 | 如果为TRUE，hda-gfx = onboard-1将被注入到GFX0和HDEF设备中。另外，如果存在ATI或Nvidia HDMI设备，它们将被分配给onboard-2。如果为FALSE，则ATI或Nvidia设备以及HDAU设备（如果存在）将在onboard-1上 |
| NoDefaultProperties          | 影响DSDT修复程序，并强制它们生成空的_DSM，仅适用于显示、声音、LAN和WiFi |
| IntelMaxValue                | 需开启SetIntelBacklight和SetIntelMaxBacklight，建议Sandy/IvyBridge用1808，Haswell/Broadwell用2776，Skylake/Kabylake用1295 |
| **Gui**                      |                                                              |
| ConsoleMode                  | 将控制台驱动程序切换到请求的控制台模式（shell，boot.efi，dumpueficalls等使用，非必要不打开），0（默认）为禁用，Min/Max为最低/最高模式，分别适合慢速/快速控制台，输入数字为选择特定模式，在boot.log中可查 |
| Mouse / Mirror               | 单轴负移动速度                                               |
| ShowOptimus                  | 在某些BIOS中启用或禁用Optimus的情况下为其供电                |
| KbdPrevLang                  | 修复旧macOS使用英语以外的语言时弹出窗口中使用混合语言的问题，会导致启动Recovery HD时始终保持英语（仅在使用AptioMemoryFix/OsxAptioFixV3且无EmuVariableUefi时可用） |
| PlayAsync                    | 同步声音播放                                                 |
| **Graphics**                 |                                                              |
| VRAM                         | 设置显存（单位为MB）                                         |
| Load VBios                   | 从EFI/CLOVER/OEM/[model]/ROM或EFI/CLOVER/ROM/[vendor]_[device].rom加载视频BIOS（用于不带任何外部文件的移动Radeon卡，或向仅包含UEFI视频BIOS的卡提供旧版视频BIOS） |
| DualLink                     | 设为0可解决某些机型显示四分之一屏幕问题                      |
| Patch VBios                  | 获得最高监视器分辨率支持（若分辨率不满足需求，可安装驱动CsmVideoDxe.efi） |
| HorizontalSyncPulseWidth     | 注入32可解决某些机型出现八个苹果的问题                       |
| VideoPorts                   | 显卡上的端口数量（包括TVO和/或HDMI）                         |
| FBName                       | 选择帧缓冲区名称                                             |
| NVCAP                        | 注入NVCAP（仅对Nvidia视频卡有效）                            |
| display-cfg                  | 显卡CFG（仅对Nvidia视频卡有效）                              |
| RadeonDeInit                 | 取消初始化Radeon卡（解决AMD Radeon HD6xxx及更高版本在睡眠后无法唤醒的问题） |
| **Kernel and Kexts Patches** |                                                              |
| KernelCpu                    | 消除某些与旧系统中不受支持的CPU（例如Yonah，Atom或Haswell）相关的内核混乱 |
| AppleRTC                     | 修复RTC问题，确保重启后不会重置BIOS                          |
| KernelLapic                  | HP笔记本电脑存在Lapic问题，可以通过使用boot参数`cpus=1`或使用此选项来解决 |
| KernelHaswellE               | 阻止Haswell-E兼容性检查                                      |
| ATI Connectors Controller    | 修改AMD视频卡控制器从而驱动AMD卡                             |
| KernelPM                     | 阻止写入MSR 0xe2（适用于10.8.5和10.9内核），防止在使用XCPM时在启动时引起内核恐慌 |
| AppleIntelCPUPM              | 与KernelPM相同，但是使用AppleIntelCPUPowerManagement时       |
| KernelXCPM                   | 适用于IvyBridge CPU的XCPM补丁                                |
| InfoPlistPatch               | 修补kext的Info.plist                                         |
| **Rt variables**             |                                                              |
| BooterConfig                 | 一般为0x28                                                   |
| CsrActiveConfig              | 0x0-启用SIP，0x3-部分禁用SIP（允许加载未签名的kexts），0x3E7-完全禁用SIP |
| ROM / UseMacAddr0            | 将板载MAC地址作为ROM                                         |
| **SMBIOS**                   |                                                              |
| Trust                        | 优先考虑在SMBIOS或SPD中找到的内存描述符值（Mac中的内存描述不正确时需禁用） |
| Slots AAPL injections        | 填充PCI列表（一般不使用此法）                                |
| **System Paramaters**        |                                                              |
| BacklightLevel               | 监控亮度水平，最大值为0xFFFF（全背光），最小值为0x0（无），默认值为0x0501（只有部分系统会受到此参数的影响，台式机可忽略） |
| InjectSystemID               | 设置为YES时注入设备树ID（IODeviceTree:/efi/platform/system-id），NO时注入运行时变量（IODeviceTree:/options/platform-uuid） |
| InjectKexts                  | 是否从EFI/CLOVER/kexts/10.xx中注入kexts                      |

### ResetAddress和ResetValue

默认值分别为0x64和0xFE。若重启不断电，则根据ACPI表中FACP.aml的值更改，其中ResetAddress对应Address，ResetValue对应Value to cause reset（这些数值本来就是十六进制，无需转换）。

### C3功能

开启C3功能需要C3Latency的值小于1000（0x3e8），可在FACP.aml搜索C3 Latency得到。若大于1000，则在Clover中将C3Latency设为0x3E7，对于支持Turbo技术的CPU可设置为0xFA。

### BooterConfig

系统启动标志位，具体含义如下。

| BiT位 | 含义                                                        |
| ----- | ----------------------------------------------------------- |
| 0     | RebootOnPanic，遇到内核崩溃自动重启                         |
| 1     | HiDPI，在启动过程中使用HiDPI模式显示                        |
| 2     | Black，在启动过程中不显示进度条                             |
| 3     | CSRActiveConfig，将读取当前生效的SIP控制标志位              |
| 4     | CSRConfigMode，仅用于Recovery/安装环境，将允许对SIP进行配置 |
| 5     | CSRBoot，仅用于Recovery/安装环境，SIP将完全禁用             |
| 6     | BlackBg，在启动过程中使用黑色背景                           |
| 7     | LoginUI，在启动过程中使用登陆界面作为背景                   |
| 8     | InstallUI                                                   |

### Graphics

Clover检测到Intel iGPU时，如果config.plist中不存在Graphics部分，则会自动启用Intel显卡注入。

### EHC1/EHC2

可用以下SSDT禁用EHC1和EHC2。

```
/*
 * USB compatibility table for disable phantoms EHC1, EHC2.
 *
 * Attention!
 * Only for 7,8,9-series chipsets and 10.11 and newer!
 *
 * To disable EHC1 and EHC2 - set an option "XHCI Mode" to "Enabled" in yours BIOS.
 * If the "XHCI Mode" option is not available in yours BIOS or works incorrectly, then use this ACPI table.
 * Disabling through BIOS is preferable whenever possible.
 *
 * WARN: for some motherboards you need to use either "EH1D = One" or "EH2D = One" but not both!
 * This is because for some motherboards, device (EHC1 or EHC2) is used by macOS. Check the IOReg.
 */
DefinitionBlock ("", "SSDT", 2, "ACDT", "EHCx_OFF", 0x00001000)
{
    Scope (\)
    {
        OperationRegion (RCRG, SystemMemory, 0xFED1F418, One)
        Field (RCRG, DWordAcc, Lock, Preserve)
        {
                ,   13, 
            EH2D,   1, 
                ,   1, 
            EH1D,   1
        }

        Method (_INI, 0, NotSerialized)  // _INI: Initialize
        {
            // In most cases this patch does benefit all operating systems,
            // yet on select pre-Windows 10 it may cause issues.
            // Remove If (_OSI ("Darwin")) in case you have none.
            If (_OSI ("Darwin")) {
                EH1D = One  // Disable EHC1
                EH2D = One  // Disable EHC2
            }
        }
    }
}
```

## Clover内核

### BOOTX64与CLOVERX64

对于Clover安装包而言，两者为同一个文件。

### 从pkg安装包提取内核

下载好pkg后用`7z`打开，一路双击直到看到Clover相关文件即可。

### 手动更新Clover内核

覆盖EFI分区的以下文件即可。

```
 EFI/BOOT/BOOTX64.EFI
 EFI/CLOVER/CLOVERX64.efi
 EFI/CLOVER/drivers64UEFI下所有文件
```

## ACPI

### 热补丁的使用

Clover下要应用的热补丁和DSDT均需放置到ACPI/patched下才能起作用，ACPI/hotpatch只是热补丁库。热补丁的应用常常要配合ACPI的更名，但更名不一定都是为了应用hotpatch，也可以是为其它因素，两者不能混为一谈。

注意，Clover对ACPI的更名只对原有的ACPI表和ACPI/patched的DSDT起作用，而不对ACPI/patched的SSDT起作用。故若使用Clover的ACPI更名，则SSDT引用的变量需采用新名称而非旧名称。

### 搜索DSDT的顺序

```
->/booted_partition/DSDT.aml
->/clover_partition/EFI/CLOVER/OEM/your_motherboard_name/ACPI/patched/DSDT.aml
->BIOS
```

故如果希望每个操作系统使用不同的DSDT，则将它们放到各自操作系统盘的根目录下即可。

### 重命名

一般而言，Clover的ACPI重命名为四个字符，对应16进制的ASCII码为八位。但是为确定某一字节的具体位置，可以利用十位甚至更多的ASCII码。

利用Clover的重命名，可以禁用某些方法或配合SSDT调用新方法。

用Hex Friend打开DSDT文件，搜索要替换的字段名即可。如对于以下代码，在Hex Friend中搜索GPRW，根据对应位置找到想要替换的位置，得到代码47 50 52 57 02，其中02是源代码Method (GPRW, 2, NotSerialized)中的2。

```
Method (GPRW, 2, NotSerialized)
{
	......
}
```

### ACPI修补所添加的函数作用

#### _CST

与CPU的C2/C4/C6/C7休眠有关。

#### _PCT

有关SpeedStep功能。

#### _PPC

控制CPU频率范围。

#### _PSS

一组包含可能的CPU states数组，与PLimitDict/UnderVoltStep/Turbo有关。

## 驱动（.efi）作用

r4986以后，Clover会先从EFI/CLOVER/drivers/UEFI加载驱动，若无则从EFI/CLOVER/drivers64UEFI加载。

| 名称                                           | 说明                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| NTFS                                           | NTFS文件系统驱动程序（一般不使用，此模块与一些主板的UEFI不兼容） |
| XhciDxe                                        | 支持USB3总线，主要是Intel控制器                              |
| AppleEfiSignTool                               | 验证AppleEFI二进制文件的开源工具（一般不使用）               |
| AppleImageLoader                               | 保护AppleEfiFat二进制驱动程序，将AppleEFI二进制文件安全加载到内存中（一般不使用） |
| AppleUISupport                                 | 实现了FileVault支持并替换了AppleKeyMapAggregator.efi、AppleEvent.efi、AppleUiTheme.efi、FirmwareVolume.efi、AppleImageCodec.efi，同时包含一些主板的哈希服务修复和unicode排序规则（一般不使用） |
| SMCHelper                                      | VirtualSmc.efi在Clover中的替代品，用于与FakeSMC配合（不能与VirtualSMC.kext配合使用） |
| HashServiceFix                                 | 修复UEFI BIOS中的哈希支持                                    |
| FirmwareVolume                                 | 为Clover启动FileVault 2创建FirmwareVolume光标的驱动          |
| EnhancedFatDxe                                 | 如果在引导过程中写入EFI分区出现问题，则需要将此驱动用UEFITool刷入固件中 |
| EnglishDxe                                     | 在UEFI Shell中支持Unicode Collation协议                      |
| CsmVideoDxe                                    | 用于Clover GUI的显卡驱动，允许使用更多分辨率，基于UEFI BIOS中的CSM兼容模块，并将启用所需的CSM |
| AudioDxe                                       | 在Clover启动时播放声音的HDA驱动                              |
| AppleUITheme                                   | 为Clover支持FileVault 2启动UI主题的驱动                      |
| AppleKeyFeeder                                 | 为Clover支持FileVault 2内PS/2键盘输入的驱动                  |
| AppleKeyAggregator                             | 为Clover支持FileVault 2启动UI的驱动                          |
| AppleImageCodec                                | 为Clover启动FileVault 2解码PNG和BMP                          |
| ApfsDriverLoader                               | 从此容器加载已嵌入APFS容器中的apfs.efi驱动程序               |
| CsmVideoDxe                                    | 比UEFI里提供更多分辨率的显卡驱动（6代以前核显开CSM使用）     |
| PS2Mouse/PS2MouseAbsolute/UsbMouse*            | 使鼠标/触摸板在CloverGUI界面工作（一般不使用）               |
| Ps2MouseDxe                                    | PS/2鼠标驱动（已存在于所有UEFI固件）                         |
| Usb*/UHCI/EHCI/XHCI                            | 解决依赖性关系不满足导致的内建驱动工作不正常的情况的一组驱动（一般不使用） |
| OswLowMemFix                                   | 针对Insyde H2O UEFI的笔记本，OsxAptioFixDrv的简化版，修复低位内存问题，两个不能同时使用（一般不使用） |
| OSXAptioFix2Drv-Free2000                       | 用于修复UEFI固件上的内存问题（作者已经声明会损坏硬件）       |
| OsxAptioFixDrv/OsxAptioFix2Drv/OsxAptioFix3Drv | 修复AMIAptioEFI内存映射（一般只用OsxAptioFixDrv.efi）        |
| OsxFatBinaryDrv                                | 允许macOS 10.9以下版本加载FAT模块（如boot.efi）              |
| AptioMemoryFix                                 | 原始OsxAptioFix2驱动程序的分支（6代及以上电脑推荐使用）      |
| DataHubDxe                                     | macOS要求强制启动的协议（一般勾选，不会引起冲突）            |
| PartitionDxe                                   | 支持非常规分区图的驱动（混合GPT/MBR或Apple分区图等）         |
| NvmExpressDxe                                  | 支持SSD连接到NVM Express总线                                 |
| EmuVariableUefi                                | 对某些UEFI启动无法调用NVRAM的机器提供的NVRAM模拟（部分Skylake架构的机器需要，如Phoenix UEFI、DELL Vostro、ThinkPad，仅在必要时使用） |
| FSInject                                       | 加载第三方kexts                                              |
| VBoxExt4                                       | EXT4文件系统驱动，用于启动LinuxEFI系统                       |
| VBoxExt3                                       | EXT2/3文件系统驱动，用于启动LinuxEFI系统                     |
| VBoxHFS                                        | HFSPlus.efi的替代品（性能差，不推荐使用）                    |
| HFSPlus                                        | HFS+文件系统驱动程序                                         |
| AptioInputFix/AppleGenericInput                | 为FileVault 2 GUI输入支持提供AMIAPTIO专有鼠标和键盘协议（通常修改的UsbKbDxe更好，一般不使用） |
| AppleDxeImageVerificationLib                   | 为EFI二进制文件提供Apple的加密签名算法（一般不使用）         |

## OcQuirks

此驱动依赖FwRuntimeServices.efi，用于替代AptioMemoryFix和所有OsxAptioFix变体，且将OpenCore的部分参数移植到Clover。将OcQuirks.efi、OcQuirks.plist和FwRuntimeServices.efi放到Clover的Drivers文件夹中，重启即可。若未找到OcQuirks，则采用以下默认值。

| Quirk                         | 默认值 | 说明                                                         |
| ----------------------------- | ------ | ------------------------------------------------------------ |
| AvoidRuntimeDefrag            | True   | 开启后会修复UEFI的运行服务，例如日期、时间、NVRAM、电源控制等 |
| DevirtualiseMmio              | False  | 开启后会减少Stolen内存占用空间，扩大`Slide=N`值的范围, 适用于大多数主板 |
| DisableSingleUser             | False  | 开启后会禁止`Cmd+S`和`-s`的使用，使设备更加接近于T2白苹果    |
| DisableVariableWrite          | False  | 开启后会禁止NVRAM写入，在Z390/HM370等没有原生macOS支持NVRAM的主板上需要开启 |
| DiscardHibernateMap           | False  | 开启后会重用原始休眠内存映射，仅某些旧硬件需要               |
| EnableSafeModeSlide           | True   | 开启后会允许在安全模式下使用Slide值                          |
| EnableWriteUnprotector        | True   | 开启后会在执行期间删除CR0寄存器中的写入保护                  |
| ForceExitBootServices         | True   | 开启后会确保ExitBootServices即使在MemoryMap发生更改时也能调用成功（除非有必要，否则请勿使用） |
| MmioWhitelist                 | Array  | 设计为用plist dict值填充，描述使用`DevirtualiseMmio`quirk时对特定固件功能至关重要的地址 |
| ProtectMemoryRegions          | False  | 开启后会用于修复人为制造和睡眠唤醒的问题（AvoidRuntimeDefrag已经修复了这个问题，所以尽可能避免使用这个Quirk） |
| ProtectSecureBoot             | False  | 避免操作系统对UEFI安全启动变量（db/dbx/PX/KEK）进行写入（主要用于避免Insyde主板和MacPro 5,1的NVRAM问题） |
| ProtectUefiServices           | False  | 保护UEFI服务不被固件覆盖                                     |
| ProvideConsoleGopEnable       | True   | 如果控制台句柄中缺少图形输出协议，请安装                     |
| ProtectCsmRegion              | False  |                                                              |
| ProvideCustomSlide            | True   | 如果Slide值存在冲突，此选项将强制macOS使用一个伪随机值（只有在遇到OnlyN/256slidevaluesareusable!时需要） |
| RebuildAppleMemoryMap         | True   | 生成与macOS兼容的内存映射                                    |
| SetupVirtualMap               | True   | 开启后会将SetVirtualAddresses调用修复为虚拟地址              |
| SignalAppleOS                 | False  | 通过OS Info将macOS加载的信息报告给其它操作系统，用于在Windows中为MacBook启用iGPU |
| SyncRuntimePermissions        | True   | 同步运行时环境的内存权限                                     |
| ShrinkMemoryMap               | False  |                                                              |
| QuirksProvideConsoleGopEnable | True   |                                                              |

# 恢复盘

## FireWolf OS X PE

类似Windows PE，FireWolf OS X PE可视作macPE，可为macOS提供恢复环境。

### 安装

在Windows下用Etcher刻录到U盘即可。

### 引导启动

在启动之前需先确保系统完整性保护（SIP）中已允许加载任意Recovery系统，即启用CSR标志位`CSR_ALLOW_ANY_RECOVERY_OS`，修改Clover的Rt Variables选项卡中的CsrActiveConfig为0x3E7即可。

# ACPIDebug的使用

ACPIDebug可用于高级人员排错，也可记录部分按键轨迹。

## 驱动

将ACPIDebug.kext放入`SLE`中并用Kext Utility修复权限，然后需要对DSDT打补丁，可通过以下两种方式实现。

### 通过修改DSDT

用`MaciASL`打开DSDT，点击`Patch`，在`_OS-X-ACPI-Debug`类别下打`Add DSDT Debug Methods`和`Instrument EC Queries`这两个补丁，退出并保存即可。

### 通过热补丁

在Clover的ACPI/patched放置OC-little包中的`SSDT-RMDT.aml`（基本补丁）和以下`SSDT-TEST.aml`，以Dell为例。

```
// In config ACPI, NEVT to XEVT
// Find:     4E 45 56 54 00
// Replace:  58 45 56 54 00
//
DefinitionBlock("", "SSDT", 2, "ACDT", "TEST", 0)
{
    External(XEVT, MethodObj)
    External(RMDT.P1, MethodObj)
    
    Method (NEVT, 0, NotSerialized)
    {
        If (_OSI ("Darwin"))
        {
            \RMDT.P1("AAA001")
        }
        \XEVT()
    }
}
//EOF
```

## 使用

重启后在Launchpad找到`控制台`，在搜索框输入`ACPIDebug`，然后按下键盘`Fn+F5/F6`。若控制台中出现信息，则ACPIDebug驱动成功。

# 用OpenCore替代Clover引导

OpenCore是取代Clover的新引导程序，可使macOS拥有更快的启动速度。

## 修改SSDT/DSDT

由于OpenCore中的SSDT、DSDT、SMBIOS等将会对所有操作系统生效，因此需要修改SSDT和DSDT以使其只对MacOS生效。具体方法为在代码生效部分加上以下代码括住。

```
If (_OSI ("Darwin"))
{
	// 原本的代码
}	
```

若使用OC-little包中的SSDT，则无需此操作。完成后将修改过的SSDT和DSDT放回Clover中，重启后在终端输入以下命令。如果日志中不存在ACPI Error，则修改成功。

```
log show --last boot | grep -Ei "ACPI"
```

## 减少DSDT重命名

具体如下。同时不要改动`_PRW`和` _DSM`，改动后可能使硬件在Windows下工作不正常。

| 可精简的重命名 | 取代方法及理由                                               |
| -------------- | ------------------------------------------------------------ |
| EHC1 to EH01   | 由OpenCore官方的SSDT-EHCx_OFF关闭EHC控制器，六代以上（如Skylake）没有EHC控制器，可直接删除 |
| EHC2 to EH02   | 由OpenCore官方的SSDT-EHCx_OFF关闭EHC控制器，六代以上（如Skylake）没有EHC控制器，可直接删除 |
| SAT0 to SATA   | 无作用                                                       |
| SAT1 to SATA   | 无作用                                                       |
| PC00 to PCIO   | 无作用                                                       |
| FPU to MATH    | 无作用                                                       |
| TMR to TIMR    | 无作用                                                       |
| GBE1 to ETH0   | 无作用                                                       |
| PIC to IPIC    | 无作用                                                       |
| HECI to IMEI   | WhateverGreen已处理                                          |
| HEC1 to IMEI   | WhateverGreen已处理                                          |
| MEI to IMEI    | WhateverGreen已处理                                          |
| IDER to MEID   | WhateverGreen已处理                                          |
| GFX0 to IGPU   | WhateverGreen已处理                                          |
| PEG0 to GFX0   | WhateverGreen已处理                                          |
| PEGP to GFX0   | WhateverGreen已处理                                          |
| SL01 to PEGP   | WhateverGreen已处理                                          |
| EC0 to EC      | 在OC-little中有仿冒EC的相关SSDT补丁                          |
| H_EC to EC     | 在OC-little中有仿冒EC的相关SSDT补丁                          |
| ECDV to EC     | 在OC-little中有仿冒EC的相关SSDT补丁                          |
| PGEC to EC     | 在OC-little中有仿冒EC的相关SSDT补丁                          |
| HDAS to HDEF   | AppleALC已处理                                               |
| CAVS to HDEF   | AppleALC已处理                                               |
| AZAL to HDEF   | AppleALC已处理                                               |
| STAS to Noop   | 由OC-little中的SSDT-AWAC相关补丁替代                         |
| PXSX to ANS1   | 用NVMeFix.kext修复NVMe SSD的电源管理                         |
| PXSX to ANS2   | 用NVMeFix.kext修复NVMe SSD的电源管理                         |
| LPC0 to LPCB   | OC-little 中分别有 SBUS 的 `SSDT` 注入补丁和 MCHC 设备补丁   |
| _OSI to XOSI   | 除非某些硬件设备只能在 Windows下工作（ I2C触摸板/ThinkPad对 FreeBSD的特殊优化），否则不要使用SSDT-XOSI补丁（直接定制SSDT也可以解除某些硬件的操作系统限制），此改动可能会使Windows启动时蓝屏 |
| OSID to XSID   | 除非某些硬件设备只能在 Windows下工作（ I2C触摸板/ThinkPad对 FreeBSD的特殊优化），否则不要使用SSDT-XOSI补丁（直接定制SSDT也可以解除某些硬件的操作系统限制），此改动可能会使Windows启动时蓝屏 |
| _DSM to XDSM   | 若所有SSDT补丁中没有依赖`_DSM`，则应该删除该重命名           |

## 摆脱对Clover的ACPI Quirks依赖

| ACPI Quirks         | 取代方法                                                     |
| ------------------- | ------------------------------------------------------------ |
| FixIPIC             | OC-little的`声卡IRQ补丁`                                     |
| FixSBUS             | OC-little的`注入SBUS设备`                                    |
| FixShutdown         | OC-little的`PTSWAK综合补丁`，需要添加其中的`EXT1`插件补丁    |
| FixDisplay          | 使用WhateverGreen和定制缓冲帧补丁                            |
| AddMCHC             | OC-little的`注入缺失的 MCHC`                                 |
| FixHDA              | 使用AppleALC                                                 |
| FixHPET             | OC-little的`声卡IRQ补丁`                                     |
| FixSATA             | 开启OpenCore中名为`ExternalDiskIcons`的Quirk，也可使用`innie.kext` |
| FixADP1             | 使用DSDT重命名`AC0_ to ADP1`，可能还要额外注入`Name (_PRW, Package (0x02) {0x1C,0x03})` |
| FixRTC              | OC-little的`声卡IRQ补丁`                                     |
| FixTIMR             | OC-little的`声卡IRQ补丁`                                     |
| AddPNLF             | OC-little的`注入PNLF`                                        |
| AddIMEI             | 使用WhateverGreen                                            |
| FixIntelGfx         | 使用WhateverGreen                                            |
| AddHDMI             | 使用WhateverGreen                                            |
| DisableASPM         | 在设备属性（DeviceProperties）中分别添加相关设备的PCI总线位置并注入属性`pci-aspm-default | DATA | <00>` |
| PluginType          | OC-little的`注入X86`                                         |
| Generate P/C States | 六代以前CPU才需要设置，可用ssdtPRGen.sh生成对应的 SSDT       |

## 搭建引导

将下载的OpenCore压缩包解压，打开后将EFI文件夹复制到硬盘的EFI分区或FAT32的U盘下。

复制Docs下的Sample.plist到EFI/OC，并更名为config.plist。将AppleSupportPkg、OcBinaryData中必需的.efi驱动放入Drivers文件夹，将DSDT/SSDT放入ACPI文件夹，将kext放入Kexts文件夹。将OcBinaryData中Resources内的文件放入Resources文件夹。

开始时建议不要添加任何SSDT/DSDT，少用改名补丁，只放`Lilu.kext`、`Whatevergreen.kext`和`VirtualSMC.kext`，以避免不必要的错误。待正常启动后，可放入其他kext。Drivers目录下的以下文件可按照需要删除。

| 驱动名称                      | 原因/用途                                                    |
| ----------------------------- | ------------------------------------------------------------ |
| ExFatDxe/ExFatDxeLegacy       | 除非EFI/某个系统分区是ExFAT格式，否则不需要保留              |
| HfsPlus/HfsPlusLegacy/VBoxHfs | 留其一即可（一般推荐用HfsPlus，四代以前机型用HfsPlusLegacy） |
| AppleUsbKbDxe/OpenUsbKbDxe    | 三代以前机型使用（现代机型应使用OpenCore中KeySupport这一Quirk） |
| NvmExpressDxe                 | 四代（Haswell）以前机型使用的NVMe硬盘加载驱动                |
| XhciDxe                       | 为二代（Sandy Bridge）以前的主板提供XHCI支持                 |
| HiiDatabase                   | 为四代以前的设备提供UEFI界面字体渲染支持，UEFI Shell中文字渲染异常时使用，新主板不需要 |
| AppleUsbKbDxe                 | 给使用模拟UEFI的老主板在OpenCore界面正常输入用（勿在Ivy Bridge即三代酷睿及以上的主板使用） |
| VirtualSmc                    | 已被替代为AppleSmcIO这一Quirk                                |
| AppleGenericInput             | 添加了对AppleKeyMapAggregator协议支持的用户输入驱动，解决了某些固件上的鼠标输入问题（AppleUsbKbDxe的等效驱动），已合并入OpenCore |
| AppleImageCodec               | 为Clover启动FileVault 2解码PNG和BMP，OpenCore已集成          |
| AppleKeyAggregator            | 为Clover支持FileVault 2启动UI的驱动，OpenCore已集成          |
| AppleKeyFeeder                | 为Clover支持FileVault 2内PS/2键盘输入的驱动，OpenCore已集成  |
| AppleUITheme                  | 为Clover支持FileVault 2启动UI主题的驱动，OpenCore已集成      |
| AptioInputFix                 | 解决UEFI固件输入问题的驱动，已与OpenCore合并                 |
| AptioMemoryFix                | NVRAM和内存驱动，用于修复UEFI固件上的内存问题，已与OpenCore合并为FwRuntimeServices |
| CsmVideoDxe                   | 用于Clover GUI的显卡驱动，允许使用更多分辨率，基于UEFI BIOS中的CSM兼容模块，并将启用所需的CSM，OpenCore不兼容 |
| DataHubDxe                    | macOS必需的DataHub协议，OpenCore自带且提供了这个Quirk        |
| EmuVariableUefi               | Clover的模拟NVRAM驱动，OpenCore不兼容，替代品为VariableRuntimeDxe |
| EnglishDxe                    | 在UEFI Shell中支持UnicodeCollation协议，OpenCore自带且提供了这个Quirk |
| EnhancedFatDxe                | 这个驱动已存在于所有UEFI固件中，无法从OpenCore直接使用（由于很多固件的FAT支持都有问题，导致在尝试写入时会损坏文件系统，如果在引导过程中写入EFI 分区出现问题，则需要将此驱动用UEFITool刷入固件中） |
| FirmwareVolume                | 为Clover启动FileVault 2创建FirmwareVolume光标的驱动，OpenCore已集成 |
| FSInject                      | Clover用于注入Kext的驱动，OpenCore自带且使用更先进的方法     |
| HashServiceFix                | 修复UEFI BIOS中的哈希支持，OpenCore自带且提供了这个Quirk     |
| OsxAptioFixDrv                | 旧的Clover NVRAM和内存驱动，用于修复UEFI固件上的内存问题，与FwRuntimeServices和OpenCore不兼容 |
| OSXAptioFix2Drv-Free2000      | Clover的内存驱动，用于修复UEFI固件上的内存问题，作者已经声明会损坏硬件 |
| OsxAptioFix3Drv               | CloverNVRAM和内存驱动，用于修复UEFI固件上的内存问题，与FwRuntimeServices和OpenCore不兼容 |
| OsxFatBinaryDrv               | Clover用于支持OS X 10.9和更早版本的FAT Binary可执行文件的驱动，与FwRuntimeServices和OpenCore不兼容 |
| OsxLowMemFixDrv               | 精简版的OsxAptioFixDrv，用于修复UEFI固件上的内存问题，与FwRuntimeServices和OpenCore不兼容 |
| PartitionDxe                  | 用于支持非常规分区图的驱动（如混合GPT/MBR或Apple分区图），OpenCore兼容性未知 |
| Ps2MouseDxe                   | PS/2鼠标驱动，已存在于所有UEFI固件中                         |
| SMCHelper                     | UEFI层面的SMC驱动，与FakeSMC配合，与OpenCore不兼容           |

Tools目录下的以下文件可按照需要删除。

| 工具名称     | 原因/用途                                                    |
| ------------ | ------------------------------------------------------------ |
| BootKicker   | 调用Mac内置的引导界面，用于白苹果安装OpenCore |
| CleanNvram   | OpenCore自带的NVRAM清理功能已经足够              |
| GopStop      | 停止显卡GOP（排错时使用）                                     |
| HdaCodecDump | 导出声卡Codec，可用于定制声卡          |
| VerifyMsrE2  | 用于检查主板上CFG锁的状态                                  |

建议保留以下文件。

```
// Kexts文件夹
Lilu.kext
AppleALC.kext
VirtualSMC.kext
SMCProcessor.kext
SMCSuperIO.kext
WhateverGreen.kext
IntelMausi.kext
Usbinjectall.kext
NVMeFix.kext
               
// Drivers文件夹
MemoryAllocation.efi
OpenRuntime.efi
HFSPlus.efi
OpenUsbKbDxe.efi
OpenCanopy.efi
```

本机用旧版Lilu和Whatevergreen时，会导致卡显卡驱动而无法进入系统的情况。因此在配置前，务必保证所有kexts均为最新版。注意，使用Catalina时，由于无线网卡驱动已放到SLE下，OC的Kexts文件夹无需再放置。

## 配置修改

用`Xcode`或`ProperTree`打开config.plist，也可使用`OpenCore Configurator`。根据以下内容进行配置。

### ACPI

#### Add

此处需添加EFI/OC/ACPI下的DSDT/SSDT路径。以添加电池热补丁SSDT-BAT0.aml为例，格式如下。

|  选项   |     设置      |      说明      |
| ------- | ------------- | -------------- |
| Comment |    Battery    | 注释（可不填） |
| Enabled |     True      |   启用/禁用    |
|  Path   | SSDT-BAT0.aml | DSDT/SSDT路径  |

注意应把DSDT放到最前面，而SSDT的顺序一般可以随意。

#### Delete

禁用某个ACPI表，常用于禁用DAMR来关闭VT-d，一般无需修改。

#### Patch

对DSDT（SSDT）的内容进行查找和替换，相当于Clover里的改名补丁。如`EC0->EC`补丁，书写格式如下。

|      选项      |       设置       |          说明           |
| -------------- | ---------------- | ----------------------- |
|    Comment     | change EC0 to EC |     注释（可不填）      |
|     Count      |        0         | 执行次数（0代表不限制） |
|    Enabled     |       True       |        启用/禁用        |
|      Find      |     4543305F     |   寻找的十六进制内容    |
|     Limit      |        0         |                         |
|      Mask      |        /         |                         |
|   OemTableId   |        /         |                         |
|    Replace     |     45435F5F     |  替换为的十六进制内容   |
|  ReplaceMask   |        /         |                         |
|      Skip      |        0         | 跳过前N个，0表示不跳过  |
|  TableLength   |        0         |                         |
| TableSignature |        /         |                         |

通过`Count`和`Skip`的应用，可以达成Clover下Tgtbridge的效果。具体而言，若想将DSDT中的某一特定字段重命名，则可在Skip下填写在该字段之前有多少个同名字段，在Count下填写该改名规则需执行几次。如现需将DSDT中的第54和55个_PRW改为XPRW，则Skip填53，Count填2。

对于华擎、华硕、微星主板，可能会遇到RTC问题而无法进入系统。此时可应用以下补丁。

```
Comment / RTC fix
Count / 0
Enabled / YES
Find / <A00A9353 54415301>
Limit / 0
Mask / <>
OemTable / <>
Replace / <A00A910A FF0BFFFF>
ReplaceMask / <>
Skip / 0
TableLength / 0
TableSignature / <>
```

#### Quirks

|       选项       | 设置 |                             说明                             |
| ---------------- | ---- | ------------------------------------------------------------ |
| FadtEnableReset  |  NO  |   在旧硬件上修复重启和关机（如果遇到关机变重启可尝试打开）   |
| NormalizeHeaders |  NO  |        清除ACPI头字段（macOS 10.13/某些主板需要打开）        |
|  RebaseRegions   | YES  | 尝试试探性地重新定位ACPI内存区域，使用自定义DSDT则必须开启（换硬件、升级BIOS等对硬件的操作会对ACPI表产生影响） |
|    ResetHwSig    |  NO  | 休眠相关（重启后因无法维持硬件签名而导致从休眠中唤醒的硬件需要开启，台式机不需要） |
| ResetLogoStatus  |  NO  |   无法在有BGRT表的系统上显示OEM Windows标志的硬件需要开启    |

### Booter

#### MmioWhitelist

默认的第一项是为Haswell芯片提供的内存寻址修复，第二项是ACPI、PCI device同时释放到内存时发生0x1000内存地址被占用而卡在PCI Configration的问题修复。一般无需修改。

#### Quirks

##### 总览

|          选项          | 内容 |                             说明                             |
| ---------------------- | ---- | ------------------------------------------------------------ |
|   AvoidRuntimeDefrag   | YES  |       修复UEFI的运行服务（日期/时间/NVRAM/电源控制等）       |
|    DevirtualiseMmio    |  NO  | 减少Stolen内存占用空间，扩大Slide=N值的范围（若要添加slide=1以使用连续性的内存注入方式则关闭） |
|   DisableSingleUser    |  NO  |            禁用单用户模式， 更接近基于T2的计算机             |
|  DisableVariableWrite  | YES  |   在没有原生macOS支持NVRAM的主板上需要开启以禁止NVRAM写入    |
|  DiscardHibernateMap   |  NO  |       开启后会重用原始休眠内存映射（仅某些旧硬件需要）       |
|  EnableSafeModeSlide   | YES  |                 允许在安全模式下使用Slide值                  |
| EnableWriteUnprotector | YES  | 在执行期间删除CR0寄存器中的写入保护，保证NVRAM能正常写入而不受到UEFI内的一些服务的影响 |
| ForceExitBootServices  |  NO  | 开启后会确保ExitBootServices即使在MemoryMap发生更改时也能调用成功，让非常老旧的主板也能使用内存寻址（非必要勿使用） |
|  ProtectMemoryRegions  |  NO  |                   与AvoidRuntimeDefrag类似                   |
|   ProtectSecureBoot    | YES  |                    保护UEFI安全启动被写入                    |
|  ProtectUefiServices   | YES  |              解决Z390系列主板卡开机卡++++的问题              |
|    ProtectCsmRegion    |  NO  | 开启后会用于修复人为制造和睡眠唤醒的问题（AvoidRuntimeDefrag已经修复了这个问题，故避免使用） |
|   ProvideCustomSlide   | YES  | 如果Slide值存在冲突，此选项将强制macOS使用一个伪随机值（遇到Only N/256 slide values are usable!时需要） |
|    ProvideMaxSlide     | YES  |                启用KASLR则选择YES，否则选择NO                |
| RebuildAppleMemoryMap  |  NO  | 重新生成内存地图来匹配苹果系统，遇到内存问题导致无法开机时可尝试使用（与EnableWriteUnprotector存在冲突关系，若开启此项则需关闭另一项，同时此项需要与SyncRuntimePermissions搭配使用） |
|    SetupVirtualMap     | YES  |               建立虚拟内存并对物理内存进行映射               |
|    ShrinkMemoryMap     |  NO  |     有巨大且不兼容内存映射的主板需要开启（非必要勿使用）     |
|     SignalAppleOS      |  NO  | 通过OS Info将macOS加载的信息报告给其它操作系统，用于在Windows中MacBook 启用iGPU |
| SyncRuntimePermissions |  NO  | 修正硬件在注入内存时无法注入权限的问题。一般此类问题存在2018年后的主板 |

##### 关于内存分配

KASLR是更加高效的内存注入方式，有以下两种关于内存的设置。

###### 开启KASLR内存注入方式

DevirtualiseMmio选择yes，ProtectUefiServices选择yes，删除boot-args里面的slide=1，删除Drivers文件夹下的Memoryallocations.efi。

###### 开启连续性内存注入方式

DevirtualiseMmio选择yes，ProtectUefiServices选择no，保留boot-args里面的slide=1，保留Drivers文件夹下的Memoryallocations.efi。

### DeviceProperties

#### Add

此处内容可以用Hackintool生成后直接复制，注意设备路径的修改。

```
// PciRoot(0x0)/Pci(0x1f,0x3)
注入音频Layout ID

// PciRoot(0x0)/Pci(0x2,0x0)
注入缓冲帧补丁
```

#### Delete

用于删除/屏蔽设备属性，等同于Clover里的ACPI重命名+TgtBridge，一般无需修改。

### Kernel

#### Add

##### 总览

此处需要填写kexts的具体内容，OpenCore会按照顺序执行，故需确保 Lilu.Kext始终在第一位。其他优先级高的为Lilu的插件，如VirtualSMC、AppleALC、WhateverGreen等。

若不知道kexts的加载顺序或加载内容，可在Clover Configuration中选择boot.log选项卡，并点击Generate log生成启动日志，在日志中搜索对应kext即可。

|      选项      |        内容         |                             说明                             |
| -------------- | ------------------- | ------------------------------------------------------------ |
|   BundlePath   |      Lilu.kext      |                          kext的名称                          |
|    Comment     |                     |                        注释（可不填）                        |
|    Enabled     |         YES         |                          启用/禁用                           |
| ExecutablePath | Contents/MacOS/Lilu | 隐藏在kext中的实际可执行文件的路径，可通过右键单击并选择`显示包内容` 查看，通常为 `Contents/MacOS/[kext]`），没有可执行文件的空壳kexts则留空（如USBPorts.kext） |
|   MaxKernel    |                     |   支持的最大系统版本（`19`为10.15，`18`为10.14），一般留空   |
|   MinKernel    |                     |                      支持的最小系统版本                      |
|   PlistPath    | Contents/Info.plist |                 隐藏在kext中的Info.plist路径                 |

此处需注意VoodooPS2与VoodooI2C的注入样板。当Plugins内有kexts时，需一并加载，且格式如下。

```
// BundlePath
A.kext/Contents/PlugIns/B.kext

// ExecutablePath
Contents/MacOS/B

// PlistPath
Contents/Info.plist
```

##### 注入样板

###### VoodooPS2

```
<dict>
	<key>BundlePath</key>
	<string>VoodooPS2Controller.kext</string>
	<key>Comment</key>
	<string>Keyboard</string>
	<key>Enabled</key>
	<true/>
	<key>ExecutablePath</key>
	<string>Contents/MacOS/VoodooPS2Controller</string>
	<key>MaxKernel</key>
	<string></string>
	<key>MinKernel</key>
	<string></string>
	<key>PlistPath</key>
	<string>Contents/Info.plist</string>
</dict>
<dict>
	<key>BundlePath</key>
			<string>VoodooPS2Controller.kext/Contents/PlugIns/VoodooPS2Keyboard.kext</string>
	<key>Comment</key>
	<string>Keyboard</string>
	<key>Enabled</key>
	<true/>
	<key>ExecutablePath</key>
	<string>Contents/MacOS/VoodooPS2Keyboard</string>
	<key>MaxKernel</key>
	<string></string>
	<key>MinKernel</key>
	<string></string>
	<key>PlistPath</key>
	<string>Contents/Info.plist</string>
</dict>
```

###### VoodooI2C

```
<dict>
		<key>BundlePath</key>
				<string>VoodooI2C.kext/Contents/PlugIns/VoodooI2CServices.kext</string>
		<key>Comment</key>
		<string>I2CServices</string>
		<key>Enabled</key>
		<true/>
		<key>ExecutablePath</key>
		<string>Contents/MacOS/VoodooI2CServices</string>
		<key>MaxKernel</key>
		<string></string>
		<key>MinKernel</key>
		<string></string>
		<key>PlistPath</key>
		<string>Contents/Info.plist</string>
</dict>	
<dict>
		<key>BundlePath</key>
				<string>VoodooI2C.kext/Contents/PlugIns/VoodooGPIO.kext</string>
		<key>Comment</key>
		<string>GPIO</string>
		<key>Enabled</key>
		<true/>
		<key>ExecutablePath</key>
		<string>Contents/MacOS/VoodooGPIO</string>
		<key>MaxKernel</key>
		<string></string>
		<key>MinKernel</key>
		<string></string>
		<key>PlistPath</key>
		<string>Contents/Info.plist</string>
</dict>
<dict>
		<key>BundlePath</key>
		<string>VoodooI2C.kext</string>
		<key>Comment</key>
		<string>I2C</string>
		<key>Enabled</key>
		<true/>
		<key>ExecutablePath</key>
		<string>Contents/MacOS/VoodooI2C</string>
		<key>MaxKernel</key>
		<string></string>
		<key>MinKernel</key>
		<string></string>
		<key>PlistPath</key>
		<string>Contents/Info.plist</string>
</dict>
<dict>
		<key>BundlePath</key>
		<string>VoodooI2CHID.kext</string>
		<key>Comment</key>
		<string>I2CHID</string>
		<key>Enabled</key>
		<true/>
		<key>ExecutablePath</key>
		<string>Contents/MacOS/VoodooI2CHID</string>
		<key>MaxKernel</key>
		<string></string>
		<key>MinKernel</key>
		<string></string>
		<key>PlistPath</key>
		<string>Contents/Info.plist</string>
</dict>
```

#### Emulate

仿冒不支持的 CPU（如奔腾、赛扬），一般无需修改。

|   选项    | 内容 |           说明            |
| --------- | ---- | ------------------------- |
| CpuidMask |  /   | 设置为零时将使用原始CPU位 |
| CpuidData |  /   |       CPU的被仿冒位       |

#### Block

屏蔽系统里的kext，在Identifier中输入kext的捆绑包标识符即可，一般为`com.apple.driver.[kext]`，可在`关于本机`-`系统报告…`-`功能扩展`中查到。如屏蔽AppleIntelLpssI2C和AppleIntelLpssI2CController，Identifier分别为com.apple.driver.AppleIntelLpssI2C和com.apple.driver.AppleIntelLpssI2CController，而禁用RTC关机校验，则屏蔽com.apple.driver.AppleRTC（Base为__ZN8AppleRTC14updateChecksumEv）。

#### Patch

用于添加系统内核补丁、Kext 补丁、AMD CPU补丁等，等同于Clover的`KextToPatch`和`KernelToPatch`。默认的第一、二项用于修复Apple RTC，若华硕主板出现RTC错误而无法开机，可以尝试打开这两个中的其中一个（一次只用一个）以修复华硕主板重启丢失BIOS设置以及需要按F1跳过安全模式的问题。

#### Quirks

|          选项           | 内容 |                             说明                             |
| ----------------------- | ---- | ------------------------------------------------------------ |
|    AppleCpuPmCfgLock    |  NO  | 若设备的CFG-Lock开启则设置为YES（尽可能用Grub关闭BIOS的CFG-Lock以避免开启，与Clover的AppleIntelCPUPM对应） |
|    AppleXcpmCfgLock     |  NO  |  若设备的CFG-Lock开启则设置为YES（与Clover的KernelPM对应）   |
|   AppleXcpmExtraMsrs    |  NO  | 主要在没有原生电源管理的CPU上启用，禁用奔腾和某些Xeon等不受支持的CPU所需的多个MSR访问（Haswell-E、Broadwell-E、Skylake-X需开启，与Clover的KernelXCPM对应） |
|   AppleXcpmForceBoost   |  NO  | 强制拉高睿频，建议在长期高负载的专业设备上使用，至强系列的处理器开启这个选项会受益 |
|    CustomSMBIOSGuid     |  NO  | 对UpdateSMBIOSMode自定义模式执行GUID修补，用于戴尔笔记本电脑（等同于Clover的DellSMBIOSPatch） |
|     DisbaleIOMapper     |  NO  |              需要绕过VT-d且BIOS中无法禁用时使用              |
|   DisableRtcChecksum    |  NO  | 越过两条rtc检查(0x58及0x59)，一般情况下卡RTC我们会使用`RTCMemoryFixup.kext`来防止 |
|  DummyPowerManagement   |  NO  | 禁用AppleIntelCpuPowerManagement原生电源管理，用于更好的替代NullCpuPowerManagement.kext |
|    ExternalDiskIcons    |  NO  | 硬盘图标补丁（macOS将内部硬盘视为外接硬盘时使用，对于Z87及以下版本的NVMe，只需通过DeviceProperties添加内置属性） |
|   IncreasePciBarSize    |  NO  | 将IOPCIFamily中32位PCI Bar的大小从1GB增加到4GB（在BIOS中启用Above4GDecoding是一种更加干净和安全的方法，某些X99板可能需要开启，这些主板通常会在IOPCIFamily上遇到内核崩溃） |
|    LapicKernelPanic     |  NO  | 禁用由AP核心lapic中断造成的内核崩溃，通常用于惠普电脑（等同于Clover的Kernel LAPIC） |
|     PanicNoKextDump     | YES  |               在发生内核崩溃时阻止输出Kext列表               |
| PowerTimeoutKernelPanic | YES  | 修复macOS Catalina中由于设备电源状态变化超时而导致的内核崩溃 |
|     ThirdPartyTrim      |  NO  | 为 SSD启用TRIM指令（NVMe SSD会自动被macOS加载，SATA SSD可以在终端执行sudo trimforce enable开启） |
|      XhciPortLimit      |  NO  |       解除十五端口限制补丁（不建议依赖，建议定制USB）        |

### Misc

#### BlessOverride

|       选项       |               内容               |                             说明                             |
| ---------------- | -------------------------------- | ------------------------------------------------------------ |
| Item 0（String） | \EFI\Microsoft\Boot\bootmgfw.efi | 用于覆盖Windows的bootmgfw.efi的位置，以便识别Windows引导项，OpenCore和Windows的引导文件在同一硬盘的同一ESP分区下时使用 |

#### Boot

##### 总览

|        选项        |     内容      |                             说明                             |
| ------------------ | ------------- | ------------------------------------------------------------ |
| ConsoleBehaviourOs | ForceGraphics |               修复OC引导界面（下文有详细说明）               |
| ConsoleBehaviourUi |   ForceText   |               修复OC引导界面（下文有详细说明）               |
|   HibernateMode    |     None      | 与系统内的休眠模式配合，引导进系统会还原休眠前的状态，会影响 SSD 寿命（None-关闭休眠支持，Auto-自动检测RTC和NVRAM模式，RTC-RTC模式） |
|   HideAuxiliary    |      YES      | 默认隐藏以下引导项，按空格键显示全部：macOS恢复、在自定义引导项时定义为Auxiliary的引导项、在 Tools中添加的操作系统（如Clean NVRAM） |
|      HideSelf      |      NO       |        在OpenCore的启动选择中隐藏自身EFI分区的启动项         |
| ConsoleAttributes  |       0       | 给引导选择界面添加特效，填入字体颜色和背景颜色的值的16进制之和 |
|  PickerAttributes  |       0       |      0x0004-简化主题图标下的文字，0x0008-使用老式的图标      |
| PickerAudioAssist  |      NO       |                    朗读屏幕上选择项的内容                    |
|  PollAppleHotKeys  |      NO       | 允许在引导过程中使用苹果原生快捷键（需要与Quirk中KeySupport=Yes或UsbKbDxe.efi结合使用, 具体体验取决于主板固件，可能会导致选择分区时卡住） |
|     Resolution     |   1920x1080   |               修复OC引导界面（下文有详细说明）               |
|     ShowPicker     |      YES      |             显示OpenCore的UI，用于查看可用引导项             |
|    TakeoffDelay    |       0       | 在启动前延迟n毫秒，提升键盘快捷键识别的正确率（n 的有效范围为5000-10000，32bit以内的正整数） |
|      Timeout       |       0       | 设置引导项等待时间（0为关闭倒计时，相当于Clover的-1，但不是跳过倒计时），设置为NO可以跳过倒计时 |
|     PickerMode     |    Builtin    | 使用OpenCore的默认GUI（External-使用其它GUI，Apple-使用原生GUI，黑苹果不支持） |

##### ConsoleAttributes

ConsoleAttributes的颜色代码含义如下。

| 颜色代码     | 指代颜色 |
| ------------ | -------- |
| **字体颜色** |          |
| 0x00         | 黑       |
| 0x01         | 蓝       |
| 0x02         | 绿       |
| 0x03         | 青       |
| 0x04         | 红       |
| 0x05         | 品红     |
| 0x06         | 棕       |
| 0x07         | 浅灰     |
| 0x08         | 深灰     |
| 0x09         | 浅蓝     |
| 0x0A         | 浅绿     |
| 0x0B         | 浅青     |
| 0x0C         | 浅红     |
| 0x0D         | 浅品红   |
| 0x0E         | 黄       |
| 0x0F         | 白       |
| **背景颜色** |          |
| 0x00         | 黑       |
| 0x10         | 蓝       |
| 0x20         | 绿       |
| 0x30         | 青       |
| 0x40         | 红       |
| 0x50         | 品红     |
| 0x60         | 棕       |
| 0x70         | 浅灰     |

#### Debug

##### 总览

一般无需修改。若需开启Debug功能，建议使用DEBUG或NOOPT版本。

|      选项       |    内容    |                             说明                             |
| --------------- | ---------- | ------------------------------------------------------------ |
| DisableWatchDog |     NO     |    若macOS在启动时卡在某些地方，可设置为YES以排除错误干扰    |
|  DisplayDelay   |     0      |                                                              |
|  DisplayLevel   | 2147483714 | `0`为隐藏所有Debug信息，`2147483714`为在屏幕上显示所有Debug信息 |
|     Target      |     19     | `0`为关闭日志记录，`3`为允许屏幕输出日志，`19`为允许屏幕输出UEFI变量日志，`65`为在ESP分区根目录生成日志文件但屏幕上不显示日志 |

对于某些选项的详细说明如下。

##### DisplayLevel

|    内容    |                 说明                 |
| ---------- | ------------------------------------ |
| 0x00000001 |              DEBUG_INIT              |
| 0x00000002 | DEBUG，NOOPT和RELEASE中的DEBUG_WARN  |
| 0x00000004 |              DEBUG_LOAD              |
| 0x00000008 |               DEBUG_FS               |
| 0x00000010 |              DEBUG_POOL              |
| 0x00000020 |              DEBUG_PAGE              |
| 0x00000040 |      DEBUG，NOOPT中的DEBUG_INFO      |
| 0x00000080 |            DEBUG_DISPATCH            |
| 0x00000100 |            DEBUG_VARIABLE            |
| 0x00000400 |               DEBUG_BM               |
| 0x00001000 |             DEBUG_BLKIO              |
| 0x00004000 |              DEBUG_NET               |
| 0x00010000 |              DEBUG_UNDI              |
| 0x00020000 |            DEBUG_LOADFILE            |
| 0x00080000 |             DEBUG_EVENT              |
| 0x00100000 |              DEBUG_GCD               |
| 0x00200000 |             DEBUG_CACHE              |
| 0x00400000 |    在自定义版本中的DEBUG_VERBOSE     |
| 0x80000000 | DEBUG，NOOPT和RELEASE中的DEBUG_ERROR |

一般开启0x00000002、0x00000040和0x80000000。`0x00000002` + `0x00000040` + `0x80000000` =` 0x80000042`，转换为十进制为`2147483714`，填入此值即可。

##### Target

| 内容 |             说明             |
| ---- | ---------------------------- |
| 0x01 |         启用日志记录         |
| 0x02 |         启用屏幕调试         |
| 0x04 |    启用日志记录到数据中心    |
| 0x08 |     启用串行端口日志记录     |
| 0x10 |     启用UEFI变量日志记录     |
| 0x20 | 启用非易失性UEFI变量日志记录 |
| 0x40 |        启用记录到文件        |

一般开启0x01、0x02、0x10和0x40。`0x01` + `0x02` + `0x10` + `0x40` = `0x53`，转换为十进制为`83`，填入此值即可。

#### Security

##### 总览

|        选项         |    内容    |                             说明                             |
| ------------------- | ---------- | ------------------------------------------------------------ |
|   AllowNvramReset   |    YES     |     允许在引导选择界面和快捷键Cmd+Opt+P+R按下时重置NVRAM     |
|   AllowSetDefault   |    YES     |         允许使用Ctrl+Enter和Ctrl+数字锁定默认启动项          |
|     AuthRestart     |     NO     |            允许重启FileVault2分区时不用再次输密码            |
|     BootProtect     | Bootstrap  | 保证opencore.efi的永久性，以免遭到其他操作系统对开机顺位的破坏（需保证RequestBootVarRouting开启） |
| ExposeSensitiveData |     3      | 用NVRAM则必须填3（2-原生NVRAM，11-在3的基础上添加主板OEM信息，HWMonitorSMC2和NVMeFix需要主板OEM信息才能正常工作） |
|        Vault        |  Optional  | OpenCore自身的加密和安全保护（Optional-不强制要求vault.plist和 vault.sig文件存在，Basic-强制要求 vault.plist存在，Secure-强制要求 vault.sig签名文件和vault.plist存在） |
|      HaltLevel      | 2147483648 |                                                              |
|  RequireSignature   |     NO     |                    黑苹果的Vault加密方式                     |
|    RequireVault     |     NO     |                      是否开启黑苹果加密                      |
|     ScanPolicy      |     0      |                 0表示允许扫描所有可用的硬盘                  |

##### ScanPolicy

对于ScanPolicy值，设为0可允许所有存在的源都启动，但通过简单的计算，可得到更大范围的灵活性与安全性。

计算ScanPolicy值时只需将所有十六进制值相加即可，全部累加后需要将此十六进制值添加到ScanPolicy（首先需要将其转换为十进制值，粘贴时Xcode会自动将其转换）。

| 位置                | 名称                          |
| ------------------- | ----------------------------- |
| 0x00000001 (bit 0)  | OC_SCAN_FILE_SYSTEM_LOCK      |
| 0x00000002 (bit 1)  | OC_SCAN_DEVICE_LOCK           |
| 0x00000100 (bit 8)  | OC_SCAN_ALLOW_FS_APFS         |
| 0x00000200 (bit 9)  | OC_SCAN_ALLOW_FS_HFS          |
| 0x00000400 (bit 10) | OC_SCAN_ALLOW_FS_ESP          |
| 0x00010000 (bit 16) | OC_SCAN_ALLOW_DEVICE_SATA     |
| 0x00020000 (bit 17) | OC_SCAN_ALLOW_DEVICE_SASEX    |
| 0x00040000 (bit 18) | OC_SCAN_ALLOW_DEVICE_SCSI     |
| 0x00080000 (bit 19) | OC_SCAN_ALLOW_DEVICE_NVME     |
| 0x00100000 (bit 20) | OC_SCAN_ALLOW_DEVICE_ATAPI    |
| 0x00200000 (bit 21) | OC_SCAN_ALLOW_DEVICE_USB      |
| 0x00400000 (bit 22) | OC_SCAN_ALLOW_DEVICE_FIREWIRE |
| 0x00800000 (bit 23) | OC_SCAN_ALLOW_DEVICE_SDCARD   |

默认值为 `0xF0103`(983,299)，是以下各项的组合。

```
OC_SCAN_FILE_SYSTEM_LOCK 
OC_SCAN_DEVICE_LOCK
OC_SCAN_ALLOW_FS_APFS
OC_SCAN_ALLOW_DEVICE_SATA 
OC_SCAN_ALLOW_DEVICE_SASEX 
OC_SCAN_ALLOW_DEVICE_SCSI 
OC_SCAN_ALLOW_DEVICE_NVME
```

建议添加OC_SCAN_ALLOW_DEVICE_USB，变为`0x00200000`，转换为十进制为`3080451`。

#### Tools

用于运行OC调试工具，例如验证CFG锁（VerifyMsrE2.efi）。

|   选项    |      内容       |            说明            |
| --------- | --------------- | -------------------------- |
| Arguments |        /        |                            |
|   Name    |   VerifyMsrE2   | OpenCore启动项中显示的名称 |
|  Comment  | Verify CFG Lock |       注释（可不填）       |
|  Enabled  |       YES       |         启用/禁用          |
|   Path    | VerifyMsrE2.efi |   Tools文件夹下的文件名    |

可在此处添加bootx64.efi以引导UEFI Shell（刷BIOS）、添加shellx64.efi以引导Grub Shell（改BIOS条目）等。

#### Entires

指定OpenCore无法自动找到的无规律引导路径。

| 选项      | 内容                                                         | 说明                                                         |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Arguments | /                                                            |                                                              |
| Auxiliary | NO                                                           | 隐藏引导项                                                   |
| Name      | CustomOS                                                     | OpenCore启动项中显示的名称                                   |
| Comment   | Not signed for security reasons                              | 注释（可不填）                                               |
| Enabled   | YES                                                          | 启用/禁用                                                    |
| Path      | PciRoot(0x0)/Pci(0x1,0x1)/Pci(0x0,0x0)/NVMe(0x1,11-22-33-44-55-66-77-88)/HD(1,GPT,00000000-0000-0000-0000-000000000000,0x800,0x64000)/EFI/BOOT/BOOTX64.EFI | 引导磁盘的PCI路径，可以通过 OpenCoreShell 的map命令或在Clover引导项找到 |

### NVRAM

#### Add

##### 4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14（Booter Path）

|          选项          |   内容   |                             说明                             |
| ---------------------- | -------- | ------------------------------------------------------------ |
|        UIScale         |    01    | OpenCore UI和引导第一阶段缩放（`01`为正常大小，等效于Clover中BooterConfig的0x28，`02`为HIDPI，等效于Clover中BooterConfig的0x2A） |
| DefaultBackgroundColor | 00000000 | 控制第一阶段苹果标志后面的背景颜色，格式为16进制颜色RGBA（黑色为00000000，灰色为BFBFBF00） |
|     EFILoginHiDPI      |    /     |                                                              |
|       flagstate        |    /     |                                                              |
|        UIScale         |    /     |                                                              |

##### 7C436110-AB2A-4BBB-A880-FE41995C9F82（SIP）

|       选项        |                     内容                      |                             说明                             |
| ----------------- | --------------------------------------------- | ------------------------------------------------------------ |
|     boot-args     | -v debug=0x100 nv_disable=1 slide=1 vsmcgen=1 |     引导参数（vsmcgen=1为MacOS 11专用，用于暴露SMC部件）     |
|     bootercfg     |                     log=0                     |                  自行添加，关闭开机时的代码                  |
| csr-active-config |                   E7030000                    | SIP 设置（`00000000`-SIP完全开启，等效于CsrActiveConfig的0x0，`30000000`-允许未签名的Kext加载并允许写入受保护的文件系统路径，等效于CsrActiveConfig的0x3，`67000000`-SIP基本关闭，等效于CsrActiveConfig的0x6，`E7030000`-SIP完全关闭，等效于CsrActiveConfig的0x3E7） |
|     nvda_drv      |                     删除                      |        设为`31`时启用NVIDIA WebDrivers，无N卡删除此项        |
|   prev-lang:kbd   |            7A682D48616E733A323532             |            将默认语言设置为简体中文（留空为英文）            |
| SystemAudioVolume |                     0x46                      |                          0x80为静音                          |

#### Block

强制重写NVRAM变量，由于Add不会覆盖NVRAM中已经存在的值，boot-args需要利用此项来刷新。

#### LegacyEnable

允许从NVRAM.plist中读取NVRAM变量。没有原生NVRAM的设备或macOS下硬件NVRAM工作不正常的设备设置为`YES`，工作正常的设备设置为`NO`。

#### LegecyOverwrite

允许 `nvram.plist` 中的变量覆盖现有NVRAM的变量，一般选择NO。

#### LegacySchema

用于分配NVRAM变量，与LegacyEnable配合使用。打开7C436110-AB2A-4BBB-A880-FE41995C9F82这一栏，添加以下两个item。

| 名称    | 类型   | 内容                 |
| ------- | ------ | -------------------- |
| item 11 | String | efi-boot-device      |
| item 12 | String | efi-boot-device-data |

#### WriteFlash

在某些系统运行时不能将易失性变量转换为非易失性变量的UEFI固件上可开启此项。一般选择NO。

### PlatformInfo

#### 总览

|        选项        |                 内容                 |                             说明                             |
| ------------------ | ------------------------------------ | ------------------------------------------------------------ |
|        Auto        |                 YES                  |                       自动注入机型信息                       |
|        MLB         |          C029314044N0000CB           |                     MLB（从Clover复制）                      |
|        ROM         |             3631A456E9C3             |                     ROM（从Clover复制）                      |
|   AdviseWindows    |                 YES                  |    允许重启到ESP分区不是磁盘中第一个分区的磁盘中的Windows    |
|    SpoofVendor     |                 YES                  |              仿冒制造商为Acidanthera以避免冲突               |
| SystemProductName  |            MacBookPro15,3            |                   系统型号（从Clover复制）                   |
| SystemSerialNumber |             C02Z5TZ6LVCG             |                  系统序列号（从Clover复制）                  |
|     SystemUUID     | 0CC4E03F-2998-4685-A433-B5ACAB56E9C3 |                   硬件UUID（从Clover复制）                   |
|   UpdateDataHub    |                 YES                  |                         更新DataHub                          |
|    UpdateNVRAM     |                 YES                  |                          更新NVRAM                           |
|    UpdateSMBIOS    |                 YES                  | 更新SMBIOS（Dell笔记本可能需要改为Custom，相当于Clover的DellSMBIOSPatch的一部分） |
|  UpdateSMBIOSMode  |                Create                | 用新分配的EfiReservedMemoryType替换原有的表（戴尔笔记本需要使用Custom并开启CustomSMBIOSGuid） |

#### SystemUUID

应使用主板UUID作为SystemUUID，以免破坏Windows的激活环境。一般主板的UUID可以在BIOS中查看，如果BIOS中看不到，可以通过传统方式启动Windows（不能是OC引导），在命令行中查看。打开cmd，输入以下命令即可。

```
wmic
csproduct list full
```

### UEFI

#### ConnectDrivers

强制加载.efi驱动程序，更改为NO将自动连接UEFI驱动程序以获得更快的启动速度。但并非所有驱动程序都可以自行连接，某些文件系统驱动程序可能无法加载，因此此处填`YES`。

#### Drivers

添加efi驱动的名称。

#### Audio

这里的声卡设置只与 UEFI 环境相关，不影响系统内音频。如果要让Duang和VoiceOver等其它音频功能工作，需要将OCBinaryData中的语音资源包放置于EFI分区的EFI/OC/Resources/Audio下，同时AudioDxe需要安装在Driver文件夹中。

| 选项            | 内容                | 说明                                                         |
| --------------- | ------------------- | ------------------------------------------------------------ |
| AudioSupport    | YES                 | 启用音频支持（不支持DP类的数字音频）                         |
| AudioDevice     | PciRoot(0x0)/Pci... | 声卡设备的PCI地址                                            |
| AudioCodec      | 0                   | 声卡的Codec地址，可以在IORegistryExplorer的HDEF-IOHDACodecDevice-IOHDACodecAddress中获得（一般是0x0） |
| AudioOut        |                     | 指定的输出设备，从0到声卡总输出数每个都试一遍，声卡的总输出数可以在Debug 版本的OpenCore日志中获得（n Outputs） |
| MinimumVolume   |                     | 输出最小音量，有效值为0-100（如果VolumeAmplifier公式计算出的最小音量小于这里给出的值，Duang就不会被播放） |
| PlayChime       | YES                 | 播放Duang                                                    |
| VolumeAmplifier | 1000                | 音量在原有基础上放的百分比，有效范围0-1000                   |

#### Input

|        选项        | 内容  |                             说明                             |
| ------------------ | ----- | ------------------------------------------------------------ |
| KeyForgetThreshold |   5   |          按住按键后每个键之间的时间间隔（单位毫秒）          |
|  KeyMergeTheshold  |   2   |             按住按键被重置的时间间隔（单位毫秒）             |
|     KeySupport     |  YES  |     开启OC的内置键盘支持（若引导界面无法选择分区则禁用）     |
|   KeySupportMode   | Auto  | 键值转换协议模式（`V1`-UEFI旧版输入协议，`V2`-UEFI新输入协议，`AMI`-APTIO输入协议） |
|      KeySwap       |  NO   |                    交换Command和Option键                     |
|   PointerSupport   |  NO   |          修复UEFI选择器协议（华硕z87/z97需要开启）           |
| PointerSupportMode |   /   |            指定OEM协议，当前仅支持Z87和Z97 ASUS板            |
|  TimerResolution   | 60000 | 固件时钟刷新的频率（单位100纳秒），普通电脑填`50000`，华硕主板填`60000` |

#### Output

| 选项                    | 内容            | 说明                                                         |
| ----------------------- | --------------- | ------------------------------------------------------------ |
| TextRenderer            | BuiltinGraphics | 文字渲染模式（`BuiltinGraphics`-使用OpenCore内置文字渲染的图形模式，并同时启用OpenCore自带的控制台管理，支持 HIDPI 和全屏范围显示，`SystemGraphics`-使用主板固件自带文字渲染的图形模式，并同时启用OpenCore自带的控制台管理，`SystemText`-使用主板固件自带文字渲染的文字模式，并同时启用OpenCore自带的控制台管理，`SystemGeneric`-使用主板固件自带文字渲染的文字模式和自带的控制台管理） |
| ConsoleMode             | /               | TextRenderer使用Builtin时此项设置不生效                      |
| Resolution              | Max             | Max将会尝试开启最大分辨率，也可填写`宽x高@Bpp`（如3840x2160@32），其中Bpp为显示位深，留空将不会改变UEFIe的默认分辨率（在固件没有GOP时该选项失效） |
| ClearScreenOnModeSwitch | NO              | 切换到文本模式之前用黑色填充整个屏幕（从图形模式切换到文本模式时，某些固件仅清除屏幕的一部分，导致屏幕上残留之前绘制的图片，此情况可打开该选项） |
| DirectGopRendering      | NO              | 直接使用OpenCore内置的GOP渲染控制台，主要用于提升或修复MacPro5,1的渲染问题（除非开启后能明显感觉到有改进，否则不要开启） |
| IgnoreTextInGraphics    | NO              | 修复不用-v开机时日志覆盖苹果标志输出的问题                   |
| ReplaceTabWithSpace     | NO              | 某些设备在UEFI Shell中编辑文件使用Tab键出问题时启用（只作用于TextRenderer: System...的文字渲染） |
| ProvideConsoleGop       | YES             | 选择启动项之后不出现macOS启动Verbose请启用，开启此选项能最大可能保证OpenCore UI和苹果标志以正确分辨率显示（macOS引导加载程序要GOP即图形输出协议存在于控制台句柄上大部分的笔记本都不提供GOP，台式机的独立显卡可以单独刷入GOP） |
| ReconnectOnResChange    | NO              | 有些固件在GOP分辨率改变后要求重新连接控制器才能输出文本，开启这个选项会导致UEFI Shell中启动OpenCore时直接黑屏，尽量避免开启 |
| SanitiseClearScreen     | YES             | 修复OpenCore在高分屏中以1024x768显示的问题（要同时开启带有OpenCore自带的控制台管理的TextRenderer并将 ConsoleMode的内容留空） |
| DirectGopRendering      | NO              | 使用内置显卡直接渲染开机画面                                 |
| UgaPassThrough          | NO              | 通过UGA代替无法使用GOP的主板，一般带UEFI的主板以及显卡请选择NO |

#### Protocols

| 选项                    | 内容 | 说明                                                         |
| ----------------------- | ---- | ------------------------------------------------------------ |
| AppleAudio              | YES  | 安装Apple Audio协议以在OpenCore中使用 VoiceOver，目前该协议支持在macOS 10.13及以上播放音频，老系统使用的AppleHDA协议暂未支持 |
| AppleBootPolicy         | NO   | 确保虚拟机/旧白苹果上兼容APFS                                |
| AppleDebugLog           | NO   | 重新安装苹果错误日志界面                                     |
| AppleEvent              | NO   | 虚拟机并具有Vault（文件保险箱）的mac需要开启                 |
| AppleFramebufferInfo    | NO   | 虚拟机专用                                                   |
| AppleImageConversion    | NO   | 重建Apple图标（重新安装Apple Image Conservation协议）        |
| AppleKeyMap             | NO   | 重建苹果功能键（重新安装Apple Key Map协议）                  |
| AppleRtcRam             | NO   | 重装AppleRTC协议                                             |
| AppleSmcIO              | YES  | 重新安装Apple SMC I/O协议（VirtualSmc.efi已经被替代为此选项） |
| AppleUserInterfaceTheme | NO   | 重新安装Apple User Interface Theme协议                       |
| ConsoleControl          | YES  | 用内置版本替换控制台控制协议                                 |
| DataHub                 | NO   | 重新安装Data Hub协议                                         |
| DeviceProperties        | NO   | 确保在VM/旧白苹果上完全兼容                                  |
| FirmwareVolume          | NO   | 修复Filevault的UI问题，提高FileVault的兼容性                 |
| HashServices            | NO   | 修复运行FileVault时鼠标光标大小不正确的问题                  |
| OSInfo                  | NO   | 为主板或者其它程序接收来自macOS引导工具的消息                |
| UnicodeCollation        | NO   | 修复主板UEFI Shell的兼容性（通常用于IvyBridge或更旧的设备）  |

#### Quirks

|          选项          | 内容 |                             说明                             |
| ---------------------- | ---- | ------------------------------------------------------------ |
|  DeduplicateBootOrder  |  NO  | 当`RequestBootVarRouting`选项为开启时，一些其他的操作系统（如windows）可能在某些主板（如华硕）中会找不到自己的引导而重新创建，最终导致黑果在没有清理NVRAM的情况下是无法进入系统时可开启 |
| ExitBootServicesDelay  |  0   | 主板退出时间（单位微秒），新主板填0，旧主板（如Z87pro）填3000000-5000000 |
| IgnoreInvalidFlexRatio | YES  |          BIOS中无法禁用MSR_FLEX_RATIO(0x194)时开启           |
|  ReleaseUsbOwnership   |  NO  | 从固件驱动程序中释放USB控制器所属权，相当于Clover的FixOwnership（大部分主板都有自动释放USB所有权的功能） |
| RequestBootVarFallback |  NO  | 一些固件会主动扫描系统启动盘的位置而阻止OC扫描，遇到此问题时可开启 |
| RequestBootVarRouting  | YES  | 从EFI_GLOBAL_VARIABLE_GUID中为OC_VENDOR_VARIABLE_GUID请求redirectBoot前缀变量，启用此项以便能够在与macOS引导项设计上不兼容的固件中可靠地使用启动磁盘设置 |
|     TscSyncTimeout     |  0   | 帮助X99/X299主板开启全核同步功能，但睡眠唤醒后失效，故应使用TSCAdjustReset.kext来做全核同步 |
|    UnblockFsConnect    | YES  |      惠普笔记本在OpenCore引导界面没有引导项时设置为YES       |

#### APFS

| 选项             | 内容 | 说明                                                         |
| ---------------- | ---- | ------------------------------------------------------------ |
| EnableJumpstart  | YES  | 从APFS容器中加载内置APFS驱动（确保在Scanpolicy中放开APFS格式） |
| GlobalConnect    | NO   | 一些主板需要选择YES才能完全加载APFS（如HP笔记本）            |
| HideVerbose      | YES  | 隐藏啰嗦模式                                                 |
| JumpstartHotPlug | YES  | 加载APFS格式的热插设备                                       |
| MinDate          | 0    | 加载最低发行的APFS格式（想加载旧的发行日期的APFS格式硬盘则填-1） |
| MinVersion       | 0    | 加载最低版本的APFS格式（0代表从HIGH SIERRA开始加载，-1代表所有版本） |

#### ReservedMemory Properties

用于保留内存。一些硬件会在把硬件EFI写进内存过程中占用必要的UEFI运行空间，所以可以通过此项预留内存保证UEFI的运行。一般情况下无需处理。

## 解决Clover和OpenCore的冲突

### 删除Clover相关程序

打开终端并输入以下命令。

```
// 删除Clover设置面板
sudo rm -rf /Library/PreferencePanes/Clover.prefPane

// 删除ESP分区下的NVRAM.plist
rm -rf /Volumes/（你的 ESP 分区）/NVRAM.plist

// 删除RC脚本
rm -rf "/etc/rc.clover.lib"
rm -rf "/etc/rc.boot.d/10.save_and_rotate_boot_log.local"
rm -rf "/etc/rc.boot.d/20.mount_ESP.local"
rm -rf "/etc/rc.boot.d/70.disable_sleep_proxy_client.local.disabled"
rm -rf "/etc/rc.boot.d/80.save_NVRAM_plist.local"
rm -rf "/etc/rc.shutdown.local"
rm -rf "/etc/rc.boot.d"
rm -rf "/etc/rc.shutdown.d"

// 删除Clover新开发的NVRAM守护程序CloverDaemonNew
launchctl unload '/Library/LaunchDaemons/com.slice.CloverDaemonNew.plist'
rm -rf '/Library/LaunchDaemons/com.slice.CloverDaemonNew.plist'
rm -rf '/Library/Application Support/Clover/CloverDaemonNew'
rm -rf '/Library/Application Support/Clover/CloverLogOut'
rm -rf '/Library/Application Support/Clover/CloverWrapper.sh'

// 获取logoutHook路径
sudo defaults read com.apple.loginwindow LogoutHook

// 删除LogoutHook.command
sudo rm -rf $(sudo defaults read com.apple.loginwindow LogoutHook)

// 清空LogoutHook的触发设置
sudo defaults delete com.apple.loginwindow LogoutHook
```

### 重置NVRAM

在Clover和OpenCore之间切换时，建议重置NVRAM。

#### 通过Clover清除

删除Clover的drivers64UEFI目录下的EmuVariableUefi.efi，重启进入Clover引导项选择界面，按下`F11（Fn+F11）`直到设备自动重启。

#### 通过OpenCore重置

在OpenCore引导界面按空格显示隐藏条目，选择`Reset NVRAM`。此法将会丢失BIOS中的自定引导项。

## 常见问题

### OpenCore的启动文件

在添加引导项时，OpenCore必须从EFI/BOOT/BOOTx64.efi启动而不是从 EFI/OC/OpenCore.efi启动。

### 判断NVRAM是否工作

删除引导工具内的`VariableRuntimeDxe.efi`、`EmuVariableRuntimeDxe.efi`和`EmuVariableUefi.efi`，删除NVRAM导出脚本`LogoutHook`和`RC Script`，删除EFI分区根目录下的`nvram.plist`，重启后输入以下命令。

```
sudo nvram myvar=test
```

再次重启在终端输入以下命令，如果终端成功输出了test，则说明主板在macOS下NVRAM正常工作，反之为不正常。

```
nvram -p | grep -i myvar
```

### 修复OC引导界面显示问题

在config.plist中修改以下配置。

```
// UEFI->Protocols
ConsoleControl=YES

// UEFI->Quirks
ProvideConsoleGop=YES
IgnoreTextInGraphics=YES
SanitiseClearScreen=YES

// Misc->Boot
ConsoleMode=留空（有些设置为Max）
Resolution设置为显示器的正常分辨率（如4k为3840x2160）
4代及以下
onsoleBehaviourOs=Graphics
ConsoleBehaviourUi=ForceText
5代+
ConsoleBehaviourOs=ForceGraphics
ConsoleBehaviourUi=ForceText
（有的固件留空或者设为Graphics/Text效果更好）
```

### 启动时的常见错误

```
https://www.cmlnt.com/?p=239
```

## OpenCore支持的UEFI驱动

| 名称               | 解释                                                         |
| ------------------ | ------------------------------------------------------------ |
| CrScreenshotDxe    | 增加OpenCore UI内截屏功能，快捷键为左Ctrl+左Alt+F12          |
| OpenRuntime        | 内存寻址补丁内存寻址补丁                                     |
| OpenUsbKbDxe       | 键盘组合键的使用                                             |
| OpenCanopy         | 加载第三方主题                                               |
| FwRuntimeServices  | 通过支持只读/只写NVRAM变量提高OpenCore和Lilu的安全性（Clover不适用） |
| HFSPlus/VBoxHfs    | 查看HFS卷                                                    |
| HiiDatabase        | 给Ivy Bridge（3 代酷睿）或更老代主板上支持UEFI字体渲染，UEFI Shell中文字渲染异常时使用 |
| NvmExpressDxe      | 对于Haswell及更早的固件，如果安装了NVMe SSD就需要使用        |
| MemoryAllocation   | 为Z390/X99等主板预留第一组512MB内存，帮助引导工具注入内核以及内核缓存至第一组512MB内存，需要配合FwRuntimeServices和引导标识符`slide=1`（若要使用hibernation功能请勿使用） |
| AppleUsbKbDxe      | 添加了对AppleKeyMapAggregator协议支持的USB键盘驱动，是`AppleGenericInput`的等效驱动 |
| UsbMouseDxe        | USB鼠标驱动程序                                              |
| AudioDxe           | OpenCore用于在UEFI环境播放音频的驱动                         |
| VBoxExt2           | EXT2文件系统驱动，用于启动LinuxEFI系统                       |
| VBoxExt4           | EXT4文件系统驱动，用于启动LinuxEFI系统                       |
| NTFS               | NTFS文件系统驱动程序                                         |
| VBoxIso9600        | ISO9600文件系统驱动                                          |
| GrubUDF            | 加载UDF文件系统                                              |
| GrubNTFS           | 加载NTFS文件系统                                             |
| GrubISO9660        | 加载ISO9660文件系统                                          |
| GrubEXFAT          | 加载NTFS文件系统                                             |
| TbtForcePower      | 启用ThunderBolt热插拔                                        |
| UsbKbDxe           | 当内置驱动程序不适用于固件时，用于Apple Hotkeys和FileVault支持 |
| VariableRuntimeDxe | EDK II 的NVRAM驱动，OpenCore中用于模拟NVRAM，需要配合`FwRuntimeServices`(.efi)和`DisableVariableWrite`(Quirk) |
| VirtualSmc         | 适当的FileVault支持                                          |
| XhciDxe            | XHCI USB控制器支持驱动程序                                   |

# ACPI表

包括SSDT/DSDT等。

## 简要说明

`DSDT`为电脑硬件表，描述计算机设备信息，系统根据DSDT驱动相应的硬件。

`SSDT`为用于修改DSDT的热补丁，可以在不更改DSDT源文件的情况下完成错误修复，并实现相应功能。

## SSDT作用

|       名称        |                             解释                             |
| ----------------- | ------------------------------------------------------------ |
|    SSDT-Config    |                       SSDT控制入口文件                       |
|     SSDT-LPC      |                    正确识别LPC总线到系统                     |
|    SSDT-SMBUS     |                     正确识别SMBus到系统                      |
|     SSDT-DMAC     |                    将DMA控制器公开给系统                     |
|     SSDT-MCHC     |                   将内存控制器暴露在系统中                   |
|     SSDT-IGPU     |    将属性添加到集成显卡设备，可以实现HDMI音频以及正确显示    |
| SSDT-Disable_DGPU |          在启动时关闭独立显卡，因为Mac OS无法使用它          |
|     SSDT-ALS0     |        添加假环境光传感器以在重新启动之间保存背光信息        |
|     SSDT-BRT6     | 将键盘的ACPI管理亮度键映射到VoodooPS2Controller.kext的PS2代码 |
|   SSDT-Dell_FN    | 将键盘的ACPI管理亮度键映射到VoodooPS2Controller.kext的PS2代码 （部分DELL机型） |
|     SSDT-PNLF     | 显示器亮度控制，为IntelBacklight.kext或AppleBacklight.kext + AppleBacklightInjector.kext添加PNLF设备 |
|     SSDT-HDAU     |     自动注入HDAU属性（适用于Haswell和Broadwell平台）      |
|     SSDT-HDEF     |             自动注入HDEF属性（声卡layoutid注入点）              |
|     SSDT-GPRW     |               通过挂接GPRW或UPRW来解决即时唤醒               |
|   SSDT-LANC_PRW   |                  通过挂接GPRW来解决即时唤醒                  |
|     SSDT-UPRW     |               通过挂接GPRW或UPRW来解决即时唤醒               |
|     SSDT-IMEI     |              添加IMEI（6系列HD4000，7系HD3000）              |
|    SSDT-PTSWAK    | 在睡眠之前对离散GPU进行驱动，并在唤醒后将其关闭，以确保系统能够跟踪它 |
|    SSDT-Debug     | 用于ACPIDebug，该文件是通过将「添加DSDT调试方法」应用于空SSDT创建的，使用「添加SSDT调试外部声明」从其他方式访问这些方法hotpatch SSDT甚至打补丁的OEM ACPI文件 |
|     SSDT-ESEL     |           关闭ESEL，在DSDT里原生ESEL被重命名为ESEX           |
|     SSDT-XWAK     |          关闭XWAK，在DSDT里原生的XWAK被重命名为ZWAK          |
| SSDT-PluginType1  | 在_PR.CPU0上插入plugin-type = 1，可能是所有支持XCPM的SMBIOS只需要这种插件类型的注入 |
|     SSDT-SATA     |                   修复某些不支持的SATA设备                   |
|     SSDT-XHC      |                       自动注入XHC属性                        |
|     SSDT-XOSI     |                         操作系统补丁                         |
|     SSDT-AWAC     | 修复 300 系列主板（B360/B365/H310/H370/Z390等）系列主板的RTC设备问题（部分微星的主板/清华同方九代模具需要应用这个补丁，否则会卡住而无法进入系统，建议八代 CoffeeLake以上的用户都添加） |
|   SSDT-EC-USBX    | 添加了一个仿冒的EC设备并完善了USB的供电（若主板的 EmbeddedController原生ACPI 命名就是 EC，则不需要该补丁） |
|     SSDT-PMC      |                         与NVRAM相关                          |
|     SSDT-PLUG     | 启用原生电源管理（Skylake六代以上的机型在Clover中可以直接勾选注入插件类型，自动注入`plugin-type=1`参数实现同等效果） |

## ACPI表的提取

### 利用软件

macOS下打开`MaciASL`，默认打开本机DSDT。

### 利用UefiTableExtract

从固件的UEFI BIOS文件中提取ACPI表。打开以下链接并下载压缩包，解压后运行install.command，再运行UefiTableExtract即可。

```
https://github.com/Saman-VDR/uefiTableExtract
```

若遇到诸如`Namespace lookup failure, AE_ALREADY_EXISTS`的错误，可尝试诸如以下命令以修复。

```
cd AML
mv SSDT-trouble.aml SSDT-trouble.bin && iasl -d SSDT-trouble.bin
iasl -da DSDT.aml SSDT*.aml
```

### 利用Clover

在Clover主页面按下`F4`即可。所有ACPI表将被提取到`EFI/Clover/ACPI/origin`目录下。这样提取出来的ACPI最纯净，推荐使用。

### 利用acpidump.efi

将该工具添加到OpenCore的引导项中，调用后会生成DSDT.dat，修改后缀名为DSDT.aml即可。有关OpenCore及工具的添加在后文有述。

若OpenCore无法打开此工具，则可在OpenCore界面进入EFI Shell并输入以下命令以手动运行。

```
fs0: // 替代为自己的EFI分区标识
ls
cd EFI\OC\Tools
acpidump.efi -b -n DSDT -z
```

## 反编译

尽管可以直接从MaciASL打开aml文件，但这样的操作实际上是对单个文件进行反汇编。由于DSDT和SSDT间可能存在复杂的应用关系，独立反汇编将可能导致错误，因此建议先将aml反编译为dsl文件。

打开以下链接下载反编译器iasl。

```
https://bitbucket.org/RehabMan/acpica/downloads/
```

下载完成后将iASL和要反汇编的文件放到同一个文件夹，然后输入以下命令。

```
sudo cp iasl /usr/bin
// 对于较旧的ACPI表
iasl -da -dl DSDT.aml SSDT*.aml
// 对于较新的ACPi表（Skylake及更高）
iasl -dl DSDT.aml SSDT*.aml
```

注意不要直接打开aml文件然后进行编译，应先反汇编为dsl后再进行。

## 编译

```
// 混合编译DSDT
iasl -l DSDT.dsl
```

## 制作hotpatch

### 电池热补丁

请基于在修改DSDT后设备能够正常显示电量的前提下制作此热补丁。电池热补丁的基本原理是，新建一个OperationRegion块，在这个新块里动态注入所有的拆分字节数据，同时对原有的未拆分字节保留空位，从而在保证位移量正确的情况下正确注入并引用新的数据。

#### 确认修改位置

打开IORegistryExplorer，查找`BAT`，记录电池设备名称，本机为`SB.PCI0.LPCB.EC0.BAT0`。对修补DSDT的补丁进行分析，内容如下。

```
// 字段拆分
into device label EC0 code_regex B0C3,\s+16, replace_matched begin C3HG,8,C3HF,8, end;
into device label EC0 code_regex B0SN,\s+16, replace_matched begin BSVN,8,BSVM,8, end;
into device label EC0 code_regex B1SN,\s+16 replace_matched begin SBUY,8,SBUP,8 end;

// 替换调用方法
into method label _BIX code_regex \(B0C3, replaceall_matched begin (B1B2(C3HG,C3HF), end;
into method label BIFA code_regex \(B0SN, replaceall_matched begin (B1B2(BSVN,BSVM), end;
into method label BIFA code_regex \(B1SN, replaceall_matched begin (B1B2(SBUY,SBUP), end;

// 数据处理方法
into method label B1B2 remove_entry;
into definitionblock code_regex . insert
begin
Method (B1B2, 2, NotSerialized)\n
{\n
Return(Or(Arg0, ShiftLeft(Arg1, 8)))\n
}\n
end;
```

可以得出，实际修改的字节数据如下。

```
B0C3->B1B2（C3HG,C3HF）
B0SN->B1B2（BSVN,BSVM）
B1SN->B1B2（SBUY,SBUP）
```

故需要拆解的字节有B0C3、B0SN、B1SN，需要添加的额外方法（Method）为B1B2。比对原DSDT和已修改好的DSDT，查找调用到这些变化过的字段的Method位置，以及需要添加的Method出现位置。本机调用到B0C3、B0SN、B1SN的Method为BIFA和_BIX，需添加的额外方法B1B2在原DSDT中不存在，在修改过的DSDT中位于根目录。由此，所有需要修改的地方已完全确定。

#### 数据插入

打开MaciASL，新建文件并插入以下代码，使其声明为热补丁文件，并保存为以`SSDT-`开头的dsl文件。

```
// 此处的xxxx用前面找到的电池设备名（此处为BAT0）替换
DefinitionBlock ("", "SSDT", 2, "hack", "xxxx", 0x00000000)
{

}
```

打开原DSDT并搜索`EmbeddedControl`，复制整个OperationRegion到这个热补丁内，注意要把整个OperationRegion复制过来（从`OperationRegion`行始，到第一个`}`止）。然后把调用到变化字段的Method和所有额外添加的Method复制到OperationRegion后。对于本机而言，需复制`OperationRegion段`、`Method (BIFA`、`Method (_BIX`、`Method (B1B2`，故完成后代码结构应如下所示。注意，在同一个设备下的Method需放到一起。

```
OperationRegion (ECOR, EmbeddedControl, Zero, 0xFF)
Field (ECOR, ByteAcc, Lock, Preserve)
{
    Offset (0x04), 
    ......
}

Method (BIFA, 0, NotSerialized)
{
		......
}

Method (_BIX, 0, NotSerialized)
{
		......
}

Method (B1B2, 2, NotSerialized)
{
    Return (Or (Arg0, ShiftLeft (Arg1, 0x08)))
}
```

#### 添加位置代码

在代码块前需添加它们的位置代码，以使BIOS修补时有迹可循。在代码块的前后分别用以下代码框住即可，注意xxxx为位置，需要用绝对路径。

```
Scope (xxxxx)
{

}
```

以本机为例，本机的OperationRegion和BIFA方法均在`_SB.PCI0.LPCB.EC0`下，而\_BIX方法位于`_SB.PCI0.LPCB.EC0`的`BAT0`设备下。因此将OperationRegion和BIFA用`Scope (_SB.PCI0.LPCB.EC0)`框住，而\_BIX方法则用`Scope (_SB.PCI0.LPCB.EC0.BAT0)`框住，如下。注意，B1B2和B1B4方法放置于根目录，故无需位置代码。

```
DefinitionBlock ("", "SSDT", 2, "hack", "BAT0", 0x00000000)
{

// OperationRegion和BIFA
    Scope (_SB.PCI0.LPCB.EC0)
    {
        OperationRegion (ECOR, EmbeddedControl, Zero, 0xFF)
        Field (ECOR, ByteAcc, Lock, Preserve)
        {
            ......
        }

        Method (BIFA, 0, NotSerialized)
        {
            ......
        }
    }


// _BIX
    Scope (_SB.PCI0.LPCB.EC0.BAT0)
    {
        Method (_BIX, 0, NotSerialized)
        {
            ......
        }
    }


// B1B2
    Method (B1B2, 2, NotSerialized)
    {
        Return (Or (Arg0, ShiftLeft (Arg1, 0x08)))
    }
}
```

#### 添加外部引用

完成以上步骤后进行编译，会发现很多`对象不存在`的错误，这是由于未添加外部引用，故只需添加外部引用即可。

首先添加设备引用。以上面代码为例，设备`_SB.PCI0.LPCB.EC0`与`_SB.PCI0.LPCB.EC0.BAT0`被引用，故需用以下语句进行声明。

```
// xxxx是设备地址
External (xxxx, DeviceObj)
```

该部分语句需添加到DefinitionBlock的下面，故代码如下，其中`DeviceObj`表示这是一个ACPI设备。再次编译，有关设备的报错均已消失。

```
DefinitionBlock ("", "SSDT", 2, "hack", "BAT0", 0x00000000)
{
	External (_SB.PCI0.LPCB.EC0, DeviceObj)
	External (_SB.PCI0.LPCB.EC0.BAT0, DeviceObj)
	Scope (_SB.PCI0.LPCB.EC0)
	{
		......
```

然后根据报错，继续在DSDT里搜索其他的报错代码。如对于BIXT，它是BAT0设备下的一个字段，在DSDT查询到的第一个结果是`Name (BIXT`，因此用以下代码进行外部引用。

```
External (_SB.PCI0.LPCB.EC0.BAT0.BIXT, IntObj)
```

注意，在DSDT中搜索到的第一个结果即为其字段定义。字段定义与字段对象的对应关系如下表。

|              字段定义              |   字段对象   |
| ---------------------------------- | ------------ |
|         Name (OOXX, Zero)          |    IntObj    |
|            Field (GNVS,            | FieldUnitObj |
|            Mutex (OOXX,            |   MutexObj   |
| Name (OOXX, Buffer (0x07){......}) |    PkgObj    |
|           Device (OOXX)            |  DeviceObj   |
|    Method (OOXX, 1, Serialized)    |  MethodObj   |

当热补丁编译不报错时，外部引用完成。

#### 字节处理

首先需要把`OperationRegion`和`Field`里的字段重命名。以本机为例，由ECOR重命名为ECAG，如下。

```
OperationRegion (ECAG, EmbeddedControl, Zero, 0xFF)
Field (ECAG, ByteAcc, Lock, Preserve)
```

然后要使字段有正确的偏移量，可把Field里所有不拆分的字节数据段名字去掉，只保留空位，如`ACST,  1`，清空变量名后变为`,  1,`。示例如下。

```
Offset (0x93),
TH00,   8,
TH01,   8,
TH10,   8,
TH11,   8,
Offset (0xBE),
,   16,
,   16,
,   16,
XC30,   8,
XC31,   8,
```

或也可采用以下注释的形式。

```
Offset (0x93),
TH00,   8,
TH01,   8,
TH10,   8,
TH11,   8,
Offset (0xBE),
/*B0TM*/,   16,
/*B0C1*/,   16,
/*B0C2*/,   16,
XC30,   8,
XC31,   8,
```

或也可手动计算偏移量，如下。

```
Offset (0x93),
TH00,   8,
TH01,   8,
TH10,   8,
TH11,   8,
Offset (0xC4), // 原本为0xBE，加上三个16字节的偏移量后变为0xC4
XC30,   8,
XC31,   8,
```

修改完成后，将之前的电量补丁Patch使其生效，然后编译并保存。

#### 添加改名补丁

将热补丁中从原始DSDT复制过来的Method名全部进行修改，如热补丁中有从DSDT复制过来的代码`Method (BIFA, 0, NotSerialized)`，则BIFA需要更名。此处以更名为BIGA为例。

##### 计算十六进制数

打开Hackintool并切换到计算器选项卡，在ASCII码一栏输入原来的字段名（四个字母），在16进制值一栏会显示对应的八个数字。同理得到更改后字段名的16进制数。

也可以打开终端，利用以下命令得到对应的十六进制数。

```
echo -n BIFA | xxd
echo -n BIGA | xxd
```

##### 添加ACPI补丁

打开Clover的config.plist，添加以下更名规则即可。

```
Comment: change BIFA to BIGA
Find: 42494641
Replace: 42494741
```

注意，若所需要更改的方法名位于电池设备（即BAT0）内，则在`Tgtbridge`栏还应加入该电池设备名的16进制数，如下。Tgtbridge的作用为寻找Tgtbridge值对应的设备，并改变这个设备下的字段名。

```
Comment: change _BIX to XBIX in BAT0
Find: 5F424958
Replace: 58424958
Tgtbridge: 42415430
```

但如果添加Tgtbridge后电池补丁反而失效，则将Tgtbridge值删除。对本机而言，添加Tgtbridge值后反而不起作用。

### 触控板热补丁

#### 【额外了解】

```
https://blog.gzxiaobai.cn/2020/05/17/%E7%94%A8%E7%83%AD%E8%A1%A5%E4%B8%81%E4%BF%AE%E7%90%86%E4%BD%A0%E7%9A%84%E8%A7%A6%E6%91%B8%E6%9D%BF%EF%BC%88TouchPad-Hotpatch%EF%BC%89/
http://yqp7js.coding-pages.com/2020/05/17/%E7%94%A8%E7%83%AD%E8%A1%A5%E4%B8%81%E4%BF%AE%E7%90%86%E4%BD%A0%E7%9A%84%E8%A7%A6%E6%91%B8%E6%9D%BF%EF%BC%88TouchPad-Hotpatch%EF%BC%89/
```

## SSDT加载顺序设置

当SSDT的对象为机器的ACPI时，各SSDT之间没有顺序要求。当SSDT的对象为另一个在新的SSDT定义的设备时，则要将此SSDT放到定义该设备的SSDT之后加载。

# ASL准则

ASL是ACPI所使用的语法。

## iASL补丁

补丁的语法如下。

```
(into|into_all) (All|DefinitionBlock|Scope|Method|Device|Processor|ThermalZone) [(label|name_adr|name_hid|code_regex|code_regex_not|parent_label|parent_type|parent_adr|parent_hid) <selector>...] (insert|set_label|replace_matched|replaceall_matched|remove_matched|removeall_matched|remove_entry|replace_content|store_%8|store_%9) begin <argument> end;
```

其中不同类型的函数如下。

```
// DOM
extent into,into_all
scope definitionblock,scope,method,device,processor,thermalzone,all
predicate label,name_adr,name_hid,parent_label,parent_type,parent_adr,parent_hid
action insert,set_label,remove_entry,replace_content

// REGEX
extent into,into_all
predicate code_regex,code_regex_not
action replace_matched,replaceall_matched,remove_matched,removeall_matched,store_%8,store_%9

// No Arguments
remove_entry,remove_matched,removeall_matched,store_%8,store_%9
```

单行注释用`#`，每个补丁用`;`分隔。

## 变量

### 命名

变量命名不超过4个字符，且不能以数字开头。

所有ACPI标识符都是4个字符，短名称是用下划线填充的。例如，XHC在AML二进制中表示为`XHC_`，EC为`EC__`，EC0为`EC0_`，MEI为`MEI_`。

### 类型

变量类型包括Integer、String、Event（事件）、Buffer（数组）、Package、Buffer Field等。定义变量时不需要显式声明其类型。

#### Integer

整数。定义示例如下。

```
Name(TEST, 0)
```

#### String

字符串。定义示例如下。

```
Name(MSTR, "ASL")
```

#### Package

对象集合。定义示例如下。

```
Name (_PRW, Package (0x02)
{
    0x0D,
    0x03
})
```

#### Buffer Field

Buffer Field的种类如下。

| 创建语句         | 大小     |
| ---------------- | -------- |
| CreateBitField   | 1-Bit    |
| CreateByteField  | 8-Bit    |
| CreateWordField  | 16-Bit   |
| CreateDWordField | 32-Bit   |
| CreateQWordField | 64-Bit   |
| CreateField      | 任意大小 |

定义示例如下。

```
CreateBitField (AAAA, Zero, CCCC)
CreateByteField (DDDD, 0x01, EEEE)
CreateWordField (FFFF, 0x05, GGGG)
CreateDWordField (HHHH, 0x06, IIII)
CreateQWordField (JJJJ, 0x14, KKKK)
CreateField (LLLL, Local0, 0x38, MMMM)
```

### 赋值

```
// 传统ASL
Store(Local0, Local1)

// ASL+
Local0 = Local1
```

## 作用域

Scope形成作用域，概念类似于数学中的集合{}。

### 作用域的引用

符号`\`引用根作用域，`^`引用上级作用域，一个`^`（父）运算符将当前作用域上移一位。

在同一目录下引用其它字段只需写相对路径，在另一设备下引用则需写绝对路径。比如对于PWBW路径`\_SB.PCI0.LPCB.PWBW`，在`_SB.PCI0.LPCB`下只需通过`PWBW`引用，在外部则要通过`\_SB.PCI0.LPCB.PWBW`引用。

### 根作用域

有且仅有一个根作用域，所以DSDT都以

```
DefinitionBlock ("xxxx", "DSDT", 0x02, "xxxx", "xxxx", xxxx){
```

开始，同时以

```
}
```

结束。xxxx参数依次表示输出文件名、OEMID、表ID、OEM版本。第三个参数根据第二个参数指定，如上面所示。如果是DSDT就一定是0x02，其他参数都可以自由修改。

### 其它作用域

根作用域下有`_GPE`，`_PR`，`_SB`，`_SI`，`_TZ`五个作用域，Device(xxxx)也可看做是一个作用域。不同属性的设备放在对应的作用域下。

#### _GPE

ACPI的事件处理。

```
Scope (_GPE)
{
    Method (_L0D, 0, NotSerialized)
    {
        ...
    }
    ...
}
```

#### _PR

处理器相关信息。不同的CPU所在的域会不同，常见的有`_PR`，`_SB`，`SCK0`等。

```
Scope (_PR)
{
    Processor (CPU0, 0x00, 0x00000410, 0x06)
    {
        ...
    }
    ...
}
```

#### _SB

所有的设备和总线。

```
Scope (_SB)
{
    Device (PCI0)
    {
        ...
    }
    ...
}
```

#### _SI

系统指示灯。

#### _TZ

热区，用于读取某些温度。

## 函数

函数最多可以传递8个参数，在函数里用Arg0-Arg7表示，不可以自定义。函数最多可以用8个局部变量，用Local0-Local7，不用定义，但是需要初始化才能使用，也就是一定要有一次赋值操作。

### 定义

用Method定义函数。函数可以定义在Device下或者Scope下，但是不能脱离Scope定义单独的函数。参数个数如果不定义默认为零。

#### 单一变量函数

```
// 定义TEST函数
Method (TEST)
{
    ...
}
```

#### 有两个输出参数的函数

使用局部变量`Local0`~`Local7`。

```
// 定义有两个输入参数的MADD函数
Method (MADD, 2)
{
    // 传统ASL
    Store(Arg0, Local0)
    Store(Arg1, Local1)
    Add(Local0, Local1, Local0)

    // ASL+
    Local0 = Arg0
    Local1 = Arg1
    Local0 += Local1
}
```

#### 带返回值的函数

```
// 实现自定义加法
Method (MADD, 2)
{
    Local0 = Arg0
    Local1 = Arg1
    Local0 += Local1

    Return (Local0)
}
```

调用方法如下。

```
// 传统ASL
Store(MADD(1,2), Local0)

// ASL+
Local0 = 1 + 2
```

#### 可序列化的函数

当函数声明为Serialized时，内存中仅能存在一个实例。一般应用在函数中创建一个对象。

```
Method(TEST, Serialized)
{
    Name(MSTR,"I will success")
}
```

如果声明TEST函数如上，则运行以下代码时，若先执行Dev1的TEST，Dev2的TEST将等待Dev1中的TEST函数执行完毕后再执行。

```
Device (Dev1)
{
	TEST()
}
Device (Dev2)
	TEST()
}
```

若声明为NotSerialized，则在其中一个Dev调用TEST的时候，另一个将调用失败，因无法同时创建同一字符串MSTR。

### 运算函数

传统ASL中没有运算符。在ASL+中引入了与C语言等同的`+-*/=`、`<<`、`>>`和逻辑判断`==`、`!=`等。

#### 算术运算函数

| ASL+   | 传统 ASL   | 中文含义   | 举例                                                         |
| ------ | ---------- | ---------- | ------------------------------------------------------------ |
| +      | Add        | 整数相加   | Local0 = 1 + 2<br/>Add (1, 2, Local0)                        |
| -      | Subtract   | 整数减法   | Local0 = 2 - 1<br/>`Subtract (2, 1, Local0)                  |
| *      | Multiply   | 整数相乘   | Local0 = 1 * 2<br/>Multiply (1, 2, Local0)                   |
| /      | Divide     | 整数除法   | Local0 = 10 / 9<br/>Divide (10, 9, Local1(余数), Local0(结果)) |
| %      | Mod        | 整数求余   | Local0 = 10 % 9<br/>Mod (10, 9, Local0)                      |
| <<     | ShiftLeft  | 左移       | Local0 = 1 << 20<br/>ShiftLeft (1, 20, Local0)               |
| >>     | ShiftRight | 右移       | Local0 = 0x10000 >> 4<br/>ShiftRight (0x10000, 4, Local0)    |
| --     | Decrement  | 整数自减 1 | Local0--<br/>Decrement (Local0)                              |
| ++     | Increment  | 整数自增 1 | Local0++<br/>Increment (Local0)                              |
| &      | And        | 整数于     | Local0 = 0x11 & 0x22<br/>And (0x11, 0x22, Local0)            |
| &#124; | Or         | 或         | Local0 = 0x01 \| 0x02<br/>Or (0x01, 0x02, Local0)            |
| ~      | Not        | 取反       | Local0 = ~(0x00)<br/>Not (0x00,Local0)                       |
| 无     | Nor        | 异或       | Nor (0x11, 0x22, Local0)                                     |

#### 逻辑运算函数

| ASL+   | 传统 ASL      | 中文含义     | 举例                                                         |
| ------ | ------------- | ------------ | ------------------------------------------------------------ |
| &&     | LAnd          | 逻辑与       | If (BOL1 && BOL2)<br/>If (LAnd(BOL1, BOL2))                  |
| !      | LNot          | 逻辑反       | Local0 = !0<br/>Store (LNot(0), Local0)                      |
| &#124; | LOr           | 逻辑或       | Local0 = (0 \| 1)<br/>Store (LOR(0, 1), Local0)              |
| <      | LLess         | 逻辑小于     | Local0 = (1 < 2)<br/>Store (LLess(1, 2), Local0)             |
| <=     | LLessEqual    | 逻辑小于等于 | Local0 = (1 <= 2)<br/>Store (LLessEqual(1, 2), Local0)       |
| >      | LGreater      | 逻辑大于     | Local0 = (1 > 2)<br/>`Store (LGreater(1, 2), Local0)         |
| >=     | LGreaterEqual | 逻辑大于等于 | Local0 = (1 >= 2)<br/>`Store (LGreaterEqual(1, 2), Local0)   |
| ==     | LEqual        | 逻辑相等     | Local0 = (Local0 == Local1)`<br/>`If (LEqual(Local0, Local1)) |
| !=     | LNotEqual     | 逻辑不等于   | Local0 = (0 != 1)<br/>`Store (LNotEqual(0, 1), Local0)       |

### 延迟函数

| 名称  |    含义    |      语法      | 参数 |                             描述                             |
| :---: | :--------: | :------------: | :--: | :----------------------------------------------------------: |
| Sleep |    延迟    | Sleep(Source1) | 整数 |              执行被延迟至少所需的毫秒数（长期）              |
| Stall | 短时间停止 | Stall(Source1) | 整数 | 执行被延迟至少所需的微秒数（短期，超过100微秒的延迟必须使用Sleep而不是Stall） |

### 系统保留方法

以`_`字符开头的函数和变量均为系统保留方法。

#### _OSI

全程为Operating System Interfaces（操作系统接口）。当系统加载时，`_OSI`会接收到一个参数，不同的系统接收的参数不同，ACPI执行的指令就不同，具体如下表。

| 操作系统            | 字符串         |
| ------------------- | -------------- |
| macOS               | "Darwin"       |
| Linux/基于Linux内核 | "Linux"        |
| FreeBSD             | "FreeBSD"      |
| Windows             | "Windows 20XX" |

采用操作系统补丁的作用在于修改其返回参数，或修改其参数对应的行为，从而解锁系统对某些部件的限制，通常情况下不推荐使用。示例如下。

```
// 需要解锁限制的部件
If ((_OSI ("Windows 2009") || _OSI ("Windows 2013")))
{
        OperationRegion (PCF0, SystemMemory, 0xF0100000, 0x0200)
        Field (PCF0, ByteAcc, NoLock, Preserve)
        {
                HVD0,   32, 
                Offset (0x160), 
                TPR0,   8
        }
        ...
}
```

ACPI还定义了`OSYS`，对应关系如下。

|     标签值     | OSYS值 |  对应系统  |
| -------------- | ------ | ---------- |
|  Windows 2009  | 0x07D9 |    Win7    |
|  Windows 2012  | 0x07DC |    Win8    |
|  Windows 2013  | 0x07DD |   Win8.1   |
|  Windows 2015  | 0x07DF |   Win10    |
|  Windows 2016  | 0x07E0 | Win10 1607 |
|  Windows 2017  | 0x07E1 | Win10 1703 |
| Windows 2017.2 | 0x07E1 | Win10 1709 |
|  Windows 2018  | 0x07E2 | Win10 1803 |
| Windows 2018.2 | 0x07E2 | Win10 1809 |
|  Windows 2019  | 0x???? | Win10 1903 |

`Win8`以上才支持I2C设备。当加载macOS时，`_OSI`接受的参数不会被ACPI识别，`OSYS`被赋予了默认值。这个默认值通常小于Win8要求的值，显然I2C无法工作，故VoodooI2C通常需要采用操作系统补丁来驱动触控板。

#### _STA

全称为Status（状态），用于描述设备的状态，与PowerResource的`_STA`不同（PowerResource的`_STA`只有两个返回值`One`和`Zero`）。

ACPI表中的每个设备，在初始化之前，都会先执行`_STA`函数，去检测这个设备的状态，如果设备存在才初始化。如果没有显式定义`_STA`函数，这个设备则被默认是存在且能正常工作的。描述设备的状态主要靠`_STA`的返回值，它的返回值有32位，目前只有末5位有定义，如下表。

| Bit位 | 含义                           |
| ----- | ------------------------------ |
| 0     | 设备是否存在                   |
| 1     | 设备是否被启用且可以解码其资源 |
| 2     | 设备是否在 UI 中显示           |
| 3     | 设备是否正常工作               |
| 4     | 设备是否存在电池               |

对以上定义，`是`则置该位为1，`否`则置为0。如果一个设备没有电池，可以正常工作并在系统中显示，那它的`_STA`返回值就是二进制的01111，换成十六进制就是0x0F。如果一个设备不存在，返回值即为00000，也就是十六进制下的0x00，或写成Zero。

`0x0B`表示设备处于半开状态，不允许解码其中的资源。`0x1F`只会在笔记本的电池设备中出现，多出的那个Bit用于通知电池控制设备（Control Method Battery Device，`PNP0C0A`）设备存在电池。

#### _CRS

全称为Current Resource Settings（当前资源设置）。`_CRS`函数返回一个`Buffer`，在触摸设备中会返回触摸设备所需的`GPIO Pin`、`APIC Pin`等。通过调整其值，可以控制设备的中断模式。

## 流程控制

以下均以ASL+举例。

### If/ElseIf/Else结构

```
If (_OSI ("Darwin"))
{
    OSYS = 0x2710
}
ElseIf (_OSI ("Linux"))
{
    OSYS = 0x03E8
}
Else
{
    OSYS = 0x07D0
}
```

### Switch/Case/Default/BreakPoint结构

BreakPoint相当于断点，意味着退出当前Switch。

```
Switch(Arg2)
{
    Case(1)
    {
If(Arg1 == 1）
{
    Return (1)
}
BreakPoint
    }
    Case(2)
    {
        ......
        Return (2)
    }
    Default
    {
        BreakPoint
    }
}
```

### While结构

```
Local0 = 10
While (Local0 >= 0x00)
{
    Local0--
    Stall (32)
}
```

### For结构

```
for (local0 = 0, local0 < 8, local0++)
{
    ...
}
```

## 外部引用External

在SSDT中外部引用时，使用`External ([路径和名称], 引用类型)`，然后把光标定位到被引用的Object里，MaciASL会在左下角显示路径。

| 引用类型       | 中文     | 外部 SSDT 引用                              | 被引用                                                       |
| -------------- | -------- | ------------------------------------------- | ------------------------------------------------------------ |
| UnknownObj     | 未知     | External (\_SB.EROR, UnknownObj             | iasl无法判断类型时才会出现（避免使用）                       |
| IntObj         | 整数     | External (TEST, IntObj                      | Name (TEST, 0)                                               |
| StrObj         | 字符串   | External (\_PR.MSTR, StrObj                 | Name (MSTR,"ASL")                                            |
| BuffObj        | 缓冲     | External (\_SB.PCI0.I2C0.TPD0.SBFB, BuffObj | Name (SBFB, ResourceTemplate ()<br/>Name (BUF0, Buffer() {"abcde"}) |
| PkgObj         | 包       | `External (_SB.PCI0.RP01._PRW, PkgObj`      | Name (_PRW, Package (0x02) { 0x0D, 0x03 })                   |
| FieldUnitObj   | 字段单元 | External (OSYS, FieldUnitObj                | OSYS,   16,                                                  |
| DeviceObj      | 设备     | External (\_SB.PCI0.I2C1.ETPD, DeviceObj    | Device (ETPD)                                                |
| EventObj       | 事件     | External (XXXX, EventObj                    | Event (XXXX)                                                 |
| MethodObj      | 函数     | External (\_SB.PCI0.GPI0._STA, MethodObj    | Method (_STA, 0, NotSerialized)                              |
| MutexObj       | 互斥体   | External (_SB.PCI0.LPCB.EC0.BATM, MutexObj  | Mutex (BATM, 0x07)                                           |
| OpRegionObj    | 操作区   | External (GNVS, OpRegionObj                 | OperationRegion (GNVS, SystemMemory, 0x7A4E7000, 0x0866)     |
| PowerResObj    | 电源资源 | External (\_SB.PCI0.XDCI, PowerResObj       | PowerResource (USBC, 0, 0)                                   |
| ProcessorObj   | 处理器   | External (\_SB.PR00, ProcessorObj           | Processor (PR00, 0x01, 0x00001810, 0x06)                     |
| ThermalZoneObj | 温控区   | External (\_TZ.THRM, ThermalZoneObj         | ThermalZone (THRM)                                           |
| BuffFieldObj   | 缓冲区   | External (\_SB.PCI0._CRS.BBBB, BuffFieldObj | CreateField (AAAA, Zero, BBBB)                               |
| DDBHandleObj   |          |                                             | /                                                            |

## 存在性判断语句

`CondRefOf`可以用来判断所有类型Object的存在与否。

```
Method (SSCN, 0, NotSerialized)
{
    If (_OSI ("Darwin"))
    {
        ...
    }
    ElseIf (CondRefOf (\_SB.PCI0.I2C0.XSCN))
    {
        If (USTP)
        {
            Return (\_SB.PCI0.I2C0.XSCN ())
        }
    }

    Return (Zero)
}
```

## 错误举例

### 例一

#### 错误代码

```
Method (ADBG, 1, Serialized)
{
    If (CondRefOf (MDBG))
    {
        Return (MDBG)
        Arg0
    }
     Return (Zero)
} 
```

#### 修正后代码

```
Method (ADBG, 1, Serialized)
{
    If (CondRefOf (MDBG))
    {
        Return (MDBG (Arg0))
    }
     Return (Zero)
} 
```

## 编译错误与警告

### (from opcode)

编译完成后在文件内的某些行会有此该提示，一般位于以下位置，表明名称已在作用域中，其外部声明是多余/不必要的。可直接注释掉此行。

```
External (DTS1, FieldUnitObj)    // (from opcode)
```

### only 0 was resolved (2 unresolved)

在编译完成后，文件内可能会有以下注释，主要含义为方法名称未定义。由于某些方法在Windows而不在ACPI表被定义，因此该警告可忽略。

```
/* There were 2 external control methods found during 
* disassembly, but only 0 was resolved (2 unresolved)
...
*/
```

## 偏移量和Field名

以下代码中GNVS可以替换为任意名称，因为0x100, Zero指向该地址的范围，偏移量地址已经被确定。而OOXX可以用0x1E代替，因为首先要保证的是字段的偏移量正确，故OOXX与OOX1互换，其数据也会跟着变化。

偏移量以字节为单位，一字节等于8位，故OOX1的偏移量为0x1E+1=0x20。

```
Name (OOXX, Zero)
OperationRegion (GNVS, SystemMemory, 0x100, Zero)
	Field (GNVS,AnyAcc, Lock, Preserve)
	{
		Offset(0x1E),
		OOXX,   16, // 0x1E
		OOX1,   8, // 0x20
		......
	}
```

# 附录

## 本机DSDT

### 电池部分

```
    Scope (_SB.PCI0.LPCB.EC0)
    {
        Device (BAT0)
        {
            Name (_HID, EisaId ("PNP0C0A"))  // _HID: Hardware ID
            Name (_UID, Zero)  // _UID: Unique ID
            Name (_PCL, Package (0x01)  // _PCL: Power Consumer List
            {
                PCI0
            })
            Method (_STA, 0, NotSerialized)  // _STA: Status
            {
                If (BATP (Zero))
                {
                    Return (0x1F)
                }
                Else
                {
                    Return (0x0F)
                }
            }

            Name (LOW2, 0x012C)
            Name (PUNT, One)
            Name (LFCC, 0x1770)
            Name (NBIF, Package (0x0D)
            {
                Zero, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                One, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                "", 
                "", 
                "", 
                ""
            })
            Name (PBIF, Package (0x0D)
            {
                One, 
                0x1770, 
                0x1770, 
                One, 
                0x39D0, 
                0x0258, 
                0x012C, 
                0x3C, 
                0x3C, 
                "M3N", 
                " ", 
                "LIon", 
                "ASUSTeK"
            })
            Name (PBST, Package (0x04)
            {
                Zero, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF
            })
            Name (BIXT, Package (0x14)
            {
                Zero, 
                Zero, 
                0x1770, 
                0x1770, 
                One, 
                0x39D0, 
                0x0258, 
                0x012C, 
                Zero, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0x3C, 
                0x3C, 
                "M3N", 
                " ", 
                "LIon", 
                "ASUSTeK"
            })
            Name (NBIX, Package (0x14)
            {
                Zero, 
                Zero, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                One, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                Zero, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                0xFFFFFFFF, 
                "", 
                "", 
                "", 
                ""
            })
            Method (FBIF, 5, NotSerialized)
            {
                Store (Arg0, PUNT)
                Store (Arg1, Local1)
                Store (Arg2, Local2)
                If (LEqual (PUNT, Zero))
                {
                    Multiply (Local1, 0x0A, Local1)
                    Multiply (Local2, 0x0A, Local2)
                }

                Store (Arg0, Index (PBIF, Zero))
                Store (Local1, Index (PBIF, One))
                Store (Local2, Index (PBIF, 0x02))
                Store (Local2, LFCC)
                Store (Arg3, Index (PBIF, 0x03))
                Store (Arg4, Index (PBIF, 0x04))
                Divide (Local1, 0x0A, , Local5)
                Store (Local5, Index (PBIF, 0x05))
                Divide (Local1, 0x64, , Local6)
                Store (Local6, Index (PBIF, 0x06))
                Store (Local6, LOW2)
                Divide (Local1, 0x64, , Local7)
                Store (Local7, Index (PBIF, 0x07))
                Store (Local7, Index (PBIF, 0x08))
            }

            Method (CBIF, 0, NotSerialized)
            {
                If (PUNT)
                {
                    Store (DerefOf (Index (PBIF, 0x04)), Local0)
                    Store (Zero, Index (PBIF, Zero))
                    Multiply (DerefOf (Index (PBIF, One)), Local0, Index (PBIF, One))
                    Multiply (DerefOf (Index (PBIF, 0x02)), Local0, Index (PBIF, 0x02))
                    Multiply (DerefOf (Index (PBIF, 0x05)), Local0, Index (PBIF, 0x05))
                    Multiply (DerefOf (Index (PBIF, 0x06)), Local0, Index (PBIF, 0x06))
                    Multiply (DerefOf (Index (PBIF, 0x07)), Local0, Index (PBIF, 0x07))
                    Multiply (DerefOf (Index (PBIF, 0x08)), Local0, Index (PBIF, 0x08))
                    Divide (DerefOf (Index (PBIF, One)), 0x03E8, , Index (PBIF, One))
                    Divide (DerefOf (Index (PBIF, 0x02)), 0x03E8, , Index (PBIF, 0x02))
                    Divide (DerefOf (Index (PBIF, 0x05)), 0x03E8, , Index (PBIF, 0x05))
                    Divide (DerefOf (Index (PBIF, 0x06)), 0x03E8, , Index (PBIF, 0x06))
                    Divide (DerefOf (Index (PBIF, 0x07)), 0x03E8, , Index (PBIF, 0x07))
                    Divide (DerefOf (Index (PBIF, 0x08)), 0x03E8, , Index (PBIF, 0x08))
                }
            }

            Method (_BIF, 0, NotSerialized)  // _BIF: Battery Information
            {
                If (LNot (BATP (Zero)))
                {
                    Return (NBIF)
                }

                If (LEqual (GBTT (Zero), 0xFF))
                {
                    Return (NBIF)
                }

                BATO ()
                BATS (Zero)
                Store (BIF9 (), Index (PBIF, 0x09))
                Store (BIF0 (), Local0)
                Store (BIF1 (), Local1)
                Store (BIF2 (), Local2)
                Store (BIF3 (), Local3)
                Store (BIF4 (), Local4)
                If (LNotEqual (Local0, Ones))
                {
                    If (LNotEqual (Local1, Ones))
                    {
                        If (LNotEqual (Local2, Ones))
                        {
                            If (LNotEqual (Local3, Ones))
                            {
                                If (LNotEqual (Local4, Ones))
                                {
                                    FBIF (Local0, Local1, Local2, Local3, Local4)
                                    CBIF ()
                                }
                            }
                        }
                    }
                }

                If (LEqual (PUNT, Zero))
                {
                    Multiply (Local2, 0x0A, Local2)
                }

                Store (Local2, LFCC)
                BATR ()
                Return (PBIF)
            }

            Method (FBST, 4, NotSerialized)
            {
                And (Arg1, 0xFFFF, Local1)
                Store (Zero, Local0)
                If (ACAP ())
                {
                    Store (One, Local0)
                }

                If (Local0)
                {
                    If (CHGS (Zero))
                    {
                        Store (0x02, Local0)
                    }
                    Else
                    {
                        Store (One, Local0)
                    }
                }
                Else
                {
                    Store (One, Local0)
                }

                If (BLLO)
                {
                    Store (0x04, Local2)
                    Or (Local0, Local2, Local0)
                }

                Store (0x09, BRAH)
                If (And (EB0S, 0x08))
                {
                    Store (0x04, Local2)
                    Or (Local0, Local2, Local0)
                }

                If (LGreaterEqual (Local1, 0x8000))
                {
                    Subtract (0xFFFF, Local1, Local1)
                }

                Store (Arg2, Local2)
                If (LEqual (PUNT, Zero))
                {
                    Store (0x09, BRAH)
                    Multiply (Local1, B0DV, Local1)
                    Multiply (Local2, 0x0A, Local2)
                }

                And (Local0, 0x02, Local3)
                If (LNot (Local3))
                {
                    Subtract (LFCC, Local2, Local3)
                    Divide (LFCC, 0xC8, Local4, Local5)
                    If (LLess (Local3, Local5))
                    {
                        Store (LFCC, Local2)
                    }
                }
                Else
                {
                    Divide (LFCC, 0xC8, Local4, Local5)
                    Subtract (LFCC, Local5, Local4)
                    If (LGreater (Local2, Local4))
                    {
                        Store (Local4, Local2)
                    }
                }

                If (LNot (ACAP ()))
                {
                    Divide (Local2, MBLF, Local3, Local4)
                    If (LLess (Local1, Local4))
                    {
                        Store (Local4, Local1)
                    }
                }

                Store (Local0, Index (PBST, Zero))
                Store (Local1, Index (PBST, One))
                Store (Local2, Index (PBST, 0x02))
                Store (Arg3, Index (PBST, 0x03))
            }

            Method (CBST, 0, NotSerialized)
            {
                If (PUNT)
                {
                    Store (0x09, BRAH)
                    Store (B0DV, Index (PBST, 0x03))
                    Store (DerefOf (Index (PBST, 0x03)), Local0)
                    Multiply (DerefOf (Index (PBST, One)), Local0, Index (PBST, One))
                    Divide (DerefOf (Index (PBST, One)), 0x03E8, , Index (PBST, One))
                    Multiply (DerefOf (Index (PBST, 0x02)), Local0, Index (PBST, 0x02))
                    Divide (DerefOf (Index (PBST, 0x02)), 0x03E8, , Index (PBST, 0x02))
                }
            }

            Method (_BST, 0, NotSerialized)  // _BST: Battery Status
            {
                Store (Zero, Index (PBST, Zero))
                Store (Ones, Index (PBST, One))
                Store (Ones, Index (PBST, 0x02))
                Store (Ones, Index (PBST, 0x03))
                If (LNot (BATP (Zero)))
                {
                    Store (One, Index (PBST, Zero))
                    Return (PBST)
                }

                If (LEqual (GBTT (Zero), 0xFF))
                {
                    Return (PBST)
                }

                BATO ()
                BATS (Zero)
                Store (BSTS (), Local0)
                Store (BCRT (), Local1)
                Store (BRCP (), Local2)
                Store (BVOT (), Local3)
                If (LNotEqual (Local0, Ones))
                {
                    If (LNotEqual (Local1, Ones))
                    {
                        If (LNotEqual (Local2, Ones))
                        {
                            If (LNotEqual (Local3, Ones))
                            {
                                FBST (Local0, Local1, Local2, Local3)
                                CBST ()
                            }
                        }
                    }
                }

                BATR ()
                Return (PBST)
            }

            Method (_BIX, 0, NotSerialized)  // _BIX: Battery Information Extended
            {
                If (LNot (BATP (Zero)))
                {
                    Return (NBIX)
                }

                If (LEqual (GBTT (Zero), 0xFF))
                {
                    Return (NBIX)
                }

                _BIF ()
                Store (DerefOf (Index (PBIF, Zero)), Index (BIXT, One))
                Store (DerefOf (Index (PBIF, One)), Index (BIXT, 0x02))
                Store (DerefOf (Index (PBIF, 0x02)), Index (BIXT, 0x03))
                Store (DerefOf (Index (PBIF, 0x03)), Index (BIXT, 0x04))
                Store (DerefOf (Index (PBIF, 0x04)), Index (BIXT, 0x05))
                Store (DerefOf (Index (PBIF, 0x05)), Index (BIXT, 0x06))
                Store (DerefOf (Index (PBIF, 0x06)), Index (BIXT, 0x07))
                Store (DerefOf (Index (PBIF, 0x07)), Index (BIXT, 0x0E))
                Store (DerefOf (Index (PBIF, 0x08)), Index (BIXT, 0x0F))
                Store (DerefOf (Index (PBIF, 0x09)), Index (BIXT, 0x10))
                Store (DerefOf (Index (PBIF, 0x0A)), Index (BIXT, 0x11))
                Store (DerefOf (Index (PBIF, 0x0B)), Index (BIXT, 0x12))
                Store (DerefOf (Index (PBIF, 0x0C)), Index (BIXT, 0x13))
                If (LEqual (DerefOf (Index (BIXT, One)), One))
                {
                    Store (Zero, Index (BIXT, One))
                    Store (DerefOf (Index (BIXT, 0x05)), Local0)
                    Multiply (DerefOf (Index (BIXT, 0x02)), Local0, Index (BIXT, 0x02))
                    Multiply (DerefOf (Index (BIXT, 0x03)), Local0, Index (BIXT, 0x03))
                    Multiply (DerefOf (Index (BIXT, 0x06)), Local0, Index (BIXT, 0x06))
                    Multiply (DerefOf (Index (BIXT, 0x07)), Local0, Index (BIXT, 0x07))
                    Multiply (DerefOf (Index (BIXT, 0x0E)), Local0, Index (BIXT, 0x0E))
                    Multiply (DerefOf (Index (BIXT, 0x0F)), Local0, Index (BIXT, 0x0F))
                    Divide (DerefOf (Index (BIXT, 0x02)), 0x03E8, Local0, Index (BIXT, 0x02))
                    Divide (DerefOf (Index (BIXT, 0x03)), 0x03E8, Local0, Index (BIXT, 0x03))
                    Divide (DerefOf (Index (BIXT, 0x06)), 0x03E8, Local0, Index (BIXT, 0x06))
                    Divide (DerefOf (Index (BIXT, 0x07)), 0x03E8, Local0, Index (BIXT, 0x07))
                    Divide (DerefOf (Index (BIXT, 0x0E)), 0x03E8, Local0, Index (BIXT, 0x0E))
                    Divide (DerefOf (Index (BIXT, 0x0F)), 0x03E8, Local0, Index (BIXT, 0x0F))
                }

                Store (B0C3, Index (BIXT, 0x08))
                Store (0x0001869F, Index (BIXT, 0x09))
                Return (BIXT)
            }

            Method (_BTP, 1, NotSerialized)  // _BTP: Battery Trip Point
            {
                ST8E (0x29, 0xFF)
                ST8E (0x2A, 0xFF)
                ST8E (0x28, 0x0F)
                If (LNotEqual (Arg0, Zero))
                {
                    Store (DerefOf (Index (PBIF, 0x04)), Local0)
                    Multiply (Arg0, 0x03E8, Local1)
                    Divide (Local1, Local0, Local2, Local3)
                    If (CHGS (Zero))
                    {
                        If (LNotEqual (Local2, Zero))
                        {
                            Add (Local3, One, Local3)
                        }
                    }

                    ST9E (0x29, 0xFF, And (Local3, 0xFF))
                    ShiftRight (Local3, 0x08, Local2)
                    And (Local2, 0xFF, Local2)
                    ST9E (0x2A, 0xFF, Local2)
                }
            }
        }
    }
```

## 本机hotpatch

### 电池部分

更名如下。

```
Comment: change BIFA to BIGA
Find: 42494641
Replace: 42494741

Comment: change _BIX to XBIX
Find: 5F424958
Replace: 58424958
```

hotpatch如下。

```
// 使用Catalina用户则需将EC0更改为EC
DefinitionBlock ("", "SSDT", 2, "hack", "BAT0", 0x00000000)
{
    External (_SB.PCI0.LPCB.EC0, DeviceObj) 
    External (_SB.PCI0.LPCB.EC0.BAT0, DeviceObj)
    External (_SB.PCI0.LPCB.EC0.BAT0._BIF, MethodObj)
    External (_SB.PCI0.LPCB.EC0.BAT0.BIXT, IntObj)
    External (_SB.PCI0.LPCB.EC0.BAT0.NBIX, IntObj)
    External (_SB.PCI0.LPCB.EC0.BAT0.PBIF, IntObj) 
    External (_SB.PCI0.LPCB.EC0.BATP, MethodObj) 
    External (_SB.PCI0.LPCB.EC0.BSLF, IntObj) 
    External (_SB.PCI0.LPCB.EC0.ECAV, MethodObj)
    External (_SB.PCI0.LPCB.EC0.GBTT, MethodObj)

    Scope (_SB.PCI0.LPCB.EC0)
    {
        OperationRegion (ECAG, EmbeddedControl, Zero, 0xFF)
        Field (ECAG, ByteAcc, Lock, Preserve)
        {
            Offset (0x04), 
            Offset (0x05), 
            Offset (0x06), 
            Offset (0x07), 
            Offset (0x08), 
            Offset (0x80), 
            Offset (0x81), 
            Offset (0x82), 
            Offset (0x83), 
            Offset (0x84), 
            Offset (0x85), 
            Offset (0x86), 
            Offset (0x87), 
            Offset (0x88), 
            Offset (0x89), 
            Offset (0x93), 
            Offset (0x95), 
            Offset (0x97), 
            Offset (0x98), 
            Offset (0x9C), 
            Offset (0x9D), 
            Offset (0x9E), 
            Offset (0xA0), 
            Offset (0xA1), 
            Offset (0xA2), 
            Offset (0xA3), 
            Offset (0xA4), 
            Offset (0xA5), 
            Offset (0xA6), 
            Offset (0xA7), 
            Offset (0xA8), 
            Offset (0xB0), 
            Offset (0xB2), 
            Offset (0xB4), 
            Offset (0xB6), 
            Offset (0xB8), 
            Offset (0xBA), 
            Offset (0xBC), 
            Offset (0xBE), 
            Offset (0xC0), 
            Offset (0xC2), 
            Offset (0xC4), 
            C3HG,   8, 
            C3HF,   8, 
            Offset (0xC8), 
            Offset (0xD0), 
            Offset (0xD2), 
            Offset (0xD4), 
            Offset (0xD6), 
            Offset (0xD8), 
            Offset (0xDA), 
            Offset (0xDC), 
            Offset (0xDE), 
            Offset (0xE0), 
            Offset (0xE2), 
            Offset (0xE4), 
            Offset (0xE6), 
            Offset (0xE8), 
            Offset (0xF0), 
            Offset (0xF2), 
            Offset (0xF4), 
            BSVN,   8, 
            BSVM,   8, 
            Offset (0xF8), 
            Offset (0xFA), 
            Offset (0xFC), 
            SBUY,   8, 
            SBUP,   8
        }

        Method (BIFA, 0, NotSerialized)
        {
            If (ECAV ())
            {
                If (BSLF)
                {
                    Store (B1B2 (SBUY, SBUP), Local0)
                }
                Else
                {
                    Store (B1B2 (BSVN, BSVM), Local0)
                }
            }
            Else
            {
                Store (Ones, Local0)
            }

            Return (Local0)
        }
    }

    Scope (_SB.PCI0.LPCB.EC0.BAT0)
    {
        Method (_BIX, 0, NotSerialized)  // _BIX: Battery Information Extended
        {
            If (LNot (BATP (Zero)))
            {
                Return (NBIX)
            }

            If (LEqual (GBTT (Zero), 0xFF))
            {
                Return (NBIX)
            }

            _BIF ()
            Store (DerefOf (Index (PBIF, Zero)), Index (BIXT, One))
            Store (DerefOf (Index (PBIF, One)), Index (BIXT, 0x02))
            Store (DerefOf (Index (PBIF, 0x02)), Index (BIXT, 0x03))
            Store (DerefOf (Index (PBIF, 0x03)), Index (BIXT, 0x04))
            Store (DerefOf (Index (PBIF, 0x04)), Index (BIXT, 0x05))
            Store (DerefOf (Index (PBIF, 0x05)), Index (BIXT, 0x06))
            Store (DerefOf (Index (PBIF, 0x06)), Index (BIXT, 0x07))
            Store (DerefOf (Index (PBIF, 0x07)), Index (BIXT, 0x0E))
            Store (DerefOf (Index (PBIF, 0x08)), Index (BIXT, 0x0F))
            Store (DerefOf (Index (PBIF, 0x09)), Index (BIXT, 0x10))
            Store (DerefOf (Index (PBIF, 0x0A)), Index (BIXT, 0x11))
            Store (DerefOf (Index (PBIF, 0x0B)), Index (BIXT, 0x12))
            Store (DerefOf (Index (PBIF, 0x0C)), Index (BIXT, 0x13))
            If (LEqual (DerefOf (Index (BIXT, One)), One))
            {
                Store (Zero, Index (BIXT, One))
                Store (DerefOf (Index (BIXT, 0x05)), Local0)
                Multiply (DerefOf (Index (BIXT, 0x02)), Local0, Index (BIXT, 0x02))
                Multiply (DerefOf (Index (BIXT, 0x03)), Local0, Index (BIXT, 0x03))
                Multiply (DerefOf (Index (BIXT, 0x06)), Local0, Index (BIXT, 0x06))
                Multiply (DerefOf (Index (BIXT, 0x07)), Local0, Index (BIXT, 0x07))
                Multiply (DerefOf (Index (BIXT, 0x0E)), Local0, Index (BIXT, 0x0E))
                Multiply (DerefOf (Index (BIXT, 0x0F)), Local0, Index (BIXT, 0x0F))
                Divide (DerefOf (Index (BIXT, 0x02)), 0x03E8, Local0, Index (BIXT, 0x02))
                Divide (DerefOf (Index (BIXT, 0x03)), 0x03E8, Local0, Index (BIXT, 0x03))
                Divide (DerefOf (Index (BIXT, 0x06)), 0x03E8, Local0, Index (BIXT, 0x06))
                Divide (DerefOf (Index (BIXT, 0x07)), 0x03E8, Local0, Index (BIXT, 0x07))
                Divide (DerefOf (Index (BIXT, 0x0E)), 0x03E8, Local0, Index (BIXT, 0x0E))
                Divide (DerefOf (Index (BIXT, 0x0F)), 0x03E8, Local0, Index (BIXT, 0x0F))
            }

            Store (B1B2 (C3HG, C3HF), Index (BIXT, 0x08))
            Store (0x0001869F, Index (BIXT, 0x09))
            Return (BIXT)
        }
    }

    Method (B1B2, 2, NotSerialized)
    {
        Return (Or (Arg0, ShiftLeft (Arg1, 0x08)))
    }
}
```

## 本机Clover文件结构

### macOS Mojave/Catalina

#### 完整情况

```
├── ACPI
│   └── patched
│       ├── SSDT-ALS0.aml
│       ├── SSDT-ARTC.aml
│       ├── SSDT-ASUS.aml
│       ├── SSDT-BAT0.aml
│       ├── SSDT-Config.aml
│       ├── SSDT-DGPU.aml
│       ├── SSDT-DMAC.aml
│       ├── SSDT-GPRW.aml
│       ├── SSDT-MCHC.aml
│       ├── SSDT-MEM2.aml
│       ├── SSDT-PNLF.aml
│       ├── SSDT-UIAC.aml
│       └── SSDT-XCPM.aml
├── CLOVERX64.efi
├── config.plist
├── drivers
│   └── UEFI
│       ├── ApfsDriverLoader.efi
│       ├── AudioDxe.efi
│       ├── DataHubDxe.efi
│       ├── FSInject.efi
│       ├── HFSPlus.efi
│       ├── OpenRuntime.efi
│       └── VirtualSmc.efi
├── kexts
│   └── Other
│       ├── AppleALC.kext
│       ├── AsusSMC.kext
│       ├── CPUFriend.kext
│       ├── CPUFriendDataProvider.kext
│       ├── CodecCommander.kext
│       ├── Lilu.kext
│       ├── LiluFriend.kext
│       ├── NoTouchID.kext
│       ├── RealtekRTL8111.kext
│       ├── SMCBatteryManager.kext
│       ├── SMCLightSensor.kext
│       ├── SMCProcessor.kext
│       ├── SMCSuperIO.kext
│       ├── USBInjectAll.kext
│       ├── VirtualSMC.kext
│       ├── VoodooI2C.kext
│       ├── VoodooI2CHID.kext
│       ├── VoodooPS2Controller.kext
│       └── WhateverGreen.kext
```

### macOS Big Sur

#### 最简情况

```
├── ACPI
│   └── patched
│       ├── SSDT-GPRW.aml
│       └── SSDT-PLUG.aml
├── CLOVERX64.efi
├── config.plist
├── drivers
│   └── UEFI
│       ├── ApfsDriverLoader.efi
│       ├── AudioDxe.efi
│       ├── DataHubDxe.efi
│       ├── FSInject.efi
│       ├── HFSPlus.efi
│       ├── OpenRuntime.efi
│       └── VirtualSmc.efi
├── kexts
│   └── Other
│       ├── Lilu.kext
│       ├── SMCBatteryManager.kext
│       ├── SMCLightSensor.kext
│       ├── SMCProcessor.kext
│       ├── SMCSuperIO.kext
│       ├── USBInjectAll.kext
│       ├── VirtualSMC.kext
│       ├── VoodooI2C.kext
│       ├── VoodooI2CHID.kext
│       ├── VoodooPS2Controller.kext
│       └── WhateverGreen.kext
```

## 本机OpenCore文件结构

### macOS Catalina

#### 完整情况

```
├── ACPI
│   ├── SSDT-ALSD.aml
│   ├── SSDT-ARTC.aml
│   ├── SSDT-ASUS.aml
│   ├── SSDT-BAT0.aml
│   ├── SSDT-Config.aml
│   ├── SSDT-DGPU.aml
│   ├── SSDT-DMAC.aml
│   ├── SSDT-EC.aml
│   ├── SSDT-GPRW.aml
│   ├── SSDT-MCHC.aml
│   ├── SSDT-MEM2.aml
│   ├── SSDT-PLUG.aml
│   ├── SSDT-PMCR.aml
│   ├── SSDT-PNLF.aml
│   ├── SSDT-SBUS.aml
│   ├── SSDT-UIAC.aml
│   └── SSDT-XCPM.aml
├── Drivers
│   ├── ApfsDriverLoader.efi
│   ├── AudioDxe.efi
│   ├── HfsPlus.efi
│   ├── OpenCanopy.efi
│   ├── OpenRuntime.efi
│   └── OpenUsbKbDxe.efi
├── Kexts
│   ├── AppleALC.kext
│   ├── AsusSMC.kext
│   ├── CodecCommander.kext
│   ├── Lilu.kext
│   ├── LiluFriend.kext
│   ├── NoTouchID.kext
│   ├── RealtekRTL8111.kext
│   ├── SMCBatteryManager.kext
│   ├── SMCLightSensor.kext
│   ├── SMCProcessor.kext
│   ├── SMCSuperIO.kext
│   ├── USBInjectAll.kext
│   ├── VirtualSMC.kext
│   ├── VoodooPS2Controller.kext
│   └── WhateverGreen.kext
├── OpenCore.efi
└── config.plist
```

### macOS Big Sur

#### 最简情况

```
├── ACPI
│   ├── SSDT-GPRW.aml
│   └── SSDT-PLUG.aml
├── Drivers
│   ├── ApfsDriverLoader.efi
│   ├── AudioDxe.efi
│   ├── HfsPlus.efi
│   ├── OpenCanopy.efi
│   ├── OpenRuntime.efi
│   └── OpenUsbKbDxe.efi
├── Kexts
│   ├── Lilu.kext
│   ├── SMCBatteryManager.kext
│   ├── SMCLightSensor.kext
│   ├── SMCProcessor.kext
│   ├── SMCSuperIO.kext
│   ├── USBMap.kext
│   ├── CtlnaAHCIPort.kext
│   ├── VirtualSMC.kext
│   ├── VoodooI2C.kext
│   ├── VoodooI2CHID.kext
│   ├── VoodooPS2Controller.kext
│   └── WhateverGreen.kext
├── OpenCore.efi
└── config.plist
```

# 相关下载

## Catalina MBR HFS Firmware Check Patch

```
https://www.insanelymac.com/forum/files/file/985-catalina-mbr-hfs-firmware-check-patch/
```

## FixShutdown-USB-SSDT.dsl

```
https://github.com/khronokernel/Opencore-Vanilla-Desktop-Guide/blob/master/extra-files/FixShutdown-USB-SSDT.dsl
```

## OpenCore

### OpenCorePkg

```
https://github.com/acidanthera/OpenCorePkg/releases
```

### AppleSupportPkg

```
https://github.com/acidanthera/AppleSupportPkg/releases
```

### OcBinaryData

```
https://github.com/acidanthera/OcBinaryData
```

### SSDT-EHCx_OFF.dsl

```
https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-EHCx_OFF.ds
```

### OpenCore Configurator

```
https://mackie100projects.altervista.org/download-opencore-configurator/
```

## ProperTree

```
https://codeload.github.com/corpnewt/ProperTree/zip/master
```

## FireWolf OS X PE

```
https://pe.firewolf.app/manual/downloads/thankyou.html
```

## CatalinaAutoMountWritable.plist

```
https://forums.macrumors.com/attachments/catalinaautomountwritable-plist-zip.847064/
```

## HWMonitorSMC2

```
https://github.com/CloverHackyColor/HWMonitorSMC2
```

## 相关efi驱动

### OcQuirks

```
https://github.com/ReddestDream/OcQuirks/releases
```

### acpidump

```
https://www.acpica.org/downloads/uefi-support
```

### HFSPlus

```
https://github.com/STLVNUB/CloverGrower/tree/master/Files/HFSPlus/x64
```

### AppleSupportPkg（包含ApfsDriverLoader、FwRuntimeServices、VBoxHfs、VerifyMsrE2）

```
https://github.com/acidanthera/AppleSupportPkg
```

### CrScreenshotDxe

```
https://github.com/LongSoft/CrScreenshotDxe
```

### OpenCore UEFI Drivers（包括XhciDxe、MemoryAllocation、VariableRuntimeDxe和NvmExpressDxe）

```
https://github.com/williambj1/OpenCore-Factory/releases/tag/OpenCore-UEFI-Drivers
```

### Acidanthera UEFI Development Kit（驱动库）

```
https://github.com/acidanthera/audk
```

## 附加kexts

### 常用kexts包

```
// Opencore的kext包
https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Kexts.md

// Goldfish64的kext包
https://onedrive.live.com/?authkey=%21APjCyRpzoAKp4xs&id=FE4038DA929BFB23%21455036&cid=FE4038DA929BFB23

// 自动编译Lilu的kext包
https://github.com/corpnewt/Lilu-and-Friends

// Lilu官方kext包
https://github.com/acidanthera/Lilu/blob/master/KnownPlugins.md

// Acidanthera的kext包
https://github.com/acidanthera/bugtracker
```

### 旧版系统kexts

```
https://olarila.com/files/?dir=Kexts
```

### NVMeFix

```
https://github.com/acidanthera/NVMeFix
```

### ACPIDebug

```
https://bitbucket.org/RehabMan/os-x-acpi-debug/downloads/
```

### AsusSMC

```
https://github.com/hieplpvip/AsusSMC/releases
```

### NullCPUPowerManagement

AMD CPU无法使用英特尔的电源管理，因此我们需要取消它。

```
https://github.com/Goldfish64/SerialMouse/releases
```

### WebCamera

相机驱动。

```
https://i.applelife.ru/2019/03/448848_WebCamera.kext.zip
```

### AnyiSightCam

在系统报告中添加相机信息。

```
https://github.com/tuiuVN/EFI_Clover/tree/13a94765f8c3f4a5c7138b2fcc1b9e8d091c65e8/K46CA/EFI/CLOVER/kexts/Other
```

### AnyCardReader

在系统报告中添加读卡器信息（需更改为自己的ID）。

```
https://github.com/holoto/ec-471g/tree/master/EFI/EFI/CLOVER/kexts/10.11
```

### AnyiSightCam

在系统报告中添加相机信息。

```
https://github.com/tuiuVN/EFI_Clover/tree/13a94765f8c3f4a5c7138b2fcc1b9e8d091c65e8/K46CA/EFI/CLOVER/kexts/Other
```

### DebugEnhancer

啰嗦模式下可输出更多内容。

```
https://github.com/acidanthera/DebugEnhancer
```

### VoodooHDA

AMD平台音频驱动（AMD平台不能用AppleALC）。

```
https://github.com/chris1111/VoodooHDA-2.9.2-Clover-V14/releases
```

###  XLNCUSBFix

修复AMD FX系统的USB控制器。

```
https://cdn.discordapp.com/attachments/566705665616117760/566728101292408877/XLNCUSBFix.kext.zip
```

###  IOElectrify

为Intel Thunderbolt硬件提供始终开启的电源。

```
https://github.com/the-darkvoid/macOS-IOElectrify/releases
```

###  AppleMCEReporterDisabler

在Catalina平台上AppleMCEReporter将导致AMD CPU和双插槽系统上的内核崩溃，故需此kext以禁用。

```
https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip
https://github.com/khronokernel/DisableMCEReporter-
```

###  VoodooTSCSync

校正华硕HEDT/Server主板上的TSC。

```
https://bitbucket.org/RehabMan/VoodooTSCSync/downloads/
```

### VoodooInput

提供Magic Trackpad 2仿真。

```
https://github.com/acidanthera/VoodooInput/releases
```

### LiluFriend

确保Lilu及其插件被加载。

```
https://github.com/PMheart/LiluFriend
```

### VoodooSDHCMod

读卡器驱动。

```
https://github.com/lvs1974/VoodooSDHCMod/releases
```

### Sinetek-rtsx

读卡器驱动。

```
https://github.com/tsingui/Sinetek-rtsx-1
```

### SerialMouse

串行鼠标驱动，在系统启动前需先将鼠标接入电脑。

```
https://github.com/Goldfish64/SerialMouse/releases
```

### NightShiftUnlocker

为较旧机型解锁夜视模式。

```
https://github.com/0xFireWolf/NightShiftUnlocker/releases
```

### RTCMemoryFixup

解决部分Z370 B360新BIOS的RTC中STA函数导致卡Apfs NVME的问题。

```
https://github.com/acidanthera/RTCMemoryFixup
```

### HibernationFixup

解决某些机器3&25模式下的休眠问题。

```
https://github.com/acidanthera/HibernationFixup
```

### SystemProfilerMemoryFixup

在MacBookAir和MacBookPro10，x平台上显示内存选项卡。

```
https://github.com/Goldfish64/SystemProfilerMemoryFixup
```

### DiskArbitrationFixup

禁用`此计算机无法读取您插入的磁盘`消息。

```
https://github.com/Goldfish64/DiskArbitrationFixup
```

### AsusSMC

为macOS上的Asus笔记本电脑提供了对ALS，键盘背光和Fn键的本地支持。需要Lilu和VirtualSMC。

```
https://github.com/hieplpvip/AsusSMC
```

### AHCI_3rdParty_eSATA/SATA

第三方eSATA/SATA驱动，连接到这些接口的设备将会识别为内置磁盘。

```
// AHCI_3rdParty_eSATA
https://github.com/NTT123/Hackintosh-HP-Z420-MacOS-High-Sierra-10.13-10.14/tree/master/CLOVER/kexts/10.12

// AHCI_3rdParty_SATA
https://github.com/forumi0721/osx_clover/tree/master/OEM/Z77%20Extreme4/kexts/10.11
```

### AHCI_Intel_Generic_SATA

英特尔SATA驱动。

```
https://github.com/NTT123/Hackintosh-HP-Z420-MacOS-High-Sierra-10.13-10.14/tree/master/CLOVER/kexts/10.12
```

# 参考教程

## 黑苹果制作虚拟机CDR镜像

```
https://blog.csdn.net/qq_41855420/article/details/102750055
```

## OpenCore引导教程

```
https://blog.daliansky.net/OpenCore-BootLoader.html
https://blog.daliansky.net/From-Clover-To-OpenCore.html
https://blog.skk.moe/post/from-clover-to-opencore/
https://blog.xjn819.com/?p=543
https://khronokernel-2.gitbook.io/opencore-vanilla-desktop-guide/
https://dortania.github.io/OpenCore-Desktop-Guide/
https://kirainmoe.com/blog/post/opencore-migration-experience/#13-%e4%bf%ae%e6%94%b9-configplist
```

## 【持续更新】OpenCore引导-v各种卡及OC引导常见问题解决方案速查表合集

```
https://imacos.top/2020/03/28/0154/
```

## macOS 10.13安装中常见的问题及解决方法

```
https://blog.daliansky.net/macOS-10.13-installation-of-common-problems-and-solutions.html
```

## OpenCore 引导迁移折腾记录

```
https://kirainmoe.com/blog/post/opencore-migration-experience/
```

## 热补丁制作教程

```
https://www.penghubingzhou.cn/2019/05/02/Battery%20Hotpatch%20Make/
https://xstar-dev.github.io/hackintosh_advanced/Guide_For_Battery_Hotpatch.html
http://yqp7js.coding-pages.com/2020/05/16/%E8%BF%9B%E9%98%B6%EF%BC%9A%E7%94%B5%E6%B1%A0%E7%83%AD%E8%A1%A5%E4%B8%81%EF%BC%88Battery-Hotpatch%EF%BC%89%E4%B9%8B%E8%B7%AF/
```

## Acidanthera故障排除

```
https://github.com/acidanthera/bugtracker
```

## AsusSMC使用教程

```
https://github.com/hieplpvip/AsusSMC/wiki/Installation-Instruction
```

## macOS Catalina 10.15安装中常见的问题及解决方法

```
https://blog.daliansky.net/Common-problems-and-solutions-in-macOS-Catalina-10.15-installation.html
```

## FIREWOLF OS X PE 9 使用手册

```
https://pe.firewolf.app/manual/chapter3/launcherfirstview.html
```

## Clover的用法

```
https://www.misonsky.cn/28.html
```

## Booting the OS X installer on LAPTOPS with Clover

```
https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/
```

## MaciASL / Wiki / Patching Syntax Grammar

```
https://sourceforge.net/p/maciasl/wiki/Patching%20Syntax%20Grammar/
```

## Patching LAPTOP DSDT/SSDTs

```
https://www.tonymacx86.com/threads/guide-patching-laptop-dsdt-ssdts.152573/
```

## Using Clover to hotpatch ACPI

```
https://www.tonymacx86.com/threads/guide-using-clover-to-hotpatch-acpi.200137/
```

## Clover EFI Bootloader v5.1 r5127 四叶草黑苹果引导程序

```
https://heipg.cn/clover/clover-efi-bootloader-v5-1-r5127.html
```

## -V进mac不滚代码直接卡++加号或者加号下几行代码Error allocating 0x11c8d pages at......卡住解决方案

```
https://imacos.top/2019/08/12/1033/
```