# 基础篇

## 通用语法及分类

- DDL: 数据定义语言，用来定义数据库对象（数据库、表、字段）
- DML: 数据操作语言，用来对数据库表中的数据进行增删改
- DQL: 数据查询语言，用来查询数据库中表的记录
- DCL: 数据控制语言，用来创建数据库用户、控制数据库的控制权限

### DDL（数据定义语言）

数据定义语言

#### 数据库操作

查询所有数据库：
`SHOW DATABASES;`
查询当前数据库：
`SELECT DATABASE();`
创建数据库：
`CREATE DATABASE [ IF NOT EXISTS ] 数据库名 [ DEFAULT CHARSET 字符集] [COLLATE 排序规则 ];`
删除数据库：
`DROP DATABASE [ IF EXISTS ] 数据库名;`
使用数据库：
`USE 数据库名;`

##### 注意事项

- UTF8字符集长度为3字节，有些符号占4字节，所以推荐用utf8mb4字符集

#### 表操作

查询当前数据库所有表：
`SHOW TABLES;`
查询表结构：
`DESC 表名;`
查询指定表的建表语句：
`SHOW CREATE TABLE 表名;`

创建表：
```mysql
CREATE TABLE 表名(
	字段1 字段1类型 [COMMENT 字段1注释],
	字段2 字段2类型 [COMMENT 字段2注释],
	字段3 字段3类型 [COMMENT 字段3注释],
	...
	字段n 字段n类型 [COMMENT 字段n注释]
)[ COMMENT 表注释 ];
```
**最后一个字段后面没有逗号**

添加字段：
`ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];`
例：`ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';`

修改数据类型：
`ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);`
修改字段名和字段类型：
`ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];`
例：将emp表的nickname字段修改为username，类型为varchar(30)
`ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';`

删除字段：
`ALTER TABLE 表名 DROP 字段名;`

修改表名：
`ALTER TABLE 表名 RENAME TO 新表名`

删除表：
`DROP TABLE [IF EXISTS] 表名;`
删除表，并重新创建该表：
`TRUNCATE TABLE 表名;`

### DML（数据操作语言）

#### 添加数据

指定字段：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);`
全部字段：
`INSERT INTO 表名 VALUES (值1, 值2, ...);`

批量添加数据：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`
`INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`

##### 注意事项

- 字符串和日期类型数据应该包含在引号中
- 插入的数据大小应该在字段的规定范围内

#### 更新和删除数据

修改数据：
`UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, ... [ WHERE 条件 ];`
例：
`UPDATE emp SET name = 'Jack' WHERE id = 1;`

删除数据：
`DELETE FROM 表名 [ WHERE 条件 ];`

### DQL（数据查询语言）

语法：
```mysql
SELECT
	字段列表
FROM
	表名字段
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后的条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

#### 基础查询

查询多个字段：
`SELECT 字段1, 字段2, 字段3, ... FROM 表名;`
`SELECT * FROM 表名;`

设置别名：
`SELECT 字段1 [ AS 别名1 ], 字段2 [ AS 别名2 ], 字段3 [ AS 别名3 ], ... FROM 表名;`
`SELECT 字段1 [ 别名1 ], 字段2 [ 别名2 ], 字段3 [ 别名3 ], ... FROM 表名;`

去除重复记录：
`SELECT DISTINCT 字段列表 FROM 表名;`

转义：
`SELECT * FROM 表名 WHERE name LIKE '/_张三' ESCAPE '/'`
/ 之后的\_不作为通配符

#### 条件查询

语法：
`SELECT 字段列表 FROM 表名 WHERE 条件列表;`

条件：

| 比较运算符          | 功能                                        |
| ------------------- | ------------------------------------------- |
| >                   | 大于                                        |
| >=                  | 大于等于                                    |
| <                   | 小于                                        |
| <=                  | 小于等于                                    |
| =                   | 等于                                        |
| <> 或 !=            | 不等于                                      |
| BETWEEN ... AND ... | 在某个范围内（含最小、最大值）              |
| IN(...)             | 在in之后的列表中的值，多选一                |
| LIKE 占位符         | 模糊匹配（\_匹配单个字符，%匹配任意个字符） |
| IS NULL             | 是NULL                                      |

| 逻辑运算符         | 功能                         |
| ------------------ | ---------------------------- |
| AND 或 &&          | 并且（多个条件同时成立）     |
| OR 或 &#124;&#124; | 或者（多个条件任意一个成立） |
| NOT 或 !           | 非，不是                     |

例子：
```mysql
-- 年龄等于30
select * from employee where age = 30;
-- 年龄小于30
select * from employee where age < 30;
-- 小于等于
select * from employee where age <= 30;
-- 没有身份证
select * from employee where idcard is null or idcard = '';
-- 有身份证
select * from employee where idcard;
select * from employee where idcard is not null;
-- 不等于
select * from employee where age != 30;
-- 年龄在20到30之间
select * from employee where age between 20 and 30;
select * from employee where age >= 20 and age <= 30;
-- 下面语句不报错，但查不到任何信息
select * from employee where age between 30 and 20;
-- 性别为女且年龄小于30
select * from employee where age < 30 and gender = '女';
-- 年龄等于25或30或35
select * from employee where age = 25 or age = 30 or age = 35;
select * from employee where age in (25, 30, 35);
-- 姓名为两个字
select * from employee where name like '__';
-- 身份证最后为X
select * from employee where idcard like '%X';
```

#### 聚合查询（聚合函数）

常见聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

语法：
`SELECT 聚合函数(字段列表) FROM 表名;`
例：
`SELECT count(id) from employee where workaddress = "广东省";`

#### 分组查询

语法：
`SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后的过滤条件 ];`

where 和 having 的区别：

- 执行时机不同：where是分组之前进行过滤，不满足where条件不参与分组；having是分组后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。

例子：

```mysql
-- 根据性别分组，统计男性和女性数量（只显示分组数量，不显示哪个是男哪个是女）
select count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性数量
select gender, count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性的平均年龄
select gender, avg(age) from employee group by gender;
-- 年龄小于45，并根据工作地址分组
select workaddress, count(*) from employee where age < 45 group by workaddress;
-- 年龄小于45，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from employee where age < 45 group by workaddress having address_count >= 3;
```

##### 注意事项

- 执行顺序：where > 聚合函数 > having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 排序查询

语法：
`SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2;`

排序方式：

- ASC: 升序（默认）
- DESC: 降序

例子：

```mysql
-- 根据年龄升序排序
SELECT * FROM employee ORDER BY age ASC;
SELECT * FROM employee ORDER BY age;
-- 两字段排序，根据年龄升序排序，入职时间降序排序
SELECT * FROM employee ORDER BY age ASC, entrydate DESC;
```

##### 注意事项

如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序

#### 分页查询

语法：
`SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;`

例子：

```mysql
-- 查询第一页数据，展示10条
SELECT * FROM employee LIMIT 0, 10;
-- 查询第二页
SELECT * FROM employee LIMIT 10, 10;
```

##### 注意事项

- 起始索引从0开始，起始索引 = （查询页码 - 1） * 每页显示记录数
- 分页查询是数据库的方言，不同数据库有不同实现，MySQL是LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写 LIMIT 10

#### DQL执行顺序

FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT

### DCL

#### 管理用户

查询用户：

```mysql
USER mysql;
SELECT * FROM user;
```

创建用户:
`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`

修改用户密码：
`ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';`

删除用户：
`DROP USER '用户名'@'主机名';`

例子：

```mysql
-- 创建用户test，只能在当前主机localhost访问
create user 'test'@'localhost' identified by '123456';
-- 创建用户test，能在任意主机访问
create user 'test'@'%' identified by '123456';
create user 'test' identified by '123456';
-- 修改密码
alter user 'test'@'localhost' identified with mysql_native_password by '1234';
-- 删除用户
drop user 'test'@'localhost';
```

##### 注意事项

- 主机名可以使用 % 通配

#### 权限控制

常用权限：

| 权限                | 说明               |
| ------------------- | ------------------ |
| ALL, ALL PRIVILEGES | 所有权限           |
| SELECT              | 查询数据           |
| INSERT              | 插入数据           |
| UPDATE              | 修改数据           |
| DELETE              | 删除数据           |
| ALTER               | 修改表             |
| DROP                | 删除数据库/表/视图 |
| CREATE              | 创建数据库/表      |

