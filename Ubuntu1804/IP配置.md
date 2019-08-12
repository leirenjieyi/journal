##配置临时IP,默认网关，DNS

    sudo ip addr add 10.102.66.200/24 dev enp0s25 	#配置临时IP

    ip link set dev enp0s25 up	#激活连接
    ip link set dev enp0s25 down	#关闭连接

    ip address show dev enp0s25	#查看enp0s25 IP
    ip address show lo		#查看loopback

    sudo ip route add default via 10.102.66.1	#设置临时默认网关
    ip route show					#查看路由表

    nameserver 8.8.8.8		#设置DNS
    ip addr flush eth0		#清除eth0的所有配置



##配置DHCP

创建或编辑 /etc/netplan/99_config.yaml 文件

network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: true


然后执行 sudo netplan apply


##配置静态地址

    network:
    version: 2
    renderer: networkd
    ethernets:
        enp0s25:
        addresses:
            - 192.168.0.100/24
        gateway4: 192.168.0.1
        nameservers:
            search: [example.com, sales.example.com, dev.example.com]
            addresses: [1.1.1.1, 8.8.8.8, 4.4.4.4]

然后执行 sudo netplay apply

