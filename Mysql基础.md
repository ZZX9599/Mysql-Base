# Mysql基础

# 1:通用语法及分类

## 1.1:概述

- DDL: 数据定义语言，用来定义数据库对象（数据库、表、字段）
- DML: 数据管理语言，用来对数据库表中的数据进行增删改
- DQL: 数据查询语言，用来查询数据库中表的记录
- DCL: 数据控制语言，用来创建数据库用户、控制数据库的控制权限

## 1.2:DDL

DDL：数据定义语言，可以针对数据库，也可以针对表

主要就是用来定义数据库或者操作数据库，或者定义表结构，操作表结构的

## 1.2:DML

DML：数据管理语言

主要就是用来对表里的数据进行管理的，对表数据的增删改等操作



注意事项

- 字符串和日期类型数据应该包含在引号中
- 插入的数据大小应该在字段的规定范围内

## 1.3:DQL

DQL：数据查询语言

主要是对数据库表的数据进行查询

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

### 1:基础查询

查询多个字段：

```
SELECT 字段1, 字段2, 字段3, ... FROM 表名

SELECT * FROM 表名
```

设置别名：

```
SELECT 字段1 [ AS 别名1 ], 字段2 [ AS 别名2 ] ... FROM 表名

SELECT 字段1 [ 别名1 ], 字段2 [ 别名2 ] ... FROM 表名
```

去除重复记录：

```
SELECT DISTINCT 字段列表 FROM 表名
```

### 2:条件查询

语法：

```
SELECT 字段列表 FROM 表名 WHERE 条件列表
```

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

### 3:聚合查询

聚合查询需要使用聚合函数

常见聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

语法：

```
SELECT 聚合函数(字段列表) FROM 表名
```

例：

```
SELECT count(id) from employee where workaddress = "广东省"
```

### 4:分组查询

语法：

```
SELECT 字段 FROM TABLE GROUP BY 分组字段名 [ HAVING 分组后的过滤条件 ]
```

### 5:面试题

where 和 having 的区别：

- where是在分组之前进行过滤，不满足where条件不参与分组
- having是分组后对结果进行过滤
- where不能对聚合函数进行判断，而having可以



例子：

```mysql
-- 根据性别分组，统计男性和女性数量（只显示分组数量，不显示哪个是男哪个是女）
select count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性数量
select gender, count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性的平均年龄
select gender, avg(age) from employee group by gender;
-- 年龄小于45，并根据工作地址分组并显示数量
select workaddress, count(*) from employee where age < 45 group by workaddress;
-- 年龄小于45，并根据工作地址分组，分组后获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from employee where age < 45 group by workaddress having address_count >= 3;
```

注意事项

- 执行顺序：where > 聚合函数 > having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

### 6:排序查询

语法：

```
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2
```

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

注意事项

如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序

### 7:分页查询

语法：

```
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;
```

例子：

```mysql
-- 查询第一页数据，展示10条
SELECT * FROM employee LIMIT 0, 10;
-- 查询第二页
SELECT * FROM employee LIMIT 10, 10;
```

注意事项

- 起始索引从0开始，起始索引 = （查询页码 - 1） * 每页显示记录数
- 分页查询是数据库的方言，不同数据库有不同实现，MySQL是LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写 LIMIT 10

小结参数：

参数一：(pageNum-1)*pageSize

参数二：pageSize

### 8:DQL执行顺序

```sql
SELECT * FROM table_name WHERE 字段=? ORDER BY 字段 ASC/DESC LIMIT (?,?)
```

FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT

## 1.4:DCL

主要是用来创建数据库用户、控制数据库的控制权限

# 2:函数

## 2.1:概述

- 字符串函数
- 数值函数
- 日期函数
- 流程函数

## 2.2:字符串函数

常用函数：

