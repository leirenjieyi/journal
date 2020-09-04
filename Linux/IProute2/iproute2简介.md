在本节中，我们将全面介绍Alexey Kuznetsov的IPROUTE2软件包中的ip实用程序。我们将开始通过大多数ip命令进行了非常详细的介绍。我们将介绍ip的链接，地址，路由，规则，邻居，隧道和监视部分命令。多播部分将在IPv6和多播的“稍后添加”部分中介绍。

我们将首先介绍ip命令的所有命令语法。这是由于截至2000年2月的情况没有ip的手册页，并且该文档仅以Latex格式提供。如果您已阅读Alexey拥有的ip-cref.tex文档如1999年6月30日发布的IPROUTE2发行版中所写，然后可以随意浏览本节的大部分内容。马修已将讨论和示例，但核心摘自ip-cref.tex。如果您对示例有任何疑问或意见，或本节中的声明请直接向Matthew提出。另请注意，在您阅读本文时，ip命令可能已更改为2.3 / 2.4。随着变化，我们将尝试使该文档保持最新。

## IP 命令语法

    ip [ OPTIONS ] OBJECT [ COMMAND [ ARGUMENTS ]]

### OPTIONS
OPTIONS是一组多值修饰符，它们影响ip实用程序的一般行为和输出。 所有选项均以“-”开头字符，可以长短形式使用。 当前，以下选项可用:

1. -V, -Version --- print the version of the ip utility and exit.
2. -s, -stats, -statistics --- outp
    
    可以重复此选项以增加输出的详细程度。 通常，附加信息是设备或功能统计或值。 在许多情况下，应该以与/ proc /目录中的输出相同的意义来考虑输出值。值的名称与值本身不直接相关。 当我们使用其他网络设备驱动程序运行此选项时，请参阅稍后。

3. -f, -family {inet, inet6, link} --- enforce which protocol family to use.

如果不存在此选项，则从其他命令行参数中猜测要使用的协议系列输出。 如果其余命令行如果没有提供足够的信息来猜测协议族，则在以下情况下，ip命令会退回到默认的inet系列。网络协议或任何协议。 链接是一个特殊的系列标识符，表示不涉及网络协议。 有几个捷径对于此选项，它们如下所示：

    -4 --- shortcut for -family inet.
    -6 --- shortcut for -family inet6.
    -0 --- shortcut for -family link.

4. -o, -oneline --- format the output records as single lines by replacing any line feeds with the "\" character.

### OBJECT

    link --- physical or logical network device.
    address --- protocol (IPv4 or IPv6) address on a device.
    neighbour --- ARP or NDISC cache entry.
    route --- routing table entry.
    rule --- rule in routing policy database.
    maddress --- multicast address.
    mroute --- multicast routing cache entry.
    tunnel --- tunnel over IP.

所有对象的名称可以全部或缩写形式书写。 IE：地址可以缩写为addr或仅仅是a。 但是如果你在脚本中使用这些命令，您应该习惯于始终使用动作的完整规范。 使用缩写使它很容易在命令行上使用，但很难理解脚本中的逻辑。 因为您可能不是唯一要交易的人然后使用脚本，则应努力使它们尽可能完整。

### COMMAND
COMMAND指定对对象执行的操作。 可能的操作集取决于对象类型。 通常情况下，add，delete和show（列出）对象，但是某些对象将不允许所有这些操作，并且许多对象还有其他操作和命令。 请注意，可用于所有对象的命令语法帮助会打印出可用命令和参数的完整列表语法约定。 如果未给出命令，则假定为默认命令。 默认命令通常是show（list），但是如果无法列出类，则默认为打印命令语法帮助。

### ARGUMENTS
ARGUMENTS是特定于命令的命令选项的列表。 参数取决于命令和对象。 可以发出两种类型的参数：

    --- flags - which are abbreviated with a single keyword
    --- parameters - consisting of a keyword followed by a value

每个命令都有一个默认参数，如果省略了参数，则使用该默认参数。 IE：dev参数是ip link命令的默认参数 ，因此，ip链接列表eth0等效于ip链接列表dev eth0。 在下面的所有命令描述中，我们区分默认参数带标记（默认）。

正如我们上面提到的对象名称一样，所有关键字都可以缩写为前几个或后几个唯一字母。 这些捷径
当以交互方式使用ip时很方便，但是不建议在脚本中使用它们，并且在报告错误时请不要使用它们
或寻求帮助。 列出了官方允许的缩写以及该命令的第一次提及。