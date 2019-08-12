发现数据库主从出现问题，Slave_SQL_Running为No,打开mysql 错误日志，竟然是空的！！！
无奈只能重启数据库，却重启失败，提示文件句柄不够，打开mysql目录发现有很多redo_log,大概是mysql启动后进入了recovery模式，加载redo_log,可是系统限制的文件句柄很小。

看了mysql关于table_open_cache介绍如下：
MySQL是多线程的，因此可能会有许多客户机同时发出对给定表的查询。为了最小化多个客户端会话在同一表上具有不同状态的问题，表由每个并发会话独立打开。这使用额外的内存，但通常会提高性能。对于MyISAM表，对于打开表的每个客户端，都需要一个额外的文件描述符。(相反，索引文件描述符在所有会话之间共享。)

TABLE_OPEN_CACHE和MAX_Connections系统变量影响服务器保持打开的文件的最大数量。如果增加其中一个或两个值，则可能会遇到操作系统对打开的文件描述符的每个进程数施加的限制。许多操作系统允许您增加打开文件的限制，尽管不同系统的方法差别很大。请参阅您的操作系统文档，以确定是否有可能增加限制，以及如何提高限制。
TABLE_OPEN_Cache与max_Connections相关。例如，对于200个并发运行的连接，指定至少200*N的表缓存大小，其中N是每个连接在执行的任何查询中的最大表数。您还必须为临时表和文件预留一些额外的文件描述符。

确保操作系统能够处理TABLE_OPEN_CACHE设置所隐含的打开文件描述符的数量。如果TABLE_OPEN_CACHE设置得太高，MySQL可能会耗尽文件描述符，并出现拒绝连接或无法执行查询等症状。

还要考虑到MyISAM存储引擎对于每个唯一的打开表需要两个文件描述符。对于已分区的MyISAM表，打开表的每个分区都需要两个文件描述符。(当MyISAM打开一个分区表时，它会打开这个表的每个分区，不管是否实际使用了给定的分区。请参阅MyISAM和分区文件描述符的用法。)若要增加MySQL可用的文件描述符的数量，请使用mysqld的-打开文件-限制启动选项。见B.6.2.18节，“未找到文件和类似的错误”。

打开表的缓存保持在TABLE_OPEN_CACHE条目级别。默认值是400。若要显式设置大小，请在启动时设置table_open_cache系统变量。MySQL可能会临时打开比此更多的表来执行查询，如本节后面所述。

MySQL关闭一个未使用的表，并在以下情况下从表缓存中删除它：

	1.当缓存已满且线程试图打开不在缓存中的表时。
	2.当缓存包含的内容超过TABLE_OPEN_CACHE条目时，缓存中的表将不再被任何线程使用。
	3.当table-flushing操作发生时。当某人发出FLUSH TABLE语句或执行mysqladmin flush-tables或mysqladmin refresh，就会发生这种情况。

当表缓存被填满时，服务器将使用以下过程来定位要使用的缓存项：
	1.释放当前未使用的表，从最近使用最少的表开始。
	2.如果必须打开新表，但缓存已满且无法释放表，则缓存将在必要时临时扩展。当缓存处于临时扩展状态且表从已使用状态变为未使用状态时，表将关闭并从缓存中释放。

```
show global status like 'Opened_tables';

show variables like '%cache%';

show variables like '%max%';

show variables like '%open%';

show global status like 'open%tables%';

show processlist;
```

```bash
sudo lsof -p mysqlpid
mysqladmin -uroot -p123456 status
mysqladmin -uroot -p123456 refresh
```