| 函数                       | 功能                                                  |
| -------------------------- | ----------------------------------------------------- |
| CONCAT(s1, s2, ..., sn)    | 字符串拼接，将s1, s2, ..., sn拼接成一个字符串         |
| LOWER(str)                 | 将字符串全部转为小写                                  |
| UPPER(str)                 | 将字符串全部转为大写                                  |
| LPAD(str, n, pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符串 |
| RPAD(str, n, pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符串 |
| TRIM(str)                  | 去掉字符串头部和尾部的空格                            |
| SUBSTRING(str, start, len) | 返回从字符串str从start位置起的len个长度的字符串       |

使用示例：

```mysql
-- 拼接
SELECT CONCAT('Hello', 'World');  HelloWorld
-- 小写
SELECT LOWER('Hello');  hello
-- 大写
SELECT UPPER('Hello');  HELLO
-- 左填充
SELECT LPAD('01', 5, '-');  ---01
-- 右填充
SELECT RPAD('01', 5, '-');  01---
-- 去除空格
SELECT TRIM(' Hello World ');  Hello World
-- 切片（起始索引为1）
SELECT SUBSTRING('Hello World', 1, 5);  Hello
```

## 2.3:数值函数

常见函数：

| 函数        | 功能                             |
| ----------- | -------------------------------- |
| CEIL(x)     | 向上取整                         |
| FLOOR(x)    | 向下取整                         |
| MOD(x, y)   | 返回x/y的模                      |
| RAND()      | 返回0~1内的随机数                |
| ROUND(x, y) | 求参数x的四舍五入值，保留y位小数 |

## 2.4:日期函数

常用函数：

| 函数                     | 功能                                              |
| ------------------------ | ------------------------------------------------- |
| CURDATE()                | 返回当前日期                                      |
| CURTIME()                | 返回当前时间                                      |
| NOW()                    | 返回当前日期和时间                                |
| YEAR(date)               | 获取指定date的年份                                |
| MONTH(date)              | 获取指定date的月份                                |
| DAY(date)                | 获取指定date的日期                                |
| DATE_ADD(date, interval) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1, date2)   | 返回起始时间date1和结束时间date2之间的天数        |

例子：

```mysql
-- DATE_ADD
SELECT CURDATE()
SELECT DATE_ADD(NOW(), INTERVAL 70 YEAR);
```

## 2.5:流程函数(了解)

常用函数：

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
from employee;
select
	name,
	(case workaddress when '北京市' then '一线城市' when '上海市' then '一线城市' else '二线城市' end) as '工作地址'
from employee;
```

# 3:约束

## 3.1:分类

| 约束     | 描述                                                     | 关键字      |
| -------- | -------------------------------------------------------- | ----------- |
| 非空约束 | 限制该字段的数据不能为null                               | NOT NULL    |
| 唯一约束 | 保证该字段的所有数据都是唯一、不重复的                   | UNIQUE      |
| 主键约束 | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY |
| 默认约束 | 保存数据如果未指定该字段的值，则采用默认值               | DEFAULT     |
| 外键约束 | 用来让两张图的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

约束是作用于表中的字段上的，可以再创建表/修改表的时候添加约束

## 3.2:常用约束

| 约束条件 | 关键字         |
| -------- | -------------- |
| 主键     | PRIMARY KEY    |
| 自动增长 | AUTO_INCREMENT |
| 不为空   | NOT NULL       |
| 唯一     | UNIQUE         |
| 默认值   | DEFAULT        |

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

## 3.3:外键约束

添加外键【了解】

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

删除外键【了解】

```
ALTER TABLE 表名 DROP FOREIGN KEY 外键名
```

实际上在开发中我们很少添加外键，只需要按照这个逻辑来做就可以，但是并不会真正添加

## 3.4:外键删除/更新行为

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与RESTRICT一致） |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与NO ACTION一致） |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录 |
| SET NULL    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（要求该外键允许为null） |
| SET DEFAULT | 父表有变更时，子表将外键设为一个默认值（Innodb不支持）       |



更改删除/更新行为【了解】

```
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名) ON UPDATE 行为 ON DELETE 行为;
```



# 4:多表查询

## 4.1:多表关系

- 一对多（多对一）
- 多对多
- 一对一

### 1:一对多

案例：部门与员工
关系：一个部门对应多个员工，一个员工对应一个部门
实现：在多的一方建立外键，指向一的一方的主键

也就是员工表需要一个其他的字段来指向部门的主键

### 2:多对多

案例：学生与课程
关系：一个学生可以选多门课程，一门课程也可以供多个学生选修
实现：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

### 3:一对一

案例：用户与用户详情
关系：一对一关系，多用于单表的列太多的情况，可以用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
实现：在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的（UNIQUE）

## 4.2:几种常见查询

### 1:笛卡儿积查询

合并查询（笛卡尔积，会展示所有组合结果）

```
select * from employee, dept;
```

笛卡尔积：两个集合A集合和B集合的所有组合情况

注意：我们在多表查询时，需要消除无效的笛卡尔积

消除无效笛卡尔积：

```
select * from employee, dept where employee.dept = dept.id
```



### 2:内连接查询

内连接查询的是两张表交集的部分

隐式内连接：

```
SELECT 字段列表 FROM 表1, 表2 WHERE 条件 ...;
```

显式内连接：

```
SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ...;
```

显式性能比隐式高

例子：

```mysql
-- 查询员工姓名，及关联的部门的名称

