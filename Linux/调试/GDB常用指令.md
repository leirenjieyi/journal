# GDB调试总结
## 调试连接的静态库
代码出现core dump,发现是使用的一个静态库内部代码问题，想要连接静态库源码调试一下，用 list 文件名：行号 （help list 可以具体查看list用法)，提示找不到文件。
首先确定你的elf文件是否带有调试信息，可以通过 objdump -h elf文件 或 readelf -s elf文件，查看elf文件中是否有调试section,一般会有debug开头的几个小节，说明是包含调试信息的。
其次，所引用的静态库也要带有调试信息。
然后通过 readelf -p .debug_str elf文件名，来查看一下debug_str小节中的内容，看看是相对路径引用还是绝对路径引用。如果是相对路径，可以在gdb中通过directory ../libmy/ 添加静态库代码的相对路径，show directory查看搜寻路径。
如果是绝对路径，可以在gdb中采用set substitute-path /home/aaa/   /home/bbb/，之后，即使你的源文件1.cpp 放在 /home/bbb下面也是可以找到的了。因为gdb帮你做了字符串替换。

## 加载调试文件
启动gdb最常用的方法是使用一个参数，指定一个可执行程序：

`gdb program`

您还可以从指定的可执行程序和core文件开始:

`gdb program core`

如果您想调试正在运行的进程，则可以指定一个进程ID作为第二个参数:

`gdb program 1234` (除非你有各core文件也叫1234，gdb先考虑core文件)

设置读取符号表文件.

    -symbols file
    -s file
设置可执行文件

    -exec file
    -e file
从文件中读取符号表，并将其用作可执行文件

    -se file
设置core文件

    -core file
    -c file
连接到进程ID号，如使用附加命令

    -pid number
    -p number

将目录添加到搜索源文件和脚本文件的路径中

    -directory directory
    -d directory


指定源文件清除源文件：
    directory dirname …
    dir dirname …
    directory
    show directories

使用文件名作为要调试的程序。它是为它的符号和纯粹内存的内容而读取的。它也是在使用Run命令时执行的程序。如果您没有指定目录，并且在gdb工作目录中找不到该文件，gdb将使用环境变量路径作为要搜索的目录列表，就像shell在寻找要运行的程序时所做的那样。可以使用PATH命令为GDB和程序更改此变量的值。

可以使用file命令将未链接的对象.o文件加载到gdb中。您将无法“运行”一个对象文件，但您可以反汇编函数和检查变量。此外，如果底层BFD功能支持它，则可以使用GDB-WITH来使用此技术修补对象文件。请注意，在这种情况下，gdb既不能解释也不能修改重定位，因此分支和一些初始化变量似乎会出现在错误的位置。但是这个特性仍然时时方便。

    file filename

不带参数的文件使gdb放弃它在可执行文件和符号表中的任何信息(清空当前gdb的调试内容)。

    file

指定要运行的程序(但不是符号表)在文件名中找到。如果有必要，gdb会搜索环境变量路径来定位程序。省略文件名意味着丢弃可执行文件上的信息。

    exec-file [filename]

从文件文件名读取符号表信息。必要时搜索路径。使用FILE命令获取要从同一个文件运行的符号表和程序。省略文件名意味着丢弃当前symbol上的信息.

    symbol-file [filename]

指定core file或是清除core file。(注意，当您的程序实际上在GDB下运行时，核心文件将被忽略。所以，如果你运行你的程序，你想调试一个核心文件，你必须杀死子进程程序的运行。为此，使用“kill”命令)

    core-file [filename]
    core

添加或删除额外的symbol文件：

    add-symbol-file filename address
    add-symbol-file filename address [ -readnow | -readnever ]
    add-symbol-file filename address -s section address …

    remove-symbol-file filename
    remove-symbol-file -a address

```sh

(gdb) add-symbol-file /home/user/gdb/mylib.so 0x7ffff7ff9480
add symbol table from file "/home/user/gdb/mylib.so" at    .text_addr = 0x7ffff7ff9480
(y or n) y
Reading symbols from /home/user/gdb/mylib.so...done.
(gdb) remove-symbol-file -a 0x7ffff7ff9480
Remove symbol table from file "/home/user/gdb/mylib.so"? (y or n) y
(gdb)
```

查看调试文件信息：

    info files
    info target

查看共享库信息：

    info share regex
    info sharedlibrary regex



## 分离symbol文件

使用objcopy将symbol从可调式的程序中拷贝出来:

