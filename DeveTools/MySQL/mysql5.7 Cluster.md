#MySQL V5.7 多源复制特性
前端时间，一直关注5.7有多源复制的特性，当时，5.7.5版本不支持，而且官方文档也没有说明，本以为，只能用mariadb的多源复制特性，没想到，最近在看官方文档时，已有了相关文档，于是，果断试了一下。下面做一下，简单的演示：

官方文档：

http://dev.mysql.com/doc/refman/5.7/en/replication-multi-source-tutorials.html

要求：

1.复制的相关信息需要存在 table ，不能存在 file：
```
--master-info-repository=TABLE

--relay-log-info-repository=TABLE
```
2.多源复制，可以基于GTID，也可以基于 position

3.部署时，所使用的命令如下：

# 基于GTID模式的：
```
CHANGE MASTER TO MASTER_HOST='master1', MASTER_USER='blackhole', MASTER_PORT=3306,MASTER_PASSWORD='black@hole' MASTER_AUTO_POSITION = 1 FOR CHANNEL 'master-1';
```
# 基于Position模式的：
```
CHANGE MASTER TO MASTER_HOST='172.16.10.54', MASTER_USER='blackhole', MASTER_PORT=3306, MASTER_PASSWORD='black@hole' ,MASTER_LOG_FILE='mysql-bin.000047', MASTER_LOG_POS=602 FOR CHANNEL 'master-1';
```
4.开启 / 关闭 相关channel的复制：

# 开启/关闭全部
```
START SLAVE thread_types;

STOP SLAVE thread_types;
```
# 开启/关闭指定channel
```
START SLAVE thread_types FOR CHANNEL channel;

STOP SLAVE thread_types FOR CHANNEL channel;
```
# 关于 stop/start slave的语法：
```
STOP/START SLAVE [thread_types] FOR CHANNEL channel

thread_type:

IO_THREAD | SQL_THREAD
```
# MySQL 5.7 目前还没有GA版本发布，暂时，不做详细测试，仅根据官方文档说明，留以备注
下面是一个简单的测试：
```
172.16.10.54：3306 master1 mysql-5.6.25 复制用户/密码：blackhole/black@hole 日志点：mysql-bin.000047、602

172.16.10.54：3307 master2 mariadb-10 复制用户/密码：blackhole/black@hole 日志点：mysql-bin.000015 、23736

172.16.10.55：3306 slave mysql-5.7.8
```
slave机器添加两个主从的方式如下：

# 添加master-1：
```
CHANGE MASTER TO MASTER_HOST='172.16.10.54', MASTER_USER='blackhole', MASTER_PORT=3306, MASTER_PASSWORD='black@hole' ,MASTER_LOG_FILE='mysql-bin.000047', MASTER_LOG_POS=602 FOR CHANNEL 'master-1';
```
# 添加master-2：
```
CHANGE MASTER TO MASTER_HOST='172.16.10.54', MASTER_USER='blackhole', MASTER_PORT=3307, MASTER_PASSWORD='black@hole' ,MASTER_LOG_FILE='mysql-bin.000015', MASTER_LOG_POS=23736 FOR CHANNEL 'master-2';
```
#启动主从：
```
start slave；
```
#启动指定主从：
```
start slave for channel 'master-1';
```
#查看主从状态：
```
show slave status\G;
```
#查看指定从机状态：
```
show slave status for channel 'master-1'\G;
```
# 最终截图如下：
```
mysql> show slave status\G;
*************************** 1. row ***************************
Slave_IO_State: Waiting for master to send event
Master_Host: 172.16.10.54
Master_User: blackhole
Master_Port: 3306
Connect_Retry: 60
Master_Log_File: mysql-bin.000047
Read_Master_Log_Pos: 602
Relay_Log_File: slave3-relay-bin-master@002d1.000002
Relay_Log_Pos: 317
Relay_Master_Log_File: mysql-bin.000047
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Channel_Name: master-1
*************************** 2. row ***************************
Slave_IO_State: Waiting for master to send event
Master_Host: 172.16.10.54
Master_User: blackhole
Master_Port: 3307
Connect_Retry: 60
Master_Log_File: mysql-bin.000015
Read_Master_Log_Pos: 23736
Relay_Log_File: slave3-relay-bin-master@002d2.000002
Relay_Log_Pos: 445
Relay_Master_Log_File: mysql-bin.000015
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Channel_Name: master-2

```