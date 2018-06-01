# 针对 ubuntu 系统IP地址配置
## 配置文件说明

1. IP地址配置文件 /etc/network/interface
2. dns地址配置文件 /etc/resolvconf/resolv.conf.d/base

## IP地址配置
### 动态DHCP获取

    $ sudo vim /etc/network/interfaces
    
    # interfaces(5) file used by ifup(8) and ifdown(8)
    auto lo
    iface lo inet loopback

    auto ens33
    iface ens33 inet dhcp

这里给 ens33配置为动态dhcp获取ip
### 配置静态IP
    # interfaces(5) file used by ifup(8) and ifdown(8)
    auto lo
    iface lo inet loopback

    auto ens32
    iface ens32 inet static
    address 10.10.10.4/24 #CIDR 表示方式

    auto ens33
    iface ens33 inet static
    address 10.10.10.5/24 #CIDR 表示方式
    gateway 10.10.10.1

    auto ens34
    iface ens34 inet static
    address 10.10.10.6    #传统表示方式
    netmask 255.255.255.0
    gateway 10.10.10.1

### 配置临时IP
    wayne@ubuntu1604:~$ sudo ifconfig ens33 10.10.10.123/24
    wayne@ubuntu1604:~$ ifconfig ens33
    ens33     Link encap:Ethernet  HWaddr 00:0c:29:92:39:e2  
          inet addr:10.10.10.123  Bcast:10.10.10.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe92:39e2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2138478 errors:0 dropped:1 overruns:0 frame:0
          TX packets:142051 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:414797983 (414.7 MB)  TX bytes:11642283 (11.6 MB)

这种方式只是临时配置ens33的IP地址，在系统重启或重启网卡后都会丢失。

## 多IP地址配置
### 临时配置多IP地址
    wayne@ubuntu1604:~$ sudo ip address add 192.168.0.10/24 dev ens33
    wayne@ubuntu1604:~$ sudo ip address show ens33
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 00:0c:29:92:39:e2 brd ff:ff:ff:ff:ff:ff
        inet 172.16.0.104/22 brd 172.16.3.255 scope global ens33
            valid_lft forever preferred_lft forever
        inet 192.168.0.10/24 scope global ens33
            valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fe92:39e2/64 scope link 
            valid_lft forever preferred_lft forever

### 删除临时IP地址

    wayne@ubuntu1604:~$ sudo ip address del 192.168.0.10/24 dev ens33
    wayne@ubuntu1604:~$ sudo ip address show ens33
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 00:0c:29:92:39:e2 brd ff:ff:ff:ff:ff:ff
        inet 172.16.0.104/22 brd 172.16.3.255 scope global ens33
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fe92:39e2/64 scope link 
        valid_lft forever preferred_lft forever

这种方式配置的ip地址是临时的，会随系统重启或网卡重启而丢失。

### 静态多IP地址

    # The loopback network interface
    auto lo
    iface lo inet loopback

    #The primary network interface
    auto eth0
    iface eth0 inet static
    address 8.8.8.2
    netmask 255.255.255.248
    gateway 8.8.8.1  要注意这里，多个不同IP段，只要1个gateway配置即可，其他IP不需要配置gateway

    auto eth0:0
    iface eth0:0 inet static
    address 8.8.8.3
    netmask 255.255.255.248

    auto eth0:1
    iface eth0:1 inet static
    address 6.6.6.130   注意这里，虽然这是不同的IP段，但是不需要配置gateway，只需要配置netmask即可
    netmask 255.255.255.224

## 重启网卡/网络

    sudo /etc/init.d/networking restart 或者 sudo service networking restart 是重启系统中的所有网卡，及重启网络服务。

    sudo ifdown ens33 && sudo ifup ens33 重启指定ens33网卡。（ifconfig ens33 up,ifocnfig ens33 down)