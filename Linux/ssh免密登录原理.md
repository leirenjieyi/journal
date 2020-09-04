# ssh (Secure Shell) 安全shell

提到安全shell,那之前肯定有不安全的，那就是telnet,telnet是明文传输，安全性很差。

SSH 使用端口 22，Linux下的安装包为openssh-server

## 对称加密和不对称加密

对称加密算法中，数据加密和解密采用的都是同一个密钥，因而其安全性依赖于所持有密钥的安全性。

非对称加密算法使用两把完全不同但又是完全匹配的一对钥匙（即一把公开密钥或加密密钥和专用密钥或解密密钥）—公钥和私钥。在使用不对称加密算法加密文件时，只有使用匹配的一对公钥和私钥，才能完成对明文的加密和解密过程。

SSH 正是使用了非对称加密的技术

## ssh非对称加密登录过程

<img src=./imgs/2160538013-5c7656118be4d.png>

初始状态：topgun终端要登录Server服务器，发起连接请求ssh work@server.com

    1. 服务端运行有ssh服务，并持续监听22号端口，因此可以生成一对公钥和私钥；此时将公钥返回给客户端
    2 .客户端使用公钥，对登录密码进行加密，（如服务器work用户密码为xxx），生成公钥加密字符串
    3. 客户端将公钥加密字符串发送给服务端
    4. 服务端使用私钥，解密公钥加密字符串，得到原始密码
    5. 校验密码是否合法（此为本机work密码）
    6. 返回登录结果给客户端：成功登录或密码错误

在非对称加密中，由于只有公钥会被传输，而私钥是服务端本地保存，因此即便公钥被监听，也无法拿到原始密码，从而登录服务器。


## ssh 中间人攻击

在非对称加密中可以有效保护登录密码不被泄漏，但这是在建立连接到真实服务器的情况下。设想一下，如果供给者并不监听密码或公钥，而是直接伪装成服务器呢：

<img src=./imgs/2898564151-5c765a0513308.png>

在该示例图中，存在Hacker服务器劫持了你的ssh建连请求（如通过DNS劫持等方式），导致你与Hacker机器的连接一切正常，因此它能拿到你的明文密码，并通过明文密码来攻击真实的服务端。

那么SSH采用了非对称的加密方式，是怎么解决这个问题的呢？

    [work@client.com: ~]$ ssh work@server.com
    The authenticity of host 'server.com (10.10.10.24)' can't be established.
    RSA key fingerprint is ad:2e:92:41:6f:31:b1:c1:35:43:eb:df:f1:18:a1:c1.
    Are you sure you want to continue connecting (yes/no)?  yes
    Warning: Permanently added 'server.com,10.10.10.24' (RSA) to the list of known hosts.
    Password: (enter password) 

在这个认证信息中，可以看到提示：无法确认主机server.com (10.10.10.24)的真实性，不过知道它的公钥指纹，是否继续连接？

输入yes继续连接后，就会确认该服务器为可信任服务器，然后添加到known_hosts文件中，下次不用再次确认，然后跳转到输入密码的验证阶段。这种简单粗暴的方式相当于让我们肉眼比对来判断目标服务器是否是真实服务器，我觉得并不是最理想的方式，希望后续会有更完美的认证方式。

    之所以用fingerprint（公钥指纹）代替key，主要是key过于长（RSA算法生成的公钥有1024位），很难直接比较。所以，对公钥进行hash生成一个128位的指纹，这样就方便比较了。


## ssh 免密登录 

ssh提供一种免密登录的方式：公钥登录。

<img src=./imgs/1093209063-5c7673e3a12f9.png>


1. 在客户端使用ssh-keygen生成一对密钥：公钥+私钥
2. 将客户端公钥追加到服务端的authorized_key文件中，完成公钥认证操作
3. 认证完成后，客户端向服务端发起登录请求，并传递公钥到服务端
4. 服务端检索authorized_key文件，确认该公钥是否存在
5. 如果存在该公钥，则生成随机数R，并用公钥来进行加密，生成公钥加密字符串pubKey(R)
6. 将公钥加密字符串传递给客户端
7. 客户端使用私钥解密公钥加密字符串，得到R
8. 服务端和客户端通信时会产生一个会话ID(sessionKey)，用MD5对R和SessionKey进行加密，生成摘要（即MD5加密字符串）
9. 客户端将生成的MD5加密字符串传给服务端
10. 服务端同样生成MD5(R,SessionKey)加密字符串
11. 如果客户端传来的加密字符串等于服务端自身生成的加密字符串，则认证成功
12. 此时不用输入密码，即完成建连，可以开始远程执行shell命令了

## 实现免密登录

ssh-keygen 密钥生成工具，生成密钥保存在~/.ssh文件夹下。

常用参数：
* -t: 密钥生成类型，rsa\dsa,默认为 rsa
* -f:指定存放私钥的文件名，公钥为私钥文件名.pub。默认为id_rsa
* -p:指定passphrase(私钥的密码)，用于确保私钥的安全，默认为空
* -C:备注，默认为user@hostname

一般直接生成密钥，全部回车：

    ssh-keygen 
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/xxx/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/xxx/.ssh/id_rsa.
    Your public key has been saved in /home/xxx/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:E6BYD7X1jfk9kkxRB2CR5pjRbNzfZ9Gh9bqQ5O7kTfs xxx@ubuntu
    The key's randomart image is:
    +---[RSA 2048]----+
    |    o.o .  +=*o++|
    |   o + + ..=B.+oo|
    |  . . o . +B+. .+|
    |         .o*.+ .=|
    |        S   O +..|
    |         . . o o |
    |            o o  |
    |           + o . |
    |            o o.E|
    +----[SHA256]-----+


