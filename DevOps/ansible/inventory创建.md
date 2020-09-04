# inventory (清单) 创建

inventory 支持 YAML 和 INI 格式文件，默认路径 “/etc/ansible/hosts”，也使用其他路径，通过 ansible -i inventorypath.

## 一般格式

INI格式：

    mail.example.com

    [webservers]
    foo.example.com
    bar.example.com

    [dbservers]
    one.example.com
    two.example.com
    three.example.com


YAML格式：

    all:
    hosts:
        mail.example.com:
    children:
        webservers:
        hosts:
            foo.example.com:
            bar.example.com:
        dbservers:
        hosts:
            one.example.com:
            two.example.com:
            three.example.com:

## 默认组

ansible 有两个默认组，all 和 ungrouped. 

all 包含所有主机列表，通常通过命令 `ansible -m ping all` 来测试inventory中哪些主机ping不通，这里的all就是 默认组all。

ungrouped 包含所有主机列表中未分组的主机，在上面的 hosts 配置中，ungrouped 组内包含 mail.example.com 主机。

## 主机在多个组、嵌套组

可以将一个主机放到多个组中，比如生产环境的一个webserver可以同时存在于 prod、atlanta、webservers 这三个组中。

    all:
    hosts:
        mail.example.com:
    children:
        webservers:
        hosts:
            foo.example.com:
            bar.example.com:
        dbservers:
        hosts:
            one.example.com:
            two.example.com:
            three.example.com:
        east:
        hosts:
            foo.example.com:
            one.example.com:
            two.example.com:
        west:
        hosts:
            bar.example.com:
            three.example.com:
        prod:
        hosts:
            foo.example.com:
            one.example.com:
            two.example.com:
        test:
        hosts:
            bar.example.com:
            three.example.com:

在上面的hosts中可以看到 one.example.com 存在于 dbserver、east、prod三个组中

上面的hosts还可以通过嵌套组的方式表示，因为 prod 和 east 内容相同，test和west相同，所以表示为如下：

    all:
    hosts:
        mail.example.com:
    children:
        webservers:
        hosts:
            foo.example.com:
            bar.example.com:
        dbservers:
        hosts:
            one.example.com:
            two.example.com:
            three.example.com:
        east:
        hosts:
            foo.example.com:
            one.example.com:
            two.example.com:
        west:
        hosts:
            bar.example.com:
            three.example.com:
        prod:
        children:
            east:
        test:
        children:
            west:

## 添加一段范围的主机

如果主机名有明显的模式可匹配，则可以通过范围的方式添加

INI:

    [nodes]
    node[01:50].example.com


YAML:

    webservers:
      hosts:
        node[01:50].example.com

## 主机变量

可以在inventory中给一个单独的主机指配变量，可以在playbook中使用这个变量

INI:

    [atlanta]
    host1 http_port=80 maxRequestsPerChild=808
    host2 http_port=303 maxRequestsPerChild=909

YAML

    atlanta:
        host1:
            http_port: 80
            maxRequestsPerChild: 808
        host2:
            http_port: 303
            maxRequestsPerChild: 909

ssh如果使用的非标准端口22的，可以通过 ：端口号 的方式添加在主机后

    host1:9527

连接用变量，按如下添加

    [targets]

    other1.example.com     ansible_connection=ssh        ansible_user=myuser
    other2.example.com     ansible_connection=ssh        ansible_user=myotheruser

## 清单别名

定义别名 

INI

    jumper ansible_port=5555 ansible_host=192.0.2.50

YAML

    hosts:
        jumper:
          ansible_port:9527
          ansible_host:192.168.0.5

## 组变量 (给很多主机定义一个变量)

如果一个组中的主机需要共享一个变量，可以给组定义一个变量：

INI：

    [atlanta]
    host1
    host2

    [atlanta:vars]
    ntp_server=ntp.atlanta.example.com
    proxy=proxy.atlanta.example.com


YAML:

    atlanta:
    hosts:
        host1:
        host2:
    vars:
        ntp_server: ntp.atlanta.example.com
        proxy: proxy.atlanta.example.com

但是，在执行之前，Ansible 始终将变量（包括库存变量）平展到主机级别。如果一个主机同时在多个组中，那么ansible 会从这些组中读取变量。如果你给不同的组中相同的变量名赋值了不同的值，那么将按照如下规则进行归并：

