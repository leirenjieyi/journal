# Remote development over SSH

微软提供的远程开发插件，Remote - SSH,此外还有 Remote Development,不过后者下载量是772K,前者是1.4M，所以选择了Remote - SSH.

安装完成后，左下角的状态栏多出 >< 标记，鼠标点击后可以通过ssh远程连接linux,打开文件夹直接进行开发，比以前使用ftp rsync 好用多了。。。

可以在 win 下的 .ssh/config中配置好ssh登录，做好免密，免密可参考 Linux 下的ssh免密登录。配置好后点击 ><  选择Remote-ssh:connect to host... ，就能够看到配置好的主机，选择既可。