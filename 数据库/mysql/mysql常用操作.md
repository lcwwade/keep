#### 1.进入和退出命令行（mac环境）

```shell
//1.进入bin目录
cd /usr/local/mysql/bin/

//2.mysql登录,root登录，输入密码即可进入
./mysql -u root -p

//登录后使用数据库
mysql -u root -p -h 192.168.1.1 database

//3.退出mysql
exit

```



#### 2. 常用命令

> 每条语句输入完毕后要在末尾填加分号';'，或者填加'\g'也可以

```mysql
//1.创建数据库：
create database 数据库名称;

//2.查看数据库：
show databases;

//3.删除数据库：
drop database 数据库名称;

//4.打开数据库：
use 数据库名称；

//5.查看表结构信息
desc tableName;

//6.查询时间：
select now();

//7.查询当前用户：
select user();

//8.查询数据库版本：
select version();

//9.查询当前使用的数据库：
select database();

//10.列出所有数据表
show tables;

//11.列出查询表中所有索引
show index from tableName; 

//12.删除索引
drop index indexName on tableName;

//13.显示当前表的建表语句
show create table tableName;

//14.列出table_name完整信息，如栏名、类型。
explain tableName / describe tableName;

//15.在不重启的情况下刷新用户权限
flush privileges

```



#### 增加新用户

>格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码"

```mysql
mysql> 如，增加一个用户user1密码为password1，让其可以在本机上登录， 并对所有数?据库有查询、插入、修改、删除的权限：

grant select,insert,update,delete on *.* to user1@localhost Identified by "password1";

如果希望该用户能够在任何机器上登陆mysql，则将localhost改为"%"。

(1) 创建用户user1从ip为192.168.100.124的主机连接到mysql服务器，密码为h3c，并赋予全部的权限

GRANT ALL PRIVILEGES ON *.* TO 'user1'@'192.168.100.124' IDENTIFIED BY 'h3c' WITH GRANT OPTION;

FLUSH PRIVILEGES;

(2) 创建用户user2从ip为192.168.100.124的主机连接到mysql服务器，密码为h3c，并赋予查改删的权限

GRANT select，update，delete ON *.* TO 'user2'@'192.168.100.124' IDENTIFIED BY 'h3c' WITH GRANT OPTION;
```

