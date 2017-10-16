# 静态链接库
## 原理
这类库的名字一般是libxxx.a，xxx为库的名字。利用静态函数库编译成的文件比较大，<b>因为整个函数库的所有数据都会被整合进目标代码中</b>，他的优点就显而易见了，即编译后的执行程序不需要外部的函数库支持，因为所有使用的函数都已经被编译进去了。当然这也会成为他的缺点，因为如果静态函数库改变了，那么你的程序必须重新编译。当要使用静态的程序库时，连接器会找出程序所需的函数，然后将它们拷贝到执行文件，由于这种拷贝是完整的，所以一旦连接成功，静态程序库也就不再需要了
## 生成
```c
// test.h
#ifndef _TEST_H
#define _TEST_H

void test_1();
void test_2();

#endif
```
```c
// test1.c
#include <stdio.h>
#include "test.h"

void test_1()
{
        print("%s","this is test 1 fun");
}
```
```c
//  test2.c
#include <stdio.h>
#include "test.h"

void test_2()
{
        print("%s","this is test 2 fun');
}
```
编译生成目标文件
```shell
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc -c test1.c test2.c
wayne@wayne-VMachine:~/Documents/code/cpp$ ls
test1.c  test1.o  test2.c  test2.o  test.h
```
将目标文件打包成静态库
```
wayne@wayne-VMachine:~/Documents/code/cpp$ ar -rsv libtest.a test1.o test2.o 
ar: creating libtest.a
a - test1.o
a - test2.o
wayne@wayne-VMachine:~/Documents/code/cpp$ ls
libtest.a  test1.c  test1.o  test2.c  test2.o  test.h
```
静态libtest.a文件生成成功，可以通过file命令查看文件相关信息是ar 文档：
```shell
wayne@wayne-VMachine:~/Documents/code/cpp$ file libtest.a 
libtest.a: current ar archive
```

## 使用静态库
```c
// main.c
#include "test.h"
int main()
{
        test_1();
        test_2();

        return 0;
}
```
编译生成可执行程序
```shell
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc -c main.c 
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc -o main main.o -L. -ltest
```
也可以直接编译
```shell
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc -o main main.c -L. -ltest
```
其中 -L 参数用于指定静态库所在的目录 -ltest指定库的名字。 \
<font size=3 color=#ffaa33>注意，linux库的命名是有约定的，采用libxxx.a, xxx才是库的名字，-l后面跟的是库的名字，而不是库的文件名；即-lxxx。

## ar 命令的详解
    ar命令可以用来创建、修改库，也可以从库中提出单个模块。
    库是一单独的文件，里面包含了按照特定的结构组织起来的其它的一些文件（称做此库文件的member）。 
    原始文件的内容、模式、时间戳、属主、组等属性都保留在库文件中。
　　下面是ar命令的格式： \
　　ar [-]{dmpqrtx}[abcfilNoPsSuvV] [membername] [count] archive files... \

　　  例如我们可以用ar rv libtest.a hello.o hello1.o来
    生成一个库，库名字是test，链接时可以用-ltest链接。该库中存放了两个模块hello.o和hello1.o。 </p>选项前可以有‘-'字符，也可以
没有。下面我们来看看命令的操作选项和任选项。

现在我们把{dmpqrtx}部分称为操作选项，而[abcfilNoPsSuvV]部分称为任选项。

　　{dmpqrtx}中的操作选项在命令中只能并且必须使用其中一个，它们的含义如下：
> d：从库中删除模块。按模块原来的文件名指定要删除的模块。如果使用了任选项v则列出被删除的每个模块。

>m：该操作是在一个库中移动成员。当库中如果有若干模块有相同的符号定义(如函数定义)，则成员的位置顺序很重要。如果没有指定任选项，任何指定的成员将移到库的最后。也可以使用'a'，'b'，或'i'任选项移动到指定的位置。 

>p：显示库中指定的成员到标准输出。如果指定任选项v，则在输出成员的内容前，将显示成员的名字。如果没有指定成员的名字，所有库中的文件将显示出来。

>q：快速追加。增加新模块到库的结尾处。并不检查是否需要替换。'a'，'b'，或'i'任选项对此操作没有影响，模块总是追加的库的结尾处。如果使用了任选项v则列出每个模块。 这时，库的符号表没有更新，可以用'ar s'或ranlib来更新库的符号表索引。 

>r：在库中插入模块(替换)。当插入的模块名已经在库中存在，则替换同名的模块。如果若干模块中有一个模块在库中不存在，ar显示一个错误消息，并不替换其他同名模块。默认的情况下，新的成员增加在库的结尾处，可以使用其他任选项来改变增加的位置。

>t：显示库的模块表清单。一般只显示模块名。 

