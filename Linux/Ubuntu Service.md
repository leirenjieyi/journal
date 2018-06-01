## what is service 
按照man service的说明, service本身是个命令, 这个service命令是用来启动service服务的, 其语法格式为:

    service SCRIPT COMMAND [OPTIONS]

其解释为: service运行一个位于/etc/init.d/下的脚本SCRIPT, 或者是一个位于/etc/init下upstart程序. upstart是ubuntu中用来代替以前的sysvinit的启动程序(笔者猜测可能是由于以前svsvinit中叫做startup, 所以现在较upstart).

__service 不一定要随系统启动__

## 手动添加 service
其实添加一个服务很简单, 只需要添加一个脚本到/etc/init.d/并赋予它可执行权限即可. 如:

    sudo touch /etc/init.d/hello
    chmod +x /etc/init.d/hello

这是ubuntu就认为有个叫hello的服务了. 可以试试键入sudo service hell 再敲TAB键, 这时候应该就可以tab出来hello了, 这说明系统已经识别出来它是一个服务了. 如果此时报错: hello.service not found, 则可能需要执行一下:

    sudo update-rc.d hello defaults

下面来测试一下, 在hello中加入一行:

    #!/bin/bash
    echo "hello"

第一行的"#!/bin/bash"一定要有, 否则有可能会报错.

然后运行命令:

    sudo service hello start

这时便会打印输出hello(如果没有打印可以尝试用sudo systemctl status sss.service查看). 如果hello中的命令为echo "hello" $1, 则会打印hello start. 可见, 我们平时输入的sudo service xxx start中的start, 也就是man中说的COMMAND, 只不过是service传给xxx服务的第一个参数而已.

至此, 我们已经有了一个可以简单显示hello的服务, 但是它不会自动启动, 这就如前文所说的, 服务不一定非要随开机自启动的. 后文会介绍如何添加自启动.

__在script前加入 set -e能够使脚本自动检测到返回0的语句（及错误语句），并退出脚本，这是一个技巧。__

## service start/stop

添加service的start / stop等, 其实很简单, 只需要在上文所建的/etc/init.d/hello加入:

```bash
case "$1" in
    start)
        echo start
        ;;
    stop)
        echo stop
        ;;
    restart)
        echo restart
        ;;
esac
```

在对应的case中进行想要的工作即可.

## 自动启动service

简单的说, 要让服务的自启动, 只需要在/etc/rc{RUNLEVEL}.d/中加入S12ServiceName的软链接, 指向/etc/init.d中对应的脚本(如本文的hello). 这里先且看说明, 稍后会介绍方法而不用手动一个个的添加:

说明:

    S12ServiceName中:
        1. 表示该服务随启动自动启动, 如果是K, 则表示Kill(杀死进程);
        2. 12表示优先级, 数越小, 越是先执行.
        3. ServiceName即服务名, 起始叫什么都行, 真正起作用的是软链接的目标, 不过一般最好与服务同名.
    其中的RUNLEVEL为系统的运行级别, 一般的linux分8个级别: 0-6和一个'S'级别.
        0代表关机(halt);
        6代表重启(restart);
        1级别是单用户模式(single),
        2-5各有不同. 但是在userlinux(包括ubuntu)中2-5级别是毫无差别的.
        'S'级别是一个比较特殊的级别, 他应该是先于其他级别运行的级别(这一点有待考证).

这里说明一下, 0-6级别的运行是互斥的, 而不是叠加运行, 也就是说如果进入(move into)4级别, 不是指0-3都要运行, 而只是完成4级别里所规定的服务.

如果要查看系统当前的运行级别可以使用命令:

runlevel

显示的数字就是当前运行级别, 一般ubuntu桌面版在我们平时使用时进入的应该是level 2 ,ubuntu中2-5级不做区分.


    wayne@ubuntu1604:/etc/init.d$ runlevel
    N 5
    wayne@ubuntu1604:/etc/init.d$ cd /etc/rc5.d/
    wayne@ubuntu1604:/etc/rc5.d$ ll S02ssh 
    lrwxrwxrwx 1 root root 13 10月 26  2017 S02ssh -> ../init.d/ssh*

sshd服务的软连接，这样开机就会自动启动sshd服务

## 自动添加和删除 service
虽然可以按照上文方法来手动添加, 但是更简单的是使用update-rc.d命令来添加. 如:

    sudo update-rc.d hello defaults

如果要删除这个服务, 则:

    sudo update-rc.d hello remove

可以看到, 运行添加时, 终端会显示:

    update-rc.d: warning: /etc/init.d/hello missing LSB information
    update-rc.d: see <http://wiki.debian.org/LSBInitScripts>
    Adding system startup for /etc/init.d/hello ...
    /etc/rc0.d/K20hello -> ../init.d/hello
    /etc/rc1.d/K20hello -> ../init.d/hello
    /etc/rc6.d/K20hello -> ../init.d/hello
    /etc/rc2.d/S20hello -> ../init.d/hello
    /etc/rc3.d/S20hello -> ../init.d/hello
    /etc/rc4.d/S20hello -> ../init.d/hello
    /etc/rc5.d/S20hello -> ../init.d/hello

然后就可以看到在上述列表中的各个级别下, 创建了对应的软链接.

remove方法如果/etc/init.d/脚本还存在, 则需要使用-f参数:

    sudo update-rc.d -f hello remove

这样会删除各个软链接, 但是并不会删除/etc/init.d/下的脚本本身.

