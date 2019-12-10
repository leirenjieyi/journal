# cmake 宏和函数

* 宏和函数是需要定义的。
* CMake中的函数与宏唯一的区别就在于，函数不能像宏那样将计算结果传出来(也不是完全不能，只是复杂一些)，并且函数中的变量是局部的，而宏中的变量在外面也可以被访问到


## 宏 macro

    macro(<name> [arg1 [arg2 [arg3 ...]]])
        COMMAND1(ARGS ...)
        COMMAND2(ARGS ...)
        ...
    endmacro(<name>)<pre name="code" class="plain"> 


    macro(sum outvar)
         set(_args ${ARGN})
         list(LENGTH _args argLength)
         if(NOT argLength LESS 4)　# 限制不能超过4个数字
             message(FATAL_ERROR "to much args!")
         endif()
         set(result 0)
         foreach(_var ${ARGN})
             math(EXPR result "${result}+${_var}")
         endforeach()
         set(${outvar} ${result})
    endmacro()
    sum(addResult 1 2 3 4 5)
    message("Result is :${addResult}")

"${ARGN}"是CMake中的一个变量，指代宏中传入的多余参数。因为我们这个宏sum中只定义了一个参数"outvar"，其余需要求和的数字都是不定长形式传入的，所以需要先将多余的参数传入一个单独的变量中。当然，在这个示例中，第一行代码显得多余，因为似乎没必要将额外参数单独放在一个变量中，但是建议这么做。对上面这个宏再进一步加强：如果我们想限制这个宏中传入的参数数目（尽管在这个宏中实际上是不必要的）


## 函数 function

    function(<name> [arg1 [arg2 [arg3 ...]]])
        COMMAND1(ARGS ...)
        COMMAND2(ARGS ...)
        ...
    endfunction(<name>)