-- 隐式内连接
select e.name, d.name from employee as e, dept as d where e.dept = d.id;
-- 显式内连接【性能更高】
select e.name, d.name from employee as e inner join dept as d on e.dept = d.id;
```

### 3:外连接查询

左外连接：

查询左表的所有数据，以及两张表交集部分数据

```
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ...;
```

相当于查询表1的所有数据，包含表1和表2交集部分数据



右外连接：

查询右表所有数据，以及两张表交集部分数据

```
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ...;
```

例子：

```mysql
-- 左外连接
select e.*, d.name from employee as e left join dept as d on e.dept = d.id;
```

### 4:自连接查询

当前表与自身的连接查询，自连接必须使用表别名

语法：

```
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ...;
```

自连接查询，可以是内连接查询，也可以是外连接查询



例子：

```mysql
-- 查询员工及其所属领导的名字
select a.name, b.name from employee a, employee b where a.manager = b.id;
-- 没有领导的也查询出来
select a.name, b.name from employee a left join employee b on a.manager = b.id;
```

## 4.3:联合查询

把多次查询的结果合并，形成一个新的查询集

语法：

```mysql
SELECT 字段列表 FROM 表A ...
UNION [ALL]
SELECT 字段列表 FROM 表B ...
```

注意事项

- UNION ALL 会有重复结果，UNION 不会
- 联合查询比使用or效率高，不会使索引失效
- Union：对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序
- Union All：对两个结果集进行并集操作，包括重复行，不进行排序

## 4.4:子查询

SQL语句中嵌套SELECT语句，称谓嵌套查询，又称子查询

```
SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2)
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

### 1:标量子查询

子查询返回的结果是单个值（数字、字符串、日期等）
常用操作符：- < > > >= < <=

例子：

```mysql
-- 需求1：查询销售部所有员工的ID

-- 先查询销售部的ID
select id from dept where name = '销售部';
-- 根据销售部部门ID，查询员工信息
select * from employee where dept = 4;
-- 合并（子查询）
select * from employee where dept = (select id from dept where name = '销售部');

-- 需求二：查询在ZZX入职之后的员工信息

-- 查询ZZX入职时间
select entrydate from employee where name = 'ZZX'
-- 查询在某个时间入职之后的员工信息
select * from employee where entrydate > ?
-- 合并
select * from employee where entrydate > (select entrydate from employee where name = 'xxx');
```

### 2:列子查询

返回的结果是一列（可以是多行）

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
-- 查询销售部和市场部的所有员工信息
select * from employee where dept in (select id from dept where name = '销售部' or name = '市场部');

-- 查询比财务部所有人工资都高的员工信息
select * from employee where salary > all(select salary from employee where dept = (select id from dept where name = '财务部'));

-- 查询比研发部任意一人工资高的员工信息
select * from employee where salary > any (select salary from employee where dept = (select id from dept where name = '研发部'));
```

### 3:行子查询

返回的结果是一行（可以是多列）

常用操作符：=, <, >, IN, NOT IN

例子：

```mysql
-- 查询与xxx的薪资及直属领导相同的员工信息
select * from employee where (salary, manager) = (12500, 1);
select * from employee where (salary, manager) = (select salary, manager from employee where name = 'xxx');
```

### 4:表子查询

返回的结果是多行多列

常用操作符：IN

例子：

```mysql
-- 查询与xxx1，xxx2的职位和薪资相同的员工
select * from employee where (job, salary) in (select job, salary from employee where name = 'xxx1' or name = 'xxx2');

