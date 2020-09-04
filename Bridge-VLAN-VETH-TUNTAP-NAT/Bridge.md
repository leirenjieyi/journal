Bridge（桥）是 Linux 上用来做 TCP/IP 二层协议交换的设备，与现实世界中的交换机功能相似。Bridge 设备实例可以和 Linux 上其他网络设备实例连接，既 attach 一个从设备，类似于在现实世界中的交换机和一个用户终端之间连接一根网线。当有数据到达时，Bridge 会根据报文中的 MAC 信息进行广播、转发、丢弃处理。

## 二层工作原理回顾
二层的工作原理主要是通过arp 形成的 mac-port 映射表，将对应的mac地址和交换机的端口做映射就行数据转发。

## 创建 bridge

创建 bridge 的方法有很多，最常见的是使用 bridge-utils 提供的 brctl 工具。


### 使用 brctl
    
    # brctl addbr bridge_name   //创建一个bridge
    # brctl addif bridge_name eth0  //将eth0添加到桥
    # brctl show    //显示当前网桥和连接的网卡
    # ip link set dev bridge_name up    //使网桥生效
    # ip link set dev bridge_name down  //使网桥失效
    # brctl delbr bridge_name           //删除网桥


### 使用 iproute2 

    # ip link add name bridge_name type bridge  //创建网桥
    # ip link set bridge_name up                //使网桥生效
    # ip link eth0 up                           //使eth0生效
    # ip link set eth0 master bridge_name       //添加eth0网卡到网桥
    # bridge link                               //显示存在的网桥和关联的网卡
    # ip link set eth0 nomaster                 //从网桥移除网卡
    # ip link set eth0 down                     //使eth0失效
    # ip link delete bridge_name type bridge    //删除网桥


