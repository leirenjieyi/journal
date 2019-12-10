## find_package 命令



1、 find_package(<Name>)命令首先会在模块路径中寻找 Find<name>.cmake，这是查找库的一个典型方式。

    具体查找路径依次为CMake： 

    变量${CMAKE_MODULE_PATH}中的所有目录。

    如果没有，然后再查看它自己的模块目录 /share/cmake-x.y/Modules/ （$CMAKE_ROOT的具体值可以通过CMake中message命令输出）。

    $CMAKE_ROOT = /usr/share/cmake-3.7

    /usr/share/cmake-3.7/Modules

    这称为模块模式。

----

    2、 如果没找到这样的文件，find_package()会在~/.cmake/packages/或/usr/local/share/中的各个包目录中查找，寻找<库名字的大写>Config.cmake 或者 <库名字的小写>-config.cmake (比如库Opencv，它会查找/usr/local/share/OpenCV中的OpenCVConfig.cmake或opencv-config.cmake)。

    这称为配置模式。


*不管使用哪一种模式，只要找到*.cmake，*.cmake里面都会定义下面这些变量：

1. <NAME>_FOUND
2. <NAME>_INCLUDE_DIRS or <NAME>_INCLUDES
3. <NAME>_LIBRARIES or <NAME>_LIBRARIES or <NAME>_LIBS
4. <NAME>_DEFINITIONS


   两种模式看起来似乎差不多，不过cmake默认采取Module模式，如果Module模式未找到库，才会采取Config模式。如果XXX_DIR路径下找不到XXXConfig.cmake文件，

则会找/usr/local/lib/cmake/XXX/中的XXXConfig.cmake文件。

总之，Config模式是一个备选策略。通常，库安装时会拷贝一份XXXConfig.cmake到系统目录中，因此在没有显式指定搜索路径时也可以顺利找到。
