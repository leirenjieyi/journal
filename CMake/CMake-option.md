## option

option 选项，让你可以根据选项值进行条件编译。

configure_file 配置文件，让你可以在代码文件中使用CMake中定义的的变量

Provides an option that the user can optionally select.
option 提供一个用户可以任选的选项。语法如下

option(<option_variable> "help string describing option"
            [initial value])

Provide an option for the user to select as ON or OFF. If no initial value is provided, OFF is used.
option 提供选项让用户选择是 ON 或者 OFF ，如果没有提供初始化值，使用OFF。
也就是说默认的值是OFF。

但是请注意：这个option命令和你本地是否存在编译缓存的关系很大。
所以，如果你有关于 option 的改变，那么请你务必清理 CMakeCache.txt 和 CMakeFiles 文件夹。
还有，请使用标准的 [initial value] 值 ON 或者 OFF。

可以在命令行通过以下的方式设置选项
比如想打开 FOO_ENABLE 选项 -DFOO_ENABLE=ON

__个人理解：这个option 类似于c中的args,用来接收自定义的参数，不过参数值只能是OFF或ON__