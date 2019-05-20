---
title: centos 安装 python3
date:   2019-03-25 23:28:43 +0800
---
环境: centos 7.5

#### 从源代码安装 python3
编译python需要 gcc 编译器、ziplib 等库文件，使用下面命令安装这些预先条件  
`yum install gcc openssl-devel bzip2-devel libffi-devel zlib-devel sqlite-devel`

下载 python 源代码  
`wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz`  
解压  
`tar -xzvf Python-3.7.2.tgz`  
进入解压目录  
`cd Python-3.7.2`  
 编译，其中 --prefix 选项可以将 python3 安装到该目录  
`./configure --prefix=/usr/local/python3`  
安装  
`make && make install`  
添加python3 的符号链接   
`ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3`  
查看是否安装成功  
`python3 -V`  
---  
#### 安装pip3
下载 get-pip.py 源文件  
`curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`  
调用 python3 安装  
`python3 get-pip.py`  
查看 pip3 是否已下载  
`ls /usr/local/python3/bin/pip3`  
如果上面的文件存在的话，创建它的软连接到 /usr/local/bin  
`ln -s /usr/local/python3/bin/pip3  /usr/local/bin/pip3`  
查看是否安装成功  
`pip3 -V`  
---  
#### 安装 virtualenv  
`yum install python-virtualenv`  
#### 参考  
[How to Install Python 3.7.2 on CentOS/RHEL 7/6 & Fedora 29-25](https://tecadmin.net/install-python-3-7-on-centos/)  
[CentOS 7 安装Python3、pip3](https://ehlxr.me/2017/01/07/CentOS-7-%E5%AE%89%E8%A3%85-Python3%E3%80%81pip3/)