>x：从库中提取一个成员。如果不指定要提取的模块，则提取库中所有的模块。 

下面在看看可与操作选项结合使用的任选项：

>a：在库的一个已经存在的成员后面增加一个新的文件。如果使用任选项a，则应该为命令行中membername参数指定一个已经存在的成员名。 

>b：在库的一个已经存在的成员前面增加一个新的文件。如果使用任选项b，则应该为命令行中membername参数指定一个已经存在的成员名。 

>c：创建一个库。不管库是否存在，都将创建。 

>f：在库中截短指定的名字。缺省情况下，文件名的长度是不受限制的，可以使用此参数将文件名截短，以保证与其它系统的兼容。 

>i：在库的一个已经存在的成员前面增加一个新的文件。如果使用任选项i，则应该为命令行中membername参数指定一个已经存在的成员名(类似任选项b)。 

>l：暂未使用 

>N：与count参数一起使用，在库中有多个相同的文件名时指定提取或输出的个数。 

>o：当提取成员时，保留成员的原始数据。如果不指定该任选项，则提取出的模块的时间将标为提取出的时间。 

>P：进行文件名匹配时使用全路径名。ar在创建库时不能使用全路径名（这样的库文件不符合POSIX标准），但是有些工具可以。 

>s：写入一个目标文件索引到库中，或者更新一个存在的目标文件索引。甚至对于没有任何变化的库也作该动作。对一个库做ar s等同于对该库做ranlib。 

>S：不创建目标文件索引，这在创建较大的库时能加快时间。 

>u：一般说来，命令ar r...插入所有列出的文件到库中，如果你只想插入列出文件中那些比库中同名文件新的文件，就可以使用该任选项。该任选项只用于r操作选项。 

>v：该选项用来显示执行操作选项的附加信息。 

>V：显示ar的版本。 
</font>

# 动态库
## 原理
在Linux操作系统中，普遍使用ELF格式作为可执行程序或者程序生成过程中的中间格式。ELF（Executable and Linking Format，可执行连接格式）是UNIX系统实验室（USL）作为应用程序二进制接口（Application BinaryInterface，ABI）而开发和发布的。工具接口标准委员会（TIS）选择了正在发展中的ELF标准作为工作在32位Intel体系上不同操作系统之间可移植的二进制文件格式。

ELF文件格式包括三种主要的类型：可执行文件、可重定向文件、共享库。

1.可执行文件（应用程序）\
可执行文件包含了代码和数据，是可以直接运行的程序。\
2．可重定向文件（\*.o）\
可重定向文件又称为目标文件，它包含了代码和数据（这些数据是和其他重定位文件和共享的object文件一起连接时使用的）。\
\*.o文件参与程序的连接（创建一个程序）和程序的执行（运行一个程序），它提供了一个方便有效的方法来用并行的视角看待文件的内容，这些*.o文件的活动可以反映出不同的需要。\
Linux下，我们可以用gcc -c编译源文件时可将其编译成*.o格式。\
3．共享文件（*.so）
也称为动态库文件，它包含了代码和数据（这些数据是在连接时候被连接器ld和运行时动态连接器使用的）。动态连接器可能称为ld.so.1，libc.so.1或者 ld-linux.so.1。\

这类库的名字一般是libxxx.M.N.so，同样的xxx为库的名字，M是库的主版本号，N是库的副版本号。当然也可以不要版本号，但名字必须有。相对于静态函数库，动态函数库在编译的时候并没有被编译进目标代码中，你的程序执行到相关函数时才调用该函数库里的相应函数，因此动态函数库所产生的可执行文件比较小。由于函数库没有被整合进你的程序，而是程序运行时动态的申请并调用，所以程序的运行环境中必须提供相应的库。动态函数库的改变并不影响你的程序，所以动态函数库的升级比较方便。linux系统有几个重要的目录存放相应的函数库，如/lib /usr/lib。动态库会在执行程序内留下一个标记指明当程序执行时，首先必须载入这个库。由于动态库节省空间，linux下进行连接的缺省操作是首先连接动态库，也就是说，如果同时存在静态和动态库，不特别指定的话，将与动态库相连接。

## 生成动态库
动态库源文件与上面静态库使用相同的源文件，编译时采用如下命令：
```
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc -o libtest.so -shared -fPIC test1.c test2.c 
wayne@wayne-VMachine:~/Documents/code/cpp$ ls
libtest.a  libtest.so  main  main.c  main.o  test1.c  test1.o  test2.c  test2.o  test.h
```
    -fPIC：表示编译为位置独立的代码，不用此选项的话编译后的代码是位置相关的所以动态载入时是通过代码拷贝的方式来满足不同进程的需要，而不能达到真正代码段共享的目的。
    -shared:表示生成共享库，及动态链接库。

