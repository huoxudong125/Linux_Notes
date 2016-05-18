Mysql的主从复制至少是需要两个Mysql的服务，当然Mysql的服务是可以分布在不同的服务器上，也可以在一台服务器上启动多个服务。

首先确保主从服务器上的Mysql版本相同。

 

**一、安装部署mysql**

1、 安装服务器端：yum install mysql-server

　　　　　　　　　yum install mysql-devel

　　安装客户端：   yum install mysql

2、启动服务

service mysqld start

3、安装完后执行下面命令进行初始化配置，会要求设置密码：

/usr/bin/mysql_secure_installation

4、设置允许远程连接到该mysql服务器

登陆到mysql: mysql -uroot -ptest123

[root@zhoujietest ~]# **mysql -uroot -****ptest123**#账号root,密码test123
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 18
Server version: 5.5.35-log MySQL Community Server (GPL) by Remi

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h'for help. Type '\c' to clear the current input statement.

mysql> 

这时比如通过navicat连接是是失败的。需要设置下mysql库下面的user表：
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| cmdb               |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)
```
```
mysql> **use mysql;**
Database changed
mysql> **update user set host='%' where user='root'****;**
ERROR 1062 (23000): Duplicate entry '%-root'for key 'PRIMARY'
mysql> 
```
虽然提示更新失败，但实际上是更新成功的。设置允许任意主机连接。

>若是进入mysql报这个错：Access denied for user (using password: **YES**)

则如下解决：

1）、关闭mysql

  service mysqld stop

2）、屏蔽权限

mysqld_safe --skip-grant-table

3）、另起一个终端执行如下：

[root@zhoujietest ~]#mysql -u root

mysql>delete from user where user='';

mysql>flush privileges; #这个一定要执行，否则关闭之前的终端错误会重现

mysql>\q

 

**二、配置mysql主从同步**

准备两台测试的虚拟机，如上安装mysql环境，并开启mysql服务

主master : 192.168.8.10

从slave  :   192.168.8.11

1、配置主库：

1）、授权给从数据库服务器

mysql>GRANT REPLICATION SLAVE ON *.* to 'rep1'@'192.168.8.11' identified by 'test123456';
mysql>FLUSH PRIVILEGES;

2）、修改主库配置文件，开启binlog，并设置server-id，每次修改配置文件后都要重启mysql服务才会生效

vim /etc/my.cnf

在该配置文件[mysqld]下面添加下面内容：
```
[mysqld]
**log****-bin=/var/lib/mysql/binlog
server-id=1
binlog-do-db =**** cmdb**

datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
......
```
server-id：master端的ID号；

log-bin：同步的日志路径及文件名，一定注意这个目录要是mysql有权限写入的（我这里是偷懒了，直接放在了下面那个datadir下面）；

binlog-do-db：要同步的数据库名

还可以显示 设置不同步的数据库：

binlog-ignore-db = mysql 不同步mysql库和test库  
binlog-ignore-db = test

 

修改配置文件后，重启服务：service mysqld restart

如果启动失败，通过cat /var/log/mysqld.log | tail -30  查看mysql启动失败的日志，从日志内容寻找解决方案。

3）、查看主服务器当前二进制日志名和偏移量，这个操作的目的是为了在从数据库启动后，从这个点开始进行数据的恢复

```
mysql> show master status;  
+---------------+----------+--------------+------------------+  
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB |   
+---------------+----------+--------------+------------------+   
| binlog.000001 |     1304 | cmdb         |                  |   
+---------------+----------+--------------+------------------+   
1 row in set (0.00 sec)
```

主服务器已配置好。

 

2、配置从库

1）、理所当然也是从配置文件着手，在/etc/my.cnf 添加下面配置：
```
[mysqld]
server-id=2
master-host=192.168.8.10
master-user=rep1
master-password=test123456
master-port=3306
replicate-do-db=cmdb
......
```
重启时报错：mysqld: unknown variable ‘master-host=

说明mysql不认识这些变量，网上搜罗了一番，原因是mysql5.5+版本主从复制不支持这些变量，需要在从库上用命令来设置：
```
mysql> CHANGE MASTER TO MASTER_HOST='192.168.8.10',
　　MASTER_PORT=3306,
　　MASTER_USER='rep1',
　　MASTER_PASSWORD='test123456',
　　MASTER_LOG_FILE='binlog.000001',
　　MASTER_LOG_POS=1304;#后面两个参数的值与主库保持一致
```

2）、启动slave进程

mysql>** slave start;**
Query OK, 0 rows affected (0.04 sec)

3）、查看slave的状态，如果下面两项值为YES，则表示配置正确：
```
Slave_IO_Running: Yes

Slave_SQL_Running: Yes
```
从库正在等待主库更新数据。。。Waitin for master to send event...

 

**三、同步主库已有数据到从库**

主库操作：

1、停止主库的数据更新操作

mysql>flush tables with read lock;

2、新开终端，生成主数据库的备份（导出数据库）

[root@zhoujietest ~]# **mysqldump -uroot -ptest123 cmdb &gt; cmdb.sql**

3、将备份文件传到从库

[root@zhoujietest ~]# **scp cmdb.sql root@192.168.8.11:/root/**

4、主库解锁

mysql>unlock tables;

 

从库操作：

1、停止从库slave

mysql>slave stop;

2、新建数据库cmdb

mysql> create database cmdb default charset utf8;

3、导入数据

[root@ops-dev ~]# mysql -uroot -ptest123 cmdb&lt;cmdb.sql 

4、查看从库已有该数据库和数据 
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| **cmdb**               |
| mysql              |
| performance_schema |
| test               |
+--------------------+

 ```

此时主从库的数据完全一致，如果对主库进行增删改操作，从库会自动同步进行操作。