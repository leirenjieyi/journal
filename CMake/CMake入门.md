## cmake 中的大小写
* cmake中的命令不区分大小写
* cmake中的变量区分大小写
* cmake命令参数列表中使用 空格 来分割变量，而不是 逗号 
* 默认习惯一般是命令采用小写，变量采用大写。
* CMake中的变量无需声明，并且没有类型概念，这一点类似于python；变量可以认为都是全局的，哪怕在一个宏中定义的变量，也可以在宏的外面被访问到；所有的变量都是一个列表变量，下文在举例时会详细说明这一点；
* 有两种引用方式：对于变量值的引用，和直接引用这个变量本身(可以理解为一个变量对象)，使用方式分别是：

    ${varName} 和 varName


## cmake中的环境变量
* cmake 保留的变量都会以 CMAKE_ 开头

* CMAKE_SOURCE_DIR 这个变量是CMakeLists.txt文件所在的目录

* CMAKE_BINARY_DIR 这个变量是cmake命令执行时所在的目录

* CMAKE_BUILD_TYPE 指定编译时时debug 还是 release,可以通过 cmake .. -DCMAKE_BUILD_TYPE=DEBUG 的参数模式传递。

一般我们会在源路径下建立一个build文件夹，然后在build文件夹中执行 cmake ../。

## cmake 脚本模式
cmake 可以像python ide那样采用脚本模式解释执行

    cmake -P cmakelist文件