.ssh 文件夹下应该有以下文件，authorized_keys 和 config 如果没有，可手动创建

    id_rsa
    id_rsa.pub
    authorized_keys
    known_hosts
    config

因为一台机器，既可能是客户端，又可能是服务端，因此同时存在 authorized_keys(服务端时使用，存储着客户端的公钥)，known_hosts（客户端使用，存储着已知服务端的公钥指纹）

config 文件为客户端使用，可以针对不同的服务端采用不同的私钥配置

    $vim ~/.ssh/config
    Host 192.168.10.100
    User abc
    IdentifyFile ~/.ssh/abc_rsa

    Host 172.16.0.23
    User xxx
    IdentifyFile ~/.ssh/xxx_rsa

这样，登录192.168.10.100的时候就会使用abc_rsa的密钥对，登录172.16.0.23的时候，就使用 xxx_rsa的密钥对。

在客户端生成密钥后，将其公钥追加到服务端的authorized_keys文件中。

## win10 ssh

在 Windows Server 2019 和 Windows 10 1809 中，OpenSSH 客户端和 OpenSSH 服务器是可单独安装的组件。 具有这些 Windows 版本的用户应使用以下说明来安装和配置 OpenSSH。

若要安装 OpenSSH，请启动“设置”，然后转到“应用”>“应用和功能”>“管理可选功能”。

扫描此列表，查看 OpenSSH 客户端是否已安装。 如果没有，则在页面顶部选择“添加功能”，然后：

    若要安装 OpenSSH 客户端，请找到“OpenSSH 客户端”，然后单击“安装”。
    若要安装 OpenSSH 服务器，请找到“OpenSSH 服务器”，然后单击“安装”。

安装完成后，请返回“应用”>“应用和功能”>“管理可选功能”，你应当会看到列出的 OpenSSH 组件。

### 通过 PowerShell 安装 OpenSSH

若要使用 PowerShell 安装 OpenSSH，请首先以管理员身份启动 PowerShell。 若要确保 OpenSSH 功能可以安装，请执行以下操作：
PowerShell

    Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'

    # This should return the following output:

    Name  : OpenSSH.Client~~~~0.0.1.0
    State : NotPresent
    Name  : OpenSSH.Server~~~~0.0.1.0
    State : NotPresent

然后，安装服务器和/或客户端功能：
PowerShell

    # Install the OpenSSH Client
    Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

    # Install the OpenSSH Server
    Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

    # Both of these should return the following output:

    Path          :
    Online        : True
    RestartNeeded : False

### 卸载 OpenSSH

若要使用 Windows“设置”卸载 OpenSSH，请启动“设置”，然后转到“应用”>“应用和功能”>“管理可选功能”。 在已安装功能的列表中，选择 OpenSSH 客户端或 OpenSSH 服务器组件，然后选择“卸载”。

若要使用 PowerShell 卸载 OpenSSH，请使用以下命令之一：
PowerShell

    # Uninstall the OpenSSH Client
    Remove-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

    # Uninstall the OpenSSH Server
    Remove-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

如果卸载 OpenSSH 时该服务正在使用中，则在删除后可能需要重启 Windows。
SSH 服务器的初始配置

若要配置 OpenSSH 服务器以在 Windows 上首次使用，请以管理员身份启动 PowerShell，然后运行以下命令来启动 SSHD 服务：
PowerShell

    Start-Service sshd
    # OPTIONAL but recommended:
    Set-Service -Name sshd -StartupType 'Automatic'
    # Confirm the Firewall rule is configured. It should be created automatically by setup.
    Get-NetFirewallRule -Name *ssh*
    # There should be a firewall rule named "OpenSSH-Server-In-TCP", which should be enabled
    # If the firewall does not exist, create one
    New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22

### 首次使用 SSH

在 Windows 上安装 OpenSSH 服务器后，可以从安装了 SSH 客户端的任何 Windows 设备上使用 PowerShell 来快速测试它。 在 PowerShell 中，键入以下命令：
PowerShell

    Ssh username@servername

到任何服务器的第一个连接都将生成类似以下内容的消息：

    The authenticity of host 'servername (10.00.00.001)' can't be established.
    ECDSA key fingerprint is SHA256:(<a large string>).
    Are you sure you want to continue connecting (yes/no)?

回答必须是“yes”或“no”。 回答 Yes 会将该服务器添加到本地系统的已知 ssh 主机列表中。

系统此时会提示你输入密码。 作为安全预防措施，密码在键入的过程中不会显示。

在连接后，你将看到类似于以下内容的命令 shell 提示符：

    domain\username@SERVERNAME C:\Users\username>

Windows OpenSSH 服务器使用的默认 shell 是 Windows 命令行解释器。


## 三步实现免密登录

    ssh-keygen  #生成密钥对文件
    ssh-copy-id user@host #将公钥拷贝到想要免密的主机上
    ssh host #直接免密登录

## 允许 root 用户 ssh 登录

    vi /etc/ssh/sshd_config
    将 PermitRootLogin 设为 yes,重启ssh服务
    PermitRootLogin yes
    systemctl restart ssh.service