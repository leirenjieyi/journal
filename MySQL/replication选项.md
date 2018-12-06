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

���������ڼ�Ķ�������־���ĵĻ���Ĵ�С�����������֧������洢���棬�ҷ����������˶�������־(log-binѡ��)����Ϊÿ���ͻ��������������־���档�������ʹ�ô���������������ӻ����С�Ի�ø��õ����ܡ�

### binlog_checksum

    Property 	Value
    System Variable 	binlog_checksum
    Scope 	Global
    Dynamic 	Yes
    Type 	String
    Default Value 	CRC32
    Valid Values   NONE/CRC32

���ú󣬴˱�����ʹ���ڵ�Ϊ��������־�е�ÿ���¼���дУ��͡�binlog_checksum֧��ֵNone(����)��CRC 32��Ĭ��ΪCRC 32�������������и���binlog_checksum��ֵ��
������binlog_checksum(ֵΪNone)ʱ����������ͨ��д��ͼ��ÿ���¼����¼�����(������У���)����֤�Ƿ������Ķ�������־�¼�д�롣