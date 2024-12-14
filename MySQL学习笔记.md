## 数据库

### 类型

#### 关系型数据库

关系型数据库使用表格（表）来存储数据，数据通过行和列的形式组织。表与表之间可以通过外键进行关联，遵循关系模型

MySQL、PostgreSQL、Oracle、Microsoft SQL Server

#### 非关系型数据库

非关系型数据库是一种不使用传统表格结构来存储数据的数据库，通常用于处理大规模和动态变化的数据

MongoDB（文档型），Redis（键值型）

### 一、MYSQL的安装

```
#第一种
cat > /etc/yum.repos.d/mysql-community.repo << EOF
[mysql]
name=mysql5.7 
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-5.7-community-el7- x86_64/ 
gpgcheck=0 
EOF

#第二种
 wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
 rpm -ivh mysql57-community-release-el7-11.noarch.rpm
 rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
 sed -i 's#http://repo.mysql.com/#https://mirrors.tuna.tsinghua.edu.cn/mysql/#g' /etc/yum.repos.d/mysql-community.repo
 sed -i 's#/el/7/#-el7-#g' /etc/yum.repos.d/mysql-community.repo


#第三种
cat > /etc/yum.repos.d/mysql.repo << EOF
[mysql-connectors-community]
name=MySQL Connectors Community
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-connectors-community-el7-\$basearch/
enabled=1
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

[mysql-tools-community]
name=MySQL Tools Community
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-tools-community-el7-\$basearch/
enabled=1
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

[mysql-8.0-community]
name=MySQL 8.0 Community Server
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-8.0-community-el7-\$basearch/
enabled=1
gpgcheck=1
gpgkey=https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
EOF
```



#### 查看自带数据库

- rpm -qa|grep mari


#### 卸载数据库

- rpm -e --nodeps mariadb-libs
- #确认是否干净了
  rpm -qa|grep mariadb

#### 安装包

- rpm -ivh mysql-community-common-5.7.26-1.el7.x86_64.rpm 
- rpm -ivh mysql-community-libs-5.7.26-1.el7.x86_64.rpm 
- rpm -ivh mysql-community-client-5.7.26-1.el7.x86_64.rpm 
- yum install net-tools
- yum install libaio

- rpm -ivh mysql-community-server-5.7.26-1.el7.x86_64.rpm 


#### 启动mysql服务

- systemctl start mysqld.service


#### 查看密码

- grep "password" /var/log/mysqld.log 


- 启动mysql

- mysql -uroot -p


#### 修改密码复杂度要求

- SET GLOBAL validate_password_policy=0;

- SET GLOBAL validate_password_length = 4;


#### 修改密码

- SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');

### 二、SQL语言

| SQL语句类型 | 说明         | 具体语句                           |
| ----------- | ------------ | ---------------------------------- |
| DDL         | 数据定义语言 | create、drop、alter                |
| DML         | 数据操作语言 | update、insert、delete             |
| DQL         | 数据查询语言 | select                             |
| DCL         | 数据控制语言 | grant、revoke                      |
| TCL         | 事务控制语言 | begin、commit、rollback、savepoint |

#### 1.DDL常用语句

**创建数据库（CREATE DATABASE）**：

```sql
CREATE DATABASE database_name;
```

**删除数据库（DROP DATABASE）**：

```sql
DROP DATABASE database_name;
```

**创建表（CREATE TABLE）**：

```sql
CREATE TABLE table_name (
    column1 data_type PRIMARY KEY,
    column2 data_type,
    column3 data_type,
   ...
);
```

**修改表名（RENAME TABLE）**：

```sql
RENAME TABLE old_table_name TO new_table_name;
```

**删除表（DROP TABLE）**：

```sql
DROP TABLE table_name;
```

**添加主键约束（ALTER TABLE - ADD PRIMARY KEY）**：

```sql
ALTER TABLE table_name ADD PRIMARY KEY (column_name);
```

**添加唯一约束（ALTER TABLE - ADD UNIQUE）**：

```sql
ALTER TABLE table_name ADD UNIQUE (column_name);
```

**添加外键约束（ALTER TABLE - ADD FOREIGN KEY）**：

```sql
ALTER TABLE child_table_name
ADD FOREIGN KEY (child_column_name) REFERENCES parent_table_name (parent_column_name);
```