更多权限请看[权限一览表](#权限一览表 "权限一览表")

查询权限：
`SHOW GRANTS FOR '用户名'@'主机名';`

授予权限：
`GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`

撤销权限：
`REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';`

##### 注意事项

- 多个权限用逗号分隔
- 授权时，数据库名和表名可以用 * 进行通配，代表所有

## 函数

- 字符串函数
- 数值函数
- 日期函数
- 流程函数

### 字符串函数

常用函数：

| 函数  | 功能  |
| ------------ | ------------ |
| CONCAT(s1, s2, ..., sn)  | 字符串拼接，将s1, s2, ..., sn拼接成一个字符串  |
| LOWER(str)  | 将字符串全部转为小写  |
| UPPER(str)  | 将字符串全部转为大写  |
| LPAD(str, n, pad)  | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度  |
| RPAD(str, n, pad)  | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度  |
| TRIM(str)  | 去掉字符串头部和尾部的空格  |
| SUBSTRING(str, start, len)  | 返回从字符串str从start位置起的len个长度的字符串  |

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

### 数值函数

常见函数：

| 函数  | 功能  |
| ------------ | ------------ |
| CEIL(x)  | 向上取整  |
| FLOOR(x)  | 向下取整  |
| MOD(x, y)  | 返回x/y的模  |
| RAND() | 返回0~1内的随机数 |
| ROUND(x, y) | 求参数x的四舍五入值，保留y位小数 |

### 日期函数

常用函数：

| 函数  | 功能  |
| ------------ | ------------ |
| CURDATE()  | 返回当前日期  |
| CURTIME()  | 返回当前时间  |
| NOW()  | 返回当前日期和时间  |
| YEAR(date)  | 获取指定date的年份  |
| MONTH(date)  | 获取指定date的月份  |
| DAY(date)  | 获取指定date的日期  |
| DATE_ADD(date, INTERVAL expr type)  | 返回一个日期/时间值加上一个时间间隔expr后的时间值  |
| DATEDIFF(date1, date2)  | 返回起始时间date1和结束时间date2之间的天数  |

例子：

```mysql
-- DATE_ADD
SELECT DATE_ADD(NOW(), INTERVAL 70 YEAR);
```

### 流程函数

常用函数：

| 函数  | 功能  |
| ------------ | ------------ |
| IF(value, t, f)  | 如果value为true，则返回t，否则返回f  |
| IFNULL(value1, value2)  | 如果value1不为空，返回value1，否则返回value2  |
| CASE WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END  | 如果val1为true，返回res1，... 否则返回default默认值  |
| CASE [ expr ] WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END  | 如果expr的值等于val1，返回res1，... 否则返回default默认值  |

例子：

```mysql
select
	name,
	(case when age > 30 then '中年' else '青年' end)
from employee;
select
	name,
	(case workaddress when '北京市' then '一线城市' when '上海市' then '一线城市' else '二线城市' end) as '工作地址'
from employee;
```

## 约束

分类：

| 约束  | 描述  | 关键字  |
| ------------ | ------------ | ------------ |
| 非空约束  | 限制该字段的数据不能为null  | NOT NULL  |
| 唯一约束  | 保证该字段的所有数据都是唯一、不重复的  | UNIQUE  |
| 主键约束  | 主键是一行数据的唯一标识，要求非空且唯一  | PRIMARY KEY  |
| 默认约束  | 保存数据时，如果未指定该字段的值，则采用默认值  | DEFAULT  |
| 检查约束（8.0.1版本后）  | 保证字段值满足某一个条件  | CHECK  |
| 外键约束  | 用来让两张图的数据之间建立连接，保证数据的一致性和完整性  | FOREIGN KEY  |

约束是作用于表中字段上的，可以再创建表/修改表的时候添加约束。

### 常用约束

| 约束条件  | 关键字  |
| ------------ | ------------ |
| 主键  | PRIMARY KEY  |
| 自动增长  | AUTO_INCREMENT  |
| 不为空  | NOT NULL  |
| 唯一  | UNIQUE  |
| 逻辑条件  | CHECK  |
| 默认值  | DEFAULT  |

例子：

```mysql
create table user(
	id int primary key auto_increment,
	name varchar(10) not null unique,
	age int check(age > 0 and age < 120),
	status char(1) default '1',
	gender char(1)
);
```

### 外键约束

添加外键：

```mysql
CREATE TABLE 表名(
	字段名 字段类型,
	...
	[CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名);

-- 例子
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id);
```

删除外键：
`ALTER TABLE 表名 DROP FOREIGN KEY 外键名;`

#### 删除/更新行为

| 行为  | 说明  |
| ------------ | ------------ |
| NO ACTION  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与RESTRICT一致）  |
| RESTRICT  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与NO ACTION一致）  |
| CASCADE  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录  |
| SET NULL  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（要求该外键允许为null）  |
| SET DEFAULT  | 父表有变更时，子表将外键设为一个默认值（Innodb不支持）  |

更改删除/更新行为：
`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名) ON UPDATE 行为 ON DELETE 行为;`

## 多表查询

### 多表关系

- 一对多（多对一）
- 多对多
- 一对一

#### 一对多

案例：部门与员工
关系：一个部门对应多个员工，一个员工对应一个部门
实现：在多的一方建立外键，指向一的一方的主键

#### 多对多

案例：学生与课程
关系：一个学生可以选多门课程，一门课程也可以供多个学生选修
实现：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

#### 一对一

案例：用户与用户详情
关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
实现：在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的（UNIQUE）

### 查询

合并查询（笛卡尔积，会展示所有组合结果）：
`select * from employee, dept;`

> 笛卡尔积：两个集合A集合和B集合的所有组合情况（在多表查询时，需要消除无效的笛卡尔积）

消除无效笛卡尔积：
`select * from employee, dept where employee.dept = dept.id;`

### 内连接查询

内连接查询的是两张表交集的部分

隐式内连接：
`SELECT 字段列表 FROM 表1, 表2 WHERE 条件 ...;`

显式内连接：
`SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ...;`

显式性能比隐式高

例子：

```mysql
-- 查询员工姓名，及关联的部门的名称
-- 隐式
select e.name, d.name from employee as e, dept as d where e.dept = d.id;
-- 显式
select e.name, d.name from employee as e inner join dept as d on e.dept = d.id;
```

### 外连接查询

左外连接：
查询左表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ...;`
相当于查询表1的所有数据，包含表1和表2交集部分数据

右外连接：
查询右表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ...;`

例子：

```mysql
-- 左
select e.*, d.name from employee as e left outer join dept as d on e.dept = d.id;
select d.name, e.* from dept d left outer join emp e on e.dept = d.id;  -- 这条语句与下面的语句效果一样
-- 右
select d.name, e.* from employee as e right outer join dept as d on e.dept = d.id;
```

左连接可以查询到没有dept的employee，右连接可以查询到没有employee的dept

### 自连接查询

当前表与自身的连接查询，自连接必须使用表别名

语法：
`SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ...;`

自连接查询，可以是内连接查询，也可以是外连接查询

例子：

```mysql
-- 查询员工及其所属领导的名字
select a.name, b.name from employee a, employee b where a.manager = b.id;
-- 没有领导的也查询出来
select a.name, b.name from employee a left join employee b on a.manager = b.id;
```

### 联合查询 union, union all

把多次查询的结果合并，形成一个新的查询集

语法：

```mysql
SELECT 字段列表 FROM 表A ...
UNION [ALL]
SELECT 字段列表 FROM 表B ...
```

#### 注意事项

- UNION ALL 会有重复结果，UNION 不会
- 联合查询比使用or效率高，不会使索引失效

### 子查询

SQL语句中嵌套SELECT语句，称谓嵌套查询，又称子查询。
`SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2);`
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

#### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等）。
常用操作符：- < > > >= < <=

例子：

```mysql
-- 查询销售部所有员工
select id from dept where name = '销售部';
-- 根据销售部部门ID，查询员工信息
select * from employee where dept = 4;
-- 合并（子查询）
select * from employee where dept = (select id from dept where name = '销售部');

-- 查询xxx入职之后的员工信息
select * from employee where entrydate > (select entrydate from employee where name = 'xxx');
```

#### 列子查询

返回的结果是一列（可以是多行）。

常用操作符：

| 操作符  | 描述  |
| ------------ | ------------ |
| IN  | 在指定的集合范围内，多选一  |
| NOT IN  | 不在指定的集合范围内  |
| ANY  | 子查询返回列表中，有任意一个满足即可  |
| SOME  | 与ANY等同，使用SOME的地方都可以使用ANY  |
| ALL  | 子查询返回列表的所有值都必须满足  |

例子：

```mysql
-- 查询销售部和市场部的所有员工信息
select * from employee where dept in (select id from dept where name = '销售部' or name = '市场部');
-- 查询比财务部所有人工资都高的员工信息
select * from employee where salary > all(select salary from employee where dept = (select id from dept where name = '财务部'));
-- 查询比研发部任意一人工资高的员工信息
select * from employee where salary > any (select salary from employee where dept = (select id from dept where name = '研发部'));
```

#### 行子查询

返回的结果是一行（可以是多列）。
常用操作符：=, <, >, IN, NOT IN

例子：

```mysql
-- 查询与xxx的薪资及直属领导相同的员工信息
select * from employee where (salary, manager) = (12500, 1);
select * from employee where (salary, manager) = (select salary, manager from employee where name = 'xxx');
```

#### 表子查询

返回的结果是多行多列
常用操作符：IN

例子：

```mysql
-- 查询与xxx1，xxx2的职位和薪资相同的员工
select * from employee where (job, salary) in (select job, salary from employee where name = 'xxx1' or name = 'xxx2');
-- 查询入职日期是2006-01-01之后的员工，及其部门信息
select e.*, d.* from (select * from employee where entrydate > '2006-01-01') as e left join dept as d on e.dept = d.id;
```

## 事务

事务是一组操作的集合，事务会把所有操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

基本操作：

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

### 四大特性ACID

- 原子性(Atomicity)：事务是不可分割的最小操作但愿，要么全部成功，要么全部失败
- 一致性(Consistency)：事务完成时，必须使所有数据都保持一致状态
- 隔离性(Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性(Durability)：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的

### 并发事务

| 问题  | 描述  |
| ------------ | ------------ |
| 脏读  | 一个事务读到另一个事务还没提交的数据  |
| 不可重复读  | 一个事务先后读取同一条记录，但两次读取的数据不同  |
| 幻读  | 一个事务按照条件查询数据时，没有对应的数据行，但是再插入数据时，又发现这行数据已经存在  |

> 这三个问题的详细演示：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=55cd 

并发事务隔离级别：

| 隔离级别  | 脏读  | 不可重复读  | 幻读  |
| ------------ | ------------ | ------------ | ------------ |
| Read uncommitted  | √  | √  | √  |
| Read committed  | ×  | √  | √  |
| Repeatable Read(默认)  | ×  | ×  | √  |
| Serializable  | ×  | ×  | ×  |

- √表示在当前隔离级别下该问题会出现
- Serializable 性能最低；Read uncommitted 性能最高，数据安全性最差

查看事务隔离级别：
`SELECT @@TRANSACTION_ISOLATION;`
设置事务隔离级别：
`SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE };`
SESSION 是会话级别，表示只针对当前会话有效，GLOBAL 表示对所有会话有效

# 进阶篇

## 1.存储引擎

MySQL体系结构：

![MySQL体系结构](https://raw.githubusercontent.com/makev5/picture/main/img/MySQL%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84.png)
![层级描述](https://raw.githubusercontent.com/makev5/picture/main/img/MySQL%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E5%B1%82%E7%BA%A7%E5%90%AB%E4%B9%89_20220315034359342837.png "层级描述")

存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是基于表而不是基于库的，所以存储引擎也可以被称为表引擎。
默认存储引擎是InnoDB。

相关操作：

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

### InnoDB

InnoDB 是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5 之后，InnoDB 是默认的 MySQL 引擎。

特点：

- DML 操作遵循 ACID 模型，支持**事务**
- **行级锁**，提高并发访问性能
- 支持**外键**约束，保证数据的完整性和正确性

文件：

- xxx.ibd: xxx代表表名，InnoDB 引擎的每张表都会对应这样一个表空间文件，存储该表的表结构（frm、sdi）、数据和索引。

参数：innodb_file_per_table，决定多张表共享一个表空间还是每张表对应一个表空间

知识点：

查看 Mysql 变量：
`show variables like 'innodb_file_per_table';`

从idb文件提取表结构数据：
（在cmd运行）
`ibd2sdi xxx.ibd`

InnoDB 逻辑存储结构：
![逻辑存储结构_20220316030616590001](https://raw.githubusercontent.com/makev5/picture/main/img/%E9%80%BB%E8%BE%91%E5%AD%98%E5%82%A8%E7%BB%93%E6%9E%84_20220316030616590001.png)

### MyISAM

MyISAM 是 MySQL 早期的默认存储引擎。

特点：

- 不支持事务，不支持外键
- 支持表锁，不支持行锁
- 访问速度快

文件：

- xxx.sdi: 存储表结构信息
- xxx.MYD: 存储数据
- xxx.MYI: 存储索引

### Memory

Memory 引擎的表数据是存储在内存中的，受硬件问题、断电问题的影响，只能将这些表作为临时表或缓存使用。

特点：

- 存放在内存中，速度快
- hash索引（默认）

文件：

- xxx.sdi: 存储表结构信息

### 存储引擎特点

| 特点  | InnoDB  | MyISAM  | Memory  |
| ------------ | ------------ | ------------ | ------------ |
| 存储限制  | 64TB  | 有  | 有  |
| *事务安全 | 支持  | -  | -  |
| *锁机制 | 行锁  | 表锁  | 表锁  |
| B+tree索引  | 支持  | 支持  | 支持  |
| Hash索引  | -  | -  | 支持  |
| 全文索引  | 支持（5.6版本之后）  | 支持  | -  |
| 空间使用  | 高  | 低  | N/A  |
| 内存使用  | 高  | 低  | 中等  |
| 批量插入速度  | 低  | 高  | 高  |
| *支持外键 | 支持  | -  | -  |

### 存储引擎的选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

- InnoDB: 如果应用对事物的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，则 InnoDB 是比较合适的选择
- MyISAM: 如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不高，那这个存储引擎是非常合适的。（例如：电商足迹，日志，评论等）
- Memory: 将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。Memory 的缺陷是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性

电商中的足迹和评论适合使用 MyISAM 引擎，缓存适合使用 Memory 引擎。

## 性能分析

### 查看执行频次

查看当前数据库的 INSERT, UPDATE, DELETE, SELECT 访问频次：
`SHOW GLOBAL STATUS LIKE 'Com_______';` 或者 `SHOW SESSION STATUS LIKE 'Com_______';`
例：`show global status like 'Com_______'`

### 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志。
MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：
	# 开启慢查询日志开关
	slow_query_log=1
	# 设置慢查询日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
	long_query_time=2
更改后记得重启MySQL服务，日志文件位置：/var/lib/mysql/localhost-slow.log

查看慢查询日志开关状态：
`show variables like 'slow_query_log';`

### profile

show profile 能在做SQL优化时帮我们了解时间都耗费在哪里。通过 have_profiling 参数，能看到当前 MySQL 是否支持 profile 操作：
`SELECT @@have_profiling;`
profiling 默认关闭，可以通过set语句在session/global级别开启 profiling：
`SET profiling = 1;`
查看所有语句的耗时：
`show profiles;`
查看指定query_id的SQL语句各个阶段的耗时：
`show profile for query query_id;`
查看指定query_id的SQL语句CPU的使用情况
`show profile cpu for query query_id;`

### explain

EXPLAIN 或者 DESC 命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接的顺序。
语法：
	# 直接在select语句之前加上关键字 explain / desc
	EXPLAIN SELECT 字段列表 FROM 表名 HWERE 条件;

EXPLAIN 各字段含义：

- id：select 查询的序列号，表示查询中执行 select 子句或者操作表的顺序（id相同，执行顺序从上到下；id不同，值越大越先执行）
- select_type：表示 SELECT 的类型，常见取值有 SIMPLE（简单表，即不适用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUBQUERY（SELECT/WHERE之后包含了子查询）等
- type：表示连接类型，性能由好到差的连接类型为 NULL、system、const、eq_ref、ref、range、index、all
- possible_key：可能应用在这张表上的索引，一个或多个
- Key：实际使用的索引，如果为 NULL，则没有使用索引
- Key_len：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好
- rows：MySQL认为必须要执行的行数，在InnoDB引擎的表中，是一个估计值，可能并不总是准确的
- filtered：表示返回结果的行数占需读取行数的百分比，filtered的值越大越好

## 2.索引

索引是帮助 MySQL **高效获取数据**的**数据结构（有序）**。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查询算法，这种数据结构就是索引。

优缺点：

优点：

- 提高数据检索效率，降低数据库的IO成本
- 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗

缺点：

- 索引列也是要占用空间的
- 索引大大提高了查询效率，但降低了更新的速度，比如 INSERT、UPDATE、DELETE

### 索引结构

| 索引结构  | 描述  |
| ------------ | ------------ |
| B+Tree  | 最常见的索引类型，大部分引擎都支持B+树索引  |
| Hash  | 底层数据结构是用哈希表实现，只有精确匹配索引列的查询才有效，不支持范围查询  |
| R-Tree(空间索引)  | 空间索引是 MyISAM 引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少  |
| Full-Text(全文索引)  | 是一种通过建立倒排索引，快速匹配文档的方式，类似于 Lucene, Solr, ES  |

| 索引  | InnoDB  | MyISAM  | Memory  |
| ------------ | ------------ | ------------ | ------------ |
| B+Tree索引  | 支持  | 支持  | 支持  |
| Hash索引  | 不支持  | 不支持  | 支持  |
| R-Tree索引  | 不支持  | 支持  | 不支持  |
| Full-text  | 5.6版本后支持  | 支持  | 不支持  |

#### B-Tree

![image-20220812095732689](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095732689.png)

二叉树的缺点可以用红黑树来解决：

![红黑树](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095622130.png)
红黑树也存在大数据量情况下，层级较深，检索速度慢的问题。

为了解决上述问题，可以使用 B-Tree 结构。
B-Tree (多路平衡查找树) 以一棵最大度数（max-degree，指一个节点的子节点个数）为5（5阶）的 b-tree 为例（每个节点最多存储4个key，5个指针）

![image-20220812095552118](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095552118.png)

> B-Tree 的数据插入过程动画参照：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=68
演示地址：https://www.cs.usfca.edu/~galles/visualization/BTree.html

#### B+Tree

结构图：

![image-20220812095455572](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095455572.png)

> 演示地址：https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html

与 B-Tree 的区别：

- 所有的数据都会出现在叶子节点
- 叶子节点形成一个单向链表

MySQL 索引数据结构对经典的 B+Tree 进行了优化。在原 B+Tree 的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的 B+Tree，提高区间访问的性能。

![image-20220812095407126](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095407126.png)

#### Hash

哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。
如果两个（或多个）键值，映射到一个相同的槽位上，他们就产生了hash冲突（也称为hash碰撞），可以通过链表来解决。

![image-20220812095321560](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095321560.png)

特点：

- Hash索引只能用于对等比较（=、in），不支持范围查询（betwwn、>、<、...）
- 无法利用索引完成排序操作
- 查询效率高，通常只需要一次检索就可以了，效率通常要高于 B+Tree 索引

存储引擎支持：

- Memory
- InnoDB: 具有自适应hash功能，hash索引是存储引擎根据 B+Tree 索引在指定条件下自动构建的

#### 面试题

1. 为什么 InnoDB 存储引擎选择使用 B+Tree 索引结构？

- 相对于二叉树，层级更少，搜索效率高
- 对于 B-Tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针也跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
- 相对于 Hash 索引，B+Tree 支持范围匹配及排序操作

### 索引分类

| 分类  | 含义  | 特点  | 关键字  |
| ------------ | ------------ | ------------ | ------------ |
| 主键索引  | 针对于表中主键创建的索引  | 默认自动创建，只能有一个  | PRIMARY  |
| 唯一索引  | 避免同一个表中某数据列中的值重复  | 可以有多个  | UNIQUE  |
| 常规索引  | 快速定位特定数据  | 可以有多个  |   |
| 全文索引  | 全文索引查找的是文本中的关键词，而不是比较索引中的值  | 可以有多个  | FULLTEXT  |

在 InnoDB 存储引擎中，根据索引的存储形式，又可以分为以下两种：

| 分类  | 含义  | 特点  |
| ------------ | ------------ | ------------ |
| 聚集索引(Clustered Index)  | 将数据存储与索引放一块，索引结构的叶子节点保存了行数据  | 必须有，而且只有一个  |
| 二级索引(Secondary Index)  | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键  | 可以存在多个  |

演示图：

![image-20220812095002760](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095002760.png)

![image-20220812095231933](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812095231933.png)

聚集索引选取规则：

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引
- 如果表没有主键或没有合适的唯一索引，则 InnoDB 会自动生成一个 rowid 作为隐藏的聚集索引

#### 思考题

1\. 以下 SQL 语句，哪个执行效率高？为什么？

```mysql
select * from user where id = 10;
select * from user where name = 'Arm';
-- 备注：id为主键，name字段创建的有索引
```

答：第一条语句，因为第二条需要回表查询，相当于两个步骤。

2\. InnoDB 主键索引的 B+Tree 高度为多少？

答：假设一行数据大小为1k，一页中可以存储16行这样的数据。InnoDB 的指针占用6个字节的空间，主键假设为bigint，占用字节数为8.
可得公式：`n * 8 + (n + 1) * 6 = 16 * 1024`，其中 8 表示 bigint 占用的字节数，n 表示当前节点存储的key的数量，(n + 1) 表示指针数量（比key多一个）。算出n约为1170。

如果树的高度为2，那么他能存储的数据量大概为：`1171 * 16 = 18736`；
如果树的高度为3，那么他能存储的数据量大概为：`1171 * 1171 * 16 = 21939856`。

另外，如果有成千上万的数据，那么就要考虑分表，涉及运维篇知识。

### 语法

创建索引：
`CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name, ...);`
如果不加 CREATE 后面不加索引类型参数，则创建的是常规索引

查看索引：
`SHOW INDEX FROM table_name;`

删除索引：
`DROP INDEX index_name ON table_name;`

案例：

```mysql
-- name字段为姓名字段，该字段的值可能会重复，为该字段创建索引
create index idx_user_name on tb_user(name);
-- phone手机号字段的值非空，且唯一，为该字段创建唯一索引
create unique index idx_user_phone on tb_user (phone);
-- 为profession, age, status创建联合索引
create index idx_user_pro_age_stat on tb_user(profession, age, status);
-- 为email建立合适的索引来提升查询效率
create index idx_user_email on tb_user(email);

-- 删除索引
drop index idx_user_email on tb_user;
```

### 使用规则

#### 最左前缀法则

如果索引关联了多列（联合索引），要遵守最左前缀法则，最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。
如果跳跃某一列，索引将部分失效（后面的字段索引失效）。

联合索引中，出现范围查询（<, >），范围查询右侧的列索引失效。可以用>=或者<=来规避索引失效问题。

#### 索引失效情况

1. 在索引列上进行运算操作，索引将失效。如：`explain select * from tb_user where substring(phone, 10, 2) = '15';`
2. 字符串类型字段使用时，不加引号，索引将失效。如：`explain select * from tb_user where phone = 17799990015;`，此处phone的值没有加引号
3. 模糊查询中，如果仅仅是尾部模糊匹配，索引不会是失效；如果是头部模糊匹配，索引失效。如：`explain select * from tb_user where profession like '%工程';`，前后都有 % 也会失效。
4. 用 or 分割开的条件，如果 or 其中一个条件的列没有索引，那么涉及的索引都不会被用到。
5. 如果 MySQL 评估使用索引比全表更慢，则不使用索引。

#### SQL 提示

是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

例如，使用索引：
`explain select * from tb_user use index(idx_user_pro) where profession="软件工程";`
不使用哪个索引：
`explain select * from tb_user ignore index(idx_user_pro) where profession="软件工程";`
必须使用哪个索引：
`explain select * from tb_user force index(idx_user_pro) where profession="软件工程";`

use 是建议，实际使用哪个索引 MySQL 还会自己权衡运行速度去更改，force就是无论如何都强制使用该索引。

#### 覆盖索引&回表查询

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能找到），减少 select *。

explain 中 extra 字段含义：
`using index condition`：查找使用了索引，但是需要回表查询数据
`using where; using index;`：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询

如果在聚集索引中直接能找到对应的行，则直接返回行数据，只需要一次查询，哪怕是select \*；如果在辅助索引中找聚集索引，如`select id, name from xxx where name='xxx';`，也只需要通过辅助索引(name)查找到对应的id，返回name和name索引对应的id即可，只需要一次查询；如果是通过辅助索引查找其他字段，则需要回表查询，如`select id, name, gender from xxx where name='xxx';`

所以尽量不要用`select *`，容易出现回表查询，降低效率，除非有联合索引包含了所有字段

**面试题：**一张表，有四个字段（id, username, password, status），由于数据量大，需要对以下SQL语句进行优化，该如何进行才是最优方案：
`select id, username, password from tb_user where username='itcast';`

解：给username和password字段建立联合索引，则不需要回表查询，直接覆盖索引

#### 前缀索引

当字段类型为字符串（varchar, text等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率，此时可以只降字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

语法：`create index idx_xxxx on table_name(columnn(n));`
前缀长度：可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高，唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。
求选择性公式：
```mysql
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(email, 1, 5)) / count(*) from tb_user;
```

show index 里面的sub_part可以看到接取的长度

#### 单列索引&联合索引

单列索引：即一个索引只包含单个列
联合索引：即一个索引包含了多个列
在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。

单列索引情况：
`explain select id, phone, name from tb_user where phone = '17799990010' and name = '韩信';`
这句只会用到phone索引字段

##### 注意事项

- 多条件联合查询时，MySQL优化器会评估哪个字段的索引效率更高，会选择该索引完成本次查询

### 设计原则

1. 针对于数据量较大（百万以上），且查询比较频繁的表建立索引
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段长度较长，可以针对于字段的特点，建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价就越大，会影响增删改的效率
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询

## 3.SQL 优化

### 插入数据

普通插入：

1. 采用批量插入（一次插入的数据不建议超过1000条）
2. 手动提交事务
3. 主键顺序插入

大批量插入：
如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令插入。

```mysql
# 客户端连接服务端时，加上参数 --local-infile（这一行在bash/cmd界面输入）
mysql --local-infile -u root -p
# 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;
select @@local_infile;
# 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table 'tb_user' fields terminated by ',' lines terminated by '\n';
```

### 主键优化

数据组织方式：在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表（Index organized table, IOT）

页分裂：页可以为空，也可以填充一般，也可以填充100%，每个页包含了2-N行数据（如果一行数据过大，会行溢出），根据主键排列。
页合并：当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。当页中删除的记录到达 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前后）看看是否可以将这两个页合并以优化空间使用。

MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或创建索引时指定

> 文字说明不够清晰明了，具体可以看视频里的PPT演示过程：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=90

主键设计原则：

- 满足业务需求的情况下，尽量降低主键的长度
- 插入数据时，尽量选择顺序插入，选择使用 AUTO_INCREMENT 自增主键
- 尽量不要使用 UUID 做主键或者是其他的自然主键，如身份证号
- 业务操作时，避免对主键的修改

### order by优化

1. Using filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区 sort buffer 中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序
2. Using index：通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高

如果order by字段全部使用升序排序或者降序排序，则都会走索引，但是如果一个字段升序排序，另一个字段降序排序，则不会走索引，explain的extra信息显示的是`Using index, Using filesort`，如果要优化掉Using filesort，则需要另外再创建一个索引，如：`create index idx_user_age_phone_ad on tb_user(age asc, phone desc);`，此时使用`select id, age, phone from tb_user order by age asc, phone desc;`会全部走索引

总结：

- 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
- 尽量使用覆盖索引
- 多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）
- 如果不可避免出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 sort_buffer_size（默认256k）

### group by优化

- 在分组操作时，可以通过索引来提高效率
- 分组操作时，索引的使用也是满足最左前缀法则的

如索引为`idx_user_pro_age_stat`，则句式可以是`select ... where profession order by age`，这样也符合最左前缀法则

### limit优化

常见的问题如`limit 2000000, 10`，此时需要 MySQL 排序前2000000条记录，但仅仅返回2000000 - 2000010的记录，其他记录丢弃，查询排序的代价非常大。
优化方案：一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过覆盖索引加子查询形式进行优化

例如：

```mysql
-- 此语句耗时很长
select * from tb_sku limit 9000000, 10;
-- 通过覆盖索引加快速度，直接通过主键索引进行排序及查询
select id from tb_sku order by id limit 9000000, 10;
-- 下面的语句是错误的，因为 MySQL 不支持 in 里面使用 limit
-- select * from tb_sku where id in (select id from tb_sku order by id limit 9000000, 10);
-- 通过连表查询即可实现第一句的效果，并且能达到第二句的速度
select * from tb_sku as s, (select id from tb_sku order by id limit 9000000, 10) as a where s.id = a.id;
```

### count优化

MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(\*) 的时候会直接返回这个数，效率很高（前提是不适用where）；
InnoDB 在执行 count(\*) 时，需要把数据一行一行地从引擎里面读出来，然后累计计数。
优化方案：自己计数，如创建key-value表存储在内存或硬盘，或者是用redis

count的几种用法：

- 如果count函数的参数（count里面写的那个字段）不是NULL（字段值不为NULL），累计值就加一，最后返回累计值
- 用法：count(\*)、count(主键)、count(字段)、count(1)
- count(主键)跟count(\*)一样，因为主键不能为空；count(字段)只计算字段值不为NULL的行；count(1)引擎会为每行添加一个1，然后就count这个1，返回结果也跟count(\*)一样；count(null)返回0

各种用法的性能：

- count(主键)：InnoDB引擎会遍历整张表，把每行的主键id值都取出来，返回给服务层，服务层拿到主键后，直接按行进行累加（主键不可能为空）
- count(字段)：没有not null约束的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，服务层判断是否为null，不为null，计数累加；有not null约束的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，直接按行进行累加
- count(1)：InnoDB 引擎遍历整张表，但不取值。服务层对于返回的每一层，放一个数字 1 进去，直接按行进行累加
- count(\*)：InnoDB 引擎并不会把全部字段取出来，而是专门做了优化，不取值，服务层直接按行进行累加

**按效率排序：count(字段) < count(主键) < count(1)  ≈ count(\*)，所以尽量使用 count(\*)**

### update优化（避免行锁升级为表锁）

InnoDB 的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从行锁升级为表锁。

如以下两条语句：
`update student set no = '123' where id = 1;`，这句由于id有主键索引，所以只会锁这一行；
`update student set no = '123' where name = 'test';`，这句由于name没有索引，所以会把整张表都锁住进行数据更新，解决方法是给name字段添加索引

## 4.视图/存储过程/触发器

### 4.1 视图

#### 4.1.1 介绍 

视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视 图的查询中使用的表，并且是在使用视图时动态生成的。 

通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的工作 就落在创建这条SQL查询语句上。

#### 4.1.2 语法

- 创建

```mysql
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [
CASCADED | LOCAL ] CHECK OPTION ]
```

- 查询

```mysql
查看创建视图语句：SHOW CREATE VIEW 视图名称;
查看视图数据：SELECT * FROM 视图名称 ...... ;
```

- 修改

```mysql
方式一：CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH
[ CASCADED | LOCAL ] CHECK OPTION ]
方式二：ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED |
LOCAL ] CHECK OPTION ]
```

- 删除

```mysql
DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ...
```

演示示例：

```mysql
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id <= 10;
-- 查询视图
show create view stu_v_1;
select * from stu_v_1;
select * from stu_v_1 where id < 3;
-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id <= 10;
alter view stu_v_1 as select id,name from student where id <= 10;
-- 删除视图
drop view if exists stu_v_1;
```

上述我们演示了，视图应该如何创建、查询、修改、删除，那么我们能不能通过视图来插入、更新数据 呢？ 接下来，做一个测试。

```mysql
create or replace view stu_v_1 as select id,name from student where id <= 10 ;
select * from stu_v_1;
insert into stu_v_1 values(6,'Tom');
insert into stu_v_1 values(17,'Tom22');
```

执行上述的SQL，我们会发现，id为6和17的数据都是可以成功插入的。 但是我们执行查询，查询出 来的数据，却没有id为17的记录。

因为我们在创建视图的时候，指定的条件为 id<=10, id为17的数据，是不符合条件的，所以没有查 询出来，但是这条数据确实是已经成功的插入到了基表中。

如果我们定义视图时，如果指定了条件，然后我们在插入、修改、删除数据时，是否可以做到必须满足 条件才能操作，否则不能够操作呢？ 答案是可以的，这就需要借助于视图的检查选项了。

#### 4.1.3 检查选项

当使用`WITH CHECK OPTION`子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插 入，更新，删除，以使其符合视图的定义。 MySQL允许基于另一个视图创建视图，它还会检查依赖视 图中的规则以保持一致性。为了确定检查的范围，MySQL提供了两个选项： `CASCADED` 和 `LOCAL` ，默认值为 `CASCADED` 。

- CASCADED 级联

比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 cascaded，但是v1视图 创建时未指定检查选项。 则在执行检查时，不仅会检查v2，还会级联检查v2的关联视图v1。

- LOCAL 本地

比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 local ，但是v1视图创 建时未指定检查选项。 则在执行检查时，只会检查v2，不会检查v2的关联视图v1。

#### 4.1.4 视图的更新

要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一 项，则该视图不可更新：

1. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等） 
2. DISTINCT 
3. GROUP BY 
4.  HAVING
5.  UNION 或者 UNION ALL

示例演示:

```mysql
create view stu_v_count as select count(*) from student;
```

上述的视图中，就只有一个单行单列的数据，如果我们对这个视图进行更新或插入的，将会报错。

```mysql
insert into stu_v_count values(10);
```

#### 4.1.5 视图作用

- 简单

视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视 图，从而使得用户不必为以后的操作每次指定全部的条件。

- 安全

数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见 到的数据

- 数据独立

视图可帮助用户屏蔽真实表结构变化带来的影响。

#### 4.1.6 案例

1. 为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽 手机号和邮箱两个字段。

```mysql
create view tb_user_view as select id,name,profession,age,gender,status,createtime
from tb_user;
select * from tb_user_view;
```

2. 查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操 作，定义一个视图。

```mysql
create view tb_stu_course_view as select s.name student_name , s.no student_no ,
c.name course_name from student s, student_course sc , course c where s.id =
sc.studentid and sc.courseid = c.id;
select * from tb_stu_course_view;
```

### 4.2 存储过程

#### 4.2.1 介绍

存储过程是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程可以简化应用开发 人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。 存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。

![image-20220812094844073](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812094844073.png)

特点:

- 封装，复用 -----------------------> 可以把某一业务SQL封装在存储过程中，需要用到 的时候直接调用即可。
- 可以接收参数，也可以返回数据 --------> 再存储过程中，可以传递参数，也可以接收返回 值。
- 减少网络交互，效率提升 -------------> 如果涉及到多条SQL，每执行一次都是一次网络传 输。 而如果封装在存储过程中，我们只需要网络交互一次可能就可以了。

#### 4.2.2 基本语法

- 创建

```mysql
CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])
BEGIN
-- SQL语句
END ;
```

- 调用

```mysql
CALL 名称 ([ 参数 ]);
```

- 查看

```mysql
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = '数据库名'; -- 查询指定数据库的存储过程及状态信息 
SHOW CREATE PROCEDURE 存储过程名称 ; -- 查询某个存储过程的定义
```

- 删除

```mysql
DROP PROCEDURE [ IF EXISTS ] 存储过程名称 ；
```

> 注意: 在命令行中，执行创建存储过程的SQL时，需要通过关键字 delimiter 指定SQL语句的 结束符。

```shell
delimiter %%

create procedure p1()
begin
    select count(*) from t_user;
end$$

delimiter ;
```

演示示例:

```mysql
-- 存储过程基本语法
-- 创建
create procedure p1()
begin
    select count(*) from t_user;
end;

-- 调用
call p1();

-- 查看
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'zsb3031';
show create procedure p1;

-- 删除
drop procedure if exists p1;
```

#### 4.2.3 变量

在MySQL中变量分为三种类型: 系统变量、用户定义变量、局部变量。

##### 4.2.3.1 系统变量

**系统变量** 是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（GLOBAL）、会话 变量（SESSION）。

- 查看系统变量

```mysql
SHOW [ SESSION | GLOBAL ] VARIABLES ; -- 查看所有系统变量
SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; -- 可以通过LIKE模糊匹配方式查找变量
SELECT @@[SESSION | GLOBAL] 系统变量名; -- 查看指定变量的值
```

- 设置系统变量

```mysql
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;
SET @@[SESSION | GLOBAL]系统变量名 = 值 ;
```

> 注意: 
>
> ​		如果没有指定SESSION/GLOBAL，默认是SESSION，会话变量。
>
> ​		mysql服务重新启动之后，所设置的全局参数会失效，要想不失效，可以在 /etc/my.cnf 中配置。
>
> ​		A. 全局变量(GLOBAL): 全局变量针对于所有的会话。 
>
> ​		B. 会话变量(SESSION): 会话变量针对于单个会话，在另外一个会话窗口就不生效了。

演示示例:

```mysql
-- 查看系统变量
show session variables ;
show session variables like 'auto%';
show global variables like 'auto%';
select @@global.autocommit;
select @@session.autocommit;
-- 设置系统变量
set session autocommit = 1;
insert into course(id, name) VALUES (6, 'ES');
set global autocommit = 0;
select @@global.autocommit;
```

##### 4.2.3.2 用户定义变量

**用户定义变量** 是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用 "@变量 名" 使用就可以。其作用域为当前连接。

- 赋值

方式一：

```mysql
SET @var_name = expr [, @var_name = expr] ... ;
SET @var_name := expr [, @var_name := expr] ... ;
```

赋值时，可以使用 = ，也可以使用 := 。

方式二：

```mysql
SELECT @var_name := expr [, @var_name := expr] ... ;
SELECT 字段名 INTO @var_name FROM 表名;
```

- 使用

```mysql
SELECT @var_name ;
```

> 注意: 用户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL。

演示示例:

```mysql
-- 赋值
set @myname = 'itcast';
set @myage := 10;
set @mygender := '男',@myhobby := 'java';
select @mycolor := 'red';
select count(*) into @mycount from tb_user;
-- 使用
select @myname,@myage,@mygender,@myhobby;
select @mycolor , @mycount;
select @abc;
```

##### 4.2.3.3 局部变量

**局部变量** 是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的 局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ... END块。

- 声明

```mysql
DECLARE 变量名 变量类型 [DEFAULT ... ] ;
```

- 赋值

```mysql
SET 变量名 = 值 ;
SET 变量名 := 值 ;
SELECT 字段名 INTO 变量名 FROM 表名 ... ;
```

演示示例:

```mysql
-- 声明局部变量 - declare
-- 赋值
create procedure p2()
begin
	declare stu_count int default 0;
	select count(*) into stu_count from student;
	select stu_count;
end;
call p2();
```

#### 4.2.4 if

1. 介绍

if 用于做条件判断，具体的语法结构为：

```mysql
IF 条件1 THEN
	.....
ELSEIF 条件2 THEN -- 可选
	.....
ELSE -- 可选
	.....
END IF;
```

在if条件判断的结构中，ELSE IF 结构可以有多个，也可以没有。 ELSE结构可以有，也可以没有。

2. 案例

根据定义的分数score变量，判定当前分数对应的分数等级。

-  score >= 85分，等级为优秀。
-  score >= 60分 且 score < 85分，等级为及格。 
- score < 60分，等级为不及格。

```mysql
create procedure p3()
begin
	declare score int default 58;
	declare result varchar(10);
	
	if score >= 85 then
		set result := '优秀';
	elseif score >= 60 then
		set result := '及格';
	else
		set result := '不及格';
	end if;
	select result;
end;

call p3();
```

上述的需求我们虽然已经实现了，但是也存在一些问题，比如：score 分数我们是在存储过程中定义 死的，而且最终计算出来的分数等级，我们也仅仅是最终查询展示出来而已。 那么我们能不能，把score分数动态的传递进来，计算出来的分数等级是否可以作为返回值返回呢？ 答案是肯定的，我们可以通过接下来所讲解的 参数 来解决上述的问题。

#### 4.2.5 参数

1. 介绍

参数的类型，主要分为以下三种：IN、OUT、INOUT。 具体的含义如下：

| 类型  | 含义                                                         | 备注 |
| ----- | ------------------------------------------------------------ | ---- |
| IN    | 参数的类型，主要分为以下三种：IN、OUT、INOUT。 具体的含义如下： | 默认 |
| OUT   | 该类参数作为输出，也就是该参数可以作为返回值                 |      |
| INOUT | 既可以作为输入参数，也可以作为输出参数                       |      |

用法：

```mysql
CREATE PROCEDURE 存储过程名称 ([ IN/OUT/INOUT 参数名 参数类型 ])
BEGIN
	-- SQL语句
END ;
```

2. 案例一

根据传入参数score，判定当前分数对应的分数等级，并返回。 

- score >= 85分，等级为优秀。 
- score >= 60分 且 score < 85分，等级为及格。 
- score < 60分，等级为不及格。

```mysql
create procedure p3(in score int, out result varchar(10))
begin
	if score >= 85 then
		set result := '优秀';
	elseif score >= 60 then
		set result := '及格';
	else
		set result := '不及格';
	end if;
end;

call p3(10, @result);
select @result;
```

3. 案例二

将传入的200分制的分数，进行换算，换算成百分制，然后返回。

```mysql
create procedure p4(inout score double)
begin
    set  score := score * 0.5;
end;

set @score = 78;
call p4(@score);
select @score;
```

#### 4.2.6 case

1. 介绍

- 语法1：

```mysql
-- 含义： 当case_value的值为 when_value1时，执行statement_list1，当值为 when_value2时，执行statement_list2， 否则就执行 statement_list
CASE case_value
	WHEN when_value1 THEN statement_list1
	[ WHEN when_value2 THEN statement_list2] ...
	[ ELSE statement_list ]
END CASE;
```

- 语法2：

```mysql
-- 含义： 当条件search_condition1成立时，执行statement_list1，当条件search_condition2成立时，执行statement_list2， 否则就执行 statement_list
CASE
	WHEN search_condition1 THEN statement_list1
	[WHEN search_condition2 THEN statement_list2] ...
	[ELSE statement_list]
END CASE;
```

2. 案例

根据传入的月份，判定月份所属的季节（要求采用case结构）。

- 1-3月份，为第一季度 
- 4-6月份，为第二季度 
- 7-9月份，为第三季度 
- 10-12月份，为第四季度

```mysql
create procedure p5(in month int)
begin
    declare result varchar(10);
    case
        when month >=1 and month <=3 then
            set result = '第一季度';
        when month >=4 and month <=6 then
            set result = '第二季度';
        when month >=7 and month <=9 then
            set result = '第三季度';
        when month >=10 and month <=12 then
            set result = '第四季度';
        else
            set result = '非法参数';
    end case ;

    select concat('您输入的月份为：', month, ',所属的季度为：', result);
end;

call p5(12);
```

> 注意：如果判定条件有多个，多个条件之间，可以使用 and 或 or 进行连接。

#### 4.2.7 while

1. 介绍

   while 循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。具体语法为：

   ```mysql
   -- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
   WHILE 条件 DO
   	SQL逻辑...
   END WHILE;
   ```

2. 案例

   计算从1累加到n的值，n为传入的参数值。

   ```mysql
   -- A. 定义局部变量, 记录累加之后的值;
   -- B. 每循环一次, 就会对n进行减1 , 如果n减到0, 则退出循环
   create procedure p6(in n int)
   begin
       declare total int default 0;
       while n > 0 do
           set total := total + n;
           set n := n - 1;
       end while;
       select total;
   end;
   
   call p6(100);
   ```

#### 4.2.8 repeat

1. 介绍

   repeat是有条件的循环控制语句, 当满足until声明的条件的时候，则退出循环 。具体语法为：

2. 案例

   计算从1累加到n的值，n为传入的参数值。(使用repeat实现)

   ```mysql
   create procedure p7(in n int)
   begin
       declare total int default 0;
       repeat
           set total := total + n;
           set n := n -1;
       until n <= 0
       end repeat;
       select total;
   end;
   
   call p7(100);
   ```
   

#### 4.2.9 loop

1. 介绍

   LOOP 实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的死循环。

   LOOP可以配合一下两个语句使用：

   - LEAVE ：配合循环使用，退出循环。
   - ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。

   ```mysql
   [begin_label:] LOOP
   	SQL逻辑...
   END LOOP [end_label];
   
   LEAVE label; -- 退出指定标记的循环体
   ITERATE label; -- 直接进入下一次循环
   ```

   上述语法中出现的 begin_label，end_label，label 指的都是我们所自定义的标记。

2. 案例一

   计算从1累加到n的值，n为传入的参数值。

   ```mysql
   -- A. 定义局部变量, 记录累加之后的值;
   -- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx
   create procedure p8(in n int)
   begin
      declare total int default 0;
   
      sum:loop
          if n <= 0 then
              leave sum;
          end if;
   
          set total := total + n;
          set n := n - 1;
      end loop sum;
      select total;
   end;
   
   call p8(100);
   ```

3. 案例二

   计算从1到n之间的偶数累加的值，n为传入的参数值。

   ```mysql
   -- A. 定义局部变量, 记录累加之后的值;
   -- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx
   -- C. 如果当次累加的数据是奇数, 则直接进入下一次循环. --------> iterate xx
   create procedure p9(in n int)
   begin
      declare total int default 0;
   
      sum:loop
           if n <= 0 then
              leave sum;
           end if;
           if n % 2 = 1 then
               set n := n - 1;
               iterate sum;
           end if;
          set total := total + n;
          set n := n - 1;
      end loop sum;
      select total;
   end;
   
   call p9(100);
   ```

#### 4.2.10 游标

1. 介绍

   游标（CURSOR）是用来存储查询结果集的数据类型 , 在存储过程和函数中可以使用游标对结果集进 行循环的处理。游标的使用包括游标的声明、OPEN、FETCH 和 CLOSE，其语法分别如下。

   A. 声明游标

   ```mysql
   DECLARE 游标名称 CURSOR FOR 查询语句 ;
   ```

   B. 打开游标

   ```mysql
   OPEN 游标名称 ;
   ```

   C.获取游标记录

   ```mysql
   FETCH 游标名称 INTO 变量 [, 变量 ] ;
   ```

   D. 关闭游标

   ```mysql
   CLOSE 游标名称 ;
   ```

2. 案例

   根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名 （name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表 (id,name,profession)中。

   ```mysql
   -- 逻辑:
   -- A. 声明游标, 存储查询结果集
   -- B. 准备: 创建表结构
   -- C. 开启游标
   -- D. 获取游标中的记录
   -- E. 插入数据到新表中
   -- F. 关闭游标
   create procedure p10(in uage int)
   begin
       declare u_name varchar(100);
       declare u_pro varchar(100);
       declare u_cursor cursor for select name,profession from t_user where age <=uage;
       declare exit handler for SQLSTATE '02000' close u_cursor;
   
       drop table if exists tb_user_pro;
       create table if not exists tb_user_pro(
           id int primary key auto_increment,
           name varchar(100),
           profession varchar(100)
       );
   
       open u_cursor;
       while true do
           fetch u_cursor into u_name,u_pro;
           insert into tb_user_pro values (null, u_name, u_pro);
       end while;
       close u_cursor;
   end;
   
   call p10(30);
   ```

#### 4.2.11 条件处理程序

1. 介绍

   条件处理程序（Handler）可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。具体 语法为：

   ```mysql
   DECLARE handler_action HANDLER FOR condition_value [, condition_value]
   ... statement ;
   
   handler_action 的取值：
   	CONTINUE: 继续执行当前程序
   	EXIT: 终止执行当前程序
   	
   condition_value 的取值：
   	SQLSTATE sqlstate_value: 状态码，如 02000
   	SQLWARNING: 所有以01开头的SQLSTATE代码的简写
   	NOT FOUND: 所有以02开头的SQLSTATE代码的简写
   	SQLEXCEPTION: 所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
   ```

   具体的错误状态码，可以参考官方文档： 

   https://dev.mysql.com/doc/refman/8.0/en/declare-handler.html 

   https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html

### 4.3 存储函数

1. 介绍

   存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的。具体语法如下：

   ```mysql
   CREATE FUNCTION 存储函数名称 ([ 参数列表 ])
   RETURNS type [characteristic ...]
   BEGIN
   	-- SQL语句
   	RETURN ...;
   END ;
   ```

   characteristic说明：

   - DETERMINISTIC：相同的输入参数总是产生相同的结果
   - NO SQL ：不包含 SQL 语句。
   - READS SQL DATA：包含读取数据的语句，但不包含写入数据的语句。

2. 案例

   计算从1累加到n的值，n为传入的参数值。

   ```mysql
   create function fun1(n int)
   returns int
   begin
       declare total int default 0;
   
       while n > 0 do
           set total := total + n;
           set n := n - 1;
       end while;
       return total;
   end;
   
   select fun1(100);
   ```

### 4.4 触发器

#### 4.4.1 介绍

触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触 发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性 , 日志记录 , 数据校验等操作 。

使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还 只支持行级触发，不支持语句级触发。

| 触发器类型      | NEW 和 OLD                                              |
| --------------- | ------------------------------------------------------- |
| INSERT 型触发器 | NEW 表示将要或者已经新增的数据                          |
| UPDATE 型触发器 | OLD 表示修改之前的数据 , NEW 表示将要或已经修改后的数据 |
| DELETE 型触发器 | OLD 表示将要或者已经删除的数据                          |

#### 4.4.2 语法

1. 创建

   ```mysql
   CREATE TRIGGER trigger_name
   BEFORE/AFTER INSERT/UPDATE/DELETE
   ON tbl_name FOR EACH ROW -- 行级触发器
   BEGIN
   	trigger_stmt ;
   END;

2. 查看

   ```mysql
   SHOW TRIGGERS ;
   ```

3. 删除

   ```mysql
   DROP TRIGGER [schema_name.]trigger_name ; -- 如果没有指定 schema_name，默认为当前数据库
   ```

#### 4.4.3 案例

通过触发器记录 tb_user 表的数据变更日志，将变更日志插入到日志表user_logs中, 包含增加, 修改 , 删除 ;

表结构准备:

```mysql
-- 准备工作 : 日志表 user_logs
create table user_logs(
	id int(11) not null auto_increment,
	operation varchar(20) not null comment '操作类型, insert/update/delete',
	operate_time datetime not null comment '操作时间',
	operate_id int(11) not null comment '操作的ID',
	operate_params varchar(500) comment '操作参数',
	primary key(`id`)
)engine=innodb default charset=utf8;
```

A. 插入数据触发器

```mysql
create trigger tb_user_insert_trigger
	after insert on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params)
VALUES
	(null, 'insert', now(), new.id, concat('插入的数据内容为:id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ',profession=', NEW.profession));
end;
```

测试：

```mysql
-- 查看
show triggers ;
-- 插入数据到tb_user
insert into tb_user(id, name, phone, email, profession, age, gender, status,
createtime) VALUES (26,'三皇子','18809091212','erhuangzi@163.com','软件工程',23,'1','1',now());
```

测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。

B. 修改数据触发器

```mysql
create trigger tb_user_update_trigger
after update on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params)
VALUES
	(null, 'update', now(), new.id,concat('更新之前的数据: id=',old.id,',name=',old.name, ', phone=',old.phone, ', email=', old.email, ', profession=', old.profession,' | 更新之后的数据: id=',new.id,',name=',new.name, ', phone=',NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));
end;
```

测试:

```mysql
-- 查看
show triggers ;
-- 更新
update tb_user set profession = '会计' where id = 23;
update tb_user set profession = '会计' where id <= 5;
```

测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。

C. 删除数据触发器

```mysql
create trigger tb_user_delete_trigger
	after delete on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, operate_params)
	VALUES (null, 'delete', now(), old.id,concat('删除之前的数据: id=',old.id,',name=',old.name, ', phone=',old.phone, ', email=', old.email, ', profession=', old.profession));
end;
```

测试:

```mysql
-- 查看
show triggers ;
-- 删除数据
delete from tb_user where id = 26;
```

测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。

## 5.锁

### 5.1 概述

锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、 RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有 效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个 角度来说，锁对数据库而言显得尤其重要，也更加复杂。

MySQL中的锁，按照锁的粒度分，分为以下三类：

- 全局锁：锁定数据库中的所有表。
- 表级锁：每次操作锁住整张表。
- 行级锁：每次操作锁住对应的行数据。

### 5.2 全局锁

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语 句，已经更新操作的事务提交语句都将被阻塞。

其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。



为什么全库逻辑备份，就需要加全局锁呢？

A. 我们一起先来分析一下不加全局锁，可能存在的问题。

假设在数据库中存在这样三张表: tb_stock 库存表，tb_order 订单表，tb_orderlog 订单日志表。

![image-20220812175930028](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812175930028.png)

- 在进行数据备份时，先备份了tb_stock库存表。
- 然后接下来，在业务系统中，执行了下单操作，扣减库存，生成订单（更新tb_stock表，插入 tb_order表）
- 然后再执行备份 tb_order表的逻辑。
- 业务中执行插入订单日志操作。
- 最后，又备份了tb_orderlog表。

此时备份出来的数据，是存在问题的。因为备份出来的数据，tb_stock表与tb_order表的数据不一 致(有最新操作的订单信息,但是库存数没减)。

那如何来规避这种问题呢? 此时就可以借助于MySQL的全局锁来解决。

​	B. 再来分析一下加了全局锁后的情况

![image-20220812180225240](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812180225240.png)

对数据库进行进行逻辑备份之前，先对整个数据库加上全局锁，一旦加了全局锁之后，其他的DDL、 DML全部都处于阻塞状态，但是可以执行DQL语句，也就是处于只读状态，而数据备份就是查询操作。 那么数据在进行逻辑备份的过程中，数据库中的数据就是不会发生变化的，这样就保证了数据的一致性 和完整性。

#### 5.2.2 语法

1. 加全局锁

   ```mysql
   flush tables with read lock ;
   ```

2. 数据备份

   ```mysql
   mysqldump -uroot –p1234 itcast > itcast.sql
   ```

3. 释放锁

   ```mysql
   unlock tables ;
   ```

#### 5.2.3 特点

数据库中加全局锁，是一个比较重的操作，存在以下问题：

- 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。
- 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。

在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致 性数据备份。

```mysql
mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql
```

### 5.3 表级锁

#### 5.3.1 介绍

表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、 InnoDB、BDB等存储引擎中。

对于表级锁，主要分为以下三类：

- 表锁
- 元数据锁（meta data lock，MDL）
- 意向锁

#### 5.3.2 表锁

对于表锁，分为两类：

- 表共享读锁（read lock）
- 表独占写锁（write lock）

语法：

- 加锁：lock tables 表名... read/write。
- 释放锁：unlock tables / 客户端断开连接 。

特点：

A. 读锁

![image-20220812181342233](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812181100562.png)

左侧为客户端一，对指定表加了读锁，不会影响右侧客户端二的读，但是会阻塞右侧客户端的写。

测试：

![image-20220812181200987](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812181200987.png)

B. 写锁

![image-20220812181738781](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812181738781.png)

左侧为客户端一，对指定表加了写锁，会阻塞右侧客户端的读和写。

测试：

![image-20220812181830248](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812181830248.png)

> 结论: 读锁不会阻塞其他客户端的读，但是会阻塞写。写锁既会阻塞其他客户端的读，又会阻塞 其他客户端的写。

#### 5.3.3 元数据锁

meta data lock , 元数据锁，简写MDL。

MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维 护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。**为了避免DML与 DDL冲突，保证读写的正确性。**

这里的元数据，大家可以简单理解为就是一张表的表结构。 也就是说，某一张表涉及到未提交的事务时，是不能够修改这张表的表结构的。

在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变 更操作的时候，加MDL写锁(排他)。

常见的SQL操作时，所添加的元数据锁：

| 对应SQL                                          | 锁类型                                  | 说明                                               |
| ------------------------------------------------ | --------------------------------------- | -------------------------------------------------- |
| lock tables xxx read / write                     | SHARED_READ_ONLY / SHARED_NO_READ_WRITE |                                                    |
| select 、select ...  lock in share mode          | SHARED_READ                             | 与SHARED_READ、 SHARED_WRITE兼容，与 EXCLUSIVE互斥 |
| insert 、update、 delete、select ...  for update | SHARED_WRIT                             | 与SHARED_READ、 SHARED_WRITE兼容，与 EXCLUSIVE互斥 |
| alter table ...                                  | EXCLUSIVE                               | 与其他的MDL都互斥                                  |

演示：

当执行SELECT、INSERT、UPDATE、DELETE等语句时，添加的是元数据共享锁（SHARED_READ / SHARED_WRITE），之间是兼容的。

![image-20220812182408957](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812182408957.png)

当执行SELECT语句时，添加的是元数据共享锁（SHARED_READ），会阻塞元数据排他锁 （EXCLUSIVE），之间是互斥的。

![image-20220812182443032](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812182443032.png)

我们可以通过下面的SQL，来查看数据库中的元数据锁的情况：

```mysql
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.metadata_locks ;
```

我们在操作过程中，可以通过上述的SQL语句，来查看元数据锁的加锁情况。

![image-20220812182547000](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812182547000.png)

#### 5.3.4 意向锁

1. 介绍

   为了避免DML在执行时，加的行锁与表锁的冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行 数据是否加锁，使用意向锁来减少表锁的检查。	

​			假如没有意向锁，客户端一对表加了行锁后，客户端二如何给表加表锁呢，来通过示意图			简单分析一 下：

​			首先客户端一，开启一个事务，然后执行DML操作，在执行DML语句时，会对涉及到的行			加行锁。

![image-20220812182839435](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812182839435.png)

​			当客户端二，想对这张表加表锁时，会检查当前表是否有对应的行锁，如果没有，则添加			表锁，此时就 会从第一行数据，检查到最后一行数据，效率较低。

![image-20220812182951244](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812182951244.png)

​		有了意向锁之后 :

​		客户端一，在执行DML操作时，会对涉及的行加行锁，同时也会对该表加上意向锁。

![image-20220812183049753](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812183049753.png)

​		而其他客户端，在对这张表加表锁的时候，会根据该表上所加的意向锁来判定是否可以成		功加表锁，而 不用逐行判断行锁情况了。

![image-20220812183133506](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812183133506.png)

2. 分类

   - 意向共享锁(IS): 由语句select ... lock in share mode添加 。 与 表锁共享锁(read)兼容，与表锁排他锁(write)互斥。
   - 意向排他锁(IX): 由insert、update、delete、select...for update添加 。与表锁共 享锁(read)及排他锁(write)都互斥，意向锁之间不会互斥。

   > 一旦事务提交了，意向共享锁、意向排他锁，都会自动释放。

   可以通过以下SQL，查看意向锁及行锁的加锁情况：

   ```mysql
   select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
   performance_schema.data_locks;
   ```

   演示：

   A. 意向共享锁与表读锁是兼容的

   ![image-20220812183329676](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812183329676.png)

   B. 意向排他锁与表读锁、写锁都是互斥的

   ![image-20220812183401322](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812183401322.png)

### 5.4 行级锁

#### 5.4.1 介绍

行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在 InnoDB存储引擎中。

InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的 锁。对于行级锁，主要分为以下三类：

- 行锁（Record Lock）：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在 RC、RR隔离级别下都支持。

  ![image-20220812190846645](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812190846645.png)

- 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事 务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。

  ![image-20220812190903527](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812190903527.png)

- 临键锁（Next-Key Lock）：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。 在RR隔离级别下支持。

  ![image-20220812190928831](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812190928831.png)

#### 5.4.2 行锁

1. 介绍

   InnoDB实现了以下两种类型的行锁：

   - 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。
   - 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他 锁。

   两种行锁的兼容情况如下：

   ![image-20220812191159073](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812191159073.png)

常见的SQL语句，在执行时，所加的行锁如下：

| SQL                           | 行锁类型    | 说明                                     |
| ----------------------------- | ----------- | ---------------------------------------- |
| INSERT ...                    | 排他锁      | 自动加锁                                 |
| UPDATE ...                    | 排他锁      | 自动加锁                                 |
| DELETE ...                    | 排他锁      | 自动加锁                                 |
| SELECT（正常）                | 不加任何 锁 |                                          |
| SELECT ... LOCK IN SHARE MODE | 共享锁      | 需要手动在SELECT之后加LOCK IN SHARE MODE |
| SELECT ... FOR UPDATE         | 排他锁      | 需要手动在SELECT之后加FOR UPDATE         |

2. 演示

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜 索和索引扫描，以防止幻读。

   - 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。
   - InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记 录加锁，此时 就会升级为表锁。

可以通过以下SQL，查看意向锁及行锁的加锁情况：

```mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
performance_schema.data_locks;
```

**示例演示**

数据准备:

```mysql
CREATE TABLE `stu` (
`id` int NOT NULL PRIMARY KEY AUTO_INCREMENT,
`name` varchar(255) DEFAULT NULL,
`age` int NOT NULL
) ENGINE = InnoDB CHARACTER SET = utf8mb4;
INSERT INTO `stu` VALUES (1, 'tom', 1);
INSERT INTO `stu` VALUES (3, 'cat', 3);
INSERT INTO `stu` VALUES (8, 'rose', 8);
INSERT INTO `stu` VALUES (11, 'jetty', 11);
INSERT INTO `stu` VALUES (19, 'lily', 19);
INSERT INTO `stu` VALUES (25, 'luci', 25);
```

演示行锁的时候，我们就通过上面这张表来演示一下。

A. 普通的select语句，执行时，不会加锁。

![image-20220812191637341](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812191637341.png)

B. select...lock in share mode，加共享锁，共享锁与共享锁之间兼容。

![image-20220812191725121](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812191725121.png)

共享锁与排他锁之间互斥。

![image-20220812191754227](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812191754227.png)

客户端一获取的是id为1这行的共享锁，客户端二是可以获取id为3这行的排它锁的，因为不是同一行 数据。 而如果客户端二想获取id为1这行的排他锁，会处于阻塞状态，以为共享锁与排他锁之间互 斥。

C. 排它锁与排他锁之间互斥

![image-20220812191839494](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812191839494.png)

当客户端一，执行update语句，会为id为1的记录加排他锁； 客户端二，如果也执行update语句更 新id为1的数据，也要为id为1的数据加排他锁，但是客户端二会处于阻塞状态，因为排他锁之间是互 斥的。 直到客户端一，把事务提交了，才会把这一行的行锁释放，此时客户端二，解除阻塞。

D. 无索引行锁升级为表锁

stu表中数据如下:

![image-20220812191945252](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812191945252.png)

我们在两个客户端中执行如下操作:

![image-20220812192006281](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812192006281.png)

在客户端一中，开启事务，并执行update语句，更新name为Lily的数据，也就是id为19的记录 。 然后在客户端二中更新id为3的记录，却不能直接执行，会处于阻塞状态，为什么呢？

原因就是因为此时，客户端一，根据name字段进行更新时，name字段是没有索引的，如果没有索引， 此时行锁会升级为表锁(因为行锁是对索引项加的锁，而name没有索引)。

接下来，我们再针对name字段建立索引，索引建立之后，再次做一个测试：

![image-20220812192047474](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812192047474.png)

此时我们可以看到，客户端一，开启事务，然后依然是根据name进行更新。而客户端二，在更新id为3 的数据时，更新成功，并未进入阻塞状态。 这样就说明，我们根据索引字段进行更新操作，就可以避 免行锁升级为表锁的情况。

#### 5.4.3 间隙锁&临键锁

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜 索和索引扫描，以防止幻读。

- 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。
- 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock 退化为间隙锁。
- 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

> 注意：间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会 阻止另一个事务在同一间隙上采用间隙锁。



**示例演示**

A. 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。

![image-20220812192238889](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812192238889.png)

B. 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock 退化为间隙锁。 

**介绍分析一下：**

我们知道InnoDB的B+树索引，叶子节点是有序的双向链表。 假如，我们要根据这个二级索引查询值 为18的数据，并加上共享锁，我们是只锁定18这一行就可以了吗？ 并不是，因为是非唯一索引，这个 结构中可能有多个18的存在，所以，在加锁时会继续往后找，找到一个不满足条件的值（当前案例中也 就是29）。此时会对18加临键锁，并对29之前的间隙加锁。

![image-20220812192344946](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812192344946.png)

C. 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

![image-20220812192428634](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220812192428634.png)

查询的条件为id>=19，并添加共享锁。 此时我们可以根据数据库表中现有的数据，将数据分为三个部 分：

[19] 

(19,25]

 (25,+∞]

所以数据库数据在加锁是，就是将19加了行锁，25的临键锁（包含25及25之前的间隙），正无穷的临 键锁(正无穷及之前的间隙)。

## 6.InnoDB引擎

### 6.1 逻辑存储结构

InnoDB的逻辑存储结构如下图所示：

![image-20220813104008525](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813104008525.png)

1. 表空间

表空间是InnoDB存储引擎逻辑结构的最高层， 如果用户启用了参数 innodb_file_per_table(在 8.0版本中默认开启) ，则每张表都会有一个表空间（xxx.ibd），一个mysql实例可以对应多个表空 间，用于存储记录、索引等数据。

2. 段

段，分为数据段（Leaf node segment）、索引段（Non-leaf node segment）、回滚段 （Rollback segment），InnoDB是索引组织表，数据段就是B+树的叶子节点， 索引段即为B+树的 非叶子节点。段用来管理多个Extent（区）。

3. 区

区，表空间的单元结构，每个区的大小为1M。 默认情况下， InnoDB存储引擎页大小为16K， 即一 个区中一共有64个连续的页。

4. 页

页，是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性， InnoDB 存储引擎每次从磁盘申请 4-5 个区。

5. 行

行，InnoDB 存储引擎数据是按行进行存放的。

在行中，默认有两个隐藏字段：

- Trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。
- Roll_pointer：每次对某条引记录进行改动时，都会把旧的版本写入到undo日志中，然后这个 隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息。

### 6.2 架构

#### 6.2.1 概述

MySQL5.5 版本开始，默认使用InnoDB存储引擎，它擅长事务处理，具有崩溃恢复特性，在日常开发 中使用非常广泛。下面是InnoDB架构图，左侧为内存结构，右侧为磁盘结构。

![image-20220813104451892](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813104451892.png)

#### 6.2.2 内存结构

![image-20220813104526893](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813104526893.png)

在左侧的内存结构中，主要分为这么四大块儿： Buffer Pool、Change Buffer、Adaptive Hash Index、Log Buffer。 接下来介绍一下这四个部分。

1. Buffer Pool

InnoDB存储引擎基于磁盘文件存储，访问物理硬盘和在内存中进行访问，速度相差很大，为了尽可能 弥补这两者之间的I/O效率的差值，就需要把经常使用的数据加载到缓冲池中，避免每次访问都进行磁 盘I/O。

在InnoDB的缓冲池中不仅缓存了索引页和数据页，还包含了undo页、插入缓存、自适应哈希索引以及 InnoDB的锁信息等等。

缓冲池 Buffer Pool，是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增 删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后再以一定频 率刷新到磁盘，从而减少磁盘IO，加快处理速度。

缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：

- free page：空闲page，未被使用。
- clean page：被使用page，数据没有被修改过。
- dirty page：脏页，被使用page，数据被修改过，也中数据与磁盘的数据产生了不一致。

在专用服务器上，通常将多达80％的物理内存分配给缓冲池 。参数设置： show variables like 'innodb_buffer_pool_size';

![image-20220813104852376](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813104852376.png)

2. Change Buffer

Change Buffer，更改缓冲区（针对于非唯一二级索引页），在执行DML语句时，如果这些数据Page 没有在Buffer Pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区 Change Buffer 中，在未来数据被读取时，再将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。

Change Buffer的意义是什么呢?

先来看一幅图，这个是二级索引的结构图：

![image-20220813104940986](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813104940986.png)

与聚集索引不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新 可能会影响索引树中不相邻的二级索引页，如果每一次都操作磁盘，会造成大量的磁盘IO。有了 ChangeBuffer之后，我们可以在缓冲池中进行合并处理，减少磁盘IO。

3. Adaptive Hash Index

自适应hash索引，用于优化对Buffer Pool数据的查询。MySQL的innoDB引擎中虽然没有直接支持 hash索引，但是给我们提供了一个功能就是这个自适应hash索引。因为前面我们讲到过，hash索引在 进行等值匹配时，一般性能是要高于B+树的，因为hash索引一般只需要一次IO即可，而B+树，可能需 要几次匹配，所以hash索引的效率要高，但是hash索引又不适合做范围查询、模糊匹配等。

 InnoDB存储引擎会监控对表上各索引页的查询，如果观察到在特定的条件下hash索引可以提升速度， 则建立hash索引，称之为自适应hash索引。

**自适应哈希索引，无需人工干预，是系统根据情况自动完成。**

参数： adaptive_hash_index

4. Log Buffer

Log Buffer：日志缓冲区，用来保存要写入到磁盘中的log日志数据（redo log 、undo log）， 默认大小为 16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事 务，增加日志缓冲区的大小可以节省磁盘 I/O。

参数：

innodb_log_buffer_size：缓冲区大小

innodb_flush_log_at_trx_commit：日志刷新到磁盘时机，取值主要包含以下三个：

​		1: 日志在每次事务提交时写入并刷新到磁盘，默认值。

​		0: 每秒将日志写入并刷新到磁盘一次。

​		2: 日志在每次事务提交后写入，并每秒刷新到磁盘一次。

![image-20220813105252328](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105252328.png)

#### 6.2.3 磁盘结构

接下来，再来看看InnoDB体系结构的右边部分，也就是磁盘结构：

![image-20220813105351263](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105351263.png)

1. System Tablespace

系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建 的，它也可能包含表和索引数据。(在MySQL5.x版本中还包含InnoDB数据字典、undolog等)

参数：innodb_data_file_path

![image-20220813105449521](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105449521.png)

系统表空间，默认的文件名叫 ibdata1。

2. File-Per-Table Tablespaces

如果开启了innodb_file_per_table开关 ，则每个表的文件表空间包含单个InnoDB表的数据和索 引 ，并存储在文件系统上的单个数据文件中。

**开关参数：**innodb_file_per_table ，该参数默认开启。

![image-20220813105551279](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105551279.png)

那也就是说，我们没创建一个表，都会产生一个表空间文件，如图：

![image-20220813105614218](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105614218.png)

3. General Tablespaces

通用表空间，需要通过 CREATE TABLESPACE 语法创建通用表空间，在创建表时，可以指定该表空 间。

A. 创建表空间

```mysql
CREATE TABLESPACE ts_name ADD DATAFILE 'file_name' ENGINE = engine_name;
```

![image-20220813105719376](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105719376.png)

B. 创建表时指定表空间

```mysql
CREATE TABLE xxx ... TABLESPACE ts_name;
```

![image-20220813105847705](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813105847705.png)

4. Undo Tablespaces

撤销表空间，MySQL实例在初始化时会自动创建两个默认的undo表空间（初始大小16M），用于存储 undo log日志。

5. Temporary Tablespaces

InnoDB 使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据。

6. Doublewrite Buffer Files

双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件 中，便于系统异常时恢复数据。

![image-20220813110022161](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813110022161.png)

7.  Redo Log

重做日志，是用来实现事务的持久性。该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log）,前者是在内存中，后者在磁盘中。当事务提交之后会把所 有修改信息都会存到该日志中, 用于在刷新脏页到磁盘时,发生错误时, 进行数据恢复使用。

以循环方式写入重做日志文件，涉及两个文件：

![image-20220813110117466](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813110117466.png)

前面我们介绍了InnoDB的内存结构，以及磁盘结构，那么内存中我们所更新的数据，又是如何到磁盘 中的呢？ 此时，就涉及到一组后台线程，接下来，就来介绍一些InnoDB中涉及到的后台线程。

![image-20220813110302243](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813110302243.png)

#### 6.2.4 后台线程

![image-20220813110357959](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813110357959.png)

在InnoDB的后台线程中，分为4类，分别是：Master Thread 、IO Thread、Purge Thread、 Page Cleaner Thread。

1. Master Thread

核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中, 保持数据的一致性， 还包括脏页的刷新、合并插入缓存、undo页的回收 。

2. IO Thread

在InnoDB存储引擎中大量使用了AIO来处理IO请求, 这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的回调。

| 线程类型             | 默认个数 | 职责                         |
| -------------------- | -------- | ---------------------------- |
| Read thread          | 4        | 负责读操作                   |
| Write thread         | 4        | 负责写操作                   |
| Log thread           | 1        | 负责将日志缓冲区刷新到磁盘   |
| Insert buffer thread | 1        | 负责将写缓冲区内容刷新到磁盘 |

我们可以通过以下的这条指令，查看到InnoDB的状态信息，其中就包含IO Thread信息。

```mysql
show engine innodb status \G;
```

![image-20220813110705426](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813110705426.png)

3. Purge Thread

主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回 收。

4. Page Cleaner Thread

协助 Master Thread 刷新脏页到磁盘的线程，它可以减轻 Master Thread 的工作压力，减少阻塞。

### 6.3 事务原理

#### 6.3.1 事务基础

1. 事务

事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系 统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

2. 特性

- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环 境下运行。
- 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

那实际上，我们研究事务的原理，就是研究MySQL的InnoDB引擎是如何保证事务的这四大特性的。

![image-20220813111119798](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813111119798.png)

而对于这四大特性，实际上分为两个部分。 其中的原子性、一致性、持久化，实际上是由InnoDB中的 两份日志来保证的，一份是redo log日志，一份是undo log日志。 而持久性是通过数据库的锁， 加上MVCC来保证的。

![image-20220813111153118](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813111153118.png)

我们在讲解事务原理的时候，主要就是来研究一下redolog，undolog以及MVCC。

#### 6.3.2 redo log

重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。

该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log file）,前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中, 用 于在刷新脏页到磁盘,发生错误时, 进行数据恢复使用。

如果没有redolog，可能会存在什么问题的？ 我们一起来分析一下。

我们知道，在InnoDB引擎中的内存结构中，主要的内存区域就是缓冲池，在缓冲池中缓存了很多的数 据页。 当我们在一个事务中，执行多个增删改的操作时，InnoDB引擎会先操作缓冲池中的数据，如果 缓冲区没有对应的数据，会通过后台线程将磁盘中的数据加载出来，存放在缓冲区中，然后将缓冲池中 的数据修改，修改后的数据页我们称为脏页。 而脏页则会在一定的时机，通过后台线程刷新到磁盘 中，从而保证缓冲区与磁盘的数据一致。 而缓冲区的脏页数据并不是实时刷新的，而是一段时间之后 将缓冲区的数据刷新到磁盘中，假如刷新到磁盘的过程出错了，而提示给用户事务提交成功，而数据却 没有持久化下来，这就出现问题了，没有保证事务的持久性。

![image-20220813111347840](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813111347840.png)

那么，如何解决上述的问题呢？ 在InnoDB中提供了一份日志 redo log，接下来我们再来分析一 下，通过redolog如何解决这个问题。

![image-20220813111426213](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813111426213.png)

有了redolog之后，当对缓冲区的数据进行增删改之后，会首先将操作的数据页的变化，记录在redo log buffer中。在事务提交时，会将redo log buffer中的数据刷新到redo log磁盘文件中。 过一段时间之后，如果刷新缓冲区的脏页到磁盘时，发生错误，此时就可以借助于redo log进行数据 恢复，这样就保证了事务的持久性。 而如果脏页成功刷新到磁盘 或 或者涉及到的数据已经落盘，此 时redolog就没有作用了，就可以删除了，所以存在的两个redolog文件是循环写的。

那为什么每一次提交事务，要刷新redo log 到磁盘中呢，而不是直接将buffer pool中的脏页刷新 到磁盘呢 ?

因为在业务操作中，我们操作数据一般都是随机读写磁盘的，而不是顺序读写磁盘。 而redo log在 往磁盘文件中写入数据，由于是日志文件，所以都是顺序写的。顺序写的效率，要远大于随机写。 这 种先写日志的方式，称之为 WAL（Write-Ahead Logging）。

#### 6.3.3 undo log

回滚日志，用于记录数据被修改前的信息 , 作用包含两个 : 提供回滚(保证事务的原子性) 和 MVCC(多版本并发控制) 。

undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的 update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。

Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些 日志可能还用于MVCC。

Undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的 rollback segment 回滚段中，内部包含1024个undo log segment。

### 6.4 MVCC

#### 6.4.1 基本概念

1. 当前读

读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加 锁。对于我们日常的操作，如：select ... lock in share mode(共享锁)，select ... for update、update、insert、delete(排他锁)都是一种当前读。

测试：

![image-20220813111713621](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813111713621.png)

在测试中我们可以看到，即使是在默认的RR隔离级别下，事务A中依然可以读取到事务B最新提交的内 容，因为在查询语句后面加上了 lock in share mode 共享锁，此时是当前读操作。当然，当我们 加排他锁的时候，也是当前读操作。

2. 快照读

简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据， 不加锁，是非阻塞读。

- Read Committed：每次select，都生成一个快照读。
- Repeatable Read：开启事务后第一个select语句才是快照读的地方。
- Serializable：快照读会退化为当前读。

测试：

![image-20220813111852663](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813111852663.png)

在测试中,我们看到即使事务B提交了数据,事务A中也查询不到。 原因就是因为普通的select是快照 读，而在当前默认的RR隔离级别下，开启事务后第一个select语句才是快照读的地方，后面执行相同 的select语句都是从快照中获取数据，可能不是当前的最新数据，这样也就保证了可重复读。

3. MVCC

全称 Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本， 使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需 要依赖于数据库记录中的三个隐式字段、undo log日志、readView。

接下来，我们再来介绍一下InnoDB引擎的表中涉及到的隐藏字段 、undolog 以及 readview，从 而来介绍一下MVCC的原理。

#### 6.4.2 隐藏字段

##### 6.4.2.1 介绍

![image-20220813112010457](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813112010457.png)

当我们创建了上面的这张表，我们在查看表结构的时候，就可以显式的看到这三个字段。 实际上除了 这三个字段以外，InnoDB还会自动的给我们添加三个隐藏字段及其含义分别是：

| 隐藏字段    | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| DB_TRX_ID   | 最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务ID。 |
| DB_ROLL_PTR | 回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个版 本。 |
| DB_ROW_ID   | 隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段。       |

而上述的前两个字段是肯定会添加的， 是否添加最后一个字段DB_ROW_ID，得看当前表有没有主键， 如果有主键，则不会添加该隐藏字段。

##### 6.4.2.2 测试

1. 查看有主键的表 stu

进入服务器中的 /var/lib/mysql/itcast/ , 查看stu的表结构信息, 通过如下指令:

```shell
ibd2sdi stu.ibd
```

查看到的表结构信息中，有一栏 columns，在其中我们会看到处理我们建表时指定的字段以外，还有 额外的两个字段 分别是：DB_TRX_ID 、 DB_ROLL_PTR ，因为该表有主键，所以没有DB_ROW_ID 隐藏字段。

![image-20220813112336807](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813112336807.png)

![image-20220813112419929](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813112419929.png)

2. 查看没有主键的表 employee

建表语句：

```mysql
create table employee (id int , name varchar(10));
```

此时，我们再通过以下指令来查看表结构及其其中的字段信息：

```mysql
ibd2sdi employee.ibd
```

查看到的表结构信息中，有一栏 columns，在其中我们会看到处理我们建表时指定的字段以外，还有 额外的三个字段 分别是：DB_TRX_ID 、 DB_ROLL_PTR 、DB_ROW_ID，因为employee表是没有 指定主键的。

#### 6.4.3 undolog

##### 6.4.3.1 介绍

回滚日志，在insert、update、delete的时候产生的便于数据回滚的日志。 当insert的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被立即删除。 而update、delete的时候，产生的undo log日志不仅在回滚时需要，在快照读时也需要，不会立即 被删除。

##### 6.4.3.2 版本链

有一张表原始数据为：

![image-20220813112739550](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813112739550.png)

> DB_TRX_ID : 代表最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务ID，是 自增的。 
>
> DB_ROLL_PTR ： 由于这条数据是才插入的，没有被更新过，所以该字段值为null。

然后，有四个并发事务同时在访问这张表。

A. 第一步

![image-20220813112856424](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813112856424.png)

当事务2执行第一条修改语句时，会记录undo log日志，记录数据变更之前的样子; 然后更新记录， 并且记录本次操作的事务ID，回滚指针，回滚指针用来指定如果发生回滚，回滚到哪一个版本。

![image-20220813112927237](C:/Users/70216/AppData/Roaming/Typora/typora-user-images/image-20220813112927237.png)

B.第二步

![image-20220813112955570](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813112955570.png)

当事务3执行第一条修改语句时，也会记录undo log日志，记录数据变更之前的样子; 然后更新记 录，并且记录本次操作的事务ID，回滚指针，回滚指针用来指定如果发生回滚，回滚到哪一个版本。

![image-20220813113056995](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813113056995.png)

C. 第三步

![image-20220813113133183](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813113133183.png)

当事务4执行第一条修改语句时，也会记录undo log日志，记录数据变更之前的样子; 然后更新记 录，并且记录本次操作的事务ID，回滚指针，回滚指针用来指定如果发生回滚，回滚到哪一个版本。

![image-20220813113212138](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813113212138.png)

> 最终我们发现，不同事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条 记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。

#### 6.4.4 readview

ReadView（读视图）是 快照读 SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务 （未提交的）id。

ReadView中包含了四个核心字段：

| 字段           | 含义                                                 |
| -------------- | ---------------------------------------------------- |
| m_ids          | 当前活跃的事务ID集合                                 |
| min_trx_id     | 最小活跃事务ID                                       |
| max_trx_id     | 预分配事务ID，当前最大事务ID+1（因为事务ID是自增的） |
| creator_trx_id | ReadView创建者的事务ID                               |

而在readview中就规定了版本链数据的访问规则：

trx_id 代表当前undolog版本链对应事务ID。

| 条件                               | 是否可以访问                               | 说明                                        |
| ---------------------------------- | ------------------------------------------ | ------------------------------------------- |
| trx_id == creator_trx_id           | 可以访问该版本                             | 成立，说明数据是当前这个事 务更改的。       |
| trx_id < min_trx_id                | 可以访问该版本                             | 可以访问该版本                              |
| trx_id > max_trx_id                | 不可以访问该版本                           | 成立，说明该事务是在 ReadView生成后才开启。 |
| min_trx_id <= trx_id <= max_trx_id | 如果trx_id不在m_ids中， 是可以访问该版本的 | 成立，说明数据已经提交。                    |

不同的隔离级别，生成ReadView的时机不同：

- READ COMMITTED ：在事务中每一次执行快照读时生成ReadView。
- REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。

#### 6.4.5 原理分析

##### 6.4.5.1 RC隔离级别

RC隔离级别下，在事务中每一次执行快照读时生成ReadView。

我们就来分析事务5中，两次快照读读取数据，是如何获取数据的?

在事务5中，查询了两次id为30的记录，由于隔离级别为Read Committed，所以每一次进行快照读 都会生成一个ReadView，那么两次生成的ReadView如下。

![image-20220813164805944](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813164805944.png)

那么这两次快照读在获取数据时，就需要根据所生成的ReadView以及ReadView的版本链访问规则， 到undolog版本链中匹配数据，最终决定此次快照读返回的数据。

A. 先来看第一次快照读具体的读取过程：

![image-20220813164835710](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813164835710.png)

![image-20220813164856088](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813164856088.png)

在进行匹配时，会从undo log的版本链，从上到下进行挨个匹配：

- 先匹配![image-20220813164927822](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813164927822.png)这条记录，这条记录对应的 trx_id为4，也就是将4带入右侧的匹配规则中。 ①不满足 ②不满足 ③不满足 ④也不满足 ， 都不满足，则继续匹配undo log版本链的下一条。
- 再匹配第二条![image-20220813165025580](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165025580.png)，这条 记录对应的trx_id为3，也就是将3带入右侧的匹配规则中。①不满足 ②不满足 ③不满足 ④也 不满足 ，都不满足，则继续匹配undo log版本链的下一条。
- 再匹配第三条![image-20220813165101496](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165101496.png)，这条记 录对应的trx_id为2，也就是将2带入右侧的匹配规则中。①不满足 ②满足 终止匹配，此次快照 读，返回的数据就是版本链中记录的这条数据。

B. 再来看第二次快照读具体的读取过程:

![image-20220813165139595](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165139595.png)

![image-20220813165157991](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165157991.png)

在进行匹配时，会从undo log的版本链，从上到下进行挨个匹配：

- 先匹配 ![image-20220813165227760](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165227760.png)这条记录，这条记录对应的 trx_id为4，也就是将4带入右侧的匹配规则中。 ①不满足 ②不满足 ③不满足 ④也不满足 ， 都不满足，则继续匹配undo log版本链的下一条。
- 再匹配第二条![image-20220813165303813](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165303813.png)，这条 记录对应的trx_id为3，也就是将3带入右侧的匹配规则中。①不满足 ②满足 。终止匹配，此次 快照读，返回的数据就是版本链中记录的这条数据。

##### 6.4.5.2 RR隔离级别

RR隔离级别下，仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。 而RR 是可 重复读，在一个事务中，执行两次相同的select语句，查询到的结果是一样的。 那MySQL是如何做到可重复读的呢? 我们简单分析一下就知道了

![image-20220813165420070](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165420070.png)

我们看到，在RR隔离级别下，只是在事务中第一次快照读时生成ReadView，后续都是复用该 ReadView，那么既然ReadView都一样， ReadView的版本链匹配规则也一样， 那么最终快照读返 回的结果也是一样的。

所以呢，MVCC的实现原理就是通过 InnoDB表的隐藏字段、UndoLog 版本链、ReadView来实现的。 而MVCC + 锁，则实现了事务的隔离性。 而一致性则是由redolog 与 undolog保证。

![image-20220813165453705](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165453705.png)

## 7. MySQL管理

### 7.1 系统数据库

Mysql数据库安装完成后，自带了一下四个数据库，具体作用如下：

| 数据库             | 含义                                                         |
| ------------------ | ------------------------------------------------------------ |
| mysql              | 存储MySQL服务器正常运行所需要的各种信息 （时区、主从、用 户、权限等） |
| information_schema | 提供了访问数据库元数据的各种表和视图，包含数据库、表、字段类 型及访问权限等 |
| performance_schema | 为MySQL服务器运行时状态提供了一个底层监控功能，主要用于收集 数据库服务器性能参数 |
| sys                | 包含了一系列方便 DBA 和开发人员利用 performance_schema 性能数据库进行性能调优和诊断的视图 |

### 7.2 常用工具

#### 7.2.1 mysql

该mysql不是指mysql服务，而是指mysql的客户端工具。

```shell
语法 ：
	mysql [options] [database]
选项 ：
	-u, --user=name #指定用户名
	-p, --password[=name] #指定密码
	-h, --host=name #指定服务器IP或域名
	-P, --port=port #指定连接端口
	-e, --execute=name #执行SQL语句并退出
```

-e选项可以在Mysql客户端执行SQL语句，而不用连接到MySQL数据库再执行，对于一些批处理脚本， 这种方式尤其方便。

示例：

```shell
mysql -uroot –p123456 db01 -e "select * from stu";
```

![image-20220813165841465](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165841465.png)

#### 7.2.2 mysqladmin

mysqladmin 是一个执行管理操作的客户端程序。可以用它来检查服务器的配置和当前状态、创建并 删除数据库等。

```shell
通过帮助文档查看选项：
	mysqladmin --help
```

![image-20220813165929595](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813165929595.png)

```shell
语法:
	mysqladmin [options] command ...
选项:
	-u, --user=name 		#指定用户名
	-p, --password[=name] 	#指定密码
	-h, --host=name 		#指定服务器IP或域名
	-P, --port=port 		#指定连接端口
```

示例：

```shell
mysqladmin -uroot –p1234 drop 'test01';
mysqladmin -uroot –p1234 version;
```

![image-20220813170027664](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170027664.png)

#### 7.2.3 mysqlbinlog

由于服务器生成的二进制日志文件以二进制格式保存，所以如果想要检查这些文本的文本格式，就会使 用到mysqlbinlog 日志管理工具。

```shell
语法 ：
	mysqlbinlog [options] log-files1 log-files2 ...
选项 ：
	-d, --database=name 指定数据库名称，只列出指定的数据库相关操作。
	-o, --offset=# 忽略掉日志中的前n行命令。
	-r,--result-file=name 将输出的文本格式日志输出到指定文件。
	-s, --short-form 显示简单格式， 省略掉一些信息。
	--start-datatime=date1 --stop-datetime=date2 指定日期间隔内的所有日志。
	--start-position=pos1 --stop-position=pos2 指定位置间隔内的所有日志。
```

示例：

A. 查看 binlog.000008这个二进制文件中的数据信息

![image-20220813170142395](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170142395.png)

上述查看到的二进制日志文件数据信息量太多了，不方便查询。 我们可以加上一个参数 -s 来显示简 单格式。

![image-20220813170207669](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170207669.png)

#### 7.2.4 mysqlshow

mysqlshow 客户端对象查找工具，用来很快地查找存在哪些数据库、数据库中的表、表中的列或者索 引。

```shell
语法 ：
	mysqlshow [options] [db_name [table_name [col_name]]]
选项 ：
	--count 显示数据库及表的统计信息（数据库，表 均可以不指定）
	-i 显示指定数据库或者指定表的状态信息
示例：
	#查询test库中每个表中的字段书，及行数
	mysqlshow -uroot -p2143 test --count
	#查询test库中book表的详细情况
	mysqlshow -uroot -p2143 test book --count
```

示例：

A. 查询每个数据库的表的数量及表中记录的数量

```shell
mysqlshow -uroot -p1234 --count
```

![image-20220813170342277](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170342277.png)

B. 查看数据库db01的统计信息

```shell
mysqlshow -uroot -p1234 db01 --count
```

![image-20220813170421516](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170421516.png)

C. 查看数据库db01中的course表的信息

```shell
mysqlshow -uroot -p1234 db01 course --count
```

![image-20220813170455949](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170455949.png)

D. 查看数据库db01中的course表的id字段的信息

```shell
mysqlshow -uroot -p1234 db01 course id --count
```

![image-20220813170529280](C:/Users/70216/AppData/Roaming/Typora/typora-user-images/image-20220813170529280.png)

#### 7.2.5 mysqldump

mysqldump 客户端工具用来备份数据库或在不同数据库之间进行数据迁移。备份内容包含创建表，及 插入表的SQL语句。

```shell
语法 ：
	mysqldump [options] db_name [tables]
	mysqldump [options] --database/-B db1 [db2 db3...]
	mysqldump [options] --all-databases/-A
连接选项 ：
	-u, --user=name 指定用户名
	-p, --password[=name] 指定密码
	-h, --host=name 指定服务器ip或域名
	-P, --port=# 指定连接端口
输出选项：
	--add-drop-database 在每个数据库创建语句前加上 drop database 语句
	--add-drop-table 在每个表创建语句前加上 drop table 语句 , 默认开启 ; 不
开启 (--skip-add-drop-table)
	-n, --no-create-db 不包含数据库的创建语句
	-t, --no-create-info 不包含数据表的创建语句
	-d --no-data 不包含数据
	-T, --tab=name 自动生成两个文件：一个.sql文件，创建表结构的语句；一
个.txt文件，数据文件
```

示例：

A. 备份db01数据库

```shell
mysqldump -uroot -p1234 db01 > db01.sql
```

![image-20220813170701957](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170701957.png)

可以直接打开db01.sql，来查看备份出来的数据到底什么样。

![image-20220813170721550](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170721550.png)

备份出来的数据包含：

- 删除表的语句
- 创建表的语句
- 数据插入语句

如果我们在数据备份时，不需要创建表，或者不需要备份数据，只需要备份表结构，都可以通过对应的 参数来实现。

B. 备份db01数据库中的表数据，不备份表结构(-t)

```shell
mysqldump -uroot -p1234 -t db01 > db01.sql
```

![image-20220813170914411](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813170914411.png)

打开 db02.sql ，来查看备份的数据，只有insert语句，没有备份表结构。

![image-20220813170936635](C:/Users/70216/AppData/Roaming/Typora/typora-user-images/image-20220813170936635.png)

C. 将db01数据库的表的表结构与数据分开备份(-T)

```shell
mysqldump -uroot -p1234 -T /root db01 score
```

![image-20220813171018718](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813171018718.png)

执行上述指令，会出错，数据不能完成备份，原因是因为我们所指定的数据存放目录/root，MySQL认 为是不安全的，需要存储在MySQL信任的目录下。那么，哪个目录才是MySQL信任的目录呢，可以查看 一下系统变量 secure_file_priv 。执行结果如下：

![image-20220813171043332](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813171043332.png)

![image-20220813171055485](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813171055485.png)

上述的两个文件 score.sql 中记录的就是表结构文件，而 score.txt 就是表数据文件，但是需 要注意表数据文件，并不是记录一条条的insert语句，而是按照一定的格式记录表结构中的数据。如 下：

![image-20220813171114496](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813171114496.png)

#### 7.2.6 mysqlimport/source

1. mysqlimport

mysqlimport 是客户端数据导入工具，用来导入mysqldump 加 -T 参数后导出的文本文件。

```shell
语法 ：
	mysqlimport [options] db_name textfile1 [textfile2...]
示例 ：
	mysqlimport -uroot -p2143 test /tmp/city.txt
```

![image-20220813171221283](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220813171221283.png)

2. source

如果需要导入sql文件,可以使用mysql中的source 指令 :

```shell
语法 ：
	source /root/xxxxx.sql
```

# 运维篇

## 1.日志

### 1.1 错误日志 

错误日志是 MySQL 中最重要的日志之一，它记录了当 mysqld 启动和停止时，以及服务器在运行过 程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，建议首先查看此日 志。 该日志是默认开启的，默认存放目录 /var/log/，默认的日志文件名为 mysqld.log 。查看日志 位置：

```shell
show variables like '%log_error%';
```

![image-20220817092147517](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817092147517.png)

### 1.2 二进制日志

#### 1.2.1 介绍

二进制日志（BINLOG）记录了所有的 DDL（数据定义语言）语句和 DML（数据操纵语言）语句，但 不包括数据查询（SELECT、SHOW）语句。

作用：①. 灾难时的数据恢复；②. MySQL的主从复制。在MySQL8版本中，默认二进制日志是开启着 的，涉及到的参数如下：

```shell
show variables like '%log_bin%';
```

![image-20220817092350281](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817092350281.png)

![image-20220817092404361](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817092404361.png)

参数说明：

- log_bin_basename：当前数据库服务器的binlog日志的基础名称(前缀)，具体的binlog文 件名需要再该basename的基础上加上编号(编号从000001开始)。
- log_bin_index：binlog的索引文件，里面记录了当前服务器关联的binlog文件有哪些。

#### 1.2.2 格式

MySQL服务器中提供了多种格式来记录二进制日志，具体格式及特点如下：

| 日志格式  | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| STATEMENT | 基于SQL语句的日志记录，记录的是SQL语句，对数据进行修改的SQL都会记录在 日志文件中。 |
| ROW       | 基于行的日志记录，记录的是每一行的数据变更。（默认）         |
| MIXED     | 混合了STATEMENT和ROW两种格式，默认采用STATEMENT，在某些特殊情况下会 自动切换为ROW进行记录。 |

```shell
show variables like '%binlog_format%';
```

![image-20220817092604447](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817092604447.png)

如果我们需要配置二进制日志的格式，只需要在 /etc/my.cnf 中配置 binlog_format 参数即 可。

#### 1.2.3 查看

由于日志是以二进制方式存储的，不能直接读取，需要通过二进制日志查询工具 mysqlbinlog 来查 看，具体语法：

```shell
mysqlbinlog [ 参数选项 ] logfilename
参数选项：
	-d 指定数据库名称，只列出指定的数据库相关操作。
	-o 忽略掉日志中的前n行命令。
	-v 将行事件(数据变更)重构为SQL语句
	-vv 将行事件(数据变更)重构为SQL语句，并输出注释信息
```

#### 1.2.4 删除

对于比较繁忙的业务系统，每天生成的binlog数据巨大，如果长时间不清除，将会占用大量磁盘空 间。可以通过以下几种方式清理日志：

| 指令                                             | 含义                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| reset master                                     | 删除全部 binlog 日志，删除之后，日志编号，将 从 binlog.000001重新开始 |
| purge master logs to 'binlog.*'                  | 删除 * 编号之前的所有日志                                    |
| purge master logs before 'yyyy-mm-dd hh24:mi:ss' | 删除日志为 "yyyy-mm-dd hh24:mi:ss" 之前 产生的所有日志       |

也可以在mysql的配置文件中配置二进制日志的过期时间，设置了之后，二进制日志过期会自动删除。

```shell
show variables like '%binlog_expire_logs_seconds%';
```

### 1.3 查询日志

查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。默认情况下， 查询日志是未开启的。

![image-20220817092830952](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817092830952.png)

如果需要开启查询日志，可以修改MySQL的配置文件 /etc/my.cnf 文件，添加如下内容：

```shell
#该选项用来开启查询日志 ， 可选值 ： 0 或者 1 ； 0 代表关闭， 1 代表开启
general_log=1
#设置日志的文件名 ， 如果没有指定， 默认的文件名为 host_name.log
general_log_file=mysql_query.log
```

开启了查询日志之后，在MySQL的数据存放目录，也就是 /var/lib/mysql/ 目录下就会出现 mysql_query.log 文件。之后所有的客户端的增删改查操作都会记录在该日志文件之中，长时间运 行后，该日志文件将会非常大。

### 1.4 慢查询日志

慢查询日志记录了所有执行时间超过参数 long_query_time 设置值并且扫描记录数不小于 min_examined_row_limit 的所有的SQL语句的日志，默认未开启。long_query_time 默认为 10 秒，最小为 0， 精度可以到微秒。

如果需要开启慢查询日志，需要在MySQL的配置文件 /etc/my.cnf 中配置如下参数：

```shell
#慢查询日志
slow_query_log=1
#执行时间参数
long_query_time=2
```

默认情况下，不会记录管理语句，也不会记录不使用索引进行查找的查询。可以使用 log_slow_admin_statements和 更改此行为 log_queries_not_using_indexes，如下所 述。

```shell
#记录执行较慢的管理语句
log_slow_admin_statements =1
#记录执行较慢的未使用索引的语句
log_queries_not_using_indexes = 1
```

> 上述所有的参数配置完成之后，都需要重新启动MySQL服务器才可以生效。

## 2.主从复制

### 2.1 概述

主从复制是指将主数据库的 DDL 和 DML 操作通过二进制日志传到从库服务器中，然后在从库上对这 些日志重新执行（也叫重做），从而使得从库和主库的数据保持同步。 MySQL支持一台主库同时向多台从库进行复制， 从库同时也可以作为其他从服务器的主库，实现链状 复制。

![image-20220817093050859](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817093050859.png)

MySQL 复制的优点主要包含以下三个方面：

- 主库出现问题，可以快速切换到从库提供服务。
- 实现读写分离，降低主库的访问压力。
- 可以在从库中执行备份，以避免备份期间影响主库服务。

### 2.2 原理

MySQL主从复制的核心就是 二进制日志，具体的过程如下：

![image-20220817093154299](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817093154299.png)

从上图来看，复制分成三步：

1. Master 主库在事务提交时，会把数据变更记录在二进制日志文件 Binlog 中。
2. 从库读取主库的二进制日志文件 Binlog ，写入到从库的中继日志 Relay Log 。
3. slave重做中继日志中的事件，将改变反映它自己的数据。

### 2.3 搭建

#### 2.3.1 准备

![image-20220817093253607](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817093253607.png)

准备好两台服务器之后，在上述的两台服务器中分别安装好MySQL，并完成基础的初始化准备(安装、 密码配置等操作)工作。 其中：

- 192.168.200.200 作为主服务器master
- 192.168.200.201 作为从服务器slave

#### 2.3.2 主库配置

1. 修改配置文件 /etc/my.cnf

```shell
#mysql 服务ID，保证整个集群环境中唯一，取值范围：1 – 232-1，默认为1
server-id=1
#是否只读,1 代表只读, 0 代表读写
read-only=0
#忽略的数据, 指不需要同步的数据库
#binlog-ignore-db=mysql
#指定同步的数据库
#binlog-do-db=db01
```

2. 重启MySQL服务器

```shell
systemctl restart mysqld
```

3. 登录mysql，创建远程连接的账号，并授予主从复制权限

```shell
#创建itcast用户，并设置密码，该用户可在任意主机连接该MySQL服务
CREATE USER 'itcast'@'%' IDENTIFIED WITH mysql_native_password BY 'Root@123456'
;
#为 'itcast'@'%' 用户分配主从复制权限
GRANT REPLICATION SLAVE ON *.* TO 'itcast'@'%';
```

4. 通过指令，查看二进制日志坐标

```shell
show master status ;
```

![image-20220817093534362](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817093534362.png)

字段含义说明：

 file : 从哪个日志文件开始推送日志文件

 position ： 从哪个位置开始推送日志

binlog_ignore_db : 指定不需要同步的数据库

#### 2.3.3 从库配置

1. 修改配置文件 /etc/my.cnf

```shell
#mysql 服务ID，保证整个集群环境中唯一，取值范围：1 – 2^32-1，和主库不一样即可
server-id=2
#是否只读,1 代表只读, 0 代表读写
read-only=1
```

2. 重新启动MySQL服务

```shell
systemctl restart mysqld
```

3. 登录mysql，设置主库配置

```shell
CHANGE REPLICATION SOURCE TO SOURCE_HOST='192.168.200.200', SOURCE_USER='itcast',
SOURCE_PASSWORD='Root@123456', SOURCE_LOG_FILE='binlog.000004',
SOURCE_LOG_POS=663;
```

上述是8.0.23中的语法。如果mysql是 8.0.23 之前的版本，执行如下SQL：

```shell
CHANGE MASTER TO MASTER_HOST='192.168.200.200', MASTER_USER='itcast',
MASTER_PASSWORD='Root@123456', MASTER_LOG_FILE='binlog.000004',
MASTER_LOG_POS=663;
```

| 参数名          | 含义               | 8.0.23之前      |
| --------------- | ------------------ | --------------- |
| SOURCE_HOST     | 主库IP地址         | MASTER_HOST     |
| SOURCE_USER     | 连接主库的用户名   | MASTER_USER     |
| SOURCE_PASSWORD | 连接主库的密码     | MASTER_PASSWORD |
| SOURCE_LOG_FILE | binlog日志文件名   | MASTER_LOG_FILE |
| SOURCE_LOG_POS  | binlog日志文件位置 | MASTER_LOG_POS  |

4. 开启同步操作

```shell
start replica ; #8.0.22之后
start slave ; #8.0.22之前
```

5. 查看主从同步状态

```shell
show replica status ; #8.0.22之后
show slave status ; #8.0.22之前
```

![image-20220817094117731](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094117731.png)

#### 2.3.4 测试

1. 在主库 192.168.200.200 上创建数据库、表，并插入数据

```shell
create database db01;
use db01;
create table tb_user(
	id int(11) primary key not null auto_increment,
	name varchar(50) not null,
	sex varchar(1)
)engine=innodb default charset=utf8mb4;

insert into tb_user(id,name,sex) values(null,'Tom', '1'),(null,'Trigger','0'),
(null,'Dawn','1');
```

2. 在从库 192.168.200.201 中查询数据，验证主从是否同步

## 3.分库分表

### 3.1 介绍

#### 3.1.1 问题分析

![image-20220817094324501](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094324501.png)

随着互联网及移动互联网的发展，应用系统的数据量也是成指数式增长，若采用单数据库进行数据存 储，存在以下性能瓶颈：

1. IO瓶颈：热点数据太多，数据库缓存不足，产生大量磁盘IO，效率较低。 请求数据太多，带宽 不够，网络IO瓶颈。
2. CPU瓶颈：排序、分组、连接查询、聚合统计等SQL会耗费大量的CPU资源，请求数太多，CPU出 现瓶颈。

为了解决上述问题，我们需要对数据库进行分库分表处理。

![image-20220817094404448](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094404448.png)

分库分表的中心思想都是将数据分散存储，使得单一数据库/表的数据量变小来缓解单一数据库的性能 问题，从而达到提升数据库性能的目的。

#### 3.1.2 拆分策略

分库分表的形式，主要是两种：垂直拆分和水平拆分。而拆分的粒度，一般又分为分库和分表，所以组 成的拆分策略最终如下：

![image-20220817094503485](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094503485.png)

#### 3.1.3 垂直拆分

1. 垂直分库

![image-20220817094530361](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094530361.png)

垂直分库：以表为依据，根据业务将不同表拆分到不同库中。

特点：

- 每个库的表结构都不一样。
- 每个库的数据也不一样。
- 所有库的并集是全量数据。

2. 垂直分表

![image-20220817094633382](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094633382.png)

垂直分表：以字段为依据，根据字段属性将不同字段拆分到不同表中。

特点：

- 每个表的结构都不一样。
- 每个表的数据也不一样，一般通过一列（主键/外键）关联。
- 所有表的并集是全量数据。

#### 3.1.4 水平拆分

1. 水平分库

![image-20220817094752197](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094752197.png)

水平分库：以字段为依据，按照一定策略，将一个库的数据拆分到多个库中。

特点：

- 每个库的表结构都一样。
- 每个库的数据都不一样。
- 所有库的并集是全量数据。

2. 水平分表

![image-20220817094841642](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817094841642.png)

水平分表：以字段为依据，按照一定策略，将一个表的数据拆分到多个表中。

特点：

- 每个表的表结构都一样。
- 每个表的数据都不一样。
- 所有表的并集是全量数据。

> 在业务系统中，为了缓解磁盘IO及CPU的性能瓶颈，到底是垂直拆分，还是水平拆分；具体是分 库，还是分表，都需要根据具体的业务需求具体分析。

#### 3.1.5 实现技术

- shardingJDBC：基于AOP原理，在应用程序中对本地执行的SQL进行拦截，解析、改写、路由处 理。需要自行编码配置实现，只支持java语言，性能较高。
- MyCat：数据库分库分表中间件，不用调整代码即可实现分库分表，支持多种语言，性能不及前 者。

![image-20220817095049271](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095049271.png)

### 3.2 MyCat概述

#### 3.2.1 介绍

Mycat是开源的、活跃的、基于Java语言编写的MySQL数据库中间件。可以像使用mysql一样来使用 mycat，对于开发人员来说根本感觉不到mycat的存在。 开发人员只需要连接MyCat即可，而具体底层用到几台数据库，每一台数据库服务器里面存储了什么数 据，都无需关心。 具体的分库分表的策略，只需要在MyCat中配置即可。

![image-20220817095131952](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095131952.png)

优势：

- 性能可靠稳定
- 强大的技术团队
- 体系完善
- 社区活跃

#### 3.2.2 下载

下载地址：http://dl.mycat.org.cn/

![image-20220817095236987](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095236987.png)

#### 3.2.3 安装

Mycat是采用java语言开发的开源的数据库中间件，支持Windows和Linux运行环境，下面介绍 MyCat的Linux中的环境搭建。我们需要在准备好的服务器中安装如下软件。

- MySQL
- JDK
- Mycat

| 服务器          | 安装软件   | 说明              |
| --------------- | ---------- | ----------------- |
| 192.168.200.210 | JDK、Mycat | MyCat中间件服务器 |
| 192.168.200.210 | MySQL      | 分片服务器        |
| 192.168.200.213 | MySQL      | 分片服务器        |
| 192.168.200.214 | MySQL      | 分片服务器        |

#### 3.2.4 目录介绍

![image-20220817095454426](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095454426.png)

bin : 存放可执行文件，用于启动停止mycat

conf：存放mycat的配置文件

lib：存放mycat的项目依赖包（jar）

logs：存放mycat的日志文件

#### 3.2.5 概念介绍

在MyCat的整体结构中，分为两个部分：上面的逻辑结构、下面的物理结构。

![image-20220817095544226](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095544226.png)

在MyCat的逻辑结构主要负责逻辑库、逻辑表、分片规则、分片节点等逻辑结构的处理，而具体的数据 存储还是在物理结构，也就是数据库服务器中存储的。

### 3.3 MyCat入门

#### 3.3.1 需求

由于 tb_order 表中数据量很大，磁盘IO及容量都到达了瓶颈，现在需要对 tb_order 表进行数 据分片，分为三个数据节点，每一个节点主机位于不同的服务器上, 具体的结构，参考下图：

![image-20220817095629966](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095629966.png)

#### 3.3.2 环境准备

准备3台服务器：

- 192.168.200.210：MyCat中间件服务器，同时也是第一个分片服务器。
- 192.168.200.213：第二个分片服务器。
- 192.168.200.214：第二个分片服务器。

![image-20220817095717944](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817095717944.png)

并且在上述3台数据库中创建数据库 db01 。

#### 3.3.3 配置

1. schema.xml

在schema.xml中配置逻辑库、逻辑表、数据节点、节点主机等相关信息。具体的配置如下：

```shell
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
	<schema name="DB01" checkSQLschema="true" sqlMaxLimit="100">
		<table name="TB_ORDER" dataNode="dn1,dn2,dn3" rule="auto-sharding-long"/>
	</schema>
    <dataNode name="dn1" dataHost="dhost1" database="db01" />
    <dataNode name="dn2" dataHost="dhost2" database="db01" />
    <dataNode name="dn3" dataHost="dhost3" database="db01" />
    <dataHost name="dhost1" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
		<heartbeat>select user()</heartbeat>
		<writeHost host="master" url="jdbc:mysql://192.168.200.210:3306?useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="1234" />
	</dataHost>
	<dataHost name="dhost2" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
		<heartbeat>select user()</heartbeat>
		<writeHost host="master" url="jdbc:mysql://192.168.200.213:3306? useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="1234" />
	</dataHost>
	<dataHost name="dhost3" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
		<heartbeat>select user()</heartbeat>
		<writeHost host="master" url="jdbc:mysql://192.168.200.214:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="1234" />
	</dataHost>
</mycat:schema>		
```

2. server.xml

需要在server.xml中配置用户名、密码，以及用户的访问权限信息，具体的配置如下：

```shell
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">DB01</property>
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="true">
		<schema name="DB01" dml="0110" >
			<table name="TB_ORDER" dml="1110"></table>
		</schema>
	</privileges>
	-->
</user>
<user name="user">
	<property name="password">123456</property>
	<property name="schemas">DB01</property>
	<property name="readOnly">true</property>
</user>
```

上述的配置表示，定义了两个用户 root 和 user ，这两个用户都可以访问 DB01 这个逻辑库，访 问密码都是123456，但是root用户访问DB01逻辑库，既可以读，又可以写，但是 user用户访问 DB01逻辑库是只读的。

#### 3.3.4 测试

##### 3.3.4.1 启动

配置完毕后，先启动涉及到的3台分片服务器，然后启动MyCat服务器。切换到Mycat的安装目录，执 行如下指令，启动Mycat：

```shell
#启动
bin/mycat start
#停止
bin/mycat stop
```

Mycat启动之后，占用端口号 8066。

启动完毕之后，可以查看logs目录下的启动日志，查看Mycat是否启动完成。

![image-20220817100514305](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817100514305.png)

##### 3.3.4.2 测试

1. 连接MyCat

通过如下指令，就可以连接并登陆MyCat。

```shell
mysql -h 192.168.200.210 -P 8066 -uroot -p123456
```

我们看到我们是通过MySQL的指令来连接的MyCat，因为MyCat在底层实际上是模拟了MySQL的协议。

2. 数据测试

然后就可以在MyCat中来创建表，并往表结构中插入数据，查看数据在MySQL中的分布情况。

```mysql
CREATE TABLE TB_ORDER (
	id BIGINT(20) NOT NULL,
    title VARCHAR(100) NOT NULL ,
	PRIMARY KEY (id)
) ENGINE=INNODB DEFAULT CHARSET=utf8 ;

INSERT INTO TB_ORDER(id,title) VALUES(1,'goods1');
INSERT INTO TB_ORDER(id,title) VALUES(2,'goods2');
INSERT INTO TB_ORDER(id,title) VALUES(3,'goods3');
INSERT INTO TB_ORDER(id,title) VALUES(1,'goods1');
INSERT INTO TB_ORDER(id,title) VALUES(2,'goods2');
INSERT INTO TB_ORDER(id,title) VALUES(3,'goods3');
INSERT INTO TB_ORDER(id,title) VALUES(5000000,'goods5000000');
INSERT INTO TB_ORDER(id,title) VALUES(10000000,'goods10000000');
INSERT INTO TB_ORDER(id,title) VALUES(10000001,'goods10000001');
INSERT INTO TB_ORDER(id,title) VALUES(15000000,'goods15000000');
INSERT INTO TB_ORDER(id,title) VALUES(15000001,'goods15000001');
```

经过测试，我们发现，在往 TB_ORDER 表中插入数据时：

- 如果id的值在1-500w之间，数据将会存储在第一个分片数据库中。
- 如果id的值在500w-1000w之间，数据将会存储在第二个分片数据库中。
- 如果id的值在1000w-1500w之间，数据将会存储在第三个分片数据库中。
- 如果id的值超出1500w，在插入数据时，将会报错。

### 3.4 MyCat配置

#### 3.4.1 schema.xml

schema.xml 作为MyCat中最重要的配置文件之一 , 涵盖了MyCat的逻辑库 、 逻辑表 、 分片规 则、分片节点及数据源的配置。

![image-20220817100814941](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817100814941.png)

主要包含以下三组标签：

- schema标签
- datanode标签
- datahost标签

##### 3.4.1.1 schema标签

1. schema 定义逻辑库

![image-20220817100924503](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817100924503.png)

schema 标签用于定义 MyCat实例中的逻辑库 , 一个MyCat实例中, 可以有多个逻辑库 , 可以通 过 schema 标签来划分不同的逻辑库。MyCat中的逻辑库的概念，等同于MySQL中的database概念 , 需要操作某个逻辑库下的表时, 也需要切换逻辑库(use xxx)。

核心属性：

- name：指定自定义的逻辑库库名
- checkSQLschema：在SQL语句操作时指定了数据库名称，执行时是否自动去除；true：自动去 除，false：不自动去除
- sqlMaxLimit：如果未指定limit进行查询，列表查询模式查询多少条记录

2. schema 中的table定义逻辑表

![image-20220817101018253](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817101018253.png)

table 标签定义了MyCat中逻辑库schema下的逻辑表 , 所有需要拆分的表都需要在table标签中定 义 。

核心属性：

- name：定义逻辑表表名，在该逻辑库下唯一
- dataNode：定义逻辑表所属的dataNode，该属性需要与dataNode标签中name对应；多个 dataNode逗号分隔
- rule：分片规则的名字，分片规则名字是在rule.xml中定义的
- primaryKey：逻辑表对应真实表的主键
- type：逻辑表的类型，目前逻辑表只有全局表和普通表，如果未配置，就是普通表；全局表，配 置为 global

##### 3.4.1.2 datanode标签

![image-20220817101113783](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817101113783.png)

核心属性：

- name：定义数据节点名称
- dataHost：数据库实例主机名称，引用自 dataHost 标签中name属性
- database：定义分片所属数据库

##### 3.4.1.3 datahost标签

![image-20220817101156339](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817101156339.png)

该标签在MyCat逻辑库中作为底层标签存在, 直接定义了具体的数据库实例、读写分离、心跳语句。 核心属性：

- name：唯一标识，供上层标签使用 
- maxCon/minCon：最大连接数/最小连接数
-  balance：负载均衡策略，取值 0,1,2,3 
- writeType：写操作分发方式（0：写操作转发到第一个writeHost，第一个挂了，切换到第二 个；1：写操作随机分发到配置的writeHost）
-  dbDriver：数据库驱动，支持 native、jdbc

#### 3.4.2 rule.xml

rule.xml中定义所有拆分表的规则, 在使用过程中可以灵活的使用分片算法, 或者对同一个分片算法 使用不同的参数, 它让分片过程可配置化。主要包含两类标签：tableRule、Function。

![image-20220817101318481](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817101318481.png)

#### 3.4.3 server.xml

server.xml配置文件包含了MyCat的系统配置信息，主要有两个重要的标签：system、user。

1. system标签

![image-20220817101414363](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817101414363.png)

主要配置MyCat中的系统配置信息，对应的系统配置项及其含义，如下：

| 属性                      | 取值       | 含义                                                         |
| ------------------------- | ---------- | ------------------------------------------------------------ |
| charset                   | utf8       | 设置Mycat的字符集, 字符集需要与MySQL的 字符集保持一致        |
| nonePasswordLogin         | 0,1        | 0为需要密码登陆、1为不需要密码登陆 ,默认 为0，设置为1则需要指定默认账户 |
| useHandshakeV10           | 0,1        | 使用该选项主要的目的是为了能够兼容高版本 的jdbc驱动, 是否采用 HandshakeV10Packet来与client进行通 信, 1:是, 0:否 |
| useSqlStat                | 0,1        | 开启SQL实时统计, 1 为开启 , 0 为关闭 ; 开启之后, MyCat会自动统计SQL语句的执行 情况 ; mysql -h 127.0.0.1 -P 9066 -u root -p 查看MyCat执行的SQL, 执行 效率比较低的SQL , SQL的整体执行情况、读 写比例等 ; show @@sql ; show @@sql.slow ; show @@sql.sum ; |
| useGlobleTableCheck       | 0,1        | 是否开启全局表的一致性检测。1为开启 ，0 为关闭 。            |
| sqlExecuteTimeout         | 1000       | SQL语句执行的超时时间 , 单位为 s ;                           |
| sequnceHandlerType        | 0,1,2      | 用来指定Mycat全局序列类型，0 为本地文 件，1 为数据库方式，2 为时间戳列方式，默 认使用本地文件方式，文件方式主要用于测试 |
| sequnceHandlerPattern     | 正则表达式 | 必须带有MYCATSEQ或者 mycatseq进入序列 匹配流程 注意MYCATSEQ_有空格的情况 |
| subqueryRelationshipCheck | true,false | 子查询中存在关联查询的情况下,检查关联字 段中是否有分片字段 .默认 false |
| useCompression            | 0,1        | 开启mysql压缩协议 , 0 : 关闭, 1 : 开 启                      |
| fakeMySQLVersion          | 5.5,5.6    | 设置模拟的MySQL版本号                                        |

| 属性                 | 取值    | 含义                                                         |
| -------------------- | ------- | ------------------------------------------------------------ |
| defaultSqlParser     |         | 由于MyCat的最初版本使用了FoundationDB 的SQL解析器, 在MyCat1.3后增加了Druid 解析器, 所以要设置defaultSqlParser属 性来指定默认的解析器; 解析器有两个 : druidparser 和 fdbparser, 在 MyCat1.4之后,默认是druidparser, fdbparser已经废除了 |
| processors           | 1,2.... | 指定系统可用的线程数量, 默认值为CPU核心 x 每个核心运行线程数量; processors 会 影响processorBufferPool, processorBufferLocalPercent, processorExecutor属性, 所有, 在性能 调优时, 可以适当地修改processors值 |
| processorBufferChunk |         | 指定每次分配Socket Direct Buffer默认 值为4096字节, 也会影响BufferPool长度, 如果一次性获取字节过多而导致buffer不够 用, 则会出现警告, 可以调大该值 |
| processorExecutor    |         | 指定NIOProcessor上共享 businessExecutor固定线程池的大小; MyCat把异步任务交给 businessExecutor 线程池中, 在新版本的MyCat中这个连接池使 用频次不高, 可以适当地把该值调小 |
| packetHeaderSize     |         | 指定MySQL协议中的报文头长度, 默认4个字 节                    |
| maxPacketSize        |         | 指定MySQL协议可以携带的数据最大大小, 默 认值为16M            |
| idleTimeout          | 30      | 指定连接的空闲时间的超时长度;如果超时,将 关闭资源并回收, 默认30分钟 |

| 属性              | 取值    | 含义                                                         |
| ----------------- | ------- | ------------------------------------------------------------ |
| txIsolation       | 1,2,3,4 | 初始化前端连接的事务隔离级别,默认为 REPEATED_READ , 对应数字为3 READ_UNCOMMITED=1; READ_COMMITTED=2; REPEATED_READ=3; SERIALIZABLE=4; |
| sqlExecuteTimeout | 300     | 执行SQL的超时时间, 如果SQL语句执行超时, 将关闭连接; 默认300秒; |
| serverPort        | 8066    | 定义MyCat的使用端口, 默认8066                                |
| managerPort       | 9066    | 定义MyCat的管理端口, 默认9066                                |

2. user标签

配置MyCat中的用户、访问密码，以及用户针对于逻辑库、逻辑表的权限信息，具体的权限描述方式及 配置说明如下：

![image-20220817102050058](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102050058.png)

在测试权限操作时，我们只需要将 privileges 标签的注释放开。 在 privileges 下的schema 标签中配置的dml属性配置的是逻辑库的权限。 在privileges的schema下的table标签的dml属性 中配置逻辑表的权限。

### 3.5 MyCat分片

#### 3.5.1 垂直拆分

##### 3.5.1.1 场景

在业务系统中, 涉及以下表结构 ,但是由于用户与订单每天都会产生大量的数据, 单台服务器的数据 存储及处理能力是有限的, 可以对数据库表进行拆分, 原有的数据库表如下。

![image-20220817102146349](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102146349.png)

现在考虑将其进行垂直分库操作，将商品相关的表拆分到一个数据库服务器，订单表拆分的一个数据库 服务器，用户及省市区表拆分到一个服务器。最终结构如下：

![image-20220817102206506](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102206506.png)

##### 3.5.1.2 准备

准备三台服务器，IP地址如图所示：

![image-20220817102244580](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102244580.png)

并且在192.168.200.210，192.168.200.213, 192.168.200.214上面创建数据库 shopping。

##### 3.5.1.3 配置

1. schema.xml

```shell
<schema name="SHOPPING" checkSQLschema="true" sqlMaxLimit="100">
	<table name="tb_goods_base" dataNode="dn1" primaryKey="id" />
	<table name="tb_goods_brand" dataNode="dn1" primaryKey="id" />
	<table name="tb_goods_cat" dataNode="dn1" primaryKey="id" />
	<table name="tb_goods_desc" dataNode="dn1" primaryKey="goods_id" />
	<table name="tb_goods_item" dataNode="dn1" primaryKey="id" />
	<table name="tb_order_item" dataNode="dn2" primaryKey="id" />
	<table name="tb_order_master" dataNode="dn2" primaryKey="order_id" />
	<table name="tb_order_pay_log" dataNode="dn2" primaryKey="out_trade_no" />
	<table name="tb_user" dataNode="dn3" primaryKey="id" />
	<table name="tb_user_address" dataNode="dn3" primaryKey="id" />
	<table name="tb_areas_provinces" dataNode="dn3" primaryKey="id"/>
	<table name="tb_areas_city" dataNode="dn3" primaryKey="id"/>
	<table name="tb_areas_region" dataNode="dn3" primaryKey="id"/>
</schema>

<dataNode name="dn1" dataHost="dhost1" database="shopping" />
<dataNode name="dn2" dataHost="dhost2" database="shopping" />
<dataNode name="dn3" dataHost="dhost3" database="shopping" />


<dataHost name="dhost1" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="master" url="jdbc:mysql://192.168.200.210:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="1234" />
</dataHost>
<dataHost name="dhost2" maxCon="1000" minCon="10" balance="0"
writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="master" url="jdbc:mysql://192.168.200.213:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8" user="root" password="1234" />
</dataHost>
<dataHost name="dhost3" maxCon="1000" minCon="10" balance="0"
writeType="0" dbType="mysql" dbDriver="jdbc" switchType="1"slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="master" url="jdbc:mysql://192.168.200.214:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" />
</dataHost>	
```

2. server.xml

```shell
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">SHOPPING</property>
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="true">
		<schema name="DB01" dml="0110" >
			<table name="TB_ORDER" dml="1110"></table>
		</schema>
	</privileges>
	-->
</user>
<user name="user">
	<property name="password">123456</property>
	<property name="schemas">SHOPPING</property>
	<property name="readOnly">true</property>
</user>
```

##### 3.5.1.4 测试

1. 上传测试SQL脚本到服务器的/root目录

![image-20220817102712479](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102712479.png)

2. 执行指令导入测试数据

重新启动MyCat后，在mycat的命令行中，通过source指令导入表结构，以及对应的数据，查看数据 分布情况。

```shell
source /root/shopping-table.sql
source /root/shopping-insert.sql
```

将表结构及对应的测试数据导入之后，可以检查一下各个数据库服务器中的表结构分布情况。 检查是 否和我们准备工作中规划的服务器一致。

![image-20220817102802216](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102802216.png)

3. 查询用户的收件人及收件人地址信息(包含省、市、区)。

在MyCat的命令行中，当我们执行以下多表联查的SQL语句时，可以正常查询出数据。

```mysql
select ua.user_id, ua.contact, p.province, c.city, r.area , ua.address from
tb_user_address ua ,tb_areas_city c , tb_areas_provinces p ,tb_areas_region r
where ua.province_id = p.provinceid and ua.city_id = c.cityid and ua.town_id =
r.areaid ;
```

![image-20220817102841188](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102841188.png)

4. 查询每一笔订单及订单的收件地址信息(包含省、市、区)。

实现该需求对应的SQL语句如下：

```mysql
SELECT order_id , payment ,receiver, province , city , area FROM tb_order_master o
, tb_areas_provinces p , tb_areas_city c , tb_areas_region r WHERE
o.receiver_province = p.provinceid AND o.receiver_city = c.cityid AND
o.receiver_region = r.areaid ;
```

但是现在存在一个问题，订单相关的表结构是在 192.168.200.213 数据库服务器中，而省市区的数 据库表是在 192.168.200.214 数据库服务器中。那么在MyCat中执行是否可以成功呢？

![image-20220817102930162](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817102930162.png)

经过测试，我们看到，SQL语句执行报错。原因就是因为MyCat在执行该SQL语句时，需要往具体的数 据库服务器中路由，而当前没有一个数据库服务器完全包含了订单以及省市区的表结构，造成SQL语句 失败，报错。

对于上述的这种现象，我们如何来解决呢？ 下面我们介绍的全局表，就可以轻松解决这个问题。

##### 3.5.1.5 全局表

对于省、市、区/县表tb_areas_provinces , tb_areas_city , tb_areas_region，是属于 数据字典表，在多个业务模块中都可能会遇到，可以将其设置为全局表，利于业务操作。

 修改schema.xml中的逻辑表的配置，修改 tb_areas_provinces、tb_areas_city、 tb_areas_region 三个逻辑表，增加 type 属性，配置为global，就代表该表是全局表，就会在 所涉及到的dataNode中创建给表。对于当前配置来说，也就意味着所有的节点中都有该表了。

```shell
<table name="tb_areas_provinces" dataNode="dn1,dn2,dn3" primaryKey="id" type="global"/>
<table name="tb_areas_city" dataNode="dn1,dn2,dn3" primaryKey="id"
type="global"/>
<table name="tb_areas_region" dataNode="dn1,dn2,dn3" primaryKey="id"
type="global"/>
```

![image-20220817103109164](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817103109164.png)

配置完毕后，重新启动MyCat。

1. 删除原来每一个数据库服务器中的所有表结构
2. 通过source指令，导入表及数据

```shell
source /root/shopping-table.sql
source /root/shopping-insert.sql
```

3. 检查每一个数据库服务器中的表及数据分布，看到三个节点中都有这三张全局表
4. 然后再次执行上面的多表联查的SQL语句

```mysql
SELECT order_id , payment ,receiver, province , city , area FROM tb_order_master o
, tb_areas_provinces p , tb_areas_city c , tb_areas_region r WHERE
o.receiver_province = p.provinceid AND o.receiver_city = c.cityid AND
o.receiver_region = r.areaid ;
```

![image-20220817103216757](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817103216757.png)

是可以正常执行成功的。

5.  当在MyCat中更新全局表的时候，我们可以看到，所有分片节点中的数据都发生了变化，每个节 点的全局表数据时刻保持一致。

#### 3.5.2 水平拆分

##### 3.5.2.1 场景

在业务系统中, 有一张表(日志表), 业务系统每天都会产生大量的日志数据 , 单台服务器的数据存 储及处理能力是有限的, 可以对数据库表进行拆分。

![image-20220817103530231](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817103530231.png)

##### 3.5.2.2 准备

准备三台服务器，具体的结构如下：

![image-20220817103557106](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817103557106.png)

并且，在三台数据库服务器中分表创建一个数据库itcast。

##### 3.5.2.3 配置

1.  schema.xml

```shell
<schema name="ITCAST" checkSQLschema="true" sqlMaxLimit="100">
<table name="tb_log" dataNode="dn4,dn5,dn6" primaryKey="id" rule="mod-long" />
</schema>

<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

tb_log表最终落在3个节点中，分别是 dn4、dn5、dn6 ，而具体的数据分别存储在 dhost1、 dhost2、dhost3的itcast数据库中。

2. server.xml

配置root用户既可以访问 SHOPPING 逻辑库，又可以访问ITCAST逻辑库。

```shell
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">SHOPPING,ITCAST</property>
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="true">
		<schema name="DB01" dml="0110" >
			<table name="TB_ORDER" dml="1110"></table>
		</schema>
	</privileges>
	-->
</user>
```

##### 3.5.2.4 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```shell
CREATE TABLE tb_log (
	id bigint(20) NOT NULL COMMENT 'ID',
	model_name varchar(200) DEFAULT NULL COMMENT '模块名',
	model_value varchar(200) DEFAULT NULL COMMENT '模块值',
	return_value varchar(200) DEFAULT NULL COMMENT '返回值',
	return_class varchar(200) DEFAULT NULL COMMENT '返回值类型',
	operate_user varchar(20) DEFAULT NULL COMMENT '操作用户',
	operate_time varchar(20) DEFAULT NULL COMMENT '操作时间',
	param_and_value varchar(500) DEFAULT NULL COMMENT '请求参数名及参数值',
	operate_class varchar(200) DEFAULT NULL COMMENT '操作类',
	operate_method varchar(200) DEFAULT NULL COMMENT '操作方法',
	cost_time bigint(20) DEFAULT NULL COMMENT '执行方法耗时, 单位 ms',
	source int(1) DEFAULT NULL COMMENT '来源 : 1 PC , 2 Android , 3 IOS',
	PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO tb_log (id, model_name, model_value, return_value, return_class,
operate_user, operate_time, param_and_value, operate_class, operate_method,
cost_time，source)
VALUES('1','user','insert','success','java.lang.String','10001','2022-01-06
18:12:28','{\"age\":\"20\",\"name\":\"Tom\",\"gender\":\"1\"}','cn.itcast.contro
ller.UserController','insert','10',1);
INSERT INTO tb_log (id, model_name, model_value, return_value, return_class,
operate_user, operate_time, param_and_value, operate_class, operate_method,
cost_time，source)
VALUES('2','user','insert','success','java.lang.String','10001','2022-01-06
18:12:27','{\"age\":\"20\",\"name\":\"Tom\",\"gender\":\"1\"}','cn.itcast.contro
ller.UserController','insert','23',1);
INSERT INTO tb_log (id, model_name, model_value, return_value, return_class,
operate_user, operate_time, param_and_value, operate_class, operate_method,
cost_time，source)
VALUES('3','user','update','success','java.lang.String','10001','2022-01-06
18:16:45','{\"age\":\"20\",\"name\":\"Tom\",\"gender\":\"1\"}','cn.itcast.contro
ller.UserController','update','34',1);
INSERT INTO tb_log (id, model_name, model_value, return_value, return_class,
operate_user, operate_time, param_and_value, operate_class, operate_method,
cost_time，source)
VALUES('4','user','update','success','java.lang.String','10001','2022-01-06
18:16:45','{\"age\":\"20\",\"name\":\"Tom\",\"gender\":\"1\"}','cn.itcast.contro
ller.UserController','update','13',2);
INSERT INTO tb_log (id, model_name, model_value, return_value, return_class,
operate_user, operate_time, param_and_value, operate_class, operate_method,
cost_time，source)
VALUES('5','user','insert','success','java.lang.String','10001','2022-01-06
18:30:31','{\"age\":\"200\",\"name\":\"TomCat\",\"gender\":\"0\"}','cn.itcast.co
ntroller.UserController','insert','29',3);
INSERT INTO tb_log (id, model_name, model_value, return_value, return_class,
operate_user, operate_time, param_and_value, operate_class, operate_method,
cost_time，source)
VALUES('6','user','find','success','java.lang.String','10001','2022-01-06
18:30:31','{\"age\":\"200\",\"name\":\"TomCat\",\"gender\":\"0\"}','cn.itcast.co
ntroller.UserController','find','29',2);
```

#### 3.5.3 分片规则

##### 3.5.3.1 范围分片

1. 介绍

根据指定的字段及其配置的范围与数据节点的对应情况， 来决定该数据属于哪一个分片。

![image-20220817104002331](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817104002331.png)

2. 配置

schema.xml逻辑表配置：

```xml
<table name="TB_ORDER" dataNode="dn1,dn2,dn3" rule="auto-sharding-long" />
```

schema.xml数据节点配置：

```xml
<dataNode name="dn1" dataHost="dhost1" database="db01" />
<dataNode name="dn2" dataHost="dhost2" database="db01" />
<dataNode name="dn3" dataHost="dhost3" database="db01" />
```

rule.xml分片规则配置：

```xml
<tableRule name="auto-sharding-long">
	<rule>
		<columns>id</columns>
		<algorithm>rang-long</algorithm>
	</rule>
</tableRule>
<function name="rang-long" class="io.mycat.route.function.AutoPartitionByLong">
	<property name="mapFile">autopartition-long.txt</property>
	<property name="defaultNode">0</property>
</function>
```

分片规则配置属性含义：

| 属性        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| columns     | 标识将要分片的表字段                                         |
| algorithm   | 指定分片函数与function的对应关系                             |
| class       | 指定该分片算法对应的类                                       |
| mapFile     | 对应的外部配置文件                                           |
| type        | 默认值为0 ; 0 表示Integer , 1 表示String                     |
| defaultNode | 默认节点 默认节点的所用:枚举分片时,如果碰到不识别的枚举值, 就让它路 由到默认节点 ; 如果没有默认值,碰到不识别的则报错 。 |

在rule.xml中配置分片规则时，关联了一个映射配置文件 autopartition-long.txt，该配置文 件的配置如下：

```shell
# range start-end ,data node index
# K=1000,M=10000.
0-500M=0
500M-1000M=1
1000M-1500M=2
```

含义：0-500万之间的值，存储在0号数据节点(数据节点的索引从0开始) ； 500万-1000万之间的 数据存储在1号数据节点 ； 1000万-1500万的数据节点存储在2号节点 ；

该分片规则，主要是针对于数字类型的字段适用。 在MyCat的入门程序中，我们使用的就是该分片规 则。

##### 3.5.3.2 取模分片

1. 介绍

根据指定的字段值与节点数量进行求模运算，根据运算结果， 来决定该数据属于哪一个分片。

![image-20220817104350779](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817104350779.png)

2. 配置

schema.xml逻辑表配置：

```xml
<table name="tb_log" dataNode="dn4,dn5,dn6" primaryKey="id" rule="mod-long" />
```

schema.xml数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml分片规则配置：

```xml
<tableRule name="mod-long">
	<rule>
	<columns>id</columns>
	<algorithm>mod-long</algorithm>
</rule>
</tableRule>
<function name="mod-long" class="io.mycat.route.function.PartitionByMod">
	<property name="count">3</property>
</function>
```

分片规则属性说明如下：

| 属性      | 描述                             |
| --------- | -------------------------------- |
| columns   | 标识将要分片的表字段             |
| algorithm | 指定分片函数与function的对应关系 |
| class     | 指定该分片算法对应的类           |
| count     | 数据节点的数量                   |

该分片规则，主要是针对于数字类型的字段适用。 在前面水平拆分的演示中，我们选择的就是取模分 片。

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

##### 3.5.3.3 一致性hash分片

1. 介绍

所谓一致性哈希，相同的哈希因子计算值总是被划分到相同的分区表中，不会因为分区节点的增加而改 变原来数据的分区位置，有效的解决了分布式数据的拓容问题。

![image-20220817104703270](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817104703270.png)

2. 配置

schema.xml中逻辑表配置：

```xml
<!-- 一致性hash -->
<table name="tb_order" dataNode="dn4,dn5,dn6" rule="sharding-by-murmur" />
```

schema.xml中数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml中分片规则配置：

```xml
<tableRule name="sharding-by-murmur">
	<rule>
		<columns>id</columns>
		<algorithm>murmur</algorithm>
	</rule>
</tableRule>
<function name="murmur" class="io.mycat.route.function.PartitionByMurmurHash">
	<property name="seed">0</property><!-- 默认是0 -->
	<property name="count">3</property>
	<property name="virtualBucketTimes">160</property>
</function>
```

分片规则属性含义：

| 属性               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| columns            | 标识将要分片的表字段                                         |
| algorithm          | 指定分片函数与function的对应关系                             |
| class              | 指定该分片算法对应的类                                       |
| seed               | 创建murmur_hash对象的种子，默认0                             |
| count              | 要分片的数据库节点数量，必须指定，否则没法分片               |
| virtualBucketTimes | 一个实际的数据库节点被映射为这么多虚拟节点，默认是160倍，也 就是虚拟节点数是物理节点数的160 倍;virtualBucketTimes*count就是虚拟结点数量 ; |
| weightMapFile      | 节点的权重，没有指定权重的节点默认是1。以properties文件的 格式填写，以从0开始到count-1的整数值也就是节点索引为key， 以节点权重值为值。所有权重值必须是正整数，否则以1代替 |
| bucketMapPath      | 用于测试时观察各物理节点与虚拟节点的分布情况，如果指定了这个 属性，会把虚拟节点的murmur hash值与物理节点的映射按行输出 到这个文件，没有默认值，如果不指定，就不会输出任何东西 |

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
create table tb_order(
	id varchar(100) not null primary key,
	money int null,
	content varchar(200) null
);
INSERT INTO tb_order (id, money, content) VALUES ('b92fdaaf-6fc4-11ec-b831-
482ae33c4a2d', 10, 'b92fdaf8-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b93482b6-6fc4-11ec-b831-
482ae33c4a2d', 20, 'b93482d5-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b937e246-6fc4-11ec-b831-
482ae33c4a2d', 50, 'b937e25d-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b93be2dd-6fc4-11ec-b831-
482ae33c4a2d', 100, 'b93be2f9-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b93f2d68-6fc4-11ec-b831-
482ae33c4a2d', 130, 'b93f2d7d-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b9451b98-6fc4-11ec-b831-
482ae33c4a2d', 30, 'b9451bcc-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b9488ec1-6fc4-11ec-b831-
482ae33c4a2d', 560, 'b9488edb-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b94be6e6-6fc4-11ec-b831-
482ae33c4a2d', 10, 'b94be6ff-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b94ee10d-6fc4-11ec-b831-
482ae33c4a2d', 123, 'b94ee12c-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b952492a-6fc4-11ec-b831-
482ae33c4a2d', 145, 'b9524945-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b95553ac-6fc4-11ec-b831-
482ae33c4a2d', 543, 'b95553c8-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b9581cdd-6fc4-11ec-b831-
482ae33c4a2d', 17, 'b9581cfa-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b95afc0f-6fc4-11ec-b831-
482ae33c4a2d', 18, 'b95afc2a-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b95daa99-6fc4-11ec-b831-
482ae33c4a2d', 134, 'b95daab2-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b9667e3c-6fc4-11ec-b831-
482ae33c4a2d', 156, 'b9667e60-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b96ab489-6fc4-11ec-b831-
482ae33c4a2d', 175, 'b96ab4a5-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b96e2942-6fc4-11ec-b831-
482ae33c4a2d', 180, 'b96e295b-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b97092ec-6fc4-11ec-b831-
482ae33c4a2d', 123, 'b9709306-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b973727a-6fc4-11ec-b831-
482ae33c4a2d', 230, 'b9737293-6fc4-11ec-b831-482ae33c4a2d');
INSERT INTO tb_order (id, money, content) VALUES ('b978840f-6fc4-11ec-b831-
482ae33c4a2d', 560, 'b978843c-6fc4-11ec-b831-482ae33c4a2d');
```

##### 3.5.3.4 枚举分片

1. 介绍

通过在配置文件中配置可能的枚举值, 指定数据分布到不同数据节点上, 本规则适用于按照省份、性 别、状态拆分数据等业务 。

![image-20220817105100626](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817105100626.png)

2. 配置

schema.xml中逻辑表配置：

```xml
<!-- 枚举 -->
<table name="tb_user" dataNode="dn4,dn5,dn6" rule="sharding-by-intfile-enumstatus"
/>
```

schema.xml中数据节点配置：

```mysql
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml中分片规则配置：

```mysql
<tableRule name="sharding-by-intfile">
	<rule>
		<columns>sharding_id</columns>
		<algorithm>hash-int</algorithm>
	</rule>
</tableRule>
<!-- 自己增加 tableRule -->
<tableRule name="sharding-by-intfile-enumstatus">
	<rule>
		<columns>status</columns>
		<algorithm>hash-int</algorithm>
	</rule>
</tableRule>
<function name="hash-int" class="io.mycat.route.function.PartitionByFileMap">
	<property name="defaultNode">2</property>
	<property name="mapFile">partition-hash-int.txt</property>
</function>
```

partition-hash-int.txt ，内容如下 :

```
1=0
2=1
3=2
```

分片规则属性含义：

| 属性        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| columns     | 标识将要分片的表字段                                         |
| algorithm   | 指定分片函数与function的对应关系                             |
| class       | 指定该分片算法对应的类                                       |
| mapFile     | 对应的外部配置文件                                           |
| type        | 默认值为0 ; 0 表示Integer , 1 表示String                     |
| defaultNode | 默认节点 ; 小于0 标识不设置默认节点 , 大于等于0代表设置默认节点 ; 默认节点的所用:枚举分片时,如果碰到不识别的枚举值, 就让它路由到默认节 点 ; 如果没有默认值,碰到不识别的则报错 。 |

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
CREATE TABLE tb_user (
	id bigint(20) NOT NULL COMMENT 'ID',
	username varchar(200) DEFAULT NULL COMMENT '姓名',
	status int(2) DEFAULT '1' COMMENT '1: 未启用, 2: 已启用, 3: 已关闭',
	PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

insert into tb_user (id,username ,status) values(1,'Tom',1);
insert into tb_user (id,username ,status) values(2,'Cat',2);
insert into tb_user (id,username ,status) values(3,'Rose',3);
insert into tb_user (id,username ,status) values(4,'Coco',2);
insert into tb_user (id,username ,status) values(5,'Lily',1);
insert into tb_user (id,username ,status) values(6,'Tom',1);
insert into tb_user (id,username ,status) values(7,'Cat',2);
insert into tb_user (id,username ,status) values(8,'Rose',3);
insert into tb_user (id,username ,status) values(9,'Coco',2);
insert into tb_user (id,username ,status) values(10,'Lily',1);
```

##### 3.5.3.5 应用指定算法

1. 介绍

运行阶段由应用自主决定路由到那个分片 , 直接根据字符子串（必须是数字）计算分片号。

![image-20220817111714099](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817111714099.png)

2.  配置

schema.xml中逻辑表配置：

```xml
<!-- 应用指定算法 -->
<table name="tb_app" dataNode="dn4,dn5,dn6" rule="sharding-by-substring" />
```

schema.xml中数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml中分片规则配置：

```xml
<tableRule name="sharding-by-substring">
	<rule>
		<columns>id</columns>
		<algorithm>sharding-by-substring</algorithm>
	</rule>
</tableRule>
<function name="sharding-by-substring"
class="io.mycat.route.function.PartitionDirectBySubString">
	<property name="startIndex">0</property> <!-- zero-based -->
	<property name="size">2</property>
	<property name="partitionCount">3</property>
	<property name="defaultPartition">0</property>
</function>
```

分片规则属性含义：

| 属性             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| columns          | 标识将要分片的表字段                                         |
| algorithm        | 指定分片函数与function的对应关系                             |
| class            | 指定该分片算法对应的类                                       |
| startIndex       | 字符子串起始索引                                             |
| size             | 字符长度                                                     |
| partitionCount   | 分区(分片)数量                                               |
| defaultPartition | 默认分片(在分片数量定义时, 字符标示的分片编号不在分片数量内时, 使用默认分片) |

示例说明 :

id=05-100000002 , 在此配置中代表根据id中从 startIndex=0，开始，截取siz=2位数字即 05，05就是获取的分区，如果没找到对应的分片则默认分配到defaultPartition 。

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
CREATE TABLE tb_app (
	id varchar(10) NOT NULL COMMENT 'ID',
	name varchar(200) DEFAULT NULL COMMENT '名称',
	PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

insert into tb_app (id,name) values('0000001','Testx00001');
insert into tb_app (id,name) values('0100001','Test100001');
insert into tb_app (id,name) values('0100002','Test200001');
insert into tb_app (id,name) values('0200001','Test300001');
insert into tb_app (id,name) values('0200002','TesT400001');
```

##### 3.5.3.6 固定分片hash算法

1. 介绍

该算法类似于十进制的求模运算，但是为二进制的操作，例如，取 id 的二进制低 10 位 与 1111111111 进行位 & 运算，位与运算最小值为 0000000000，最大值为1111111111，转换为十 进制，也就是位于0-1023之间。

![image-20220817112107239](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817112107239.png)

特点：

- 如果是求模，连续的值，分别分配到各个不同的分片；但是此算法会将连续的值可能分配到相同的 分片，降低事务处理的难度。
- 可以均匀分配，也可以非均匀分配。
- 分片字段必须为数字类型。

2. 配置

schema.xml中逻辑表配置：

```xml
<!-- 固定分片hash算法 -->
<table name="tb_longhash" dataNode="dn4,dn5,dn6" rule="sharding-by-long-hash" />
```

schema.xml中数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml中分片规则配置：

```xml
<tableRule name="sharding-by-long-hash">
	<rule>
		<columns>id</columns>
		<algorithm>sharding-by-long-hash</algorithm>
	</rule>
</tableRule>
<!-- 分片总长度为1024，count与length数组长度必须一致； -->
<function name="sharding-by-long-hash"
class="io.mycat.route.function.PartitionByLong">
	<property name="partitionCount">2,1</property>
	<property name="partitionLength">256,512</property>
</function>
```

分片规则属性含义：

| 属性            | 描述                             |
| --------------- | -------------------------------- |
| columns         | 标识将要分片的表字段名           |
| algorithm       | 指定分片函数与function的对应关系 |
| class           | 指定该分片算法对应的类           |
| partitionCount  | 分片个数列表                     |
| partitionLength | 分片范围列表                     |

约束 :

1. 分片长度 : 默认最大2^10 , 为 1024 ;
2.  count, length的数组长度必须是一致的 ;

以上分为三个分区:0-255,256-511,512-1023

示例说明 :

![image-20220817112434156](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817112434156.png)

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
CREATE TABLE tb_longhash (
	id int(11) NOT NULL COMMENT 'ID',
	name varchar(200) DEFAULT NULL COMMENT '名称',
	firstChar char(1) COMMENT '首字母',
	PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
insert into tb_longhash (id,name,firstChar) values(1,'七匹狼','Q');
insert into tb_longhash (id,name,firstChar) values(2,'八匹狼','B');
insert into tb_longhash (id,name,firstChar) values(3,'九匹狼','J');
insert into tb_longhash (id,name,firstChar) values(4,'十匹狼','S');
insert into tb_longhash (id,name,firstChar) values(5,'六匹狼','L');
insert into tb_longhash (id,name,firstChar) values(6,'五匹狼','W');
insert into tb_longhash (id,name,firstChar) values(7,'四匹狼','S');
insert into tb_longhash (id,name,firstChar) values(8,'三匹狼','S');
insert into tb_longhash (id,name,firstChar) values(9,'两匹狼','L');
```

##### 3.5.3.7 字符串hash解析算法

1. 介绍

截取字符串中的指定位置的子字符串, 进行hash算法， 算出分片。

![image-20220817112534950](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817112534950.png)

2. 配置

schema.xml中逻辑表配置：

```xml
<!-- 字符串hash解析算法 -->
<table name="tb_strhash" dataNode="dn4,dn5" rule="sharding-by-stringhash" />
```

schema.xml中数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
```

rule.xml中分片规则配置：

```xml
<tableRule name="sharding-by-stringhash">
	<rule>
		<columns>name</columns>
		<algorithm>sharding-by-stringhash</algorithm>
	</rule>
</tableRule>
<function name="sharding-by-stringhash"
class="io.mycat.route.function.PartitionByString">
	<property name="partitionLength">512</property> <!-- zero-based -->
	<property name="partitionCount">2</property>
	<property name="hashSlice">0:2</property>
</function>
```

分片规则属性含义：

| 属性            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| columns         | 标识将要分片的表字段                                         |
| algorithm       | 指定分片函数与function的对应关系                             |
| class           | 指定该分片算法对应的类                                       |
| partitionLength | hash求模基数 ; length*count=1024 (出于性能考虑)              |
| partitionCount  | 分区数                                                       |
| hashSlice       | hash运算位 , 根据子字符串的hash运算 ; 0 代表 str.length() , -1 代表 str.length()-1 , 大于0只代表数字自身 ; 可以理解 为substring（start，end），start为0则只表示0 |

示例说明：

![image-20220817112808354](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817112808354.png)

3.  测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
create table tb_strhash(
	name varchar(20) primary key,
	content varchar(100)
)engine=InnoDB DEFAULT CHARSET=utf8mb4;
INSERT INTO tb_strhash (name,content) VALUES('T1001', UUID());
INSERT INTO tb_strhash (name,content) VALUES('ROSE', UUID());
INSERT INTO tb_strhash (name,content) VALUES('JERRY', UUID());
INSERT INTO tb_strhash (name,content) VALUES('CRISTINA', UUID());
INSERT INTO tb_strhash (name,content) VALUES('TOMCAT', UUID());
```

##### 3.5.3.8 按天分片算法

1. 介绍

按照日期及对应的时间周期来分片。

![image-20220817112936321](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817112936321.png)

2. 配置

schema.xml中逻辑表配置：

```xml
<!-- 按天分片 -->
<table name="tb_datepart" dataNode="dn4,dn5,dn6" rule="sharding-by-date" />
```

schema.xml中数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml中分片规则配置：

```xml
<tableRule name="sharding-by-date">
	<rule>
		<columns>create_time</columns>
		<algorithm>sharding-by-date</algorithm>
	</rule>
</tableRule>
<function name="sharding-by-date"
class="io.mycat.route.function.PartitionByDate">
	<property name="dateFormat">yyyy-MM-dd</property>
	<property name="sBeginDate">2022-01-01</property>
	<property name="sEndDate">2022-01-30</property>
	<property name="sPartionDay">10</property>
</function>
<!--
	从开始时间开始，每10天为一个分片，到达结束时间之后，会重复开始分片插入
	配置表的 dataNode 的分片，必须和分片规则数量一致，例如 2022-01-01 到 2022-12-31 ，每
10天一个分片，一共需要37个分片。
-->
```

分片规则属性含义：

| 属性        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| columns     | 标识将要分片的表字段                                         |
| algorithm   | 指定分片函数与function的对应关系                             |
| class       | 指定该分片算法对应的类                                       |
| dateFormat  | 日期格式                                                     |
| sBeginDate  | 开始日期                                                     |
| sEndDate    | 结束日期，如果配置了结束日期，则代码数据到达了这个日期的分片后，会重 复从开始分片插入 |
| sPartionDay | 分区天数，默认值 10 ，从开始日期算起，每个10天一个分区       |

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
create table tb_datepart(
	id bigint not null comment 'ID' primary key,
	name varchar(100) null comment '姓名',
	create_time date null
);
insert into tb_datepart(id,name ,create_time) values(1,'Tom','2022-01-01');
insert into tb_datepart(id,name ,create_time) values(2,'Cat','2022-01-10');
insert into tb_datepart(id,name ,create_time) values(3,'Rose','2022-01-11');
insert into tb_datepart(id,name ,create_time) values(4,'Coco','2022-01-20');
insert into tb_datepart(id,name ,create_time) values(5,'Rose2','2022-01-21');
insert into tb_datepart(id,name ,create_time) values(6,'Coco2','2022-01-30');
insert into tb_datepart(id,name ,create_time) values(7,'Coco3','2022-01-31');
```

##### 3.5.3.9 自然月分片

1. 介绍

使用场景为按照月份来分片, 每个自然月为一个分片。

![image-20220817113334004](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817113334004.png)

2. 配置

schema.xml中逻辑表配置：

```xml
<!-- 按自然月分片 -->
<table name="tb_monthpart" dataNode="dn4,dn5,dn6" rule="sharding-by-month" />
```

schema.xml中数据节点配置：

```xml
<dataNode name="dn4" dataHost="dhost1" database="itcast" />
<dataNode name="dn5" dataHost="dhost2" database="itcast" />
<dataNode name="dn6" dataHost="dhost3" database="itcast" />
```

rule.xml中分片规则配置：

```xml
<tableRule name="sharding-by-month">
	<rule>
		<columns>create_time</columns>
		<algorithm>partbymonth</algorithm>
	</rule>
</tableRule>
<function name="partbymonth" class="io.mycat.route.function.PartitionByMonth">
	<property name="dateFormat">yyyy-MM-dd</property>
	<property name="sBeginDate">2022-01-01</property>
	<property name="sEndDate">2022-03-31</property>
</function>
<!--
从开始时间开始，一个月为一个分片，到达结束时间之后，会重复开始分片插入
配置表的 dataNode 的分片，必须和分片规则数量一致，例如 2022-01-01 到 2022-12-31 ，一
共需要12个分片。
-->
```

分片规则属性含义：

| 属性       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| columns    | 标识将要分片的表字段                                         |
| algorithm  | 指定分片函数与function的对应关系                             |
| class      | 指定该分片算法对应的类                                       |
| dateFormat | 日期格式                                                     |
| sBeginDate | 开始日期                                                     |
| sEndDate   | 结束日期，如果配置了结束日期，则代码数据到达了这个日期的分片后，会重复 从开始分片插入 |

3. 测试

配置完毕后，重新启动MyCat，然后在mycat的命令行中，执行如下SQL创建表、并插入数据，查看数 据分布情况。

```mysql
create table tb_monthpart(
	id bigint not null comment 'ID' primary key,
	name varchar(100) null comment '姓名',
	create_time date null
);
insert into tb_monthpart(id,name ,create_time) values(1,'Tom','2022-01-01');
insert into tb_monthpart(id,name ,create_time) values(2,'Cat','2022-01-10');
insert into tb_monthpart(id,name ,create_time) values(3,'Rose','2022-01-31');
insert into tb_monthpart(id,name ,create_time) values(4,'Coco','2022-02-20');
insert into tb_monthpart(id,name ,create_time) values(5,'Rose2','2022-02-25');
insert into tb_monthpart(id,name ,create_time) values(6,'Coco2','2022-03-10');
insert into tb_monthpart(id,name ,create_time) values(7,'Coco3','2022-03-31');
insert into tb_monthpart(id,name ,create_time) values(8,'Coco4','2022-04-10');
insert into tb_monthpart(id,name ,create_time) values(9,'Coco5','2022-04-30');
```

### 3.6 MyCat管理及监控

#### 3.6.1 MyCat原理

![image-20220817162352684](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817162352684.png)

在MyCat中，当执行一条SQL语句时，MyCat需要进行SQL解析、分片分析、路由分析、读写分离分析 等操作，最终经过一系列的分析决定将当前的SQL语句到底路由到那几个(或哪一个)节点数据库，数据 库将数据执行完毕后，如果有返回的结果，则将结果返回给MyCat，最终还需要在MyCat中进行结果合 并、聚合处理、排序处理、分页处理等操作，最终再将结果返回给客户端。

而在MyCat的使用过程中，MyCat官方也提供了一个管理监控平台MyCat-Web（MyCat-eye）。 Mycat-web 是 Mycat 可视化运维的管理和监控平台，弥补了 Mycat 在监控上的空白。帮 Mycat 分担统计任务和配置管理任务。Mycat-web 引入了 ZooKeeper 作为配置中心，可以管理多个节 点。Mycat-web 主要管理和监控 Mycat 的流量、连接、活动线程和内存等，具备 IP 白名单、邮 件告警等模块，还可以统计 SQL 并分析慢 SQL 和高频 SQL 等。为优化 SQL 提供依据。

#### 3.6.2 MyCat管理

Mycat默认开通2个端口，可以在server.xml中进行修改。

- 8066 数据访问端口，即进行 DML 和 DDL 操作。
- 9066 数据库管理端口，即 mycat 服务管理控制功能，用于管理mycat的整个集群状态

连接MyCat的管理控制台：

```shell
mysql -h 192.168.200.210 -p 9066 -uroot -p123456
```

| 命令              | 含义                        |
| ----------------- | --------------------------- |
| show @@help       | 查看Mycat管理工具帮助文档   |
| show @@version    | 查看Mycat的版本             |
| reload @@config   | 重新加载Mycat的配置文件     |
| show @@datasource | 查看Mycat的数据源信息       |
| show @@datanode   | 查看MyCat现有的分片节点信息 |
| show @@threadpool | 查看Mycat的线程池信息       |
| show @@sql        | 查看执行的SQL               |
| show @@sql.sum    | 查看执行的SQL统计           |

#### 3.6.3 MyCat-eye

##### 3.6.3.1 介绍

Mycat-web(Mycat-eye)是对mycat-server提供监控服务，功能不局限于对mycat-server使 用。他通过JDBC连接对Mycat、Mysql监控，监控远程服务器(目前仅限于linux系统)的cpu、内 存、网络、磁盘。

Mycat-eye运行过程中需要依赖zookeeper，因此需要先安装zookeeper。

##### 3.6.3.2 安装

1.  zookeeper安装
2.  Mycat-web安装

##### 3.6.3.3 访问

[http://192.168.200.210:8082/mycat](http://192.168.200.210:8082/mycat)

![image-20220817163016804](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163016804.png)

##### 3.6.3.4 配置

1. 开启MyCat的实时统计功能(server.xml)

```xml
<property name="useSqlStat">1</property> <!-- 1为开启实时统计、0为关闭 -->
```

2. 在Mycat监控界面配置服务地址

![image-20220817163137823](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163137823.png)

##### 3.6.3.5 测试

配置好了之后，我们可以通过MyCat执行一系列的增删改查的测试，然后过一段时间之后，打开 mycat-eye的管理界面，查看mycat-eye监控到的数据信息。

A. 性能监控

![image-20220817163213960](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163213960.png)

B. 物理节点

![image-20220817163242055](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163242055.png)

C. SQL统计

![image-20220817163301974](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163301974.png)

D. SQL表分析

![image-20220817163326202](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163326202.png)

E. SQL监控

![image-20220817163342769](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163342769.png)

F. 高频SQL

![image-20220817163402920](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163402920.png)

## 4.读写分离

### 4.1 介绍

读写分离,简单地说是把对数据库的读和写操作分开,以对应不同的数据库服务器。主数据库提供写操 作，从数据库提供读操作，这样能有效地减轻单台数据库的压力。 通过MyCat即可轻易实现上述功能，不仅可以支持MySQL，也可以支持Oracle和SQL Server。

![image-20220817163449786](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163449786.png)

### 4.2 一主一从

#### 4.2.1 原理

MySQL的主从复制，是基于二进制日志（binlog）实现的。

![image-20220817163524597](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163524597.png)

#### 4.2.2 准备

| 主机            | 角色   | 用户名 | 密码 |
| --------------- | ------ | ------ | ---- |
| 192.168.200.211 | master | root   | 1234 |
| 192.168.200.212 | slave  | root   | 1234 |

### 4.3 一主一从读写分离

MyCat控制后台数据库的读写分离和负载均衡由schema.xml文件datahost标签的balance属性控 制。

#### 4.3.1 schema.xml配置

```xml
<!-- 配置逻辑库 -->
<schema name="ITCAST_RW" checkSQLschema="true" sqlMaxLimit="100" dataNode="dn7">
</schema>
<dataNode name="dn7" dataHost="dhost7" database="itcast" />
<dataHost name="dhost7" maxCon="1000" minCon="10" balance="1" writeType="0"
dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="master1" url="jdbc:mysql://192.168.200.211:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" >
		<readHost host="slave1" url="jdbc:mysql://192.168.200.212:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" />
	</writeHost>
</dataHost>
```

上述配置的具体关联对应情况如下：

![image-20220817163905565](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817163905565.png)

writeHost代表的是写操作对应的数据库，readHost代表的是读操作对应的数据库。 所以我们要想 实现读写分离，就得配置writeHost关联的是主库，readHost关联的是从库。 

而仅仅配置好了writeHost以及readHost还不能完成读写分离，还需要配置一个非常重要的负责均衡 的参数 balance，取值有4种，具体含义如下：

| 参数值 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| 0      | 不开启读写分离机制 , 所有读操作都发送到当前可用的writeHost上 |
| 1      | 全部的readHost 与 备用的writeHost 都参与select 语句的负载均衡（主要针对 于双主双从模式） |
| 2      | 所有的读写操作都随机在writeHost , readHost上分发             |
| 3      | 所有的读请求随机分发到writeHost对应的readHost上执行, writeHost不负担读压 力 |

所以，在一主一从模式的读写分离中，balance配置1或3都是可以完成读写分离的。

#### 4.3.2 server.xml配置

配置root用户可以访问SHOPPING、ITCAST 以及 ITCAST_RW逻辑库。

```xml
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">SHOPPING,ITCAST,ITCAST_RW</property>
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="true">
		<schema name="DB01" dml="0110" >
			<table name="TB_ORDER" dml="1110"></table>
		</schema>
	</privileges>
	-->
</user>
```

#### 4.3.3 测试

配置完毕MyCat后，重新启动MyCat。

```shell
bin/mycat stop
bin/mycat start
```

然后观察，在执行增删改操作时，对应的主库及从库的数据变化。 在执行查询操作时，检查主库及从 库对应的数据变化。

在测试中，我们可以发现当主节点Master宕机之后，业务系统就只能够读，而不能写入数据了。

![image-20220817164203819](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817164203819.png)

那如何解决这个问题呢？这个时候我们就得通过另外一种主从复制结构来解决了，也就是我们接下来讲 解的双主双从。

### 4.4 双主双从

#### 4.4.1 介绍

一个主机 Master1 用于处理所有写请求，它的从机 Slave1 和另一台主机 Master2 还有它的从 机 Slave2 负责所有读请求。当 Master1 主机宕机后，Master2 主机负责写请求，Master1 、 Master2 互为备机。架构图如下:

![image-20220817164254618](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817164254618.png)

#### 4.4.2 准备

我们需要准备5台服务器，具体的服务器及软件安装情况如下：

| 编号 | IP              | 预装软件     | 角色              |
| ---- | --------------- | ------------ | ----------------- |
| 1    | 192.168.200.210 | MyCat、MySQL | MyCat中间件服务器 |
| 2    | 192.168.200.211 | MySQL        | M1                |
| 3    | 192.168.200.212 | MySQL        | S1                |
| 4    | 192.168.200.213 | MySQL        | M2                |
| 5    | 192.168.200.214 | MySQL        | S2                |

> 关闭以上所有服务器的防火墙： 
>
> ​	systemctl stop firewalld 
>
> ​	systemctl disable firewalld

#### 4.4.3 搭建

##### 4.4.3.1 主库配置

1. Master1(192.168.200.211)

![image-20220817164534666](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817164534666.png)

A. 修改配置文件 /etc/my.cnf

```mysql
#mysql 服务ID，保证整个集群环境中唯一，取值范围：1 – 2^32-1，默认为1
server-id=1
#指定同步的数据库
binlog-do-db=db01
binlog-do-db=db02
binlog-do-db=db03
# 在作为从数据库的时候，有写入操作也要更新二进制日志文件
log-slave-updates
```

B. 重启MySQL服务器

```shell
systemctl restart mysqld
```

C. 创建账户并授权

```mysql
#创建itcast用户，并设置密码，该用户可在任意主机连接该MySQL服务
CREATE USER 'itcast'@'%' IDENTIFIED WITH mysql_native_password BY 'Root@123456';
#为 'itcast'@'%' 用户分配主从复制权限
GRANT REPLICATION SLAVE ON *.* TO 'itcast'@'%';
```

通过指令，查看两台主库的二进制日志坐标

```mysql
show master status ;
```

![image-20220817164755157](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817164755157.png)

2. Master2(192.168.200.213)

![image-20220817164818084](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817164818084.png)

A. 修改配置文件 /etc/my.cnf

```mysql
#mysql 服务ID，保证整个集群环境中唯一，取值范围：1 – 2^32-1，默认为1
server-id=3
#指定同步的数据库
binlog-do-db=db01
binlog-do-db=db02
binlog-do-db=db03
# 在作为从数据库的时候，有写入操作也要更新二进制日志文件
log-slave-updates
```

B. 重启MySQL服务器

```shell
systemctl restart mysqld
```

C. 创建账户并授权

```mysql
#创建itcast用户，并设置密码，该用户可在任意主机连接该MySQL服务
CREATE USER 'itcast'@'%' IDENTIFIED WITH mysql_native_password BY 'Root@123456';
#为 'itcast'@'%' 用户分配主从复制权限
GRANT REPLICATION SLAVE ON *.* TO 'itcast'@'%';
```

通过指令，查看两台主库的二进制日志坐标

```mysql
show master status ;
```

![image-20220817164946082](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817164946082.png)

#### 4.4.3.2 从库配置

1.  Slave1(192.168.200.212)

![image-20220817165023895](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817165023895.png)

A. 修改配置文件 /etc/my.cnf

```shell
#mysql 服务ID，保证整个集群环境中唯一，取值范围：1 – 232-1，默认为1
server-id=2
```

B. 重新启动MySQL服务器

```shell
systemctl restart mysqld
```

2. Slave2(192.168.200.214)

![image-20220817165132377](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817165132377.png)

A. 修改配置文件 /etc/my.cnf

```mysql
#mysql 服务ID，保证整个集群环境中唯一，取值范围：1 – 232-1，默认为1
server-id=4
```

B. 重新启动MySQL服务器

```shell
systemctl restart mysqld
```

##### 4.4.3.3 从库关联主

1.  两台从库配置关联的主库

![image-20220817165246699](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817165246699.png)



> 需要注意slave1对应的是master1，slave2对应的是master2。

A. 在 slave1(192.168.200.212)上执行

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.200.211', MASTER_USER='itcast',
MASTER_PASSWORD='Root@123456', MASTER_LOG_FILE='binlog.000002',
MASTER_LOG_POS=663;
```

B. 在 slave2(192.168.200.214)上执行

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.200.213', MASTER_USER='itcast',
MASTER_PASSWORD='Root@123456', MASTER_LOG_FILE='binlog.000002',
MASTER_LOG_POS=663;
```

C. 启动两台从库主从复制，查看从库状态

```mysql
start slave;
show slave status \G;
```

![image-20220817165413379](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817165413379.png)

2. 两台主库相互复制

![image-20220817165441819](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817165441819.png)

Master2 复制 Master1，Master1 复制 Master2。

A. 在 Master1(192.168.200.211)上执行

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.200.213', MASTER_USER='itcast',
MASTER_PASSWORD='Root@123456', MASTER_LOG_FILE='binlog.000002',
MASTER_LOG_POS=663;
```

B. 在 Master2(192.168.200.213)上执行

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.200.211', MASTER_USER='itcast',
MASTER_PASSWORD='Root@123456', MASTER_LOG_FILE='binlog.000002',
MASTER_LOG_POS=663;
```

C. 启动两台从库主从复制，查看从库状态

```mysql
start slave;
show slave status \G;
```

![image-20220817165611967](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817165611967.png)

经过上述的三步配置之后，双主双从的复制结构就已经搭建完成了。 接下来，我们可以来测试验证一 下。

#### 4.4.4 测试

分别在两台主库Master1、Master2上执行DDL、DML语句，查看涉及到的数据库服务器的数据同步情 况。

```mysql
create database db01;
use db01;
create table tb_user(
	id int(11) not null primary key ,
	name varchar(50) not null,
	sex varchar(1)
)engine=innodb default charset=utf8mb4;
insert into tb_user(id,name,sex) values(1,'Tom','1');
insert into tb_user(id,name,sex) values(2,'Trigger','0');
insert into tb_user(id,name,sex) values(3,'Dawn','1');
insert into tb_user(id,name,sex) values(4,'Jack Ma','1');
insert into tb_user(id,name,sex) values(5,'Coco','0');
insert into tb_user(id,name,sex) values(6,'Jerry','1');
```

- 在Master1中执行DML、DDL操作，看看数据是否可以同步到另外的三台数据库中。
- 在Master2中执行DML、DDL操作，看看数据是否可以同步到另外的三台数据库中。

完成了上述双主双从的结构搭建之后，接下来，我们再来看看如何完成这种双主双从的读写分离。

### 4.5 双主双从读写分离

#### 4.5.1 配置

MyCat控制后台数据库的读写分离和负载均衡由schema.xml文件datahost标签的balance属性控 制，通过writeType及switchType来完成失败自动切换的。

1. schema.xml

配置逻辑库：

```xml
<schema name="ITCAST_RW2" checkSQLschema="true" sqlMaxLimit="100" dataNode="dn7">
</schema>
```

配置数据节点：

```xml
<dataNode name="dn7" dataHost="dhost7" database="db01" />
```

配置节点主机：

```xml
<dataHost name="dhost7" maxCon="1000" minCon="10" balance="1" writeType="0"
dbType="mysql" dbDriver="jdbc" switchType="1" slaveThreshold="100">
	<heartbeat>select user()</heartbeat>
	<writeHost host="master1" url="jdbc:mysql://192.168.200.211:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" >
		<readHost host="slave1" url="jdbc:mysql://192.168.200.212:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" />
	</writeHost>
	<writeHost host="master2" url="jdbc:mysql://192.168.200.213:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" >
		<readHost host="slave2" url="jdbc:mysql://192.168.200.214:3306?
useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf8"
user="root" password="1234" />
	</writeHost>
</dataHost>
```

具体的对应情况如下：

![image-20220817170002128](https://raw.githubusercontent.com/makev5/picture/main/img/image-20220817170002128.png)

属性说明：

> balance="1"
>
> ​		 代表全部的 readHost 与 stand by writeHost 参与 select 语句的负载均衡，简 单的说，当双主双从模式(M1->S1，M2->S2，并且 M1 与 M2 互为主备)，正常情况下， M2,S1,S2 都参与 select 语句的负载均衡
>
> writeType 
>
> ​		0 : 写操作都转发到第1台writeHost, writeHost1挂了, 会切换到writeHost2上; 
>
> ​		1 : 所有的写操作都随机地发送到配置的writeHost上 ;
>
>  switchType 
>
> ​		-1 : 不自动切换 
>
> ​		1 : 自动切换

2. user.xml

配置root用户也可以访问到逻辑库 ITCAST_RW2。

```xml
<user name="root" defaultAccount="true">
	<property name="password">123456</property>
	<property name="schemas">SHOPPING,ITCAST,ITCAST_RW2</property>
	<!-- 表级 DML 权限设置 -->
	<!--
	<privileges check="true">
		<schema name="DB01" dml="0110" >
			<table name="TB_ORDER" dml="1110"></table>
		</schema>
	</privileges>
	-->
</user>
```

#### 4.5.2 测试

登录MyCat，测试查询及更新操作，判定是否能够进行读写分离，以及读写分离的策略是否正确。

当主库挂掉一个之后，是否能够自动切换。

# 数据类型

## 整型

| 类型名称      | 取值范围                                  | 大小    |
| ------------- | ----------------------------------------- | ------- |
| TINYINT       | -128〜127                                 | 1个字节 |
| SMALLINT      | -32768〜32767                             | 2个宇节 |
| MEDIUMINT     | -8388608〜8388607                         | 3个字节 |
| INT (INTEGHR) | -2147483648〜2147483647                   | 4个字节 |
| BIGINT        | -9223372036854775808〜9223372036854775807 | 8个字节 |

无符号在数据类型后加 unsigned 关键字。

## 浮点型

| 类型名称            | 说明               | 存储需求   |
| ------------------- | ------------------ | ---------- |
| FLOAT               | 单精度浮点数       | 4 个字节   |
| DOUBLE              | 双精度浮点数       | 8 个字节   |
| DECIMAL (M, D)，DEC | 压缩的“严格”定点数 | M+2 个字节 |

## 日期和时间

| 类型名称  | 日期格式            | 日期范围                                          | 存储需求 |
| --------- | ------------------- | ------------------------------------------------- | -------- |
| YEAR      | YYYY                | 1901 ~ 2155                                       | 1 个字节 |
| TIME      | HH:MM:SS            | -838:59:59 ~ 838:59:59                            | 3 个字节 |
| DATE      | YYYY-MM-DD          | 1000-01-01 ~ 9999-12-3                            | 3 个字节 |
| DATETIME  | YYYY-MM-DD HH:MM:SS | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59         | 8 个字节 |
| TIMESTAMP | YYYY-MM-DD HH:MM:SS | 1980-01-01 00:00:01 UTC ~ 2040-01-19 03:14:07 UTC | 4 个字节 |

## 字符串

| 类型名称   | 说明                                         | 存储需求                                                   |
| ---------- | -------------------------------------------- | ---------------------------------------------------------- |
| CHAR(M)    | 固定长度非二进制字符串                       | M 字节，1<=M<=255                                          |
| VARCHAR(M) | 变长非二进制字符串                           | L+1字节，在此，L< = M和 1<=M<=255                          |
| TINYTEXT   | 非常小的非二进制字符串                       | L+1字节，在此，L<2^8                                       |
| TEXT       | 小的非二进制字符串                           | L+2字节，在此，L<2^16                                      |
| MEDIUMTEXT | 中等大小的非二进制字符串                     | L+3字节，在此，L<2^24                                      |
| LONGTEXT   | 大的非二进制字符串                           | L+4字节，在此，L<2^32                                      |
| ENUM       | 枚举类型，只能有一个枚举字符串值             | 1或2个字节，取决于枚举值的数目 (最大值为65535)             |
| SET        | 一个设置，字符串对象可以有零个或 多个SET成员 | 1、2、3、4或8个字节，取决于集合 成员的数量（最多64个成员） |

## 二进制类型

| 类型名称       | 说明                 | 存储需求               |
| -------------- | -------------------- | ---------------------- |
| BIT(M)         | 位字段类型           | 大约 (M+7)/8 字节      |
| BINARY(M)      | 固定长度二进制字符串 | M 字节                 |
| VARBINARY (M)  | 可变长度二进制字符串 | M+1 字节               |
| TINYBLOB (M)   | 非常小的BLOB         | L+1 字节，在此，L<2^8  |
| BLOB (M)       | 小 BLOB              | L+2 字节，在此，L<2^16 |
| MEDIUMBLOB (M) | 中等大小的BLOB       | L+3 字节，在此，L<2^24 |
| LONGBLOB (M)   | 非常大的BLOB         | L+4 字节，在此，L<2^32 |

# 权限一览表

> 具体权限的作用详见[官方文档](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html "官方文档")

GRANT 和 REVOKE 允许的静态权限

| Privilege                                                    | Grant Table Column           | Context                               |
| :----------------------------------------------------------- | :--------------------------- | :------------------------------------ |
| [`ALL [PRIVILEGES]`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_all) | Synonym for “all privileges” | Server administration                 |
| [`ALTER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_alter) | `Alter_priv`                 | Tables                                |
| [`ALTER ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_alter-routine) | `Alter_routine_priv`         | Stored routines                       |
| [`CREATE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create) | `Create_priv`                | Databases, tables, or indexes         |
| [`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-role) | `Create_role_priv`           | Server administration                 |
| [`CREATE ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-routine) | `Create_routine_priv`        | Stored routines                       |
| [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-tablespace) | `Create_tablespace_priv`     | Server administration                 |
| [`CREATE TEMPORARY TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-temporary-tables) | `Create_tmp_table_priv`      | Tables                                |
| [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-user) | `Create_user_priv`           | Server administration                 |
| [`CREATE VIEW`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-view) | `Create_view_priv`           | Views                                 |
| [`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_delete) | `Delete_priv`                | Tables                                |
| [`DROP`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop) | `Drop_priv`                  | Databases, tables, or views           |
| [`DROP ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop-role) | `Drop_role_priv`             | Server administration                 |
| [`EVENT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_event) | `Event_priv`                 | Databases                             |
| [`EXECUTE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_execute) | `Execute_priv`               | Stored routines                       |
| [`FILE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_file) | `File_priv`                  | File access on server host            |
| [`GRANT OPTION`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_grant-option) | `Grant_priv`                 | Databases, tables, or stored routines |
| [`INDEX`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_index) | `Index_priv`                 | Tables                                |
| [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_insert) | `Insert_priv`                | Tables or columns                     |
| [`LOCK TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_lock-tables) | `Lock_tables_priv`           | Databases                             |
| [`PROCESS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_process) | `Process_priv`               | Server administration                 |
| [`PROXY`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_proxy) | See `proxies_priv` table     | Server administration                 |
| [`REFERENCES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_references) | `References_priv`            | Databases or tables                   |
| [`RELOAD`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_reload) | `Reload_priv`                | Server administration                 |
| [`REPLICATION CLIENT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-client) | `Repl_client_priv`           | Server administration                 |
| [`REPLICATION SLAVE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-slave) | `Repl_slave_priv`            | Server administration                 |
| [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_select) | `Select_priv`                | Tables or columns                     |
| [`SHOW DATABASES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-databases) | `Show_db_priv`               | Server administration                 |
| [`SHOW VIEW`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-view) | `Show_view_priv`             | Views                                 |
| [`SHUTDOWN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_shutdown) | `Shutdown_priv`              | Server administration                 |
| [`SUPER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_super) | `Super_priv`                 | Server administration                 |
| [`TRIGGER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_trigger) | `Trigger_priv`               | Tables                                |
| [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_update) | `Update_priv`                | Tables or columns                     |
| [`USAGE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_usage) | Synonym for “no privileges”  | Server administration                 |

GRANT 和 REVOKE 允许的动态权限

| Privilege                                                    | Context                                           |
| :----------------------------------------------------------- | :------------------------------------------------ |
| [`APPLICATION_PASSWORD_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_application-password-admin) | Dual password administration                      |
| [`AUDIT_ABORT_EXEMPT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_audit-abort-exempt) | Allow queries blocked by audit log filter         |
| [`AUDIT_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_audit-admin) | Audit log administration                          |
| [`AUTHENTICATION_POLICY_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_authentication-policy-admin) | Authentication administration                     |
| [`BACKUP_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_backup-admin) | Backup administration                             |
| [`BINLOG_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_binlog-admin) | Backup and Replication administration             |
| [`BINLOG_ENCRYPTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_binlog-encryption-admin) | Backup and Replication administration             |
| [`CLONE_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_clone-admin) | Clone administration                              |
| [`CONNECTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_connection-admin) | Server administration                             |
| [`ENCRYPTION_KEY_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_encryption-key-admin) | Server administration                             |
| [`FIREWALL_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_firewall-admin) | Firewall administration                           |
| [`FIREWALL_EXEMPT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_firewall-exempt) | Firewall administration                           |
| [`FIREWALL_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_firewall-user) | Firewall administration                           |
| [`FLUSH_OPTIMIZER_COSTS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-optimizer-costs) | Server administration                             |
| [`FLUSH_STATUS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-status) | Server administration                             |
| [`FLUSH_TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-tables) | Server administration                             |
| [`FLUSH_USER_RESOURCES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-user-resources) | Server administration                             |
| [`GROUP_REPLICATION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_group-replication-admin) | Replication administration                        |
| [`GROUP_REPLICATION_STREAM`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_group-replication-stream) | Replication administration                        |
| [`INNODB_REDO_LOG_ARCHIVE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_innodb-redo-log-archive) | Redo log archiving administration                 |
| [`NDB_STORED_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_ndb-stored-user) | NDB Cluster                                       |
| [`PASSWORDLESS_USER_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_passwordless-user-admin) | Authentication administration                     |
| [`PERSIST_RO_VARIABLES_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_persist-ro-variables-admin) | Server administration                             |
| [`REPLICATION_APPLIER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-applier) | `PRIVILEGE_CHECKS_USER` for a replication channel |
| [`REPLICATION_SLAVE_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-slave-admin) | Replication administration                        |
| [`RESOURCE_GROUP_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_resource-group-admin) | Resource group administration                     |
| [`RESOURCE_GROUP_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_resource-group-user) | Resource group administration                     |
| [`ROLE_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_role-admin) | Server administration                             |
| [`SESSION_VARIABLES_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_session-variables-admin) | Server administration                             |
| [`SET_USER_ID`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_set-user-id) | Server administration                             |
| [`SHOW_ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-routine) | Server administration                             |
| [`SYSTEM_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_system-user) | Server administration                             |
| [`SYSTEM_VARIABLES_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_system-variables-admin) | Server administration                             |
| [`TABLE_ENCRYPTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_table-encryption-admin) | Server administration                             |
| [`VERSION_TOKEN_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_version-token-admin) | Server administration                             |
| [`XA_RECOVER_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_xa-recover-admin) | Server administration                             |

# 图形化界面工具

- Workbench(免费): http://dev.mysql.com/downloads/workbench/
- navicat(收费，试用版30天): https://www.navicat.com/en/download/navicat-for-mysql
- Sequel Pro(开源免费，仅支持Mac OS): http://www.sequelpro.com/
- HeidiSQL(免费): http://www.heidisql.com/
- phpMyAdmin(免费): https://www.phpmyadmin.net/
- SQLyog: https://sqlyog.en.softonic.com/

# 安装

# 小技巧

1. 在SQL语句之后加上`\G`会将结果的表格形式转换成行文本形式
2. 查看Mysql数据库占用空间：
```mysql
SELECT table_schema "Database Name"
     , SUM(data_length + index_length) / (1024 * 1024) "Database Size in MB"
FROM information_schema.TABLES
GROUP BY table_schema;
```