```sh
wayne@ubuntu1604:~/Documents/code/cpp/test/crc$ gcc -o crc16 helloworld.cpp ALE_CRC_16.c -g
wayne@ubuntu1604:~/Documents/code/cpp/test/crc$ objcopy --only-keep-debug crc16 crc16.debug
wayne@ubuntu1604:~/Documents/code/cpp/test/crc$ ls
ALE_CRC_16.c  ALE_CRC_16.h  crc16  crc16.debug  gdb.txt  helloworld.cpp 
```
使用strip从可执行文件中剔除其它数据，只剩下调试symbol:
```sh
wayne@ubuntu1604:~/Documents/code/cpp/test/crc$ cp crc16 crc16.strip.debug
wayne@ubuntu1604:~/Documents/code/cpp/test/crc$ strip --only-keep-debug crc16.strip.debug
wayne@ubuntu1604:~/Documents/code/cpp/test/crc$ ls
ALE_CRC_16.c  ALE_CRC_16.h  crc16  crc16.debug  crc16.strip.debug  gdb.txt  helloworld.cpp
```

这两种实现方式不同，一个是从带有调试信息的可执行文件中将调试信息拷贝出来；一种是将可执行文件中的其他信息剔除只剩下调试信息。通常将调试信息拷贝完成后使用strip filename剔除可执行文件中的调试信息使程序减小；当需要调试时单独加载symbol文件



## gdb调试日志

启用日志

    set logging on

禁用日志

    set logging off

设置日志文件,默认为gdb.txt

    set logging file [file]

默认情况gdb追加到logfile末尾，可以设置overwrite来覆盖记录

    set logging overwrite [on|off]

默认情况gdb输出到终端和日志中，设置重定向将只输出到logfile中
    
    set logging redirect [on|off]

显示当前logging setting 设置

    show logging

## 常用指令

运行：

    run
    r

运行，并停留在main:

    start

设置参数显示参数：

    set args [args1] [args2] ....
    show args

添加path:

    path [directory]
    show paths

设置环境变量：

    set environment varname [=value]
    unset environment varname
    show environment [varname]

设置工作目录：

    cd [directory]
    pwd

附加到进程：

    attach [process-id]
    detach

停止正在调试的进程：

    kill

线程信息：

    info threads

设置fork时，跟踪哪个：

    set follow-fork-mode [parent/child]
    show follow-fork-mode

查看变量类型：

    whatis [var]

执行完当前函数并返回：

    finish

直接返回，不再执行：

    return

### 断点

设置普通断点：

    break location

location：

    linenum
    filename:linenum
    -offset
    +offset
    function
    filename:function

下一条命令设置断点：

    break

条件断点：

    break … if cond

只运行一次断点：

    tbreak [args]

匹配正则表达式的断点：

    rbreak [regex]

查看断点信息：

    info breakpoints [list…]
    info break [list…]

### 监控断点

    watch [-l|-location] expr [thread thread-id] [mask maskvalue]
    info watch [list....]

### 清除断点

清除下一条指令的断点：

    clear

清除指定的断点：
    
    clear location
    clear function
    clear filename:function
    clear linenum
    clear filename:linenum

删除断点：

    delete [breakpoints] [list…]

### 禁用启用断点

disable [breakpoints] [list…]

    Disable the specified breakpoints—or all breakpoints, if none are listed. A disabled breakpoint has no effect but is not forgotten. All options such as ignore-counts, conditions and commands are remembered in case the breakpoint is enabled again later. You may abbreviate disable as dis.
enable [breakpoints] [list…]

    Enable the specified breakpoints (or all defined breakpoints). They become effective once again in stopping your program.
enable [breakpoints] once list…

    Enable the specified breakpoints temporarily. GDB disables any of these breakpoints immediately after stopping your program.
enable [breakpoints] count count list…

    Enable the specified breakpoints temporarily. GDB records count with each of the specified breakpoints, and decrements a breakpoint’s count when it is hit. When any count reaches 0, GDB disables that breakpoint. If a breakpoint has an ignore count (see Break Conditions), that will be decremented to 0 before count is affected.
enable [breakpoints] delete list…

    Enable the specified breakpoints to work once, then die. GDB deletes any of these breakpoints as soon as your program stops there. Breakpoints set by the tbreak command start out in this state. 

### 继续断点

continue [ignore-count]
c [ignore-count]
fg [ignore-count]

    Resume program execution, at the address where your program last stopped; any breakpoints set at that address are bypassed. The optional argument ignore-count allows you to specify a further number of times to ignore a breakpoint at this location; its effect is like that of ignore (see Break Conditions).

    The argument ignore-count is meaningful only when your program stopped due to a breakpoint. At other times, the argument to continue is ignored.

    The synonyms c and fg (for foreground, as the debugged program is deemed to be the foreground program) are provided purely for convenience, and have exactly the same behavior as continue. 

To resume execution at a different place, you can use return (see Returning from a Function) to go back to the calling function; or jump (see Continuing at a Different Address) to go to an arbitrary location in your program.