## 使用动态库
### 动态链接
```shell
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc main.c -L. -ltest -o main
wayne@wayne-VMachine:~/Documents/code/cpp$ ldd main
	linux-vdso.so.1 =>  (0x00007ffce776f000)
	libtest.so => not found
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f0efb0e9000)
	/lib64/ld-linux-x86-64.so.2 (0x0000559d2d602000)
```
* 发现gcc在使用时是区分参数顺序的，gcc -o test -L. -ltest main.c 无法通过编译。\

上面通过使用ldd命令查看发现，libtest.so 找不到，这就涉及了linux中动态链接库的查找目录和顺序，会在下面的ldconfig和ldd部分进行说明。

### 动态加载
动态加载非常灵活，它依赖于一套Linux提供的标准API来完成。在源程序里，你可以很自如的运用API来加载、使用、释放so库资源。

以下函数在代码中使用需要包含头文件：dlfcn.h


    const char *dlerror(void)
    当动态链接库操作函数执行失败时，dlerror可以返回出错信息，返回值为NULL时表示操作函数执行成功。

    void *dlopen(const char *filename, int flag)
    用于打开指定名字（filename）的动态链接库，并返回操作句柄。调用失败时，将返回NULL值，否则返回的是操作句柄。

    void *dlsym(void *handle, char *symbol)
    根据动态链接库操作句柄（handle）与符号（symbol），返回符号对应的函数的执行代码地址。由此地址，可以带参数执行相应的函数。
    
    int dlclose (void *handle)
    用于关闭指定句柄的动态链接库，只有当此动态链接库的使用计数为0时，才会真正被系统卸载。


 void *dlopen(const char *filename, int flag)

filename:如果名字不以“/”开头，则非绝对路径名，将按下列先后顺序查找该文件。

    1.用户环境变量中的LD_LIBRARY值；
    2.动态链接缓冲文件/etc/ld.so.cache
    3.目录/lib,/usr/lib

flag表示在什么时候解决未定义的符号（调用）。取值有两个：

    1.RTLD_LAZY : 表明在动态链接库的函数代码执行时解决。
    2.RTLD_NOW :表明在dlopen返回前就解决所有未定义的符号，一旦未解决，dlopen将返回错误。

```c
// main.c
#include <dlfcn.h>
#include "test.h"
#define NULL 0
typedef void (*call)();

int main()
{
        call fn=NULL;
        void *hand=dlopen("./libtest.so",RTLD_LAZY);
        if(!hand)
        {
                return -1;
        }
        fn=(call)dlsym(hand,"test_1");
        fn();
        return 0;
}

```
```shell
wayne@wayne-VMachine:~/Documents/code/cpp$ gcc main.c -o main -ldl
wayne@wayne-VMachine:~/Documents/code/cpp$ ./main
this is test 1 funwayne
```
* 注意,编译过程需要链接libdl.so链接库

# ldconfig 和 ldd

## ldconfig
ldconfig是一个动态链接库管理命令。为了让动态链接库为系统所共享,需运行动态链接库的管理命令--ldconfig。 ldconfig 命令的用途,主要是在默认搜寻目录(/lib和/usr/lib)以及动态库配置文件/etc/ld.so.conf内所列的目录下,搜索出可共享的动态链接库(格式lib*.so*),进而创建出动态装入程序(ld.so)所需的连接和缓存文件。缓存文件默认为 /etc/ld.so.cache,此文件保存已排好序的动态链接库名字列表，为了让动态链接库为系统所共享，需运行动态链接库的管理命令ldconfig，此执行程序存放在/sbin目录下。ldconfig通常在系统启动时运行,而当用户安装了一个新的动态链接库，修改了ld.so.conf时,就需要手工运行这个命令。


linux下的共享库机制采用了类似于高速缓存的机制，将库信息保存在/etc/ld.so.cache里边。程序连接的时候首先从这个文件里边查找，然后再到ld.so.conf的路径里边去详细找

ldconfig命令行用法如下:

ldconfig [-v|--verbose] [-n] [-N] [-X] [-f CONF] [-C CACHE] [-rROOT] [-l] [-p|--print-cache]
[-c FORMAT] [--format=FORMAT] [-V] [-?|--help|--usage] path...

ldconfig可用的选项说明如下:

(1) -v或--verbose : 用此选项时,ldconfig将显示正在扫描的目录及搜索到的动态链接库,还有它所创建的链接的名字.

(2) -n : 用此选项时,ldconfig仅扫描命令行指定的目录,不扫描默认目录(/lib,/usr/lib),也不扫描配置文件/etc/ld.so.conf所列的目录.

