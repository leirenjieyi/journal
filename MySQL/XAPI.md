## mysql connecter xapi 使用

1. 首先确定MySQL版本是否大于5.7，如果小于5.7则只能使用connecter1.1 或是 jdbc

	select version();

2. 如果mysql版本小于8.0，则需要安装X plugin；检查是否安装X plugin:


		show plugins;

		+----------------------------+----------+--------------------+---------+---------+

		| Name                       | Status   | Type               | Library | License |

		+----------------------------+----------+--------------------+---------+---------+

		...


		| mysqlx                     | ACTIVE   | DAEMON             | NULL    | GPL     |


如果没有，则尝试安装mysqlx plugin:
	
	mysql>install plugin mysqlx soname 'mysqlx.so';

如果报错，找不到 mysqlx.so,原因可能是直接采用ubuntu apt 安装，这样有两种解决方式：采用源码编译，找到编译好的mysqlx.so,拷贝到当前安装的mysql plugin目录下；
另外一个就是卸载重装。

这里采用卸载重装的方式解决：

	sudo systemctl stop mysql.service
	sudo apt purag mysql-server-5.7
	#等待卸载完成后
	sudo apt autoremove

重装是采用MySQL 提供的APT安装方式，到页面 https://dev.mysql.com/downloads/repo/apt/ 下载最下边的deb package,这里包含

	The MySQL APT repository includes the latest versions of:

	    MySQL 8.0
	    MySQL 5.7
	    MySQL 5.6
	    MySQL Cluster 8.0 (RC)
	    MySQL Cluster 7.6
	    MySQL Cluster 7.5
	    MySQL Workbench - Ubuntu Only
	    MySQL Router
	    MySQL Shell
	    MySQL Connector/C++
	    MySQL Connector/J
	    MySQL Connector/Python

然后安装下载的deb 

	sudo dpkg -i xxx.deb #安装过程会让选择mysql 的版本，选择好后 ok
	#安装完成后
	sudo apt update
	#更新完成后
	sudo apt install mysql-server #注意这里没有版本
	#安装完成后
	sudo systemctl status mysql.service
	#如果没有启动则手动启动
	sudo systemctl start mysql.service

这时，进入mysql ,查看mysqlx 插件，发现mysqlx插件依然没有，手动安装一下：

	mysql>install plugin mysqlx soname 'mysqlx';
	mysql>show plugins;

发现mysqlx安装成功。

3. 安装libmysqlcppconn8-x,libmysqlcppconn7,libmysqlconn-dev

libmyysqlcppconn8-x（当前x为2）：这个包提供X DevAPI(c++) 和 X DevAPI for C 的实现；

libmysqlcppconn7:是jdbc API的实现库；

libmysqlcppconn-dev:这个包提供以上两个包的共享库依赖；

这个安装有两种方式，一个是直接去mysql下载中的Connecter 中下载deb，另一种是通过mysql 提供的apt,也就是上面提到的 APT安装方式，到页面 https://dev.mysql.com/downloads/repo/apt/ 下载最下边的deb package；

安装好后update一下，然后直接安装：

	sudo apt install libmyysqlcppconn8-2
	sudo apt install libmysqlcppconn7
	sudo apt install libmysqlcppconn-dev

4. 代码实现参考

	https://dev.mysql.com/doc/x-devapi-userguide/en/database-connection-example.html
