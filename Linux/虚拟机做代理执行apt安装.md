## 虚拟机安装tinyproxy做代理，解决服务器无法连接外网进行软件包的安装

_实验室部署了几台ubuntu 1604的服务器，但是没有外网环境，手头有一台笔记本，一个可以做热点的4G手机_

本打算直接在本子上的win10装一个代理，发现很麻烦，没有现成可用的软件，只能迂回一下，在win10中的VMware中的ubuntu1604上安装了tinyproxy做代理服务器,这里详细说一下配置的步骤：

**1.配置虚拟机网卡**
    
虚拟机需要至少两块网卡，一块设置成桥接，一块设置成NAT；解释下为什么需要这样：

首先，物理机win10通过手机的wifi热点连接外网，VMware中的ubuntu通过NAT,共享物理机的无线上网。这里不能使用桥接，因为VMware的桥接是统一设置的，要么选自动要么指定一个网卡做网桥，如果这里选择了桥接，那所有桥接就只能绑定到无线网卡，这样服务器就无法和虚拟机通信了，所以桥接只能绑定到有线网卡。

其次，桥接要绑定到有线网卡，这样在虚拟机里将桥接的网卡ip设成和服务器同网段的，就能互相访问。

连接示意图如下：

<img src=../2017/img/daili.png>

    安装tinyproxy并配置
    sudo apt install tinyproxy
    sudo vim /etc/tinyproxy.conf
    注释掉
    #listen 192.168.0.1 
    这样就会监听所有网卡
    启动服务
    sudo systemctl start tinyproxy.service
    查看一下是否有端口监听
    sudo netstat -anp|grep 8888
    


设置apt的代理方式：

可以通过配置文件
sudo vi /etc/apt/apt.conf
Acquire::http::proxy "http://192.168.1.4:8888/";

或通过命令行参数：

sudo apt install make -o Acquire::http::proxy="http://192.168.1.4:8888/"