(3) -N : 此选项指示ldconfig不重建缓存文件(/etc/ld.so.cache).若未用-X选项,ldconfig照常更新文件的连接.

(4) -X : 此选项指示ldconfig不更新文件的连接.若未用-N选项,则缓存文件正常更新.

(5) -f CONF : 此选项指定动态链接库的配置文件为CONF,系统默认为/etc/ld.so.conf.

(6) -C CACHE : 此选项指定生成的缓存文件为CACHE,系统默认的是/etc/ld.so.cache,此文件存放已排好序的可共享的动态链接库的列表.

(7)  -r ROOT : 此选项改变应用程序的根目录为ROOT(是调用chroot函数实现的).选择此项时,系统默认的配置文件 /etc/ld.so.conf,实际对应的为 ROOT/etc/ld.so.conf.如用-r /usr/zzz时,打开配置文件 /etc/ld.so.conf时,实际打开的是/usr/zzz/etc/ld.so.conf文件.用此选项,可以大大增加动态链接库管理的灵活性.

(8) -l : 通常情况下,ldconfig搜索动态链接库时将自动建立动态链接库的连接.选择此项时,将进入专家模式,需要手工设置连接.一般用户不用此项.

(9) -p或--print-cache : 此选项指示ldconfig打印出当前缓存文件所保存的所有共享库的名字.

(10) -c FORMAT 或 --format=FORMAT : 此选项用于指定缓存文件所使用的格式,共有三种: ld(老格式),new(新格式)和compat(兼容格式,此为默认格式).

(11) -V : 此选项打印出ldconfig的版本信息,而后退出.

(12) -? 或 --help 或--usage : 这三个选项作用相同,都是让ldconfig打印出其帮助信息,而后退出.

<b>ldconfig几个需要注意的地方</b>
 
1. 往/lib和/usr/lib里面加东西，是不用修改/etc/ld.so.conf的，但是完了之后要调一下ldconfig，不然这个library会找不到 
2. 想往上面两个目录以外加东西的时候，一定要修改/etc/ld.so.conf，然后再调用ldconfig，不然也会找不到 
比如安装了一个MySQL到/usr/local/mysql，mysql有一大堆library在/usr/local/mysql/lib下面，这时 就需要在/etc/ld.so.conf下面加一行/usr/local/mysql/lib，保存过后ldconfig一下，新的library才能在 程序运行时被找到。 
3. 如果想在这两个目录以外放lib，但是又不想在/etc/ld.so.conf中加东西（或者是没有权限加东西）。那也可以，就是export一个全局变 量LD_LIBRARY_PATH，然后运行程序的时候就会去这个目录中找library。一般来讲这只是一种临时的解决方案，在没有权限或临时需要的时 候使用。 
4. ldconfig做的这些东西都与运行程序时有关，跟编译时一点关系都没有。编译的时候还是该加-L就得加，不要混淆了。 
5. 总之，就是不管做了什么关于library的变动后，最好都ldconfig一下，不然会出现一些意想不到的结果。不会花太多的时间，但是会省很多的事。

<b>ldconfig提示“is not asymbolic link”的解决方法</b>

在编译的时候会出现以下错误：

ldconfig 
ldconfig: /lib/libdb-4.7.so is not a symbolic link

这是因为正常情况下libdb-4.7.so是一个符号连接，而不是一个实体文件，因此只需要把它改成符号连接即可
```
mv libdb-4.7.so libdb-4.so.7
ln -s libdb-4.so.7 libdb-4.7.so
```
## ldd 命令
ldd命令原理介绍：

1. 首先ldd不是一个可执行程序，而只是一个shell脚本
2. ldd能够显示可执行模块的dependency，其原理是通过设置一系列的环境变量，如下：LD_TRACE_LOADED_OBJECTS、LD_WARN、LD_BIND_NOW、LD_LIBRARY_VERSION、LD_VERBOSE等。当
LD_TRACE_LOADED_OBJECTS环境变量不为空时，任何可执行程序在运行时，它都会只显示模块的dependency，而程序并不真正执行。要不你可以在shell终端测试一下，如下：

    (1) export LD_TRACE_LOADED_OBJECTS=1

    (2) 再执行任何的程序，如ls等，看看程序的运行结果
3. ldd显示可执行模块的dependency的工作原理，其实质是通过ld-linux.so（elf动态库的装载器）来实现的。我们知道，ld-linux.so模块会先于executable模块程序工作，并获得控制权，因此当上述的那些环境变量被设置时，ld-linux.so选择了显示可执行模块的dependency。
4. 实际上可以直接执行ld-linux.so模块，如：/lib/ld-linux.so.2 --list program（这相当于ldd program）