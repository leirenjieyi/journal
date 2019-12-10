## execute_process

执行进程
    这条命令可以执行系统命令，将输出保存到cmake变量或文件中。比如你想通过git命令读取版本号，在代码中使用。又比如你想列出某些文件的名称在代码中使用。

运行一个或多个给定的命令序列，每一个进程的标准输出流向（管道）下一个进程的标准输入。所有的流程使用一个单独的标准错误管道。

Options:

COMMAND

    A child process command line.

    CMake executes the child process using operating system APIs directly. All arguments are passed VERBATIM to the child process. No intermediate shell is used, so shell operators such as > are treated as normal arguments. (Use the INPUT_*, OUTPUT_*, and ERROR_* options to redirect stdin, stdout, and stderr.)

命令
    子进程命令行。CMake使用操作系统的APIs直接执行子进程。所有的参数逐字传递。没有中间脚本被使用，所以像>（输出重定向）这样的脚本操作符被当作普通参数处理。（使用INPUT_、OUTPUT_、ERROR_那些选项去重定向 stdin、stdout、stderr。）

WORKING_DIRECTORY
    The named directory will be set as the current working directory of the child processes.

工作路径
    指定的目录将被设置为子进程的当前工作目录。

TIMEOUT
    The child processes will be terminated if they do not finish in the specified number of seconds (fractions are allowed).

超时
    子进程如果在指定的秒数内未结束将被中断（允许使用分数）。

RESULT_VARIABLE
    The variable will be set to contain the result of running the processes. This will be an integer return code from the last child or a string describing an error condition.

结果变量
    变量被设置为包含子进程的运行结果。返回码将是一个来自于最后一个子进程的整数或者一个错误描述字符串。

OUTPUT_VARIABLE, ERROR_VARIABLE
    The variable named will be set with the contents of the standard output and standard error pipes, respectively. If the same variable is named for both pipes their output will be merged in the order produced.

输出变量，错误变量
    命名的变量将被分别设置为标准输出和标准错误管道的内容。如果为2个管道命名了相同的名字，他们的输出将按照产生顺序被合并。

INPUT_FILE, OUTPUT_FILE, ERROR_FILE
    The file named will be attached to the standard input of the first process, standard output of the last process, or standard error of all processes, respectively. If the same file is named for both output and error then it will be used for both.

输入文件，输出文件，错误文件
    命名的文件将分别与第一个子进程的标准输入，最后一个子进程的标准输出，所有进程的标准输出相关联。如果为输出和错误指定了相同的文件名，2个都将会被关联。

OUTPUT_QUIET, ERROR_QUIET
    The standard output or standard error results will be quietly ignored.

输出忽略，错误忽略
    标准输出和标准错误的结果将被静默地忽略。


```cmake
execute_process(
COMMAND git rev-parse --abbrev-ref HEAD
WORKING_DIRECTORY "@CMAKE_SOURCE_DIR@"
OUTPUT_VARIABLE GIT_BRANCH
OUTPUT_STRIP_TRAILING_WHITESPACE
)
```

上面代码将 CMAKE_SOURCE_DIR 的git 版本 保存到 GIT_BRANCH中