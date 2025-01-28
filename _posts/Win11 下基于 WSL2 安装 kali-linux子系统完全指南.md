---
title: "Win11 下基于 WSL2 安装 kali-linux子系统完全指南"
date: 2025-01-28 10：20
categories: [kali, Build]
tags: [渗透]
---
### 引言
这篇文章完全出于装 13 的目的，因为我也不会渗透测试，只会到处找脚本，俗称 “脚本小子” 但是有工具了，就觉得自己很邢了~ 其次有一点先说一下，WSL2 的运行内存较高，空闲状态下约 800M 左右，电脑内存没有 16G 慎装，最后放一张安装完成的效果图，如下 👇 原生体验，和 Win系统共存，kali 系统就只启动一个菜单栏。
![[Pasted image 20250123235842.png]]
这种原生的体验如德芙丝滑
	版本 Windows 11家庭版（要是专业版或者企业版更好）
### 1、WSL 介绍

适用于 Linux 的 Windows 子系统可让开发人员按原样运行 GNU/Linux 环境 - 包括大多数命令行工具、实用工具和应用程序且不会产生传统虚拟机或双启动设置开销。

- 运行 Bash shell 脚本和 GNU/Linux 命令行应用程序，
- 使用类似于 Unix 的命令行 shell 调用 Windows 应用程序。
- 在 Windows 上调用 GNU/Linux 应用程序。
- 运行直接集成到 Windows 桌面的 [GNU/Linux 图形应用程序](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gui-apps)
- [将 GPU 加速](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/gpu-compute)用于机器学习、数据科学场景等

|wsl1和wsl2 比较功能|WSL 1|WSL 2|
|:--|:--|:--|
|Windows 和 Linux 之间的集成|✅|✅|
|启动时间短|✅|✅|
|与传统虚拟机相比，占用的资源量少|✅|✅|
|可以与当前版本的 VMware 和 VirtualBox 一起运行|✅|✅|
|托管 VM|❌|✅|
|完整的 Linux 内核|❌|✅|
|完全的系统调用兼容性|❌|✅|
|跨 OS 文件系统的性能|✅|❌|

