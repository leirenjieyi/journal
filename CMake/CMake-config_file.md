    
    configure_file(<input> <output>
                [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
                [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])

configure_file 主要实现如下两个功能:

    1.将 <input> 文件里面的内容全部复制到 <output> 文件中；
    2.根据参数规则，替换 @VAR@ 或 ${VAR} 变量；


 参数解析

    COPYONLY
        仅拷贝 <input> 文件里面的内容到 <output> 文件， 不进行变量的替换；
    ESCAPE_QUOTES
        使用反斜杠（C语言风格）来进行转义；
    @ONLY
        限制替换， 仅仅替换 @VAR@ 变量， 不替换 ${VAR} 变量
    NEWLINE_STYLE
        指定输入文件的新行格式， 例如：Unix 中使用的是 \n, windows 中使用的 \r\n

注意: COPYONLY 和 NEWLINE_STYLE 是冲突的，不能同时使用；


## 用法示例

CMakeLists.txt
```cmake
cmake_minimum_required(VERSION 3.5)

project(Tutorial)

message(STATUS "PROJECT_SOURCE_DIR " ${PROJECT_SOURCE_DIR})
message(STATUS "PROJECT_BINARY_DIR " ${PROJECT_BINARY_DIR})

set(CMAKEDEFINE_VAR1 1)
set(CMAKEDEFINE_VAR2 0)

set(DEFINE_VAR1 1)
set(DEFINE_VAR2 0)


configure_file (
  "${PROJECT_SOURCE_DIR}/Config.h.in"
  "${PROJECT_BINARY_DIR}/Config.h"
  )

include_directories("$PROJECT_SOURCE_DIR")

add_executable(DEMO tutorial.cpp)
```


Config.h.in
```h
/**
 * This is the configure demo  
 *    - CMAKEDEFINE_VAR1 = @CMAKEDEFINE_VAR1@
 *    - CMAKEDEFINE_VAR2 = @CMAKEDEFINE_VAR2@
 *    - DEFINE_VAR1      = @DEFINE_VAR1@
 *    - DEFINE_VAR2      = @DEFINE_VAR2@
 */

/**
 *  cmakedefine 会根据变量的值是否为真（类似 if）来变换为 #define VAR ... 或  #undef VAR 
 */
#cmakedefine CMAKEDEFINE_VAR1 @CMAKEDEFINE_VAR1@
#cmakedefine CMAKEDEFINE_VAR2 @CMAKEDEFINE_VAR2@


/**
 * define 会直接根据规则来替换
 */
#define DEFINE_VAR1 @DEFINE_VAR1@
#define DEFINE_VAR2 ${DEFINE_VAR2}
```


tutorial.cpp
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "Config.h"

int main (int argc, char *argv[]) {
  
#ifdef CMAKEDEFINE_VAR1
   fprintf(stdout,"CMAKEDEFINE_VAR1 = %d\n", CMAKEDEFINE_VAR1);
#endif 

#ifdef CMAKEDEFINE_VAR2
   fprintf(stdout,"CMAKEDEFINE_VAR2 = %d\n", CMAKEDEFINE_VAR2);
#endif 

#ifdef DEFINE_VAR1
   fprintf(stdout,"DEFINE_VAR1 = %d\n", DEFINE_VAR1);
#endif 

#ifdef DEFINE_VAR2
   fprintf(stdout,"DEFINE_VAR2 = %d\n", DEFINE_VAR2);
#endif 
 
  return 0;
}
```

cmake 后生成的Config.h文件
```h
/**
 * This is the configure demo  
 *    - CMAKEDEFINE_VAR1 = 1
 *    - CMAKEDEFINE_VAR2 = 0
 *    - DEFINE_VAR1      = 1
 *    - DEFINE_VAR2      = 0
 */

/**
 *  cmakedefine 会根据变量的值是否为真（类似 if）来变换为 #define VAR ... 或  #undef VAR 
 */
#define CMAKEDEFINE_VAR1 1
/* #undef CMAKEDEFINE_VAR2 */


/**
 * define 会直接根据规则来替换
 */
#define DEFINE_VAR1 1
#define DEFINE_VAR2 0
```