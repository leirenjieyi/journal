1：ADD_LIBRARY()语法

add_library(<name> [STATIC | SHARED | MODULE]
            [EXCLUDE_FROM_ALL]
            source1 [source2 ...])



<name> ：库的名字，直接写名字即可，不要写lib，会自动加上前缀的哈。
[STATIC | SHARED | MODULE] ：类型有三种。

	SHARED,动态库
	STATIC,静态库
	MODULE,在使用 dyld 的系统有效,如果不支持 dyld,则被当作 SHARED 对待。


EXCLUDE_FROM_ALL：这个库不会被默认构建，除非有其他的组件依赖或者手
工构建。

2：使用

SET(LIBHELLO_SRC hello.c)
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})


3：注意，一般我们使用的静态库/动态库只是后缀名不同而已，上面构建的libhello.so与libhello_static.a，显然名字不同哦。这时你会有一个想法，那我把hello_static改成hello，结果是不可行的，静态库无法构建。重名会忽略第二条指令。

解决方法：改libhello_static.a的属性–输出名字

SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")

这样就可以生成libhello.so与libhello.a了

4：关于动态库的版本号

#VERSION 指代动态库版本,SOVERSION 指代 API 版本。
SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)




在这里插入图片描述从中我们可以看出：不管你的动态库版本是什么，对外调用总是libxxx.so。

## link_directories

该指令的作用主要是指定要链接的库文件的路径，该指令有时候不一定需要。因为find_package和find_library指令可以得到库文件的绝对路径。不过你自己写的动态库文件放在自己新建的目录下时，可以用该指令指定该目录的路径以便工程能够找到。

link_directories(
    libdir
)


# add_library CMake官方文档

## 常规库
    add_library(<name> [STATIC | SHARED | MODULE]
                [EXCLUDE_FROM_ALL]
                source1 [source2 ...])

添加一个名为\<name>的库目标，该目标将用命令调用中列出的源文件进行构建。\<name>对应于逻辑目标名称，并且在项目中必须是全局唯一的。实际构建测库文件名称依赖于本地平台约定习俗的命名方式，例如 linux平台 lib\<name>.a 或者 windows平台 \<name>.lib

STATIC,SHARED,MODULE 用来指定生成库的类型，STATIC 是静态库供其它目标进行连接；SHARED是共享库，在运行时加载；MODULE 是插件类型，不能被连接到编译目标，但是可以在运行是通过dlopen之类的函数动态加载。如果没有指定这个类型参数，这个类型