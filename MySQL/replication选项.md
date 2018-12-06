### binlog_cache_size

    Property 	Value
    Command-Line Format 	--binlog-cache-size=#
    System Variable 	binlog_cache_size
    Scope 	Global
    Dynamic 	Yes
    Type 	Integer
    Default Value 	32768
    Minimum Value 	4096
    Maximum Value (64-bit platforms) 	18446744073709551615
    Maximum Value (32-bit platforms) 	4294967295

对事务处理期间的二进制日志更改的缓存的大小。如果服务器支持事务存储引擎，且服务器启用了二进制日志(log-bin选项)，则为每个客户机分配二进制日志缓存。如果经常使用大型事务，则可以增加缓存大小以获得更好的性能。

### binlog_checksum

    Property 	Value
    System Variable 	binlog_checksum
    Scope 	Global
    Dynamic 	Yes
    Type 	String
    Default Value 	CRC32
    Valid Values   NONE/CRC32

启用后，此变量将使主节点为二进制日志中的每个事件编写校验和。binlog_checksum支持值None(禁用)和CRC 32。默认为CRC 32。不能在事务中更改binlog_checksum的值。
当禁用binlog_checksum(值为None)时，服务器将通过写入和检查每个事件的事件长度(而不是校验和)来验证是否将完整的二进制日志事件写入。