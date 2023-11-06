# MySQL

mysql [-h ip地址] [-p 端口号] -u 用户名 -p

```shell
mysql -u root -p	#基本root登录
```

## 基础

DBMS：数据库管理系统

关系型数据库：建立在关系模型基础上，由多张相互连接的二维表组成的数据库

#### SQL分类：

DDL：数据定义语言	DML：数据操作语言	DQL：数据查询语言	DCL：数据控制语言

SQL以分号结束

##### DDL：数据定义语言

```sql
SHOW DATABASES;#查询所有数据库
SELECE DATABASES();#查询当前数据库
#创建
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];
#删除
DROP DATABASE [IF EXISTS] 数据库名;
#使用
USE 数据库名;
```

一般为了避免字符问题，使用utf8mb4字符创建

```sql
SHOW TABELS;#查询当前数据库所有表
DESC 表名;	#查询表结构
SHOW CREATE TABLE 表名;#查询指定表的创建语句
```

###### 创建

```sql
CREATE TABLE 表名（
	字段1 字段1类型[COMMENT 字段1注释]，
	字段2 字段2类型[COMMENT 字段2注释]，
	......
	字段2 字段2类型[COMMENT 字段2注释]
)[COMMENT 表注释];
```

###### 数据类型

| 类型         | 大小    | 范围（有符号）                                               | 范围（无符号）                                               | 用途            |
| ------------ | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------- |
| TINYINT      | 1 Bytes | (-128，127)                                                  | (0，255)                                                     | 小整数值        |
| SMALLINT     | 2 Bytes | (-32 768，32 767)                                            | (0，65 535)                                                  | 大整数值        |
| MEDIUMINT    | 3 Bytes | (-8 388 608，8 388 607)                                      | (0，16 777 215)                                              | 大整数值        |
| INT或INTEGER | 4 Bytes | (-2 147 483 648，2 147 483 647)                              | (0，4 294 967 295)                                           | 大整数值        |
| BIGINT       | 8 Bytes | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)      | (0，18 446 744 073 709 551 615)                              | 极大整数值      |
| FLOAT        | 4 Bytes | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  | 单精度 浮点数值 |
| DOUBLE       | 8 Bytes | (-1.7976931348623157E+308，-2.2250738585072014 E-308)，0，(2.225073858 5072014E-308，1.7976931348623157E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |

###### 字符串类型

| 类型       | 大小                  | 用途                            |
| ---------- | --------------------- | ------------------------------- |
| CHAR       | 0-255 bytes           | 定长字符串                      |
| VARCHAR    | 0-65535 bytes         | 变长字符串                      |
| TINYBLOB   | 0-255 bytes           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 bytes           | 短文本字符串                    |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 bytes        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                    |

varchar会根据存储内容改变占用空间，但性能差于char

###### 时间类型

| 类型      | 大小 ( bytes) | 范围                                                         | 格式                | 用途                     |
| --------- | ------------- | ------------------------------------------------------------ | ------------------- | ------------------------ |
| DATE      | 3             | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 日期值                   |
| TIME      | 3             | '-838:59:59'/'838:59:59'                                     | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1             | 1901/2155                                                    | YYYY                | 年份值                   |
| DATETIME  | 8             | '1000-01-01 00:00:00' 到 '9999-12-31 23:59:59'               | YYYY-MM-DD hh:mm:ss | 混合日期和时间值         |
| TIMESTAMP | 4             | '1970-01-01 00:00:01' UTC 到 '2038-01-19 03:14:07' UTC结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYY-MM-DD hh:mm:ss | 混合日期和时间值，时间戳 |

###### 添加字段

```sql
ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];
```

###### 修改数据类型

```sql
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);
```

###### 修改字段名和字段类型

```sql
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];
```

###### 删除字段

```sql
ALTER TABLE 表名 DROP 字段名;
```

###### 修改表名

```sql
ALTER TABLE 表名 RENAME TO 新表名;
```

###### 删除表

```sql
DROP TABLE [IF EXISTS] 表名;
```

删除指定表，并重新创建：

```sql
TRUNCATE TABLE 表名;
```

##### DML：数据操作语言

###### 插入数据

```sql
INSERT INTO 表名 (字段名1，字段名2...) VALUES(值1，值2,...);	#给指定字段添加数据
INSERT INTO 表名 VALUES(值1，值2,...);	#给所有字段添加数据
```

批量添加数据

```sql
INSERT INTO 表名 (字段名1，字段名2...) VALUES(值1，值2,...),(值1，值2,...),(值1，值2,...);
INSERT INTO 表名 VALUES(值1，值2,...),(值1，值2,...),(值1，值2,...);
```

插入数据时字段顺序与数据一致

字符串与日期应包含在引号中

数据大小应在字段规定范围内

###### 修改数据

```sql
UPDATE 表名 SET 字段名1=值1,字段名2=值2,......[WHERE 条件]
```

如果不设置条件，则会修改整个表

###### 删除数据

```sql
DELETE FROM 表名 [WHERE 条件];
```

DELETE不能删除某一字段的值，应用UPDATE

##### DQL：数据查询语言

```sql
SELECT 字段列表 
	FROM 表名列表 
	WHERE 条件列表 #条件查询
	GROUP BY 分组字段列表 #分组查询
	HAVING 分组后条件列表 
	ORDER BY 排序字段列表 #排序查询
    LIMIT 分页参数; #分页查询
```

基本查询：

```sql
SELECT 字段1[AS 别名1],字段2[AS 别名2],... FROM 表名;
SELECT DISTINCT 字段列表 FROM 表名;	#去除重复记录
```

条件查询：

比较运算符：

| 大于 | 大于等于 | 小于 | 小于等于 | 等于 | 不等于 | 在...范围内      | 多选一  | 模糊匹配    | 是NULL  |
| ---- | -------- | ---- | -------- | ---- | ------ | ---------------- | ------- | ----------- | ------- |
| >    | >=       | <    | <=       | =    | <>或!= | BETWEEN...AND... | IN(...) | LIKE 占位符 | IS NULL |

占位符： _是单个字符,%是任意个字符

逻辑运算符：

并且：AND 或 &&			或者：OR 或 ||		非：NOT 或 ！

###### 聚合函数

将一列数据作为整体进行纵向计算

| 统计数量 | 最大值 | 最小值 | 平均值 | 求和 |
| -------- | ------ | ------ | ------ | ---- |
| count    | max    | min    | avg    | sum  |

```sql
SELECT 聚合函数(字段列表) FROM 表名;
```

NULL值不参与聚合计算

**分组查询:**

```sql
SELECT 字段列表 FROM 表名列表 [WHERE 条件列表] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
```

where与having区别：

执行时机不同：	where是分组前，不满足where不参与分组；having是分组后

判断条件不同：	where不能对聚合函数进行判断

**排序查询：**

升序(默认)：ASC				降序：DESC

```sql
SELECT 字段 FROM 表名 ORDER BY 排序字段1 [ASC 或 DESC] 排序字段2 [ASC 或 DESC];
```

支持多层排序，排序按照字段顺序，字段2在字段1相同的基础上再排序

**分页查询：**

```sql
SELECT 字段列表 FROM 表名 LIMIT 起始索引,查询记录数;
```

起始索引从0开始，起始索引=(查谊页码-1)*每页显示记录数

分查询是数据库的方言，不同的数据库有不同的实现，Mysql是LIMIT

如果查询的是第一页数据，起始索引可以省略，比如直接简写为limit 10

可以理解为跳过几条数据，查询之后的n条数据

##### DCL：数据控制语言

用来控制数据库用户，控制数据库的访问权限

```sql
USE mysql;
SELECT * FROM user;	#查询用户
CREATE USER '用户名'@ '主机名' IDENTIFIED BY '密码'; #创建用户
ALTER USER '用户名'@ '主机名' IDENTIFIED WITH mysql_native_password BY '新密码';	#修改密码
DROP USER '用户名'@ '主机名';	#删除用户
```

主机名可以使用%通配

权限：

```sql
SHOW GRANTS FOR '用户名'@ '主机名';	#查询权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@ '主机名';	#授予权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@ '主机名';	#撤销权限
```

##### 函数

###### 字符串函数

| 函数                             | 功能                                                      |
| -------------------------------- | --------------------------------------------------------- |
| CONCAT(s1, s2, ..., sn)          | 字符串拼接，将s1, s2, ..., sn拼接成一个字符串             |
| LOWER(str)                       | 将字符串全部转为小写                                      |
| UPPER(str)                       | 将字符串全部转为大写                                      |
| LPAD(str, n, pad)                | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度 |
| RPAD(str, n, pad)                | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度 |
| TRIM(str)                        | 去掉字符串头部和尾部的空格                                |
| SUBSTRING(str, start, len)       | 返回从字符串str从start位置起的len个长度的字符串           |
| REPLACE(column, source, replace) | 替换字符串                                                |

使用示例：

```mysql
-- 拼接
SELECT CONCAT('Hello', 'World');
-- 小写
SELECT LOWER('Hello');
-- 大写
SELECT UPPER('Hello');
-- 左填充
SELECT LPAD('01', 5, '-');
-- 右填充
SELECT RPAD('01', 5, '-');
-- 去除空格
SELECT TRIM(' Hello World ');
-- 切片（起始索引为1）
SELECT SUBSTRING('Hello World', 1, 5);
```

```sql
update emp set workno =lpad(workno,5,'0');	#将workno统一为5位数，不足补0
```

###### 数值函数

常见函数：

| 函数        | 功能                             |
| ----------- | -------------------------------- |
| CEIL(x)     | 向上取整                         |
| FLOOR(x)    | 向下取整                         |
| MOD(x, y)   | 返回x/y的模                      |
| RAND()      | 返回0~1内的随机数                |
| ROUND(x, y) | 求参数x的四舍五入值，保留y位小数 |

###### 日期函数

常用函数：

| 函数                               | 功能                                              |
| ---------------------------------- | ------------------------------------------------- |
| CURDATE()                          | 返回当前日期                                      |
| CURTIME()                          | 返回当前时间                                      |
| NOW()                              | 返回当前日期和时间                                |
| YEAR(date)                         | 获取指定date的年份                                |
| MONTH(date)                        | 获取指定date的月份                                |
| DAY(date)                          | 获取指定date的日期                                |
| DATE_ADD(date, INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1, date2)             | 返回起始时间date1和结束时间date2之间的天数        |

```mysql
SELECT DATE_ADD(NOW(), INTERVAL 70 YEAR);	#从当前时间往后推70天
```

###### 流程函数

| 函数                                                         | 功能                                                      |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| IF(value, t, f)                                              | 如果value为true，则返回t，否则返回f                       |
| IFNULL(value1, value2)                                       | 如果value1不为空，返回value1，否则返回value2              |
| CASE WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END    | 如果val1为true，返回res1，... 否则返回default默认值       |
| CASE [ expr ] WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END | 如果expr的值等于val1，返回res1，... 否则返回default默认值 |

例子：

```mysql
select
	name,
	(case when age > 30 then '中年' else '青年' end)
from employee;	#
select
	name,
	(case workaddress when '北京市' then '一线城市' when '上海市' then '一线城市' else '二线城市' end) as '工作地址'
from employee;	#查询表的员工姓名和工作地址，北京/上海为一线城市，其它为二线城市
```

##### 约束

约束是作用于表中字段上的规则，用于限制存储在表中的数据

保证数据库中数据的正确，有效性和完整性

| 约束                    | 描述                                                     | 关键字      |
| ----------------------- | -------------------------------------------------------- | ----------- |
| 非空约束                | 限制该字段的数据不能为null                               | NOT NULL    |
| 唯一约束                | 保证该字段的所有数据都是唯一、不重复的                   | UNIQUE      |
| 主键约束                | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY |
| 默认约束                | 保存数据时，如果未指定该字段的值，则采用默认值           | DEFAULT     |
| 检查约束（8.0.1版本后） | 保证字段值满足某一个条件                                 | CHECK       |
| 外键约束                | 用来让两张图的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

例子：

```mysql
create table user(
	id int primary key auto_increment,#id为主键，并且自动增长
	name varchar(10) not null unique,	#不为空，并且唯一
	age int check(age > 0 and age < 120),	#大于0小于等于120
	status char(1) default '1',	#没有指定值时默认为1
	gender char(1)	#无
);
```

###### 外键约束

用来让两张表数据产生连接，保证数据一致性与完整性

```mysql
CREATE TABLE 表名(
	字段名 字段类型,
	...
	[CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);#创建表时添加

ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名);

-- 例子
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id);
```

删除外键：
```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名;
```

外键约束删除/更新行为

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与RESTRICT一致） |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与NO ACTION一致） |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录 |
| SET NULL    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（要求该外键允许为null） |
| SET DEFAULT | 父表有变更时，子表将外键设为一个默认值（Innodb不支持）       |

更改删除/更新行为：
`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名) ON UPDATE 行为 ON DELETE 行为;`

##### 多表查询

多表关系

- 一对多（多对一）
- 多对多
- 一对一

查询

合并查询（笛卡尔积，会展示所有组合结果）：
`select * from employee, dept;`

> 笛卡尔积：两个集合A集合和B集合的所有组合情况（在多表查询时，需要消除无效的笛卡尔积）

消除无效笛卡尔积：
`select * from employee, dept where employee.dept = dept.id;`

###### 内连接查询

内连接查询的是两张表交集的部分

隐式内连接：
`SELECT 字段列表 FROM 表1, 表2 WHERE 条件 ...;`

显式内连接：
`SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ...;`

显式性能比隐式高

###### 外连接查询

左外连接：
查询左表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ...;`
相当于查询表1的所有数据，包含表1和表2交集部分数据

右外连接：
查询右表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ...;`

###### 自连接查询

当前表与自身的连接查询，自连接必须使用表别名

语法：
`SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ...;`

自连接查询，可以是内连接查询，也可以是外连接查询

###### 联合查询 union, union all

把多次查询的结果合并，形成一个新的查询集

语法：

```mysql
SELECT 字段列表 FROM 表A ...
UNION [ALL]
SELECT 字段列表 FROM 表B ...
```

- UNION ALL 会有重复结果，UNION 不会
- 联合查询比使用or效率高，不会使索引失效

##### 子查询

SQL语句中嵌套SELECT语句，称谓嵌套查询，又称子查询。

```sql
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```
**子查询外部的语句可以是 INSERT / UPDATE / DELETE / SELECT 的任何一个**

根据子查询结果可以分为：

- 标量子查询（子查询结果为单个值）
- 列子查询（子查询结果为一列）
- 行子查询（子查询结果为一行）
- 表子查询（子查询结果为多行多列）

根据子查询位置可分为：

- WHERE 之后
- FROM 之后
- SELECT 之后

###### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等）。
常用操作符：- < > > >= < <=

###### 行子查询

返回的结果是一行（可以是多列）。
常用操作符：=, <, >, IN, NOT IN

```mysql
-- 查询与xxx的薪资及直属领导相同的员工信息
select * from employee where (salary, manager) = (12500, 1);
select * from employee where (salary, manager) = (select salary, manager from employee where name = 'xxx');
```

###### 列子查询

返回的结果是一列（可以是多行）。

常用操作符：

| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| IN     | 在指定的集合范围内，多选一             |
| NOT IN | 不在指定的集合范围内                   |
| ANY    | 子查询返回列表中，有任意一个满足即可   |
| SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
| ALL    | 子查询返回列表的所有值都必须满足       |

例子：

```mysql
-- 查询比财务部所有人工资都高的员工信息
select * from employee where salary > all(select salary from employee where dept = (select id from dept where name = '财务部'));
-- 查询比研发部任意一人工资高的员工信息
select * from employee where salary > any (select salary from employee where dept = (select id from dept where name = '研发部'));
```

###### 表子查询

返回的结果是多行多列
常用操作符：IN

```mysql
-- 查询与xxx1，xxx2的职位和薪资相同的员工
select * from employee where (job, salary) in (select job, salary from employee where name = 'xxx1' or name = 'xxx2');
-- 查询入职日期是2006-01-01之后的员工，及其部门信息
select e.*, d.* from (select * from employee where entrydate > '2006-01-01') as e left join dept as d on e.dept = d.id;
```

##### 事务

事务是一组操作的集合是不可分割的工作单位；事务会把所有操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

```mysql
-- 1. 查询张三账户余额
select * from account where name = '张三';
-- 2. 将张三账户余额-1000
update account set money = money - 1000 where name = '张三';
-- 此语句出错后张三钱减少但是李四钱没有增加
模拟sql语句错误
-- 3. 将李四账户余额+1000
update account set money = money + 1000 where name = '李四';

-- 查看事务提交方式
SELECT @@AUTOCOMMIT;
-- 设置事务提交方式，1为自动提交，0为手动提交，该设置只对当前会话有效
SET @@AUTOCOMMIT = 0;
-- 提交事务
COMMIT;
-- 回滚事务
ROLLBACK;

-- 设置手动提交后上面代码改为：
select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

操作方式二：

开启事务：
`START TRANSACTION 或 BEGIN TRANSACTION;`
提交事务：
`COMMIT;`
回滚事务：
`ROLLBACK;`

操作实例：

```mysql
start transaction;
select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

###### 事务四大特性ACID

- 原子性(Atomicity)：事务是不可分割的最小操作但愿，要么全部成功，要么全部失败
- 一致性(Consistency)：事务完成时，必须使所有数据都保持一致状态
- 隔离性(Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性(Durability)：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的

###### 并发事务问题

| 问题       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 脏读       | 一个事务读到另一个事务还没提交的数据                         |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同             |
| 幻读       | 一个事务按照条件查询数据时，没有对应的数据行，但是再插入数据时，又发现这行数据已经存在 |

###### 事务隔离级别

| 隔离级别              | 脏读 | 不可重复读 | 幻读 |
| --------------------- | ---- | ---------- | ---- |
| Read uncommitted      | √    | √          | √    |
| Read committed        | ×    | √          | √    |
| Repeatable Read(默认) | ×    | ×          | √    |
| Serializable          | ×    | ×          | ×    |

- √表示在当前隔离级别下该问题会出现
- Serializable 性能最低；Read uncommitted 性能最高，数据安全性最差

查看事务隔离级别：
```sql
SELECT @@TRANSACTION_ISOLATION;
```
设置事务隔离级别：
```sql
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE };
```
SESSION 是会话级别，表示只针对当前会话有效，GLOBAL 表示对所有会话有效

## 进阶

#### 存储引擎

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是**基于表而不是基于库**的，所以存储引擎也可以被称为表引擎。
默认存储引擎是InnoDB。

![mysql体系结构](..\note\图\mysql体系结构.jpg)

```mysql
-- 查询建表语句
show create table account;
-- 建表时指定存储引擎
CREATE TABLE 表名(
	...
) ENGINE=INNODB;
-- 查看当前数据库支持的存储引擎
show engines;
```

- 连接层
  最上层是一些客户端和链接服务，主要完成一些类似于连接处理、授权认证、及相关的安全方案。服务器也会为安全接入的每个客户
  端验证它所具有的操作权限。
- 服务层
  第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存
  储引擎的功能也在这一层实现，如 过程、函数等。
- 引擎层
  存储引擎真正的负责了MySL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我
  们可以根据自己的需要，来选取合适的存储引擎。
- 存储层
  主要是将数据存储在文件系统之上，并完成与存储引擎的交互

###### InnoDB

InnoDB 是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5 之后，InnoDB 是默认的 MySQL 引擎。

特点：

- DML 操作遵循 ACID 模型，支持**事务**
- **行级锁**，提高并发访问性能
- 支持**外键**约束，保证数据的完整性和正确性

- xxx.ibd: xxx代表表名，InnoDB 引擎的每张表都会对应这样一个表空间文件，存储该表的表结构（frm、sdi）、数据和索引。

参数：innodb_file_per_table，决定多张表共享一个表空间还是每张表对应一个表空间

###### MyISAM

MyISAM 是 MySQL 早期的默认存储引擎。

特点：

- **不支持事务，不支持外键**
- 支持表锁，不支持行锁
- 访问速度快

文件：

- xxx.sdi: 存储表结构信息
- xxx.MYD: 存储数据
- xxx.MYI: 存储索引

###### Memory

Memory 引擎的表数据是**存储在内存**中的，受硬件问题、断电问题的影响，只能将这些表作为临时表或缓存使用。

特点：

- 存放在内存中，速度快
- 可使用hash索引（默认）

文件：

- xxx.sdi: 存储表结构信息

##### 存储引擎特点

| 特点         | InnoDB              | MyISAM | Memory |
| ------------ | ------------------- | ------ | ------ |
| 存储限制     | 64TB                | 有     | 有     |
| 事务安全     | 支持                | -      | -      |
| 锁机制       | 行锁                | 表锁   | 表锁   |
| B+tree索引   | 支持                | 支持   | 支持   |
| Hash索引     | -                   | -      | 支持   |
| 全文索引     | 支持（5.6版本之后） | 支持   | -      |
| 空间使用     | 高                  | 低     | N/A    |
| 内存使用     | 高                  | 低     | 中等   |
| 批量插入速度 | 低                  | 高     | 高     |
| 支持外键     | 支持                | -      | -      |

- InnoDB:是Mysl的默认存储擎，支持事务、外键。如果应用对**事务的完整性**有比较高的要求，在并发条件下要求数据的一致
  性生，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么lnnoDB存储引擎是比较合适的选择。
- MyISAM :如果应用是以读操作和插入操作为主，只有**很少的更新和删除操作**，并且对事务的完整性、并发性要求不是很高，那
  么选择这个存储引擎是非常合适的。
- MEMORY:将所有数据保存在内存中，访问速度快，通常用于**临时表及缓存**。MEMORY的缺陷就是对表的大小有限制，太大的表
  无法缓存在内存中，而且无法保障数据的安全性。

#### 性能分析

##### 查看执行频次

查看当前数据库的 INSERT, UPDATE, DELETE, SELECT 等访问频次：
`SHOW GLOBAL STATUS LIKE 'Com_______';` 或者 `SHOW SESSION STATUS LIKE 'Com_______';`

##### 慢查询日志

	# 默认关闭，开启慢查询日志开关
	slow_query_log=1
	# 设置慢查询日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
	long_query_time=2
打开后存于/var/lib/mysql/localhost-slow.log

更改后记得重启MySQL服务

##### profile

show profile 能在做SQL优化时帮我们了解时间都耗费在哪里。

通过 have_profiling 参数，能看到当前 MySQL 是否支持 profile 操作

`SELECT @@have_profiling;`
profiling 默认关闭，可以通过set语句在session/global级别开启 profiling：
`SET profiling = 1;`
查看所有语句的耗时：
`show profiles;`
查看指定query_id的SQL语句各个阶段的耗时：
`show profile for query query_id;`
查看指定query_id的SQL语句CPU的使用情况
`show profile cpu for query query_id;`

##### explain

EXPLAIN 或者 DESC 命令获取 MySQL 如何执行 SELECT 语句的信息

EXPLAIN 或者 DESC 命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接的顺序。
```
# 直接在select语句之前加上关键字 explain / desc
EXPLAIN SELECT 字段列表 FROM 表名 HWERE 条件;
```
EXPLAIN 各字段含义：

- id：select 查询的序列号，表示查询中执行 select 子句或者操作表的顺序（id相同，执行顺序从上到下；id不同，值越大越先执行）
- select_type：表示 SELECT 的类型，常见取值有 SIMPLE（简单表，即不适用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUBQUERY（SELECT/WHERE之后包含了子查询）等
- type：表示连接类型，性能由好到差的连接类型为 NULL、system、const、eq_ref、ref、range、index、all
- possible_key：可能应用在这张表上的索引，一个或多个
- Key：实际使用的索引，如果为 NULL，则没有使用索引
- Key_len：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好
- rows：MySQL认为必须要执行的行数，在InnoDB引擎的表中，是一个估计值，可能并不总是准确的
- filtered：表示返回结果的行数占需读取行数的百分比，filtered的值越大越好

#### 索引

```sql
#创建索引：
CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name, ...);
#如果不加 CREATE 后面不加索引类型参数，则创建的是常规索引
#查看索引：
SHOW INDEX FROM table_name;
#删除索引：
DROP INDEX index_name ON table_name;
```



优点：

- 提高数据检索效率，降低数据库的IO成本
- 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗

缺点：

- 索引列也是要占用空间的
- 索引大大提高了查询效率，但降低了更新的速度，比如 INSERT、UPDATE、DELETE

| 索引结构            | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| B+Tree              | 最常见的索引类型，大部分引擎都支持B+树索引                   |
| Hash                | 底层数据结构是用哈希表实现，只有精确匹配索引列的查询才有效，不支持范围查询 |
| R-Tree(空间索引)    | 空间索引是 MyISAM 引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少 |
| Full-Text(全文索引) | 是一种通过建立倒排索引，快速匹配文档的方式，类似于 Lucene, Solr, ES |

**聚集索引**：将数据存储与索引放一块，索引结构的叶子节点保存了行数据，有且仅有一个

**二级索引**：将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键，可以有多个

为什么 InnoDB 存储引擎选择使用 B+Tree 索引结构？

- 相对于二叉树，层级更少，搜索效率高
- 对于 B-Tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针也跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
- 相对于 Hash 索引，B+Tree 支持范围匹配及排序操作

##### 最左前缀法则

如果索引关联了多列（联合索引），要遵守最左前缀法则，最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。
如果跳跃某一列，索引将部分失效（后面的字段索引失效）。

##### 索引失效情况

1. 在索引列上进行运算操作，索引将失效。如：`explain select * from tb_user where substring(phone, 10, 2) = '15';`
2. 字符串类型字段使用时，不加引号，索引将失效。如：`explain select * from tb_user where phone = 17799990015;`，此处phone的值没有加引号
3. 模糊查询中，如果仅仅是尾部模糊匹配，索引不会是失效；如果是**头部模糊匹配**，索引失效。如：`explain select * from tb_user where profession like '%工程';`，前后都有 % 也会失效。
4. 用 or 分割开的条件，如果 or 其中一个条件的列没有索引，那么涉及的索引都不会被用到。
5. 如果 MySQL 评估使用索引比全表更慢，则不使用索引。

##### 覆盖索引&回表查询

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能找到），减少 select *。

当普通索引找不到我们要的完整信息，迫不得已要执行回表查询，再回到主键索引或者聚集索引中查询数据

##### 前缀索引

当字段类型为字符串（varchar, text等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率，此时可以只降字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

##### 规定使用索引

使用索引use index：
`explain select * from tb_user use index(idx_user_pro) where profession="软件工程";`
不使用哪个索引ignore index：
`explain select * from tb_user ignore index(idx_user_pro) where profession="软件工程";`
必须使用哪个索引force index：
`explain select * from tb_user force index(idx_user_pro) where profession="软件工程";`

use 是建议，实际使用哪个索引 MySQL 还会自己权衡运行速度去更改，force就是无论如何都强制使用该索引

##### 设计原则

1. 针对于数据量较大，且查询比较频繁的表建立索引
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段长度较长，可以针对于字段的特点，建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价就越大，会影响增删改的效率
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询

#### SQL优化

##### 插入数据

普通插入：

1. 采用批量插入（一次插入的数据不建议超过1000条）
2. 手动提交事务
3. 主键顺序插入

大批量插入：
如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令插入

##### 主键优化

主键设计原则：

- 满足业务需求的情况下，尽量降低主键的长度
- 插入数据时，尽量选择顺序插入，选择使用 AUTO_INCREMENT 自增主键
- 尽量不要使用 UUID 做主键或者是其他的自然主键，如身份证号
- 业务操作时，避免对主键的修改

数据组织方式：在InnoDB存储引擎中，表数据都是**根据主键顺序组织存放**的，这种存储方式的表称为索引组织表（Index organized table, IOT）

页分裂：页可以为空，也可以填充一半，也可以填充100%，每个页包含了2-N行数据（如果一行数据过大，会行溢出），根据主键排列。
页合并：当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。当页中删除的记录到达 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前后）看看是否可以将这两个页合并以优化空间使用。

MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或创建索引时指定

##### order by优化

1. Using filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区 sort buffer 中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序
2. Using index：通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高

尽量优化为index

如果order by字段全部使用升序排序或者降序排序，则都会走索引，但是如果一个字段升序排序，另一个字段降序排序（假设索引都是升序），则不会走索引

- 根据排序字段建立合适的索引，多字段排序时，也遵循**最左前缀法则**
- 尽量使用覆盖索引
- 多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）
- 如果不可避免出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 sort_buffer_size（默认256k）

##### group by优化

- 在分组操作时，可以通过索引来提高效率
- 分组操作时，索引的使用也是满足最左前缀法则的

##### limit优化

常见的问题如`limit 2000000, 10`，此时需要 MySQL 排序前2000000条记录，但仅仅返回2000000 - 2000010的记录，其他记录丢弃，越往后性能越差，查询排序的代价非常大。
优化方案：一般大数据量分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化

```sql
-- 此语句耗时很长
select * from tb_sku limit 9000000, 10;
-- 通过覆盖索引加快速度，直接通过主键索引进行排序及查询
select id from tb_sku order by id limit 9000000, 10;
-- 通过连表查询即可实现第一句的效果，并且能达到第二句的速度
select s.* from tb_sku as s, (select id from tb_sku order by id limit 9000000, 10) as a where s.id = a.id;
-- 下面的语句是错误的，因为 MySQL 不支持 in 里面使用 limit
select * from tb_sku where id in (select id from tb_sku order by id limit 9000000, 10);
```

##### count优化

MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(*) 的时候会直接返回这个数，效率很高（前提是不适用where）；
InnoDB 在执行 count(*) 时，需要把数据一行一行地从引擎里面读出来，然后累计计数。
优化方案：自己计数，如创建key-value表存储在内存或硬盘，或者是用redis

各种用法的性能：

- count(主键)：InnoDB引擎会遍历整张表，把每行的主键id值都取出来，返回给服务层，服务层拿到主键后，直接按行进行累加（主键不可能为空）
- count(字段)：没有not null约束的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为null，不为null，计数累加；有not null约束的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接按行进行累加（额外需要判断null）
- count(1)：InnoDB 引擎遍历整张表，但不取值。服务层对于返回的每一层，放一个数字 1 进去，直接按行进行累加
- count(*)：InnoDB 引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加

按效率排序：count(字段) < count(主键) < count(1) < count(*)，所以尽量使用 count(*)

##### update优化

InnoDB 的行锁是针对**索引**加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级为表锁。

如以下两条语句：
`update student set no = '123' where id = 1;`，这句由于id有主键索引，所以只会锁这一行；
`update student set no = '123' where name = 'test';`，这句由于name没有索引，所以会把整张表都锁住进行数据更新，解决方法是给name字段添加索引

#### 视图

只保存SQL逻辑，不保存实际数据

```sql
create [or replace] view 视图名 as 查询语句 [with cascaded check option]
# 查询创建视图语句
show create view 视图名
#查询视图
select * from 视图名
```

##### 检查选项

创建视图时添加```with cascaded check option```，视图会检查更改的内容是否符合视图定义

检查选项有cascaded和local，当视图是在其他视图上创建时，cascaded只会检查当前视图约束，而local会递归检查依赖的视图的约束。但如果依赖的视图没有设置检查选项则不会影响

##### 视图更新

视图更新要求视图的行与基础表一对一，否则不会更新，包含以下选项时不可更新

- 聚合函数或窗口函数
- DISTINCT
- GROUP BY
- HAVING
- UNION或UNION ALL

#### 存储过程

对SQL语句的代码封装和重用，有点类似函数定义

```sql
create procedure 过程名()
begin
	sql语句;
end;
#执行存储过程
call 过程名()
#展示存储过程定义
show create procedure 过程名()
#删除存储过程
drop [if exists] procedure 过程名()
#查看指定数据库的存储过程
select * from INFORMATION_SCHEMA.ROUTINES where ROUTINE_SCHEMA='xxx'
```

如果在命令行内定义，会因为sql语句内的分号提前结束，可通过自定义结束符号```delimiter $$```更改执行结束符号。使用结束后再改回分号```delimiter ;```

##### 定义变量

declare 变量名 变量类型

```sql
DECLARE a INT default 50;
DECLARE b varchar(10);
```

##### if语法

```sql
IF 条件1 THEN
......
ELSEIF 条件2 THEN#可选
......
ELSE#可选
......
END IF;
```

##### 存储函数

有返回值的存储过程，参数只能为IN类型

#### 触发器

在insert/update/delete前或后，触发并执行定义的sql语句。使用OLD或NEW来引用变化前后的记录内容。目前触发器只支持行级触发，不支持语句级触发

语句级：执行一行sql触发一次，与sql影响的数据量无关

行级：每影响一个行触发一次

```sql
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE
ON 表名 FOR EACH ROW
BEGIN
	sql语句;
END
#查看触发器
show TRIGGERS
#删除
drop TRIGGER [表名].触发器名
```

insert只有new可用，delete只有old可用

#### InnoDB

##### 逻辑存储结构

![InnoDB](.\图\InnoDB.jpg)

表空间(ibd文件)：一个mysql实例可以对应多个表空间，用于存储记录，索引等数据

段：分为数据段，索引段，回滚段

InnoDB是索引组织表，数据段就是B+树的叶子结点，索引段就是B+树的非叶子结点，段用来管理多个区

区：表空间的单元结构，默认区大小1M。InnoDB默认页大小16K，即默认区有64页

页：InnoDB磁盘管理的最小单元。为了保证页连续，InnoDB每次从磁盘申请4-5个区

行：即存储的数据

Trx_id：每次对某条记录改动时，都会把对应事务id赋值给trx_id

Roll_pointer：每次修改时会把旧版本写到undo日志，该列则指向修改前的信息

##### 内存架构

###### Buffer Poll

缓冲池，执行操作时先修改缓冲池数据，若没有则先从磁盘加载，修改后再刷新到磁盘，减少磁盘IO

缓冲池以页为单位，包含free page（未使用），clean page（使用，数据未变动），dirty page（使用，数据已变动且与磁盘不一致）

###### Change Buffer

更改缓冲区（针对非唯一二级索引页），在执行DML语句时如果数据不在Buffer Poll，不会直接操作磁盘，而是先把变更存在Change Buffer。未来数据被读取时再将数据合并恢复到Buffer Poll，再将合并后数据写入磁盘

意义：由于二级索引通常非唯一，且一般插入随机，并且删除和更新会影响索引数中不相邻的二级索引页。如果每次都操作磁盘会造成大量的磁盘IO。

###### Adaptive Hash Index

自适应哈希索引，用于优化对buffer poll的查询

###### Log buffer

日志缓冲区，保存要写入磁盘的log日志，默认大小16M

##### 磁盘结构

###### system tablespace

系统表空间，更改缓冲区的存储区域

###### file-per-table tablespaces

每个表的文件表空间，包含单个InnoDB表的数据和索引，并存储在单个数据文件

###### general tablespaces

通用表空间，需要手动创建。创建表时可以手动指定使用该表空间

###### Undo tablespaces

撤销表空间，存储undo log日志

###### temporary tablespaces

临时表空间

###### doublewrite buffer files

双写缓冲区，InnoDB在将数据读入buffer pool前先写入该处，便于异常恢复

###### redo log

重做日志，保证事务持久性。当事务提交后会把所有修改信息存于该日志，用于刷新脏页到磁盘发生错误时进行数据恢复

有重做日志缓冲和重做日志文件两部分，分别在内存和磁盘

##### 事务

原子性，一致性，永久性由redo log和undo log保证；隔离性由锁和mvcc保证

###### redo log

分为重做日志缓冲和重做日志文件

事务提交时除了在buffer pool中写入数据，还会在redolog buffer写入一份，而redolog会将其写入磁盘的日志文件。当事务完成提交后，在脏页写入磁盘发生错误时可从redolog日志恢复。

由于redolog日志是一整个文件，对其为顺序写入，比从buffer pool**立即**写入磁盘随机位置性能要高。若buffer pool成功写入则该文件失效并定时清理

若redolog写入失败则事务也会失败

###### undo log

回滚日志，记录之前被修改的信息

不同的是undo log是逻辑日志，记录的是操作

##### MVCC多版本并发控制

- 当前读

读取记录的最新版本。读取时保证其他事务不能修改当前记录，会对读取记录加锁

- 快照读

读取时不加锁就是快照读，读取的是可见版本，可能是历史数据

Read Committed：每次select生成一个快照读

Repeatable Read：开启事务第一个select才是快照读

Serializable：快照读退化为当前读

- MVCC

维护一个数据的多个版本，使读写操作没有冲突，依赖三个隐式字段，undo log，readview

三个隐藏字段  DB_TRX_ID最近修改事务ID  DB_ROLL_PTR回滚指针   DB_ROW_ID隐藏主键