**删除约束（ALTER TABLE - DROP CONSTRAINT）**：

```sql
ALTER TABLE table_name DROP CONSTRAINT constraint_name;
```

#### 2.DML常用语句

**插入数据（INSERT）**：

插入所有列的值：

```sql
INSERT INTO table_name VALUES (value1, value2, value3,...);
```

明确指定列插入值：

```sql
INSERT INTO table_name (column1, column2, column3,...) VALUES (value1, value2, value3,...);
```

**更新数据（UPDATE）**：

```sql
UPDATE table_name
SET column1 = value1, column2 = value2,...
WHERE condition;
```

**删除数据（DELETE）**：

```sql
DELETE FROM table_name
WHERE condition;
```

#### 3.DQL常用语句

**基本查询**：

```sql
SELECT column1, column2,... FROM table_name;
```

**条件查询**：

```sql
SELECT * FROM table_name WHERE condition; 
```

**排序结果**：

```sql
SELECT * FROM table_name ORDER BY column_name ASC/DESC; 
```

**聚合函数**：

```sql
SELECT COUNT(*), SUM(column_name), AVG(column_name), MAX(column_name), MIN(column_name) FROM table_name;
```

**分组查询**：

```sql
SELECT column_name, aggregate_function(column_name) FROM table_name GROUP BY column_name;
```

**多表连接查询**：

```sql
-- 内连接
SELECT * FROM table1 INNER JOIN table2 ON table1.column = table2.column;

-- 左连接
SELECT * FROM table1 LEFT JOIN table2 ON table1.column = table2.column;

-- 右连接
SELECT * FROM table1 RIGHT JOIN table2 ON table1.column = table2.column;
```

**子查询**：

```sql
SELECT column_name FROM table_name WHERE column_name IN (SELECT column_name FROM another_table);

SELECT column_name FROM table_name WHERE column_name = (SELECT MAX(column_name) FROM another_table);
```

**数据查询（SELECT）**：

```sql
SELECT * FROM table_name;  -- 选择表中的所有列
SELECT column1, column2 FROM table_name;  -- 选择指定列
SELECT column_name AS alias_name FROM table_name;  -- 为列指定别名
```

### 三、事务和范式

**事务（Transaction）**

在数据库中，事务是一组作为单个逻辑工作单元执行的操作。事务具有以下四个重要特性，通常称为 ACID 特性：

1. 原子性（Atomicity）：事务中的所有操作要么全部成功执行，要么全部不执行。如果事务中的任何一个操作失败，整个事务都会回滚到事务开始之前的状态。
2. 一致性（Consistency）：事务执行前后，数据库必须从一个合法的状态转变到另一个合法的状态。也就是说，事务的执行不能违反数据库中定义的任何完整性约束。
3. 隔离性（Isolation）：多个事务并发执行时，它们之间相互隔离，一个事务的执行不能影响其他事务的执行结果。不同的隔离级别会对并发事务之间的可见性和相互影响程度有所不同。
4. 持久性（Durability）：一旦事务成功提交，其对数据库所做的更改就会永久保存，即使系统出现故障也不会丢失。

**范式（Normal Form）**

范式是数据库设计中的一种规范和原则，目的是减少数据冗余，提高数据的一致性和完整性。常见的范式有：

1. 第一范式（1NF）：确保每列的原子性，即列不能再被分割为多个子列。
2. 第二范式（2NF）：在满足第一范式的基础上，确保非主键列完全依赖于主键，而不是部分依赖。
3. 第三范式（3NF）：在满足第二范式的基础上，确保非主键列之间不存在传递依赖。

### 四、日志

##### 前言

MySQL中有以下日志文件，分别是：

　　1：**重做日志（redo log）**

　　2：**回滚日志（undo log）**

　　3：**二进制日志（binlog）**

　　4：**错误日志（errorlog）**

　　5：**慢查询日志（slow query log）**

　　6：**一般查询日志（general log）**

　　7：**中继日志（relay log）**

#### 一、重做日志（redo log）

**作用：**

