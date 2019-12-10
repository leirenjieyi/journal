# cmake 中的循环

## foreach() ... endforeach()
    
    set(mylist "a" "b" c "d")
    foreach(_var ${mylist})
         message("当前变量是：${_var}")
    endforeach()

这是最常用的用法


    set(result 0)
    foreach(_var RANGE 0 100)
         math(EXPR result "${result}+${_var}")
    endforeach()
    message("from 0 plus to 100 is:${result}")

这个是用 RANGE 关键字实现的步进




## while() ... endwhile()

while循环没什么特别的，会使用判断就会使用while循环


## if 条件
    if(expression)
        # then section.
        COMMAND1(ARGS ...)
        COMMAND2(ARGS ...)
        ...
    elseif(expression2)
        # elseif section.
        COMMAND1(ARGS ...)
        COMMAND2(ARGS ...)
        ...
    else(expression)
        # else section.
        COMMAND1(ARGS ...)
        COMMAND2(ARGS ...)
        ...
    endif(expression)


CMake中的判断可以说是比较变态的。首先看看官方文档中的说明：对于表达式"if(<constant>)",True if the constant is 1, ON, YES, TRUE, Y, or a non-zero number. False if the constant is 0, OFF, NO, FALSE, N, IGNORE, "", or ends in the suffix '-NOTFOUND'.
就是说，CMake中if语句中对于条件真假的判断，不完全是以变量的真假为标准的，有时还和变量名有关。
总之，使用if 时，要特别注意这一点。

    1 ON YES TRUE Y !0 =TRUE
    0 OFF NO FALSE N IGNORE 以-NOTFOUND 为后缀。

    
    if(WIN32)
         message("this operation platform is windows")
    elseif(UNIX)
         message("this operation platform is Linux")
    endif()
