# ip link - 网络设备配置

## ip link set(s)
您可以使用ip链接请求多个参数更改。 如果您请求多个参数更改，但任何一个更改失败，则ip在失败后立即中止，因此失败之前的参数更改已完成并且没有被撤消在中止。 这是唯一使用ip命令会使系统处于不可预测状态的情况。 解决方案是避免通过一个IP链接设置调用更改多个参数。 根据需要使用尽可能多的单个ip链接设置命令您想要的动作。

### 参数

* dev NAME (default) --- NAME specifies the network device to operate on
* up / down --- change the state of the device to UP or to DOWN
* arp on / arp off --- change NOARP flag status on the device
Note that this operation is not allowed if the device is already in the UP state. Since neither the ip utility nor the kernel check for this
condition, you can get very unpredictable results changing the flag while the device is running. It is better to set the device down
then issue this command.
* multicast on / multicast off --- change MULTICAST flag on the device.
* dynamic on / dynamic off --- change DYNAMIC flag on the device.
* name NAME --- change name of the device.
* txqueuelen NUMBER / txqlen NUMBER --- change transmit queue length of the device
* mtu NUMBER --- change MTU of the device.
* address LLADDRESS --- change station address of the interface.
* broadcast LLADDRESS, brd LLADDRESS or peer LLADDRESS --- change link layer broadcast address or peer address in the case of a POINTOPOINT
interface

## ip link show,list,lst,sh,ls,l

### 参数

* dev NAME (default) --- NAME specifies network device to show.
If this argument is omitted, the command lists all the devices.
* up --- display only running interfaces.

### 输出

    #ip link ls
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enos01: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
        link/ether 00:0c:29:14:ac:9a brd ff:ff:ff:ff:ff:ff
    3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
        link/ether 00:0c:29:14:ac:a4 brd ff:ff:ff:ff:ff:ff

每行前面的序号是ifindex,这个序号唯一标识一个网络接口。如果你 `cat /proc/net/dev` 你将看到这些设备按照这个序号排列：

    #cat /proc/net/dev
    Inter-|   Receive                                                |  Transmit
    face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
        lo:   47424     608    0    0    0     0          0         0    47424     608    0    0    0     0       0          0
    enos01: 28397503   72395    0    0    0     0          0         0  3761839   60793    0    0    0     0       0          0
    eth1: 3167497   39610    0    0    0     0          0         0 10534381   47841    0    0    0     0       0          0


序号后面是网络接口名称，名称同样是唯一的，当然接口也会消失，如果对应的驱动没有模块没有安装。然后其他接口就会取代这个没有使用的名字。此外 ip link set dev name NEWNAME 命令可以修改设备名称。

接口名称也可以在“ @”符号后附加其他名称或关键字NONE。 这表示该设备以主/从设备关系绑定到另一台设备。 因此，通过该设备发送的数据包将被封装并通过主设备转发。 如果名称为NONE，则主设备未知。

名字后是mtu,及最大传输单元

qdisc（排队规则）显示接口上使用了哪种排队算法。 特别是关键字noqueue表示interface不对任何内容排队，关键字noop表示该接口处于黑洞模式，在该模式下，所有发送到它立即被丢弃。

详细统计可以通过 -s 选项：

    ip -s link ls dev eth0