　　确保事务的持久性。redo日志记录事务执行后的状态，用来恢复未写入data file的已成功事务更新的数据。防止在发生故障的时间点，尚有脏页未写入磁盘，在重启[mysql](https://www.2cto.com/database/MySQL/)服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性。

**内容：**

　　物理格式的日志，记录的是物理数据页面的修改的信息，其redo log是顺序写入redo log file的物理文件中去的。

**什么时候产生：**

　　事务开始之后就产生redo log，redo log的落盘并不是随着事务的提交才写入的，而是在事务的执行过程中，便开始写入redo log文件中。

**什么时候释放：**

　　当对应事务的脏页写入到磁盘之后，redo log的使命也就完成了，重做日志占用的空间就可以重用（被覆盖）。

**对应的物理文件：**

　　默认情况下，对应的物理文件位于[数据库](https://www.2cto.com/database/)的data目录下的ib_logfile1&ib_logfile2

　　innodb_log_group_home_dir 指定日志文件组所在的路径，默认./ ，表示在数据库的数据目录下。

　　innodb_log_files_in_group 指定重做日志文件组中文件的数量，默认2

**关于文件的大小和数量，由以下两个参数配置：**

　　innodb_log_file_size 重做日志文件的大小。

　　innodb_mirrored_log_groups 指定了日志镜像文件组的数量，默认1

**其他：**

　　很重要一点，redo log是什么时候写盘的？前面说了是在事物开始之后逐步写盘的。

　　之所以说重做日志是在事务开始之后逐步写入重做日志文件，而不一定是事务提交才写入重做日志缓存，原因就是，重做日志有一个缓存区Innodb_log_buffer，Innodb_log_buffer的默认大小为8M(这里设置的16M),Innodb存储引擎先将重做日志写入innodb_log_buffer中。

然后会通过以下三种方式将innodb日志缓冲区的日志刷新到磁盘

　　Master Thread 每秒一次执行刷新Innodb_log_buffer到重做日志文件。

　　每个事务提交时会将重做日志刷新到重做日志文件。

　　当重做日志缓存可用空间 少于一半时，重做日志缓存被刷新到重做日志文件

　　由此可以看出，重做日志通过不止一种方式写入到磁盘，尤其是对于第一种方式，Innodb_log_buffer到重做日志文件是Master Thread线程的定时任务。

　　因此重做日志的写盘，并不一定是随着事务的提交才写入重做日志文件的，而是随着事务的开始，逐步开始的。

另外引用《MySQL技术内幕 Innodb 存储引擎》（page37）上的原话：

　　即使某个事务还没有提交，Innodb存储引擎仍然每秒会将重做日志缓存刷新到重做日志文件。

　　这一点是必须要知道的，因为这可以很好地解释再大的事务的提交（commit）的时间也是很短暂的。

#### 二、回滚日志（undo log）

**作用：**

　　保证数据的原子性，保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读

**内容：**

　　逻辑格式的日志，在执行undo的时候，仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作实现的，这一点是不同于redo log的。

**什么时候产生：**

　　事务开始之前，将当前是的版本生成undo log，undo 也会产生 redo 来保证undo log的可靠性

**什么时候释放：**

　　当事务提交之后，undo log并不能立马被删除，而是放入待清理的链表，由purge线程判断是否由其他事务在使用undo段中表的上一个事务之前的版本信息，决定是否可以清理undo log的日志空间。

**对应的物理文件：**

　　MySQL5.6之前，undo表空间位于共享表空间的回滚段中，共享表空间的默认的名称是ibdata，位于数据文件目录中。

　　MySQL5.6之后，undo表空间可以配置成独立的文件，但是提前需要在配置文件中配置，完成数据库初始化后生效且不可改变undo log文件的个数

　　如果初始化数据库之前没有进行相关配置，那么就无法配置成独立的表空间了。

**关于MySQL5.7之后的独立undo 表空间配置参数如下：**

　　innodb_undo_directory = /data/un[dos](https://www.2cto.com/os/dos/)pace/ –undo独立表空间的存放目录 innodb_undo_logs = 128 –回滚段为128KB innodb_undo_tablespaces = 4 –指定有4个undo log文件

　　如果undo使用的共享表空间，这个共享表空间中又不仅仅是存储了undo的信息，共享表空间的默认为与MySQL的数据目录下面，其属性由参数innodb_data_file_path配置。

**其他：**

　　undo是在事务开始之前保存的被修改数据的一个版本，产生undo日志的时候，同样会伴随类似于保护事务持久化机制的redolog的产生。

　　默认情况下undo文件是保持在共享表空间的，也即ibdatafile文件中，当数据库中发生一些大的事务性操作的时候，要生成大量的undo信息，全部保存在共享表空间中的。

　　因此共享表空间可能会变的很大，默认情况下，也就是undo 日志使用共享表空间的时候，被“撑大”的共享表空间是不会也不能自动收缩的。

　　因此，mysql5.7之后的“独立undo 表空间”的配置就显得很有必要了。

#### 三、二进制日志（binlog）：

**作用：**

　　用于复制，在主从复制中，从库利用主库上的binlog进行重播，实现主从同步。

　　用于数据库的基于时间点的还原。

**内容：**

　　逻辑格式的日志，可以简单认为就是执行过的事务中的sql语句。

　　但又不完全是sql语句这么简单，而是包括了执行的sql语句（增删改）反向的信息，也就意味着delete对应着delete本身和其反向的insert；update对应着update执行前后的版本的信息；insert对应着delete和insert本身的信息。

　　在使用mysqlbinlog解析binlog之后一些都会真相大白。

　　因此可以基于binlog做到类似于oracle的闪回功能，其实都是依赖于binlog中的日志记录。

**什么时候产生：**

　　事务提交的时候，一次性将事务中的sql语句（一个事物可能对应多个sql语句）按照一定的格式记录到binlog中。

　　这里与redo log很明显的差异就是redo log并不一定是在事务提交的时候刷新到磁盘，redo log是在事务开始之后就开始逐步写入磁盘。

　　因此对于事务的提交，即便是较大的事务，提交（commit）都是很快的，但是在开启了bin_log的情况下，对于较大事务的提交，可能会变得比较慢一些。

　　这是因为binlog是在事务提交的时候一次性写入的造成的，这些可以通过测试验证。

**什么时候释放：**

　　binlog的默认是保持时间由参数expire_logs_days配置，也就是说对于非活动的日志文件，在生成时间超过expire_logs_days配置的天数之后，会被自动删除。

**对应的物理文件：**

　　配置文件的路径为log_bin_basename，binlog日志文件按照指定大小，当日志文件达到指定的最大的大小之后，进行滚动更新，生成新的日志文件。

　　对于每个binlog日志文件，通过一个统一的index文件来组织。

**其他：**

　　二进制日志的作用之一是还原数据库的，这与redo log很类似，很多人混淆过，但是两者有本质的不同

　　**作用不同**：redo log是保证事务的持久性的，是事务层面的，binlog作为还原的功能，是数据库层面的（当然也可以精确到事务层面的），虽然都有还原的意思，但是其保护数据的层次是不一样的。

　　**内容不同**：redo log是物理日志，是数据页面的修改之后的物理记录，binlog是逻辑日志，可以简单认为记录的就是sql语句

　　另外，两者日志产生的时间，可以释放的时间，在可释放的情况下清理机制，都是完全不同的。

　　恢复数据时候的效率，基于物理日志的redo log恢复数据的效率要高于语句逻辑日志的binlog

　　关于事务提交时，redo log和binlog的写入顺序，为了保证主从复制时候的主从一致（当然也包括使用binlog进行基于时间点还原的情况），是要严格一致的，MySQL通过两阶段提交过程来完成事务的一致性的，也即redo log和binlog的一致性的，理论上是先写redo log，再写binlog，两个日志都提交成功（刷入磁盘），事务才算真正的完成。

#### **四、错误日志**

　　错误日志记录着mysqld启动和停止,以及服务器在运行过程中发生的错误的相关信息。在默认情况下，系统记录错误日志的功能是关闭的，错误信息被输出到标准错误输出。
　　指定日志路径两种方法:
　　　　编辑my.cnf 写入 log-error=[path]
　　　　通过命令参数错误日志 mysqld_safe –user=mysql –log-error=[path] &

#### **五、普通查询日志 general query log** 

　　记录了服务器接收到的每一个查询或是命令，无论这些查询或是命令是否正确甚至是否包含语法错误，general log 都会将其记录下来 ，记录的格式为 {Time ，Id ，Command，Argument }。也正因为mysql服务器需要不断地记录日志，开启General log会产生不小的系统开销。 因此，Mysql默认是把General log关闭的。

查看日志的存放方式：show variables like ‘log_output’;

　　如果设置mysql> set global log_output=’table’ 的话，则日志结果会记录到名为gengera_log的表中，这表的默认引擎都是CSV
　　如果设置表数据到文件set global log_output=file;
　　设置general log的日志文件路径：
　　　　set global general_log_file=’/tmp/general.log’;
　　　　开启general log： set global general_log=on;
　　　　关闭general log： set global general_log=off;



　　然后在用：show global variables like ‘general_log’

#### **六、慢查询日志** 

　　慢日志记录执行时间过长和没有使用索引的查询语句，报错select、update、delete以及insert语句，慢日志只会记录执行成功的语句。

　　1. 查看慢查询时间： 

　　show variables like “long_query_time”;默认10s

　　2. 查看慢查询配置情况： 

　　show status like “%slow_queries%”;

　　3. 查看慢查询日志路径： 

　　show variables like “%slow%”;

　　4. 开启慢日志

　　查看已经开启：

###  五、MySQL备份与恢复

#### 1、备份类型

| 备份类型 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| 完全备份 | 备份整个数据库全部数据                                       |
| 部分备份 | 只备份部份数据子集，例如部份库或表                           |
| 增量备份 | 仅备份最近一次完全备份或增量备份（如果存在增量）以来变化的数据，备份较快，还原复杂 |
| 差异备份 | 仅备份最近一次完全备份以来变化的数据，备份较慢，还原简单     |
| 冷备份   | 读、写操作均不可进行，数据库停止服务                         |
| 温备份   | 可读不可写                                                   |
| 热备份   | 读、写操作均可执行，MyISAM 只支持温备，不支持热备，InnoDB都支持 |
| 物理备份 | 直接复制数据文件进行备份，与存储引擎有关，占用较多的空间，速度快 |
| 逻辑备份 | 从数据库中"导出"数据另存而进行的备份，与存储引擎无关，占用空间少，速度慢，可能丢失精度 |

#### 2、冷备份实现

冷备份即将MySQL服务停止，使用rsync命令将数据目录备份到远端服务器。

如果不将服务停止，在备份过程有写操作干扰备份进行，导致备份的数据不一致。

```
rsync -a /var/lib/mysql root@10.0.0.183:/root/data/
```

在生产环境中，不可能这么干，所以了解一下即可。

#### 3、温备份

温备份即在数据库进行备份时, 数据库的读操作可以执行, 但是不能执行写操作。

这种方式一般是通过FLUSH TABLES WITH READ LOCK给表加读锁，然后通过rsync、scp、mysqldump等工具进行备份。

#### 4、热备份之mysqldump逻辑备份

##### 4.1、mysqldump简介

mysqldump是MySQL自带的客户端备份工具。它的备份原理是通过协议连接到 MySQL数据库，将需要备份的数据查询出来，将查询出的数据转换成对应的insert 语句，当我们需要还原这些数据时，只要执行这些 insert 语句，即可将对应的数据还原。

##### 4.2、mysqldump保证数据一致性

在默认情况下，mysqldump在导出数据时，会调用FLUSH TABLES WITH READ LOCK给所有的表加一个读锁，它会阻塞所有的写操作，影响应用的正常运行，在导出结束后才会解锁表。

在这种情况下，如果要解决这个问题，可以加一个**-single-transaction**参数，当然这是针对InnoDB引擎而言，InnoDB引擎是支持事务的，而MyISAM引擎是不支持事务的。-single-transaction可以在不锁表的情况下，完成备份。

```
#MySQLdump执行过程原理如下，暂时整不明白，放这里
FLUSH TABLES WITH READ LOCK
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ
START TRANSACTION /*!40100 WITH CONSISTENT SNAPSHOT
commit
参考：
https://blog.csdn.net/qq_34556414/article/details/106781973
http://dbaselife.com/doc/1075/
```

##### 4.3、mysqldump使用方法

```
mysqldump [OPTIONS] database [tables...]                    #导出一个库的一个或多个表
mysqldump [OPTIONS] --databases [OPTIONS] DB1 [DB2 DB3...]  #导出一个或多个数据库
mysqldump [OPTIONS] --all-databases [OPTIONS]               #导出所有数据库

#常用选项
-u|--user=val        #指定用户名
-P|--port=N          #指定端口，默认3306
-p|--password[=val]  #指定密码
-A|--all-databases   #备份所有数据库(创建数据库语句)
-Y|--all-tablespaces #备份所有表空间(建表语句)
-y|--no-tablespaces  #不备份表空间
-a|--create-options  #在create table语句中保留所有mysql特性选项，默认项，可用 --skip-create-options 去掉
-B|--databases db1 db2 #导出指定数据库
-E|--events          #导出事件
-R|--routines        #导出存储过程以及自定义函数
-c|--complete-insert #导出完整的insert语句，包含列名和值，但可能会受到max_allowed_packet参数影响而导致插入失败
-e|--extended-insert #导出多个VALUES列的INSERT语法,默认项，可使用 --skip�extended-insert 关闭
-C|--compress        #在客户端和服务端启用压缩传输数据
-d|--no-data         #只备份表结构,不备份数据,即只备份create table
-t|--no-create-info  #只备份数据，不备份表结构
-n|--no-create-db    #不备份create database，可被-A或-B覆盖
-f|--force           #忽略错误
-S|--socket=val      #指定socket文件地址 
F|--flush-logs       #开始导出之前刷新日志，如果使用了 --databases|--all�databases 导出多个库，会逐个刷新；如果使用了--lock-all-tables|--master-data，日志将会被刷新一次且会锁表

--single-transaction  #不锁表导出数据
--triggers           #导出触发器，默认项，可用 --skip-triggers 关闭
--default-character-set=val #设置默认字符集，默认utf8
--delete-source-logs #备份前刷新二进制日志，相当于执行 flush logs,备份后重置，相当于purge logs，需要配合 --source-data 选项一起使用
--delete-master-logs #备份后删除日志，需要配合 --master-data 选项一起使用
--master-data=N      #此配置在新版中被废弃，使用 --source-data 选项代替
--source-data=N      #将binlog的pos位置和文件名追加到输出文件中，取值为1|2
 #1会保留 CHANGE MASTER TO 语句，适合于集群使用
#2也会有 CHANGE MASTER TO 语句，但会被注释，适合于单机使用
#该选项将打开--lock-all-tables 选项，除非--single�transaction也被指定
 
--default-character-set=val #设置默认字符集，默认值为utf8
--add-drop-database #每个数据库创建语句之前添加 drop database 语句
--add-drop-table #每个数据表创建语句之前添加 drop table 语句
--add-drop-trigger #每个触发器创建语句之前添加 drop trigger 语句
--add-locks #在每个表导出之前lock table，导出后unlock table,默认项，可
使用 --skip-add-locks 去掉
--compatible=val #让备份数据兼容其它相间库类型，可选值包括 
 
#ansi|mysql323|mysql40|postgresql|oracle|mssql|db2|maxdb|
 #no_key_options|no_tables_options|no_field_options
#多个值用逗号分隔，此处并不能保证全完兼容，是尽最大可能保证兼容
--flush-privileges #在导出mysql数据库之后，执行 FLUSH PRIVILEGES，
 #用于导出mysql数据库和依赖mysql数据库数据的任何时候
 
--character-sets-dir=dir #指定字符集文件目录 
--comments #导出备份文件中是否包含注释信息，默认项，可用--skip-comments 关闭
--compact #导出更少的信息，一般用于调试，比如说不导出注释信息等，配合下列选项一起
 #--skip-add-drop-table|--skip-add-locks|--skip�comments|--skip-disable-keys
 
--protocol=val #指定客户端连接协议tcp|socket|pipe|memory
--hex-blob #使用十六进制格式导出二进制字段,影响到的字段类型有BINARY, VARBINARY, BLOB
```

##### 4.4、mysqldump备份策略

```
#--single-transaction不锁表进行备份
#--flush-privileges会在生产的备份文件生成的末尾FLUSH PRIVILEGES，可以在导入后刷新权限
mysqldump -uroot -p -A -F -E -R --triggers --single-transaction --flush-logs --source-data=2 --flush-privileges --default-character-set=utf8 --hex-blob > ${BACKUP}/fullbak_${BACKUP_TIME}.sql

#如果版本比较久的话，--source-data要改为--master-data
```

#### 5、热备份之xtrabackup物理备份

暂时略

#### 6、增量备份

##### 6.1、mysqlbinlog

mysqlbinlog只是一个解析二进制日志的工具，因为可能乱码，它不是用来直接备份的工具。

解析二进制文件，使其不乱码，然后重定向到文件中。

```linux
#用法
mysqlbinlog [options] log-files

#常用选项
--start-position  #起始位置
--stop-position   #结束位置
--start-datetime  #起始时间点
--stop-datetime   #结束时间点

#样例
mysqlbinlog --start-position=157 /data/mysql/binlog.000001 > addbackup.sql
mysqlbinlog --start-position=157 --stop-position=687 /data/mysql/binlog.000001 | gzip > addbackup.sql.gz
mysqlbinlog --start-datetime="2023-08-14 10:00:00" /data/mysql/binlog.000001 > addbackup.sql
```

##### 6.2、增量备份

(1)第一步，进行全量备份

增量备份的前提是完全备份，在完全备份的基础上利用mysqlbinlog进行增量备份。

```linux
#先进行全量备份
mysqldump -uroot -p -A -F -E -R --triggers --single-transaction --flush-logs --source-data=2 --flush-privileges --default-character-set=utf8 --hex-blob > all.sql

#找到全量备份文件备份到了哪个二进制日志文件的哪个position
root@Ubuntu22:/data/mysql# head -n 30 backup.sql | grep -i change
-- CHANGE MASTER TO MASTER_LOG_FILE='binlog.000001', MASTER_LOG_POS=157;
```

(2)基于二进制日志进行增量备份

增量备份的过程其实就是备份二进制日志的过程，将全量备份后的二进制日志按照时间进行切割。

```linux
#mysqladmin -uroot -plqxLQX--%213 flush-logs
方案一：每天0点flush logs，然后将昨天的二进制日志备份到到远端
方案二：使用rsync实时同步二进制日志备份到远端
```

每周一次全量备份，实时使用rsync+inotify实现二进制备份到远端

### 六、主从搭建

#### 主从好处：

1.主库出现问题，切换从库提供服务

2.读写分离，主库写，从库读

3.正常得加全局锁才能备份，搭建主从后用主库增删改，从库全局锁再备份（有延迟）

#### 原理：

基于二进制日志

![image-20241019220905099](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20241019220905099.png)

主库的操作都会写入binlog日志（二进制日志，记录了主库所有数据变更）

从库i/o线程发起请求连接master数据库连接binlog日志，通过binlog dump，返回到io线程写入中继日志（relay log），在salve还有一个线程叫sql线程，读取中继日志，把数据变化反映到自身数据库。

#### 步骤：

##### 拷贝所有：

mysqldump -u root -p --all-databases >all.sql

##### 首先，准备主从服务器环境，装上同版本MySQL，网络弄通

##### 主服务器修改my.cnf配置文件，设置server-id，开启二进制日志，重启服务

--vim /etc/my.cnf

server-id=117

log-bin=mysql-bin

read-only=0#0读写 1只读

--systemctl restart mysqld#重启

##### 创建具有复制权限的用户，运行show master status;把文件名和位置记下来。

```sql
CREATE USER 'repl_user'@'%' IDENTIFIED BY 'your_password';
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'%';
FLUSH PRIVILEGES;
SHOW MASTER STATUS;
```

##### 从服务器修改my.cnf配置文件，设置server-id，重启服务

--vim /etc/my.cnf

server-id=118

read-only=1#0读写 1只读

--systemctl restart mysqld#重启

change master to连接，start slave启动，show slave status\g;检查

```sql
CHANGE MASTER TO
MASTER_HOST='主服务器 IP 地址',
MASTER_USER='repl_user',
MASTER_PASSWORD='your_password',
MASTER_LOG_FILE='主服务器二进制日志文件名',
MASTER_LOG_POS=主服务器二进制日志位置;
```

```sql
START SLAVE;#启动服务
SHOW SLAVE STATUS\G;#查看服务状态
show processlist\G;#查看主节点dump线程
```

###### 