A typical technique for using stepping is to set a breakpoint (see Breakpoints; Watchpoints; and Catchpoints) at the beginning of the function or the section of your program where a problem is believed to lie, run your program until it stops at that breakpoint, and then step through the suspect area, examining the variables that are interesting, until you see the problem happen.

step

    Continue running your program until control reaches a different source line, then stop it and return control to GDB. This command is abbreviated s.

        Warning: If you use the step command while control is within a function that was compiled without debugging information, execution proceeds until control reaches a function that does have debugging information. Likewise, it will not step into a function which is compiled without debugging information. To step through functions without debugging information, use the stepi command, described below. 

    The step command only stops at the first instruction of a source line. This prevents the multiple stops that could otherwise occur in switch statements, for loops, etc. step continues to stop if a function that has debugging information is called within the line. In other words, step steps inside any functions called within the line.

    Also, the step command only enters a function if there is line number information for the function. Otherwise it acts like the next command. This avoids problems when using cc -gl on MIPS machines. Previously, step entered subroutines if there was any debugging information about the routine.
step count

    Continue running as in step, but do so count times. If a breakpoint is reached, or a signal not related to stepping occurs before count steps, stepping stops right away.
next [count]

    Continue to the next source line in the current (innermost) stack frame. This is similar to step, but function calls that appear within the line of code are executed without stopping. Execution stops when control reaches a different line of code at the original stack level that was executing when you gave the next command. This command is abbreviated n.

    An argument count is a repeat count, as for step.

    The next command only stops at the first instruction of a source line. This prevents multiple stops that could otherwise occur in switch statements, for loops, etc.
set step-mode
set step-mode on

    The set step-mode on command causes the step command to stop at the first instruction of a function which contains no debug line information rather than stepping over it.

    This is useful in cases where you may be interested in inspecting the machine instructions of a function which has no symbolic info and do not want GDB to automatically skip over this function.
set step-mode off

    Causes the step command to step over any functions which contains no debug information. This is the default.
show step-mode

    Show whether GDB will stop in or step over functions without source line debug information.
finish

    Continue running until just after function in the selected stack frame returns. Print the returned value (if any). This command can be abbreviated as fin.

    Contrast this with the return command (see Returning from a Function).
until
u

    Continue running until a source line past the current line, in the current stack frame, is reached. This command is used to avoid single stepping through a loop more than once. It is like the next command, except that when until encounters a jump, it automatically continues execution until the program counter is greater than the address of the jump.

    This means that when you reach the end of a loop after single stepping though it, until makes your program continue execution until it exits the loop. In contrast, a next command at the end of a loop simply steps back to the beginning of the loop, which forces you to step through the next iteration.

    until always stops your program if it attempts to exit the current stack frame.

    until may produce somewhat counterintuitive results if the order of machine code does not match the order of the source lines. For example, in the following excerpt from a debugging session, the f (frame) command shows that execution is stopped at line 206; yet when we use until, we get to line 195:

    (gdb) f
    #0  main (argc=4, argv=0xf7fffae8) at m4.c:206
    206                 expand_input();
    (gdb) until
    195             for ( ; argc > 0; NEXTARG) {

    This happened because, for execution efficiency, the compiler had generated code for the loop closure test at the end, rather than the start, of the loop—even though the test in a C for-loop is written before the body of the loop. The until command appeared to step back to the beginning of the loop when it advanced to this expression; however, it has not really gone to an earlier statement—not in terms of the actual machine code.

    until with no argument works by means of single instruction stepping, and hence is slower than until with an argument.
until location
u location

    Continue running your program until either the specified location is reached, or the current stack frame returns. The location is any of the forms described in Specify Location. This form of the command uses temporary breakpoints, and hence is quicker than until without an argument. The specified location is actually reached only if it is in the current frame. This implies that until can be used to skip over recursive function invocations. For instance in the code below, if the current location is line 96, issuing until 99 will execute the program up to line 99 in the same invocation of factorial, i.e., after the inner invocations have returned. 

### 调用堆栈

backtrace

bt

    Print a backtrace of the entire stack: one line per frame for all frames in the stack.

    You can stop the backtrace at any time by typing the system interrupt character, normally Ctrl-c.
backtrace n

bt n

    Similar, but print only the innermost n frames.
backtrace -n

bt -n

    Similar, but print only the outermost n frames.
backtrace full

bt full

bt full n

bt full -n


    Print the values of the local variables also. As described above, n specifies the number of frames to print.


## 打印

### 内存数据

x/nfu address

n:显示数量

f:显示格式(‘x’, ‘d’, ‘u’, ‘o’, ‘t’, ‘a’, ‘c’, ‘f’, ‘s’)

u:单位尺寸


b

    Bytes. 
h

    Halfwords (two bytes). 
w

    Words (four bytes). This is the initial default. 
g

    Giant words (eight bytes). 


x address (默认/1xw)
