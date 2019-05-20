---
title: 树莓派入门(二)ssh.md
date:   2018-09-21 23:28:43 +0800
---
在安装好 raspbian 系统后，可以准备 ssh 到树莓派上了。首先要开启 ssh 功能.自 Raspbian 在 2016年11月发布更新后，系统的 ssh 功能是默认关闭的。要开启 ssh 也很简单，只需在raspbian SD 卡的根目录新建一个名为 ssh （无后缀）的空白文件，然后将内存卡插入树莓派，开机后 raspbian 会自动开启 ssh 功能。 
### 需要的工具
* 一根网线
* 一台PC  

ssh 需要树莓派的 ip 地址。下面是获取地址的两种方式。  

---
### 方式一 利用无线路由器
如果你所在的网络使用了路由器，那么事情很简单。
1. 用网线将树莓派与路由器连接起来
2. 树莓派重启后会被路由器分配一个 ip 地址
3. 进入路由器管理界面(通常为 192.168.1.1)，查看树莓派的 ip。  

	![router_pi_ip.png](https://i.loli.net/2018/09/22/5ba5d52a6b8fc.png)

---
### 方式二 利用手机热点(windows 系统)
比方式一略复杂。我们知道连接到同一个手机热点的设备都在同一个局域网之下，我们要利用这一点。  

1. 通过网线将 PC 和树莓派连接到一起。
2. 开启手机热点，将PC 连接到该热点。
3. 按下 `win+x` 键，随后按 w 键进入网络设置，点击更改适配器选项进入网络连接页面。

	![adapter_setting.png](https://i.loli.net/2018/09/22/5ba5d589d6f93.png)

4. 在无线网络连接上点击鼠标右键 —— 属性， 在共享一栏的“允许其他网络用户通过此计算机的 internet 连接来连接(N)” 打勾，最后点击"确定"更改设置。

	![enable_share.png](https://i.loli.net/2018/09/22/5ba5d5b997154.png)

5. 这时树莓派跟 PC 应该在同一个局域网了。但要知道树莓派的 ip ,还需要借助一个命令行软件 [nmap](https://nmap.org/).这一步就下载并安装 nmap。安装完成后在 powershell 中键入 `nmap`，看是否安装成功。
6. 打开 powershell，输入 `ipconfig`，在回显内容中查找 `Ethernet adapter 以太网` 一栏的内容，将其中列出的 `IPv4 Address` 的值记录下来，它应该为 192.168.xxx.1 。(在我的例子中为 192.168.137.1)  

	![ipconfig.png](https://i.loli.net/2018/09/22/5ba5d627be025.png)  

7. 在 powershell 中输入 `nmap -sn  192.168.xxx.0/24` (在我的例子中为 `nmap -sn 192.168.137.0/24`)。等待几秒后，能看到标有 `(Raspberry Pi Foundation)` 的设备的 ip 地址，就是树莓派的地址了。 

	![nmap_result.png](https://i.loli.net/2018/09/22/5ba5d63dbf49d.png)

总结下来，就是利用手机热点作为路由器，pc 的无线网络分享给树莓派后两部设备处于同一局域网下，最后使用 `nmap` 寻找树莓派的地址就好了。

### ssh 
有了树莓派的地址，那么只需要一个 ssh 软件就能进入树莓派了。mac 和 linux 使用系统自带的终端即可，windows 则需下载 [putty](https://www.putty.org/) 这个工具。 
打开 putty，输入树莓派的 ip 地址和端口号 22 ，点击 open 连接。(可能会提示树莓派的指纹，确认即可)。  

![putty.png](https://i.loli.net/2018/12/22/5c1e17898c4a3.png)

连接成功后输入默认的用户名 `pi` 和默认密码 `raspberry` 就进入系统啦。
