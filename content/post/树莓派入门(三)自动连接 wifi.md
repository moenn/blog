---
title: 树莓派入门(三)自动连接 wifi
date:   2018-09-22 20:26:43 +0800
---
既然树莓派3搭载了无线网卡，那么最方便的连接网络的方法就是 wifi 了，而且若你的 pc 连接了同一个网络的话还可以 ssh 上去控制它。但要注意树莓派可能连接不了 5G 频率的无线网络。要进行这一节的内容，首先确保你[安装好了 raspbian 系统](https://www.mengsheng.me/2018/09/21/%E6%A0%91%E8%8E%93%E6%B4%BE%E5%85%A5%E9%97%A8(%E4%B8%80)%E5%AE%89%E8%A3%85%E7%B3%BB%E7%BB%9F.html)，并且能够 [ssh 到树莓派](https://www.mengsheng.me/2018/09/21/%E6%A0%91%E8%8E%93%E6%B4%BE%E5%85%A5%E9%97%A8(%E4%BA%8C)ssh.html)。  

<!-- 配置完成后，树莓派每次开机都会自动连接设置好的无线网络。    -->
#### 方式一 图形界面 raspi-config
---  
如果你用的 raspbian 系统较新的话，就可以使用图形界面 `raspi-config` 来配置 wifi。
1. 先 ssh 进树莓派，输入 `sudo raspi-config` 进入设置界面。使用上下箭头进行选择，ENTER 键确认选择，依次进入 `2.Network Options` - `Wi-Fi` ，选择国家代码(CN)，然后在 SSID 界面输入无线网络的名称，在 Passphrase 界面输入密码，按回车键确认就配置好了。最后 ESC 返回命令行。

	![raspi-config.png](https://i.loli.net/2018/09/22/5ba64502eef8e.png)

	![country_code.png](https://i.loli.net/2018/09/22/5ba645029cfc6.png)

2. 输入 `sudo reboot` 重启树莓派，把树莓派的网线拔掉。
3. 在路由器管理界面或在 PC 上使用 `nmap` 查找树莓派的 ip ，若能找到则说明树莓派成功连入无线网络了。(若没有就是配置过程不正确，比如无线网络名字或密码打错)。
4. 使用上一步的 ip ssh 到树莓派，输入 `sudo ifconfig wlan0` 查看无线网络连接。  
	
	![ifconfig.png](https://i.loli.net/2018/09/22/5ba64502b479b.png)

#### 方式二 命令行
---
1. 安装 vim, `sudo apt-get install vim`.
2. 使用 vim 打开无线网络配置文件， `sudo vim /etc/wpa_supplicant/wpa_supplicant.conf`
3. 在 `wpa_supplicant.conf` 中写入以下信息，"testing"为你要连接的 wifi 名称，"testingPassword" 为密码，把这两项替换为你的网络名称和密码。然后 `:wq` 保存退出。
	```
	network={
    	ssid="testing"
    	psk="testingPassword"
	}
	```
4. 输入 `sudo reboot` 重启树莓派，下面的步骤就和方式一相同了。