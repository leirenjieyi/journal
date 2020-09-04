如果您的 Ansible 的清单随时间而波动，主机会根据业务需求进行旋转和关闭，则"如何构建库存"中描述的静态库存解决方案将不能满足您的需求。您可能需要跟踪来自多个源的主机：云提供商、LDAP、Cobbler 和/或企业 CMDB 系统。

Ansible 通过动态扩展清单系统集成了所有这些选项。Ansible 支持两种与外部清单连接的方法：清单插件和清单脚本。


清单插件利用最新的更新的 Ansible 核心代码。我们建议使用用于动态清单的脚本中的插件。您可以编写自己的插件以连接到其他动态库存源。

您仍然可以使用清单脚本。当我们实现清单插件时，我们通过脚本清单插件确保了向后兼容性。下面的示例演示了如何使用清单脚本。

如果您希望使用 GUI 来处理动态库存，Red Hat Ansible 清单数据库将与您的所有动态库存源同步，提供对结果的 Web 和 REST 访问，并提供图形库存编辑器。使用所有主机的数据库记录，您可以关联过去的事件历史记录，并查看哪些主机上次运行时发生故障。

## 模式一 通过Invertory脚本
Ansible 可通过python脚本动态的导入清单，这个脚本需要能接收两个参数 --list 和 --host < host >

--list 需要返回所有主机列表

--host < host > 需要返回host的全部参数

<https://github.com/ansible/ansible/tree/stable-2.9/contrib/inventory> 提供了通用的示例，包括配置和脚本。



### 清单脚本示例 Cobbler

Cobbler是通过PXE自动部署操作系统的一个服务端，主要提供DHCP,TFTP等服务，可批量装机；
通过<https://github.com/ansible/ansible/tree/stable-2.9/contrib/inventory> 下载 Cobbler 的配置文件 cobbler.ini 放到 /etc/ansible 下，然后下载 cobbler.py ,赋给可执行权限 chmod +x cobbler.py ，

ansible -i cobbler.py -m ping all



## 模式二 通过插件

默认情况下，Ansible 附带的大多清单插件都禁用，需要将清单列入 ansible.cfg 文件中才能正常工作。这是默认 Ansible 的插件配置：

    #enable_plugins = host_list, virtualbox, yaml, constructed

此列表还确定每个插件尝试分析清单源的顺序。列表中未包含的任何插件都未被考虑，因此您可以"优化"库存加载，将清单加载量最小化到实际使用内容。例如：

    [inventory]
    enable_plugins = advanced_host_list, constructed, yaml

向白名单中指定一个集合中的清单插件，需要使用全称：

    [inventory]
    enable_plugins = namespace.collection_name.inventory_plugin_name


启用清单插件后使用清单插件的唯一要求是提供要分析的清单源。Ansible 将尝试对提供的每个清单来源按顺序使用已启用的库存插件列表。一旦库存插件成功解析源，将跳过该源的任何剩余库存插件。

例如：提供的清单源为 亚马逊的虚拟主机 AWS ec2,则会从enable_plugins提供的插件列表中，从第一个开始匹配，直到匹配到能够解析 AWS ec2源的 aws_ec2插件为止。

若要开始使用具有 YAML 配置源的清单插件，请创建一个包含有关插件的已接受文件名架构的文件，然后添加插件：plugin_name。每个插件都记录任何命名限制。例如，aws_ec2插件必须以 aws_ec2.(yml|yaml)命名。

    plugin:aws_ec2


有很多可用的 inventory plugin ,可以参考 <https://docs.ansible.com/ansible/latest/plugins/inventory.html#inventory-plugins> 

或者通过命令

`ansible-doc -l -t inventory` 查看支持 auto inventory 的插件。

使用：

当 enable_plugins 中启用了某个插件，他就会使用对应的插件去解析 ansible -i 后面带的配置文件。
如果使用了某个插件 ，并且在 /etc/ansible 下有对应的插件的 .yml文件，则可直接运行 ansible -i xxx.yml 命令。

例如，使用 nmap 插件，配置 enable_plugins= nmap,编写 /etc/ansible/nmap.yml 文件：

nmap.yml

    ---
      name: nmap
      strict: False    #如果True，则使无效条目成为致命错误，False 则跳过并继续。
      cache:True
      address: 192.168.1.0/24

可以执行 `ansible -i nmap.yml all -m ping` 测试。不过nmap这个很慢，单个主机测试需要6s左右。

