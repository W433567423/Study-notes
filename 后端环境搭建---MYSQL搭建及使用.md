## MYSQL搭建及使用
* * *
- 安装mysql  `apt install mysql-server`
- 初始化mysql `mysqld--initialize-insecure`
- 开启mysql服务(ubuntu)
	`service mysql start`
	`service mysql status`
(windows)
	设置环境变量
	`mysqld --install <服务名> -defaults-file="\\mysql80\my.ini"`
	`net start <服务名>`启动mysql
- 登陆mysql`sudo mysql -uroot -p` 
- 创建一个用户并设置密码`create user '<用户名>' identified with mysql_native_password by '<密码>';`
- 修改用户名密码`alter user '<用户名>' identified by '<密码>';`
- 赋予权限`grant all privileges ON *.* TO admin@'%'`
* * *
**mysql中的数据类型**
- 字符类型 `char varchar text`
- 整数类型 `tinyint smallint mediumint int bigint`
- 浮点类型 `float double`
- 日期/时间类型 `DATE DATETIME`
* * *
**数据库操作**
- 看数据库`show database;`
- 创建数据库`create database IF NOT EXISTS <数据库名> CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;`
- 使用数据库`use <数据库名>；`
- 删除数据库`drop database <数据库名> IF EXISTS;`
- 创建数据表
```
	create table <数据库名>(userid int NOT NULL AUTO_INCREMENT,
		username varchar(30) NOT NULL,
		password int NOT NULL,
		address varchar(50) default '没有填写地址',
		valid TINYINT default 1,
		birth DATETIME null,
		PRIMARY KEY (userid));
```
**数据表操作**
- 看表`show tables;`
- 修改表名`ALTER TABLE <数据库名> IF EXISTS；`
- 删除表`DROP TABLE <数据表名> RENAME TO <新名字>;`
- 修改表字段`ALTER TABLE <表名> CHANGE <旧字段名> <新字段名> <新数据类型>;`
- 增加表字段`ALTER RABLE <表名> add <字段名> <数据类型>;`
- 删除表字段`ALTER RABLE <表名> DROP <字段名>;`

**数据操作**
* * *
- 插入数据`insert into <数据库>(<字段名>,<字段名>,<字段名>,<字段名>,<字段名>) values(<数据>，<数据>，<数据>，<数据>，<数据>);`
- **查询数据**
	- 查询所有 `select * from <表名>;`
	- 投影查询 
		- 别名设置select <字段名> as <别名>;
	- limit查询
		- `limit <起始位置>,<记录数>;`
		- `limit <记录数>;`
		- `limit <记录数> offset <起始位置>;`
		
	- 条件查询
		- `and查询 or查询 between查询 in查询 null查询 模糊查询`
			-  *in查询某几个值* 
			- *like %代表不确定*
			- _代表一个未知字符
- 更新数据 `update <表名> set <字段名>=<赋值> where <条件>`

23/02/2023 22:45

* * *
**远程登陆数据库**
-  显式密码登陆 `sudo mysql -h <IP/域名> -P <端口> -u <用户 >-p<密码>`
-  隐式登陆 `mysql -h <IP/域名> -P <端口> -u <用户> -p`
  
24/02/2023 10:55