1.提供信息的变量

    CMAKE_ARGC

    命令行运行cmake的时候传入的参数的个数

    CMAKE_ARGV0

    命令行运行时的第一个参数,以此类推CMAKE_ARG1 CMAKE_ARG2…

    CMAKE_AR

    编译使用的archive tool 的名称

    CMAKE_BINARY_DIR

    输出的最顶层目录,如何使代码内部编译的话则与变量CMAKE_SOURCE_DIR一致

    CMAKE_BUILD_TOOL

    内容与CMAKE_MAKE_PROGRAM一致

    CMAKE_CACHEFILE_DIR

    CMakeCache.txt所在的路径

    CMAKE_CACHE_MAJOR_VERSION

    创建CMakeCache.txt的cmake的Major版本号

    CMAKE_CACHE_MINOR_VERSION

    创建CMakeCache.txt的cmake的Minor版本号

    CMAKE_CACHE_PATCH_VERSION

    创建CMakeCache.txt的cmake的Patch版本号

        CMAKE_CFG_INTDIR

    CMAKE_COMMAND

    CMake可执行文件的全路径

    CMAKE_CROSSCOMPILING

    CMake是否正在执行交叉编译

    CMAKE_CTEST_COMMAND

    ctest的全路径

    CMAKE_CURRENT_BINARY_DIR

    当前的二进制输出目录，在cmake中每个add_subdirectory都会增加一个二进制输出目录，而这个变量则表示当前cmake正进入的那个subdirectory所对应的二进制输出目录

    CMAKE_CURRENT_LIST_DIR

    CMake当前执行的cmakelists.txt所在的目录

    CMAKE_CURRENT_LIST_FILE

    CMake当前执行的cmakelists.txt的全路径

    CMAKE_CURRENT_LIST_LINE

    当前执行cmakelists.txt所在的行

    CMAKE_CURRENT_SOURCE_DIR

    CMake当前处理的源码的目录

    CMAKE_DL_LIBS

    CMAKE_EDIT_COMMAND

    cmakegui或ccmake可执行程序所在目录，这个变量只有在cmake_generator被设置为makefile类型的时候才会被设置。可以图形化编辑cmake cache的程序

    CMAKE_EXECUTABLE_SUFFIX

    设置可执行文件生成的后缀名,如windows的.exe

    CMAKE_EXTRA_GENERATOR

    当用Eclipse、CodeBlock、KDevelop 这些编译器的时候，cmake生成makefiles文件和与上述ide相关的工程文件,而CMAKE_EXTRA_GENERATOR变量就是存储这些工程文件的生成器路径

    CMAKE_EXTRA_SHARED_LIBRARY_SUFFIXES

    动态链接库的附加后缀名，不同于CMAKE_SHARE_LIBRARY_SUFFIX,CMAKE_EXTRA_SHARED_LIBRARY_SUFFIXES用于在寻找导入依赖库的时候进行识别与分析

    CMAKE_GENERATOR

    当前使用的编译生成器

    CMAKE_GENERATOR_TOOLSET

    CMAKE_HOME_DIRECTORY

    顶层源码目录

    CMAKE_IMPORT_LIBRARY_PREFIX

    导入库所使用的前缀

    CMAKE_IMPORT_LIBRARY_SUFFIX

    导入库使用的后缀

    CMAKE_JOB_POOL_COMPILE

    CMAKE_JOB_POOL_LINK

    CMAKE_LINK_LIBRARY_SUFFIX

    连接静态库的后缀名

    CMAKE_MAJOR_VERSION

    CMake版本

    CMAKE_MAKE_PROGRAM

    指编译工具的全路径或者文件名，这个变量取决于CMAKE_GENERATOR。
    1. 如何使makefile的生成工具,那么这个变量被设置为 make、gmake或nmake
    2. 如果是Ninjia,被设置为ninjia
    3. 如果是xcode,被设置为xcodebuild
    4. 如果是visual studio,CMAKE_MAKE_PROGRAM将根据VS版本不同设置成一下exe的全路径
    + msbuild.exe (VS>10)
    + devenv.exe (VS 7,8,9)
    + VCExpress.exe (VS 8,9)
    + msdev.exe (VS 6)

    CMAKE_MINIMUM_REQUIRED_VERSION

    由函数cmake_minimum_required指定

    CMAKE_MINOR_VERSION

    CMake版本

    CMAKE_PARENT_LIST_FILE

    include当前目录的cmakelists.txt所在目录

    CMAKE_PATCH_VERSION

    CMake版本

    CMAKE_PROJECT_NAME

    工程名

    CMAKE_RANLIB

    CMAKE_ROOT

    cmake程序所在目录

    CMAKE_SCRIPT_MODE_FILE

    cmake 脚本模式下脚本的全路径

    CMAKE_SHARED_LIBRARY_PREFIX

    连接的动态库使用的前缀

    CMAKE_SHARED_LIBRARY_SUFFIX

    连接的动态库使用的后缀

    CMAKE_SHARED_MODULE_PREFIX

    连接的可加载模块使用的前缀

    CMAKE_SHARED_MODULE_SUFFIX

    连接的可加载模块使用的后缀

    CMAKE_SIZEOF_VOID_P

    void类型指针的大小

    CMAKE_SKIP_INSTALL_RULES

    cmake是否跳过生成安装规则

    CMAKE_SKIP_RPATH

    CMAKE_SOURCE_DIR

    顶层源码目录

    CMAKE_STANDARD_LIBRARIES

    标准的链接库，他们将被连接到每一个生成的可执行程序和动态链接库中

    CMAKE_STATIC_LIBRARY_PREFIX

    链接的静态链接库所使用的前缀

    CMAKE_STATIC_LIBRARY_SUFFIX

    链接的静态链接库所使用的后缀