在一个剧本执行前，变量会默认的规避平展到指定host.叠加顺序如下：

1. all group
2. parent group
3. child group
4. host

默认情况下，Ansible 按字母顺序合并同一父/子级别的组，最后加载的组覆盖以前的组。例如，与a_group合并的b_group，b_group匹配的 var 将覆盖a_group。

您可以通过设置组变量更改此行为ansible_group_priority以更改相同级别的组的合并顺序（在父/子顺序解析后）。数字越大，合并的后期，赋予其更高的优先级。如果未设置，则此变量默认为 1。例如：

    a_group:
        testvar: a
        ansible_group_priority: 10
    b_group:
        testvar: b

在上面这个例子中，如果两个组有相同的优先级，结果就是 testvar==b,但a_group设置了高优先级，那么结果就是testvar==a

## 嵌套组变量

INI

    [atlanta]
    host1
    host2

    [raleigh]
    host2
    host3

    [southeast:children]
    atlanta
    raleigh

    [southeast:vars]
    some_server=foo.southeast.example.com
    halon_system_timeout=30
    self_destruct_countdown=60
    escape_pods=2

    [usa:children]
    southeast
    northeast
    southwest
    northwest


YAML

    all:
    children:
        usa:
        children:
            southeast:
            children:
                atlanta:
                hosts:
                    host1:
                    host2:
                raleigh:
                hosts:
                    host2:
                    host3:
            vars:
                some_server: foo.southeast.example.com
                halon_system_timeout: 30
                self_destruct_countdown: 60
                escape_pods: 2
            northeast:
            northwest:
            southwest:

## 组织变量结构

虽然你可以将变量放在主清单文件中，但分文件存储定义的变量可以变得更清晰。主机和组变量文件必须使用YAML格式，扩展名为 .yml .yaml .json

ansible 通过搜索与清单文件或剧本文件相关的路径来加载主机或组变量文件。如果你的清单文件在 /etc/ansible/hosts 包含一个主机 foosball 属于组 raleigh 和 webservers,那么将会有如下的yaml文件：

    /etc/ansible/group_vars/raleigh # can optionally end in '.yml', '.yaml', or '.json'
    /etc/ansible/group_vars/webservers
    /etc/ansible/host_vars/foosball


## 使用多清单源

可以通过命令行参数或是环境变量 ANSIBLE_INVENTORY 来指定多清单。

    ansible-play get_logs.yml -i inventory_1 -i inventory_2

您还可以通过将多个清单源和源类型合并到目录下来创建清单。这对于组合静态和动态主机以及将它们作为一个清单进行管理非常有用。以下清单结合了清单插件源、动态清单脚本和具有静态主机的文件：

    inventory/
    openstack.yml          # configure inventory plugin to get hosts from Openstack cloud
    dynamic-inventory.py   # add additional hosts with dynamic inventory script
    static-inventory       # add static hosts and groups
    group_vars/
        all.yml              # assign variables to all hosts


`ansible-playbook example.yml -i inventory`

如果存在可变冲突或组依赖其他库存源，则控制库存源的合并顺序可能很有用。清单根据文件名按字母顺序合并，以便可以通过向文件添加前缀来控制结果：

    inventory/
    01-openstack.yml          # configure inventory plugin to get hosts from Openstack cloud
    02-dynamic-inventory.py   # add additional hosts with dynamic inventory script
    03-static-inventory       # add static hosts
    group_vars/
        all.yml                 # assign variables to all hosts


