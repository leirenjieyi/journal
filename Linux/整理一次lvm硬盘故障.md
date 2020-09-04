## 起因
Lenovo Systemx 3650 m5 液晶显示屏显示 ‘HDD 3 disk fault',ubuntu1604 能ping 通，但不能telnet,显示器上看没反应，黑屏；尝试重启后无法启动。(吐槽：一个程序员，感觉完全成了运维。。。，悲哀)

## 故障排除
从原因上看，是硬盘故障无疑，但就是想进一步确认，进bios,RAID管理界面显示那张盘 offline;但是这张盘才用不到3年，本着给公司省钱的思想，尝试重新插拔，插拔后，又在RAID上重新配置了一下虚拟硬盘，上电，果然系统起来了，瞬间觉得自己很伟大；

进系统，发现密码忘记了，进单用户模式：

选择 recovery 模式，按 'e' 进入编辑 grub 启动配置，修改 Linux 那一行 ，将 ro nomodeset recovery 改成 rw single init=/bin/bash ,然后 ctrl+x 或 f10,进入到单用户模式，修改用户密码，重启系统；
注意，重启系统 reboot 不好使，exit 会导致内核直接panic,直接卡死，需要用：
    
    reboot -f

## 删除旧内核版本

系统重启后密码改过来了，不过在grub界面发现有多个内核版本，手贱、追求完美，就决定删掉不用的：

    在Ubuntu内核镜像包含了以下的包。

    linux-image-: 内核镜像
    linux-image-extra-: 额外的内核模块
    linux-headers-: 内核头文件

首先检查系统中安装的内核镜像。

    $ dpkg --list|grep linux-image
    $ dpkg --list|grep linux-headers

在列出的内核镜像中，你可以移除一个特定的版本（比如3.19.0-15）。

    $ sudoapt-get purge linux-image-3.19.0-15
    $ sudoapt-get purge linux-headers-3.19.0-15

上面的命令会删除内核镜像和它相关联的内核模块和头文件。

注意如果你还没有升级内核那么删除旧内核会自动触发安装新内核。这样在删除旧内核之后，GRUB配置会自动升级来移除GRUB菜单中相关GRUB入口。

如果你有很多没用的内核，你可以用shell表达式来一次性地删除多个内核。注意这个括号表达式只在bash或者兼容的shell中才有效。

    $ sudoapt-get purge linux-image-3.19.0-{18,20,21,25}
    $ sudoapt-get purge linux-headers-3.19.0-{18,20,21,25}

如果GRUB配置由于任何原因在删除旧内核后没有正确升级，你可以尝试手动用update-grub2命令来更新配置。

    $ sudo update-grub2


## 硬盘以只读方式挂载着

查看系统日志，发现mysql提示无法写入数据，查看mount,发现故障硬盘的挂载方式是 ro,尝试修改//etc/mtab,提示文件不可修改

去网上查解决方法，大部分意思就是说，你这个盘有坏道，先修复一下：

    >umount /var/RecData
    >fsck -y /dev/sdb1
    提示一堆，大概意思就是这个盘是个空盘，没有数据。

    然后尝试手动挂载，提示错误：can't read superblock,尝试用超级块进行修复，这里可参考云笔记中的文章：《linux操作系统故障处理-ext4文件系统超级块损坏修复》
        mke2fs -n /dev/sdb
        e2fsck -b 32768 /dev/sdb

    但是每个超级块都不可读取，不得不的用 badblocks 检查一下：
        badblocks -v /dev/sdb > badlog.txt
    结果发现全是坏道。。。这下彻底放弃了，直接申请换新


## 涉及到lvm故障盘换新
换上盘重启后，系统黑屏，或是卡在 Loading initial ramdisk ,网上查原因，大部分说是fstab中的挂载点有问题，于是乎，又从recovery模式的单用户进去，修改了fstab,将原来对硬盘的上层的lv的挂载全部注释掉了，终于重启后，进入了系统。

