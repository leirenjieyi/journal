## list命令

    list(LENGTH <list><output variable>)
    list(GET <list> <elementindex> [<element index> ...]<output variable>)
    list(APPEND <list><element> [<element> ...])
    list(FIND <list> <value><output variable>)
    list(INSERT <list><element_index> <element> [<element> ...])
    list(REMOVE_ITEM <list> <value>[<value> ...])
    list(REMOVE_AT <list><index> [<index> ...])
    list(REMOVE_DUPLICATES <list>)
    list(REVERSE <list>)
    list(SORT <list>)

    
    LENGTH　　　　   　　　　 返回list的长度
    GET　　　　　　    　　　　返回list中index的element到value中
    APPEND　　　　    　　　　添加新element到list中
    FIND　　　　　　   　　　　返回list中element的index，没有找到返回-1
    INSERT 　　　　　　　　　 将新element插入到list中index的位置
    REMOVE_ITEM　　　　　　从list中删除某个element
    REMOVE_AT　　　　　　　从list中删除指定index的element
    REMOVE_DUPLICATES       从list中删除重复的element
    REVERSE 　　　　　　　　将list的内容反转
    SORT 　　　　　　　　　　将list按字母顺序排序


LIST与SET命令类似，即使列表本身是在父域中定义的，LIST命令也只会在当前域创建新的变量，要想将这些操作的结果向上传递，需要通过SET　PARENT_SCOPE， SET CACHE INTERNAL或运用其他值域扩展的方法。

　　注意：cmake中的list是以分号隔开的一组字符串。可以使用set命令创建一个列表。例如：set(var a b c d e)创建了一个这样的列表：a;b;c;d;e。 set(var “a b c d e”)创建了一个字符串或只有一个元素的列表。

　　当指定index时，如果<element index>为大于或等于0的值，它从列表的开始处索引，0代表列表的第一个元素。如果<element index>为小于或等于-1的值，它从列表的结尾处索引，-1代表列表的最后一个元素。