-- 查询入职日期是2006-01-01之后的员工，及其部门信息
select e.*, d.* from (select * from employee where entrydate > '2006-01-01') as e left join dept as d on e.dept = d.id;
```

# 5:事务

## 5.1:概述

事务:是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系 统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败

就比如: 张三给李四转账1000块钱，张三银行账户的钱减少1000，而李四银行账户的钱要增加 1000。 这一组操作就必须在一个事务的范围内，要么都成功，要么都失败



## 1.2:控制事务方式

### 1:方式一

查看/设置事务提交方式

```sql
-- 查看事务提交方式【mysql默认为1-->即自动提交】
SELECT @@AUTOCOMMIT;
-- 设置事务提交方式，1为自动提交，0为手动提交，该设置只对当前会话有效
SET @@AUTOCOMMIT = 0;
```

提交事务

```sql
COMMIT
```

回滚事务

```sql
ROLLBACK
```

注意：上述的这种方式，我们是修改了事务的自动提交行为，把默认的自动提交修改为了手动提交，此时我们执行的DML语句都不会提交, 需要手动的执行commit进行提交



### 2:方式二

开启事务

```sql
START TRANSACTION 或 BEGIN 
```

提交事务

```sql
COMMIT
```

回滚事务

```sql
ROLLBACK
```



## 1.3:事务特性

- 原子性(Atomicity)：事务是不可分割的最小操作单元，要么全部成功，要么全部失败
- 一致性(Consistency)：事务完成时，必须使所有数据都保持一致状态
- 隔离性(Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性(Durability)：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的



解释：

原子性：假设一个业务转账，一个增加1000，一个减少1000，我们把他们放在同一个事务里

面，则要么增加1000和减少1000的操作同时成功或者失败。不可能出现一个成功一个失败

一致性：还是上面的转账例子，一致性就是他们的余额之和是不会改变的

隔离性：多个事务在相互执行的时候是不受干扰的

持久性：操作结束之后，对数据库的数据改变是持久的



## 1.4:并发事务

| 问题       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 脏读       | 一个事务读到另一个事务还没提交的数据                         |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同             |
| 幻读       | 一个事务按照条件查询数据时，没有对应的数据行，但是再插入数据时，又发现这行数据已经存在 |



## 1.5:并发问题

### 1:脏读

![image-20220901164817873]( https://zzx-note.oss-cn-beijing.aliyuncs.com/mysql/image-20220901164817873.png)

当事务的隔离级别是 Read Uncommitted 的时候会出现这个问题

### 2:不可重复读

正常情况下，一个事务内，执行相同的select命令，查到的结果应该是一样的

![image-20220901165048581]( https://zzx-note.oss-cn-beijing.aliyuncs.com/mysql/image-20220901165048581.png)

当事务隔离级别是 Read Committed 的时候，两次读之间出现了另外的事务更新了数据并且提交了事务，这个时候两次查询的数据不一致，这就是不可重复读

### 3:幻读

在一个事务内，正常情况是我之前查询没有数据，我在这个事务没执行插入前，也应该是没数据

![image-20220901165211229]( https://zzx-note.oss-cn-beijing.aliyuncs.com/mysql/image-20220901165211229.png)

## 1.6:并发事务隔离级别

| 隔离级别                   | 脏读 | 不可重复读 | 幻读 |
| -------------------------- | ---- | ---------- | ---- |
| Read uncommitted           | √    | √          | √    |
| Read committed(Oracle默认) | ×    | √          | √    |
| Repeatable Read(Mysql默认) | ×    | ×          | √    |
| Serializable               | ×    | ×          | ×    |

- √表示在当前隔离级别下该问题会出现
- Serializable 性能最低；Read uncommitted 性能最高，数据安全性最差

查看事务隔离级别【了解】

```
SELECT @@TRANSACTION_ISOLATION;
```



设置事务隔离级别【了解】

```
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE };
```

SESSION 是会话级别，表示只针对当前会话有效，GLOBAL 表示对所有会话有效