2.控制CMake行为的变量

    BUILD_SHARED_LIBS

    如果设置为on,那么add_library将创建动态链接库

    CMAKE_ABSOLUTE_DESTINATION_FILES

    已经安装的文件列表

    CMAKE_BUILD_TYPE

    指定编译的配置

    CMAKE_COLOR_MAKEFILE

    指定makefile编译输出是否带颜色

    CMAKE_CONFIGURATION_TYPES

    指定支持多配置编译器的编译配置,比如visual studio 里边的配置集

    CMAKE_DEBUG_TARGET_PROPERTIES

    DEBUG配置所设置的属性

    CMAKE_DISABLE_FIND_PACKAGE_

    对于find_package中没有设置为require的类型的package，如果设置了这个变量为TRUE的话，那么会跳过这个查找

    CMAKE_FIND_LIBRARY_PREFIXES

    在查找链接库时候使用的前缀

    CMAKE_FIND_LIBRARY_SUFFIXES

    在查找链接库时候使用的后缀

    CMAKE_IGNORE_PATH

    find_xxx()中忽略的路径

    CMAKE_INCLUDE_PATH

    find_file()和find_path()中使用的路径

    CMAKE_INCLUDE_DIRECTORIES_BEFORE

    是否默认在include_directories()中添加一系列路径

    CMAKE_INSTALL_PREFIX

    安装的根目录

    CMAKE_LIBRARY_PATH

    find_library()所使用的路径

    CMAKE_MFC_FLAG

    Visual studio 中设置是否使用MFC
    1 在静态库中使用MFC
    2 在动态DLL中使用MFC
    0 使用标准windows库

    CMAKE_MODULE_PATH

    cmake modules的搜索路径

    CMAKE_PROGRAM_PATH

    find_program()的搜索路径

3.描述系统信息的变量

    APPLE

    如果运行在苹果系统，那么这个变量为TRUE

    BORLAND

    如果编译器为Borland compiler，那么这个变量为TRUE

    CMAKE_CL_64

    为TRUE，则说明使用64位的微软编译器

    CMAKE_COMPILER_2005

    为TRUE表示使用Visual studio 2005编译

    CMAKE_HOST_APPLE

    为TRUE则表示本机主机为Apple OS X系统

    CMAKE_HOST_SYSTEM_NAME

    本机操作系统名

    CMAKE_HOST_SYSTEM_PROCESSOR

    本机CPU名

    CMAKE_HOST_SYSTEM

    本机系统名

    CMAKE_HOST_SYSTEM_VERSION

    本机系统版本

    CMAKE_HOST_UNIX

    为TRUE表示本机为UNIX 或者UNIX LIKE的其他操作系统

    CMAKE_HOST_WIN32

    为TRUE表示本机为windows系统,包括32位还有64位，还包括cygwin

    CMAKE_SYSTEM_NAME

    交叉编译中目标机器的系统名

    CMAKE_SYSTEM_PROCESSOR

    交叉编译中目标机器的处理器

    CMAKE_SYSTEM

    交叉编译中目标机器的系统名,由CMAKE_SYSTEM_NAME和CMAKE_SYSTEM_VERSION组成

    CMAKE_SYSTEM_VERSION

    交叉编译中目标机器的系统版本

    CYGWIN

    为TRUE则说明使用的是CYGWIN编译

    ENV

    用来访问系统变量,比如在windows中访问PATH变量的值,可以这么表示$ENV{‘path’}

    MSVC10

    TRUE表示当前使用的是Visual C 10.0,即对应Visual studio 2010

    MSVC11

    TRUE表示当前使用的是Visual C 11.0,即对应Visual studio 2012

    MSVC12

    TRUE表示当前使用的是Visual C 12.0,即对应Visual studio 2013

    MSVC60

    TRUE表示当前使用的是Visual C 6.0,即对应Visual c++ 6.0

    MSVC70

    TRUE表示当前使用的是Visual C 7.0,即对应Visual c++ 7.0

    MSVC71

    TRUE表示当前使用的是Visual C 7.1,即对应Visual studio 2003

    MSVC80

    TRUE表示当前使用的是Visual C 8.0,即对应Visual studio 2005

    MSVC90

    TRUE表示当前使用的是Visual C 9.0,即对应Visual studio 2008

    MSVC_IDE

    TRUE表示是使用Visual studio IDE编译

    MSVC

    TRUE表示使用微软的Visual C编译

    MSVC_VERSION

    Visual C版本号
    msvc++ 5.0 1100
    msvc++ 6.0 1200
    msvc++ 7.0 1300
    msvc++ 7.1 1310
    msvc++ 8.0 1400
    msvc++ 9.0 1500
    msvc++ 10.0 1600
    msvc++ 11.0 1700
    msvc++ 12.0 1800
    msvc++ 14.0 1900

    UNIX

    True表示系统为UNIX或UNIX-like的系统

    WIN32

    True表示为windows系统，包括32位和64位

    XCODE_VERSION

    如果使用的是xcode编译,那么表示XCODE的版本号