## 主机连接 相关参数


    ansible_connection
        Connection type to the host. This can be the name of any of ansible’s connection plugins. SSH protocol types are smart, ssh or paramiko. The default is smart. Non-SSH based types are described in the next section.

    General for all connections:

    ansible_host
        The name of the host to connect to, if different from the alias you wish to give to it.
    ansible_port
        The connection port number, if not the default (22 for ssh)
    ansible_user
        The user name to use when connecting to the host
    ansible_password
        The password to use to authenticate to the host (never store this variable in plain text; always use a vault. See Variables and Vaults)

    Specific to the SSH connection:

    ansible_ssh_private_key_file
        Private key file used by ssh. Useful if using multiple keys and you don’t want to use SSH agent.
    ansible_ssh_common_args
        This setting is always appended to the default command line for sftp, scp, and ssh. Useful to configure a ProxyCommand for a certain host (or group).
    ansible_sftp_extra_args
        This setting is always appended to the default sftp command line.
    ansible_scp_extra_args
        This setting is always appended to the default scp command line.
    ansible_ssh_extra_args
        This setting is always appended to the default ssh command line.
    ansible_ssh_pipelining
        Determines whether or not to use SSH pipelining. This can override the pipelining setting in ansible.cfg.
    ansible_ssh_executable (added in version 2.2)
        This setting overrides the default behavior to use the system ssh. This can override the ssh_executable setting in ansible.cfg.

    Privilege escalation (see Ansible Privilege Escalation for further details):

    ansible_become
        Equivalent to ansible_sudo or ansible_su, allows to force privilege escalation
    ansible_become_method
        Allows to set privilege escalation method
    ansible_become_user
        Equivalent to ansible_sudo_user or ansible_su_user, allows to set the user you become through privilege escalation
    ansible_become_password
        Equivalent to ansible_sudo_password or ansible_su_password, allows you to set the privilege escalation password (never store this variable in plain text; always use a vault. See Variables and Vaults)
    ansible_become_exe
        Equivalent to ansible_sudo_exe or ansible_su_exe, allows you to set the executable for the escalation method selected
    ansible_become_flags
        Equivalent to ansible_sudo_flags or ansible_su_flags, allows you to set the flags passed to the selected escalation method. This can be also set globally in ansible.cfg in the sudo_flags option

    Remote host environment parameters:

    ansible_shell_type
        The shell type of the target system. You should not use this setting unless you have set the ansible_shell_executable to a non-Bourne (sh) compatible shell. By default commands are formatted using sh-style syntax. Setting this to csh or fish will cause commands executed on target systems to follow those shell’s syntax instead.

    ansible_python_interpreter
        The target host python path. This is useful for systems with more than one Python or not located at /usr/bin/python such as *BSD, or where /usr/bin/python is not a 2.X series Python. We do not use the /usr/bin/env mechanism as that requires the remote user’s path to be set right and also assumes the python executable is named python, where the executable might be named something like python2.6.
    ansible_*_interpreter
        Works for anything such as ruby or perl and works just like ansible_python_interpreter. This replaces shebang of modules which will run on that host.

    New in version 2.1.

    ansible_shell_executable
        This sets the shell the ansible controller will use on the target machine, overrides executable in ansible.cfg which defaults to /bin/sh. You should really only change it if is not possible to use /bin/sh (i.e. /bin/sh is not installed on the target machine or cannot be run from sudo.). 


## 非SSH 连接类型

Ansible 通过SSH执行 playbook ,但是不仅限于这种连接方式，给host指定 ansible_connection=< connector > ,能够修改连接类型。如下的非SSH连接器可用：

local

这个连接器能被用来在控制机自身部署playbook

docker

此连接器使用本地 Docker 客户端将 playbook 直接部署到 Docker 容器中。此连接器处理以下参数：


    ansible_host
        The name of the Docker container to connect to.
    ansible_user
        The user name to operate within the container. The user must exist inside the container.
    ansible_become
        If set to true the become_user will be used to operate within the container.
    ansible_docker_extra_args
        Could be a string with any additional arguments understood by Docker, which are not command specific. This parameter is mainly used to configure a remote Docker daemon to use. 

下面是如何立即部署到创建的容器的示例：

    - name: create jenkins container
    docker_container:
        docker_host: myserver.net:4243
        name: my_jenkins
        image: jenkins

    - name: add container to inventory
    add_host:
        name: my_jenkins
        ansible_connection: docker
        ansible_docker_extra_args: "--tlsverify --tlscacert=/path/to/ca.pem --tlscert=/path/to/client-cert.pem --tlskey=/path/to/client-key.pem -H=tcp://myserver.net:4243"
        ansible_user: jenkins
    changed_when: false

    - name: create directory for ssh keys
    delegate_to: my_jenkins
    file:
        path: "/var/jenkins_home/.ssh/jupiter"
        state: directory



# 分组依据

可以通过 功能、位置、测试|生产等方面进行分组。