总结：就是可以在 Win 环境下直接原生运行 linux ，而不需要安装虚拟机等外部程序，更多介绍可以看微软官方文档： [适用于 Linux 的 Windows 子系统文档](https://learn.microsoft.com/zh-cn/windows/wsl/)

---

### 2、开启系统功能

程序和功能这几项全部打开（hyper-V 开启的目的是为了桥接网络，因为linux子系统默认使用的本机下的虚拟子网，与宿主机并不在同一个局域网内，如果没有这个需求可以不用开）（hyper-v如果没有这一项别着急，第八点会说）

[!![[Pasted image 20250123235658.png]]
效果演示

---

### 3、安装WSL2

系统功能开启，电脑重启完成后开始安装 Linux 内核更新包和 Windows Subsystem for Linux 组件  
✅下载 Linux 内核更新包，然后安装 [适用于 x64 计算机的 WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)  
✅安装 Linux 必要组件（链接会打开电脑的微软商店进行安装）[Windows Subsystem for Linux](https://www.microsoft.com/store/productId/9P9TQF7MRM4R)安装（使用管理员权限）后，重新启动计算机  
✅打开 PowerShell，将 WSL 2 设置为默认版本，此时，WSL2的运行环境就完成了。

```
$ wsl --set-default-version 2
```

|WSL2基本命令|PowerShell 里执行|说明|
|---|---|---|
|列出已安装的 Linux 系统|wsl --list --verbose|查看安装在 Windows 计算机上的 Linux 发行版列表，其中包括状态|
|更新 WSL|wsl --update|将 WSL 版本更新到最新版本|
|检查 WSL 版本|wsl --version|检查有关 WSL 及其组件的版本信息|
|终止正在运行子系统|wsl --shutdown|立即终止所有正在运行的发行版和 WSL 2 轻量级实用工具虚拟机|
|停止单个子系统|wsl -t 名称|立即终止子系统，例 wsl -t kali-linux|
|卸载子系统|wsl --unregister 名称|立即卸载子系统并关联的所有数据,例 wsl --unregister kali-liunx|
|检查 WSL 状态|wsl --status|查看有关 WSL 配置的常规信息，例如默认发行版类型、默认发行版和内核版本|
|更改默认用户|名称 config --default-user 用户|例如：kali-linux config --default-user bt 会将 kali 发行版的默认用户更改为 bt 用户|

---

### 4、安装kali-linux

打开微软的商店搜索 linux 就可以看到许多的linux 系统，选择 Kali Linux 安装，如果你没找到可以打开这个链接 [Kali Linux](https://www.microsoft.com/store/productId/9PKR34TNCV07) （链接会打开电脑的微软商店进行安装）

- 安装完成后，开始菜单会有kali字样的快捷方式，运行过后有一个 cmd 在模拟终端，打开过后先设置用户名（注:root用户是默认存在的，这里不要输入 root ，随意设置一个你喜欢的用户名就行）然后设置密码就ok了。**输入密码是看不到的** 过程如下 👇
    
    ```
    Installing, this may take a few minutes...
    Please create a default UNIX user account. The username does not need to match your Windows username.
    For more information visit: https://aka.ms/wslusers
    Enter new UNIX username: bt  #设置用户名
    New password:                #设置密码
    Retype new password:         #确认密码
    passwd: password updated successfully
    Installation successful!
    bt@rog:~$ uname -a
    ```
    
    [![](https://i.tiax.cn/img-1259793745202305241151256.png)](https://i.tiax.cn/img-1259793745202305241151256.png)
    

此时系统就已经完成安装了，但是系统是最小化安装的，里面非常干净，什么工具都没有，先更新源，然后开始安装桌面环境和工具集。

✅先切换到 root 账户

```
$ sudo su
```

✅给 root 账户设置一个密码

```
$ passwd root
```

✅安装 vim 编辑器

```
$ apt install vim
```

---

#### 换源

✅换源（非必要） 这个里面默认的是官方源，建议不改，如果速度太慢的话可以改阿里云的源或中科大，不换就直接开始更新。（换哪个源就把哪行链接代码输进去）

```
$ vim /etc/apt/sources.list

#以下3个源地址，随意挑选一个

#阿里云
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib

#官方源
deb http://http.kali.org/kali kali-rolling main non-free contrib
deb-src http://http.kali.org/kali kali-rolling main non-free contrib

#中科大
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
```

✅更新系统与依赖，依次执行

```
$ apt update
# 用于更新软件包列表和元数据，让系统知道有哪些软件包需要更新

$ apt upgrade
# 用于升级所有已安装的软件包到最新版本，但不会安装新的软件包

$ apt dist-upgrade
# 用于升级系统的发行版，包括内核、系统库等，会安装新的软件包，并且会删除旧的软件包

$ apt clean
# 用于清理本地软件包缓存，删除已经下载的软件包，以释放磁盘空间
```

---

### 5、桌面环境安装

桌面环境安装，也就是引言里面的安装完成效果图 kali 菜单那样，这一步是非必要的，因为 kali 在终端也可以直接使用，看个人喜好，对于小白和只是玩玩的脚本小子，装一个桌面环境用来装 13 还是比较友好的。

#### Win-KeX

✅安装 kali 官方推荐的 WSL2 的桌面环境， [官方说明文档](https://www.kali.org/docs/wsl/win-kex/) ，执行 Win-KeX 安装命令，大概 3G 左右，要亿点点时间。

```
$ apt install -y kali-win-kex
```

然而我在安装后，启动几个模式均有问题，无缝模式的菜单栏乱跳，没有声音，有时无法启动等问题，均没有找到解决方案，所以不推荐使用 Win-KeX ，推荐使用 xfce4 环境 xfce 是类 UNIX 操作系统上的轻量级桌面环境（也就是引言中效果图的那种，只启动一个菜单栏，其实 win-kex 的无缝模式就应该是这种效果，但是始终有问题，不知道是 win11 的支持不好还是本身就有 BUG 找了很久也没解决方案，索性放弃）不过可以先试试你的环境是否可以正常使用（据说win10 下是没有问题的）如果不正常再继续看下文安装 xfce4 环境。

---

Win-KeX 支持三种模式的桌面环境，最后在终端中输入 kex 启动时先设置密码，然后会出现一个 view-only password，这个选择 n 即可。第一次运行防火墙弹出的提示，一定记得勾选公有网络，如果在安装之后 运行 Kex 发现 Xlaunch 启动但是没有客户端的情况，一定要去防火墙设置看一下是不是禁用了公有网络

- 窗口模式 （启动一个全屏覆盖桌面的应用，按 F8 可以切换窗口模式）

```
$ kex --win -s
```

- 无缝模式 （官方的演示效果是只启动 kali系统的任务栏）

```
$ kex --sl -s
```

- 增强模式 （启动win的远程桌面进行连接）

```
$ kex --esm --ip -s
```
### 6、系统汉化

汉化比较简单，执行下面两条命令即可，执行第二条命令后会出现一个选择语言的界面，使用键盘键（向下键）一直翻到最后，选择 zh_CN.UTF-8 UTF-8 这个，然后按空格键勾选，会有一个 * 号标识，然后按回车确定，再选择 zh_CN.UTF-8 UTF-8 ，然后使用 wsl --shutdown 重启子系统。

```
$ apt install locales
$ dpkg-reconfigure locales
```

---

### 7、工具安装

按照教程此时已经就完成了 kali 系统安装，桌面环境安装，以及系统汉化了。现在就可以开始装工具了，工具安装有 3 种选择。

#### 完整版

执行如下命令，完整版大约 25G 左右，需要亿点点时间，安装期间弹出的设置页面有OK选OK，有YES选YES即可，最后一个选 from inetd （不推荐安装完整版，体积太大，只是装13的话80%的工具都用不到，也不知道怎么用）

```
$ sudo apt install -y kali-linux-large
```

---

#### 单一安装

打开 kali 的官方工具列表，[Kali Linux Tools](https://www.kali.org/tools/) 需要哪个安装哪个，比如 nmap，单独安装会自动把该工具所需要的依赖一并安装了（推荐，需要哪个就装哪个，避免装13用不着的工具，节约磁盘空间）

```
$ sudo apt install nmap-common
```

---

#### 工具集

kali 官方给推荐的工具组合，比如无线攻击工具集，嗅探欺骗工具集，热门工具集top10等等组合（推荐，我自己也是用的这个） [工具集列表文档](https://www.kali.org/tools/kali-meta/)

```
$ sudo apt install kali-tools-802-11
```

---

### 8、网络桥接

WSL2允许用户自行指定用于 WSL2 的网卡，因此不再需要任何的脚本等等即可固定 WSL2 地址甚至支持 IPv6 和外部访问，wsl2 默认是使用宿主机下的子网，使用 $ ip a 命令，会看到 kali 系统目前是在子网里面，无法与宿主机在同一个局域网里，此办法使用 hyper-V 的【虚拟交换机管理器】创建一个虚拟交换机，然后来连接到WSL2 的网卡上，就可以让wsl2下运行的子系统和宿主机在同一个局域网了。
如果你是Windows家庭版，那么你肯定是找不到hyper-v的，因为这个功能根本就没有在家庭版中，所以我们需要一些特殊的操作
Hyper-V是Windows专业版专属功能，但大多数（除商业本）品牌机内置的Windows都是家庭版。只能通过命令开启，方法如下：（图是直接找的网上的）
## 安装Hyper-V

a.在桌面空白处右键-新建-文本文档，命名为 hyper-v.cmd，如图1-1

（提示：必须开启显示文件[扩展名](https://zhida.zhihu.com/search?content_id=216573387&content_type=Article&match_order=1&q=%E6%89%A9%E5%B1%95%E5%90%8D&zhida_source=entity)选项，否则无效，开启方法参考图1-2）

![](https://pic4.zhimg.com/v2-d36e36d33e05a9e941ee6a54a4ce3295_1440w.jpg)

1-1

![](https://pic4.zhimg.com/v2-bb3eb8ae6b787131c30a5193aaf7e5e5_1440w.jpg)

1-2

b.单击这个文件，右键-显示更多选项-编辑，如图1-3

![](https://pic1.zhimg.com/v2-12d02750711070e51f925f2d15cab258_1440w.jpg)

1-3

c.输入以下代码，如图1-4

```powershell
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

![](https://pica.zhimg.com/v2-c07047cc94c33ed40a45911f6062a87a_1440w.jpg)

1-4

d.点击文件-保存，如图1-5

![](https://pic3.zhimg.com/v2-36c54f48079dec905b3521d6c2ff6ec0_1440w.jpg)

1-5

e.单击这个文本文档，右键-以管理员权限运行，如图1-6

![](https://pic4.zhimg.com/v2-ea8d6007c094c6b0824c99b78950bc91_1440w.jpg)

1-6

f.等待加载完成即可，成功安装后，输入y并回车即可

✅管理员运行打开 【hyper-V 管理器】（找不到就直接搜索就行） >【虚拟交换机管理器】 > 【创建虚拟交换机(外部)】 然后如下图 👇 输入名称，选择你要桥接的网卡，然后确定即可，此时打开电脑的【网络连接】就应该是图 3 中，已经桥接了网络。
![[Pasted image 20250123235553.png]]

创建
![[Pasted image 20250123235607.png]]

新增虚拟交换机

[!![[Pasted image 20250123235617.png]]

网络桥接完成


✅在用户目录 %USERPROFILE% 下面创建一个配置文件 .wslconfig ，如上步骤，使用 Hyper-V 创建了一个叫做 WSLBridge 的外部虚拟交换机来给 WSL2 用，于是配置文件内容如下
**（ 用户目录%USERPROFILE% 就是C盘下的 C:\Users\root 我的用户名是root 实际根据你自己的来 ）**

```
[wsl2]
networkingMode=bridged
vmSwitch=WSLBridge
ipv6=true
```

✅kali子系统上不需要什么设置 .wslconfig 创建好了，直接执行 wsl --shutdown 重启子系统就可以了，此时再使用 $ ip a 查看网络，已经连接到宿主机所在的局域网
### 踩坑之路正式开始

当你兴致勃勃打开cmd输入以下命令，准备见证奇迹的时候
---结果等了半天，发现kali连网络都没有，甚至网络管理器都未运行，解决办法如下，按顺序操作
![](https://i-blog.csdnimg.cn/blog_migrate/54c00a31736a7970ad2c303f25890d00.png)

解决方法：启用NetworkManager

```bash
systemctl enable --now NetworkManager
```
这里可能会报错，那是可能因为systemd未启用，我们可以通过以下命令来配置wsl启用 systemd:

```bash
echo -e "[boot]\nsystemd=true" | sudo tee -a /etc/wsl.conf
```

配置后需要通过`wsl --shutdown`命令关闭wsl，来进行wsl的完整重启。**（一定要重启）
## 如何判断systemd是否启用成功

判断wsl是否已启用systemd，可通过以下命令查看：

```bash
ps --no-headers -o comm 1
```

如果命令返回的是`init`说明systemd未启用，如果是`systemd`那么你的systemd已启用成功了。  
[![image](https://img2022.cnblogs.com/blog/1114902/202210/1114902-20221001075757263-360215180.png)]
但是你还是连不上网，当你执行ip a命令时很可能看到这种情况![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a87a16b6a85c45cba0d0891b383b7c53.png)
这其实是不正确的情况
### 解决方案
事实上连接不上的原因其实很简单，根据上图的kali wsl中的网络配置，你会发现有一个10.255.255.254的ip，然后再看cmd连接kex时的**“10.255.255.254 找不到xxxxxxx”**

说明连接时，它寻找的ip是10.255.255.254，而这玩意是kali wsl的本机ip

所以很简单，我们只需要把要连接的ip修改为我们windows本机的wsl hyper-v的虚拟网卡地址就可以了，我这里是172.24.192.1（你的不一定是这个ip，不要照抄）
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fb0df6c863f04b368e0be4f72608f265.png)
修改的方法很简单，只需要修改kali wsl的/etc/resolv.conf文件（不是执行下面的命令，而是在kali的文件路径中找到这个文件，修改文件的内容）

nameserver 172.24.192.1
个时候你再尝试运行`wsl -d kali-linux kex --sl --wtstart -s`

你应该就会发现输出的信息当中，它连接的ip确实变成了172.24.192.1（具体情况取决于你实际的虚拟网卡地址）。这下kali应该就能连接到网络了，大功告成。
### 常见问题
#### 如何完全卸载

✅使用 wsl --unregister kali-liunx 命令删除已经安装的子系统

✅打开电脑【设置】>【应用】>【安装的应用】>【搜索中输入 linux 】> 【搜索出来的 linux 相关的全部卸载】

✅执行第 2 步骤 开启系统功能，去掉已经打开的功能，重启电脑，彻底卸载linux子系统和 wsl2 相关组件

✅使用 [Everything](https://www.voidtools.com/zh-cn/) 搜索 kali，Windows Subsystem for Linux，关键字，删除相关残留文件

---

#### systemd支持

- systemd是一个Linux操作系统中的系统和服务管理器，它是替代传统SysV Init的新一代初始化系统（启动Beef-Xss 就需要 systemd ，其他工具有些也需要）

✅在子系统目录 /etc/ 下面增加一个 wsl.conf 文件，文件内容如下，然后重启子系统。

```
[boot]
systemd=true
```

---

#### 脚本小子框架

- wifi钓鱼框架：[fluxion](https://github.com/FluxionNetwork/fluxion)

✅安装和启动 ，更多可参考这篇文章 [暴力穷举法破解Wi-Fi](https://www.cnblogs.com/KrisAntiSce/p/13155548.html)

```
$ git clone https://github.com/FluxionNetwork/fluxion.git
$ cd fluxion
$ ./fluxion.sh -i
$ ./fluxion.sh
```

- 中间人攻击框架；[xerosploit](https://github.com/IR0DayToday/Xerosploit-PY3)

✅安装和启动，使用方法参考作者github主页

```
$ git clone https://github.com/IR0DayToday/Xerosploit-PY3
$ cd xerosploit && sudo python install.py
$ sudo python3 xerosploit.py
```
#### 想从C盘搬到其他盘

- WSL2默认安装在C盘中的，包含子系统也都是，如果安装一个完整版这个体积就该有30G了，通常不想看到C盘爆满而导致系统出现问题，因此可以将 WSL2 安装的虚拟子系统移动到其他位置。

✅首先在对应的盘创建好目录，比如 D:\kali-linux，然后关闭正在运行的子系统

```
wsl --shutdown
```

✅然后使用命令导出Kali Linux 系统到D盘

```
wsl --export kali-linux d:\kali-linux.tar
```

✅等待导出完成，然后注销当前正在运行的 WSL2中的Kali Linux

```
wsl --unregister kali-linux
```

✅重新导入Kali Linux的系统到 WSL2

```
wsl --import kali-linux d:\kali-linux d:\kali-linux.tar --version 2
```

✅导入后默认 用户变成了root，重新设置下

`kali-linux config --default-user bt`
没了。。。。。