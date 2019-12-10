服务器上调试程序时，有时会遇到所需资源被占用的情况，这时就要查一下是什么程序占用了资源。需要用到lsof与fuser指令。

lsof用于显示打开文件的信息

lsof  filename 显示打开指定文件的所有进程
lsof -a 表示两个参数都必须满足时才显示结果
lsof -c string   显示COMMAND列中包含指定字符的进程所有打开的文件
lsof -u username  显示所属user进程打开的文件
lsof -g gid 显示归属gid的进程情况
lsof +d /DIR/ 显示目录下被进程打开的文件
lsof +D /DIR/ 同上，但是会搜索目录下的所有目录，时间相对较长l

例如

vigar@vigar-laptop:~/下载/nice$ lsof /opt/google/chrome/chrome
COMMAND  PID  USER  FD   TYPE DEVICE SIZE/OFF    NODE NAME
chrome  4913 vigar txt    REG    8,6 68449288 1835017 /opt/google/chrome/chrome
chrome  4917 vigar txt    REG    8,6 68449288 1835017 /opt/google/chrome/chrome
chrome  4919 vigar txt    REG    8,6 68449288 1835017 /opt/google/chrome/chrome

lsof查特定端口

hpcentos5-/opt/test/bin> lsof -itcp:5000
COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
perl    8587 admin    3u  IPv4 510003      0t0  TCP hpcentos5:commplex-main (LISTEN)

 

fuser 的作用与lsof非常相似，也会将使用指定文件的进程信息全部列出

vigar@vigar-laptop:~/下载/nice$ fuser -v /opt/google/chrome/chrome
                     用户     进程号 权限   命令
/opt/google/chrome/chrome:
                     vigar      4913 ...e. chrome
                     vigar      4917 ...e. chrome
                     vigar      4919 ...e. chrome

     权限含义：

    　  c 将此文件作为当前目录使用。 
        e 将此文件作为程序的可执行对象使用。 
        r 将此文件作为根目录使用。 
        s 将此文件作为共享库（或其他可装载对象）使用

 

   fuser另一个非常有用的功能是-k,可以杀死指定进程（发SIGKILL信号）  
复制代码

vigar@vigar-laptop:~/下载/nice$ fuser aaa.avi
aaa.avi:             14097
vigar@vigar-laptop:~/下载/nice$ fuser -k aaa.avi
aaa.avi:             14097
vigar@vigar-laptop:~/下载/nice$ fuser aaa.avi
vigar@vigar-laptop:~/下载/nice$ 
可知占用aaa.avi的进程已经被干掉。当然，执行这条指令需要足够的权限，有root权限最好，其它人是无法杀死别人的进程的。

复制代码

 当利用lsof或fuser查出指定进程号后,即可用netstat -anp | grep pid查出其开放端口
复制代码

test> netstat -anp | grep 7172
(Not all processes could be identified, non-owned process info
tcp        0      0 :::8777                     :::*                        LISTEN      7172/java           
tcp        0      0 ::ffff:10.0.7.128:23564     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:23550     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:10629     ::ffff:10.0.3.91:1414       ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:23562     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:23572     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           

复制代码

 有时在机器上查出某个进程在跑，却不知道程序部署在何处，可以用ps -ef 查出pid,用lsof| grep pid，根据得到的信息推断出程序位置