# gitserver win10安装

__安装环境__ 
> 操作系统：win10 x64\
> Hyper-V

__定义__ \
>COPSSH 安装目录：$ICW \
>git 安装目录：$Git

__安装步骤__

* 下载并安装 [git for windows](https://git-scm.com/download/win)
* 下载并安装 [COPSSH](http://download.csdn.net/detail/u012678179/9277131)

* 配置COPSSH \
>安装完成后,在开始菜单中找到 [COPSSH Control Panel]() ,打开后状态显示为 running表示安装启动正常，点到user标签页，选择当前用户作为ssh链接用户，这里选用当前用户是因为做过了几次试验，发现单独创建用户作为ssh链接用在免密码push 或 pull 的时候一直报错，可能是win权限问题，没有仔细研究；选择好当前用户后点击 [Apply]() 
>
>在本机的git bin中或虚拟机linux系统中，使用`ssh-keygen`命令生成ssh的密钥：
```bash
wayne@wayne-VMachine:~$ ssh-keygen -t rsa -C "wu_hen1314@163.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/wayne/.ssh/id_rsa): /home/wayne/ssh_key
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/wayne/ssh_key.
Your public key has been saved in /home/wayne/ssh_key.pub.
The key fingerprint is:
SHA256:uNsMuLoXHXsA9GGYN3dKtRq9OajI00bec4tY84lHU2w wu_hen1314@163.com
The key's randomart image is:
+---[RSA 2048]----+
|   ..oo  ..      |
|    +oo.o...     |
|     o.+.oo.     |
|      o..+ oE    |
|     .o+S +o     |
|   ..*o+. o.     |
|    =.B.=...     |
|    .+ B B.o     |
|  o+. o =.+      |
+----[SHA256]-----+
```
>连输两次回车，将密码置为空。\
>将生成的 ssh_key ssh_key.pub拷贝到$ICW/home/Wayne/.ssh/中，并在.ssh中创建文件authorized_keys,将ssh_key.pub中的内容追加到authorized_keys文件中
```bash
$:cat ssh_key.pub >> authorized_keys
```
完成后，打开[COPSSH Control Panel]()->User，选择当前用户，点击[Keys]()按钮，可以看到刚才添加的公钥，点击[Apply]()按钮即可。\
到这里SSH的免密码登陆算是完成了，可以在win上或是linux上试一下
```bash
ssh Wayne@10.10.10.100
```
第一次会将记录保存在know_hosts，输入yes即可，之后就可免密码直接登陆。

__在COPSSH中集成Git__
>打开$ICW/home/Wayne/.bashrc,添加如下代码：
```bash
gitpath='/cygdrive/c/Program Files/Git/mingw64/bin'
gitcorepath='/cygdrive/c/Program Files/Git/mingw64/libexec/git-core' 
PATH=${gitpath}:${gitcorepath}:${PATH}
```
尝试过将这段代码放在$ICW/etc/profile中，不过不起作用，clone的时候提示找不到ssh_update_pack,不知道什么原因，而$ICW/etc/下又没有bashrc文件，所以只能放在用户目录下。

# 测试
可以直接在$ICW/home/Wayne/中创建文件夹，然后再文件夹中执行 `git init --bare`;也可以通过COPSSH自带的[Start a Unix Bash Shell]()或是通过linux ssh链接到COPSSH,通过如下命令创建：
```bash
wayne@wayne-VMachine:~/.ssh$ ssh Wayne@10.10.10.100
Last login: Mon Jul 24 17:54:21 2017 from 10.10.10.101

Wayne@DESKTOP-0MO1HVK ~
$ mkdir wayne.git

Wayne@DESKTOP-0MO1HVK ~
$ cd wayne.git

Wayne@DESKTOP-0MO1HVK ~/wayne.git
$ git init --bare
Initialized empty Git repository in D:/Application/ICW/home/Wayne/wayne.git/
```
创建完成后，通过clone进行克隆项目
```bash
wayne@wayne-VMachine:~$ git clone Wayne@10.10.10.100:wayne.git
Cloning into 'wayne'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
wayne@wayne-VMachine:~$ cd wayne
wayne@wayne-VMachine:~/wayne$ touch a b c
wayne@wayne-VMachine:~/wayne$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	a
	b
	c

nothing added to commit but untracked files present (use "git add" to track)
wayne@wayne-VMachine:~/wayne$ git add .
wayne@wayne-VMachine:~/wayne$ git commit -m "test"
[master (root-commit) f4e64fc] test
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 a
 create mode 100644 b
 create mode 100644 c
wayne@wayne-VMachine:~/wayne$ git push origin master
Counting objects: 3, done.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 202 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To Wayne@10.10.10.100:wayne.git
 * [new branch]      master -> master
```
测试通过，可以正常使用了。

# 知识点
* win10中可以在文件属性->安全->高级中设置文件属主权限等 
* ssh的链接格式 user@host ,从上面可以看到，在创建文件夹的时候直接在当前用户的home目录中创建 floder.git 文件夹，在clone时，可以直接通过这种方式url进行引用 `user@host:floder.git`
* cyg中引用本地win中的绝对路径一般都是这种格式 /`cygdrive`/C/Windows/System32

# 更改目录
一直引用$ICW/home/Wayne/下路径太长，不好存放，所以将Wayne的home目录直接放在D:\git下。\
修改用户的home目录,打开$ICW/etc/passwd文件，将用户Wayne的/home/Wayne路径修改为/cygdrive/D/git ,将原/home/Wayne中的.ssh文件夹和.bashrc等文件拷贝到D:\git下。