不过发现另一个lv只能通过lvs看到，/dev/mapper下却没有，先确认一下这个lv有没有用到故障的物理盘：
```
    gsmrlab@ATPMON-CDR-COPY:~$ sudo lvdisplay -m
  --- Logical volume ---
  LV Path                /dev/ATPMON-CDR-COPY-vg/lv0
  LV Name                lv0
  VG Name                ATPMON-CDR-COPY-vg
  LV UUID                SBA8bB-58ZE-Engj-H0Hk-TlQD-04PB-KpsACH
  LV Write Access        read/write
  LV Creation host, time ATPMON-CDR-COPY, 2018-09-30 22:37:25 +0800
  LV Status              available
  # open                 1
  LV Size                93.13 GiB
  Current LE             23841
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:0
   
  --- Segments ---
  Logical extents 0 to 23840:
    Type		linear
    Physical volume	/dev/sda3
    Physical extents	0 to 23840
   
   
  --- Logical volume ---
  LV Path                /dev/ATPMON-CDR-COPY-vg/lv1
  LV Name                lv1
  VG Name                ATPMON-CDR-COPY-vg
  LV UUID                G1Xvja-z9JW-bECH-oaL8-Er4P-YbxD-bwTuX0
  LV Write Access        read/write
  LV Creation host, time ATPMON-CDR-COPY, 2018-09-30 22:37:48 +0800
  LV Status              available
  # open                 1
  LV Size                93.13 GiB
  Current LE             23841
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:1
   
  --- Segments ---
  Logical extents 0 to 23840:
    Type		linear
    Physical volume	/dev/sda3
    Physical extents	23841 to 47681
   
   
  --- Logical volume ---
  LV Path                /dev/ATPMON-CDR-COPY-vg/lv2
  LV Name                lv2
  VG Name                ATPMON-CDR-COPY-vg
  LV UUID                FePRp3-3DOY-e7vQ-8w4C-zAd4-mX8D-qujzN2
  LV Write Access        read/write
  LV Creation host, time ATPMON-CDR-COPY, 2018-09-30 22:38:01 +0800
  LV Status              available
  # open                 1
  LV Size                369.45 GiB
  Current LE             94580
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:2
   
  --- Segments ---
  Logical extents 0 to 94579:
    Type		linear
    Physical volume	/dev/sda3
    Physical extents	47682 to 142261
   
   
  --- Logical volume ---
  LV Path                /dev/vgmysql/lv3
  LV Name                lv3
  VG Name                vgmysql
  LV UUID                QLSwho-RdVB-1Omf-7moW-stYK-cPif-L4gGSG
  LV Write Access        read/write
  LV Creation host, time ATPMON-CDR-COPY, 2020-05-15 12:01:21 +0800
  LV Status              available
  # open                 1
  LV Size                2.18 TiB
  Current LE             571965
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:3
   
  --- Segments ---
  Logical extents 0 to 571964:
    Type		linear
    Physical volume	/dev/sdb
    Physical extents	0 to 571964

```

发现在/dev/mapper中没有的 lv 没有使用故障盘，那为什么不行呢？仔细看了一下，发现那个lv的 LV Status 是 not available,网上查找：

    vgchange -ay //激活所有lv

果然好用，这下/dev/mapper中出现了对应的lv,挂载之，这下改处理故障盘了，故障盘其实已经拆掉，重新换上了好盘。但lvm中不能自动识别，所以首先要把故障盘在lvm中的空间删掉：

    sudo vgreduce --removemissing

自动删除不存在的pv

然后直接

    sudo pvcreate /dev/sdb
    sudo vgcreate vgmysql /dev/sdb
    sudo lvcreate -l 100%VG -n lv3 vgmysql
    sudo mkfs.ext4 /dev/vgmysql/lv3
    sudo mount /dev/mapper/vgmysql-lv3 /var/RecData
成功