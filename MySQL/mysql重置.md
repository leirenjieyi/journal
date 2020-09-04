## 起因
而然发现服务器的物理磁盘坏掉了，Lenovo systemx3650 m5报告HDD 3 fault,进入uefi查看，也显示硬盘offline,这块硬盘主要用作了mysql data存储，也就是mysql数据库的存储。因此整个mysql的数据文件没了。。。

## 初始化Mysql
不过系统还能正常运行，开始初始化mysql:

```
gsmrlab@ATPMON-CDR-COPY:/usr/local$ mysqld --initialize
mysqld: Can't create directory '/var/RecData/mysql/' (Errcode: 13 - Permission denied)
2020-05-15T04:12:57.695418Z 0 [ERROR] Aborting
```
发现RecData下没有mysql文件夹，手动创建，并更改用户为mysql

```
sudo mkdir /var/RecData/mysql
sudo chown mysql:mysql /var/RecData/mysql
```
再次初始化没问题了

不过正常的初始化还可以带上如下参数：
```
sudo mysqld    --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql/data/
```



初始化后启动mysql

```
sudo systemctl start mysql.service
sudo systemctl status mysql.service
```
启动 mysql 总算成功了；不过用mysql客户端登陆不了,现象如同密码错误的现象。停止mysql服务，启用跳过权限检查的方式启动服务

```
mysqld --datadir=/var/RecData/mysql/  --skip-grant-tables
```

这种方式一直起不来，这个之前在windows上用过，是可以的；既然起不来也不纠结了，采用修改mysql 配置文件，在 mysqld.cnf 的 [mysqld]下 添加 ‘skip-grant-tables’,然后

```
sudo systemctl start mysql.service
```

成功启动后，登录mysql服务

```
mysql
```

查询用户表，发现用户表内是空的，这可能是我在 initialize的时候没有加 --user=root选项，只能手动创建用户：

```
mysql>create user 'root'@'localhost' identified by 'gsmrlab';

ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement //提示错误，从网上查到需要 flush privileges

mysql>flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql> create user 'root'@'localhost' identified by 'gsmrlab';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)


```
添加好root用户后，可以进行登录了，不过使用mysql workbench登录，出现错误：

    无法连接mysql数据库
    mysql Table ‘performance_schema.session_variables’ doesn’t exist

网上给出解决办法

    >sudo systemctl stop mysql.service
    >sudo mysql_upgrade -u root -p --force

运行上面这个命令后出错:
    
    mysql_upgrade: [ERROR] 1881: Operation not allowed when innodb_forced_recovery > 0.
    
这个，从网上查到 my.conf中有对应的 innodb_forced_recovery的配置项，我的配置项值为 1，将其改成 0，重新执行：

    >sudo systemctl stop mysql.service
    Checking server version.
    Running queries to upgrade MySQL server.
    Checking system database.
    mysql.columns_priv                                 OK
    mysql.db                                           OK
    mysql.engine_cost                                  OK
    mysql.event                                        OK
    mysql.func                                         OK
    mysql.general_log                                  OK
    mysql.gtid_executed                                OK
    mysql.help_category                                OK
    mysql.help_keyword                                 OK
    mysql.help_relation                                OK
    mysql.help_topic                                   OK
    mysql.innodb_index_stats                           OK
    mysql.innodb_table_stats                           OK
    mysql.ndb_binlog_index                             OK
    mysql.plugin                                       OK
    mysql.proc                                         OK
    mysql.procs_priv                                   OK
    mysql.proxies_priv                                 OK


这下没问题了，然后启动mysql服务，用 mysql workbench连接 ok.

这里查了下 innodb_forced_recovery 项含义：

    nnodb_force_recovery影响整个InnoDB存储引擎的恢复状况。默认为0，表示当需要恢复时执行所有的

    innodb_force_recovery可以设置为1-6,大的数字包含前面所有数字的影响。当设置参数值大于0后，可以对表进行select,create,drop操作,但insert,update或者delete这类操作是不允许的。

    1(SRV_FORCE_IGNORE_CORRUPT):忽略检查到的corrupt页。
    2(SRV_FORCE_NO_BACKGROUND):阻止主线程的运行，如主线程需要执行full purge操作，会导致crash。
    3(SRV_FORCE_NO_TRX_UNDO):不执行事务回滚操作。
    4(SRV_FORCE_NO_IBUF_MERGE):不执行插入缓冲的合并操作。
    5(SRV_FORCE_NO_UNDO_LOG_SCAN):不查看重做日志，InnoDB存储引擎会将未提交的事务视为已提交。
    6(SRV_FORCE_NO_LOG_REDO):不执行前滚的操作。 
