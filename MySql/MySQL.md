## 数据库基本概念

### 特点

> 可移植
>
> 端口号：3306
>
> 建一个数据库，就是在系统下建一个目录；建表就是在对应目录下建立文件
>
> 存储介质：磁盘，内存

### 主流数据库

- SQL Sever：微软的产品，.Net程序员的最爱，中大型项目。 
- Oracle：甲骨文产品，适合大型项目，复杂的业务逻辑，并发一般来说不如MySQL。 
- MySQL：世界上最受欢迎的数据库，属于甲骨文，并发性好，不适合做复杂的业务。主要用在电商，SNS，论坛。对简单的SQL处理效果好。 
- PostgreSQL：加州大学伯克利分校计算机系开发的关系型数据库，不管是私用，商用，还是学术研究使用，可 以免费使用，修改和分发。
-  SQLite：是一款轻型的数据库，是遵守ACID的关系型数据库管理系统，它包含在一个相对小的C库中。它的设计目标是嵌入式的，而且目前已经在很多嵌入式产品中使用了它，它占用资源非常的低，在嵌入式设备中，可能只需要几百K的内存就够了。 
- H2：是一个用Java开发的嵌入式数据库，它本身只是一个类库，可以直接嵌入到应用项目中。

### 服务器，数据库，表关系

- 所谓安装数据库服务器，只是在机器上安装了一个数据库管理系统程序，这个管理程序可以管理多个数据库， 一般开发人员会针对每一个应用创建一个数据库。 
- 为保存应用中实体的数据，一般会在数据库中创建多个表，以保存程序中实体的数据。 
- 数据库服务器、数据库和表的关系如下：![image-20230220110856217](%E5%9B%BE%E7%89%87/MySQL/image-20230220110856217.png)

### SQL语言

![image-20230220105827532](%E5%9B%BE%E7%89%87/MySQL/image-20230220105827532.png)

### 数据库表逻辑

![image-20230220110811897](%E5%9B%BE%E7%89%87/MySQL/image-20230220110811897.png)

## 库基本操作

进入数据库

~~~ mysql
mysql -u... -h... -p...
mysql -uroot -h127.0.0.1 -p******
~~~

显示数据库列表

~~~ mysql
show databases;
~~~

创建数据库

~~~ mysql
create database 数据库名;
~~~

字符集和校验规则

~~~mysql
create database 数据库名 charset=utf8 collate utf8_general_ci

charset: 设置字符集
collate：校验规则

 -- 查看系统默认字符集和校验规则
show variables like 'character_set_database';字符集
show variables like 'collation_database';//校验规则

表的规则会默认数据库的规则，就近优先

-- 改变编码规则
alter database 库名 charset=gbk; //不建议
~~~

删除数据库

~~~mysql
drop database [if exists] 库名;

[]判断规则
if exists 如果存在
~~~

显示数据库/表的创建语句

~~~mysql
show create table 表名;
show create database 库名名;

显示创建数据库/表时，进行的约束，别名，设置等
~~~

选择数据库

~~~ mysql
use 数据库名;
~~~

显示数据库访问链接状态

~~~mysql
show processlist;
~~~

## 表基本操作

~~~mysql
create table users (
	id int,
	name varchar(20) comment '用户名',
	password char(32) comment '密码是32位的md5值',
	birthday date comment '生日'
	) character set utf8 engine MyISAM; -- engine 引擎类别
~~~

创建数据库的表

~~~ mysql
crate table (
    列1 类型 comment '别名'，
    列2 类型 comment '别名'，
    ......
    列n 类型
)character set 字符集 collate 校验规则 engine 存储引擎; 
-- 如果没有设置编码规则，使用库的规则，遵循就近原则。
~~~

显示表结构

~~~mysql
desc 表名; -- 指定表内容

show tables; -- 显示所有表
~~~

![image-20230220162732069](%E5%9B%BE%E7%89%87/MySQL/image-20230220162732069.png)

修改表

~~~mysql
-- 添加 列
alter table 表名 add(新增列名 类型); -- 默认为最后列
alter table 表名 add(新增列名 类型 after 列名); -- 指定插入到某列之后

-- 修改 类型
alter table 表名 modify(修改的列 修改后的类型); -- 修改类型

-- 删除 列
alter table 表名 drop(删除的列);

-- 修改 列名/表名
alter table 表名 rename to 新表名; -- to可以省略
alter table 表名 change 列名 新列名 类型; -- 修改类型，且新字段需要完整定义
~~~

删除表

```mysql
drop table 表名;
```

## 数据类型

数据类型分类

![image-20230220165330424](%E5%9B%BE%E7%89%87/MySQL/image-20230220165330424.png)

数值类型

![image-20230220165406093](%E5%9B%BE%E7%89%87/MySQL/image-20230220165406093.png)

> 数据类型本质是一种约束，所有数值必须满足对应的类型条件，如果不满足，数据库不会执行插入操作直接终止，该数据将不会被记录
>
> 注：不同的数据库对于数据类型越界的反应不一样，有的会报错，有的会更改数据后执行

tinyint类型

> 数值越界测试：
>
> ~~~mysql
> mysql> create table tt1(num tinyint);
> Query OK, 0 rows affected (0.02 sec)
> 
> mysql> insert into tt1 values(1);
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into tt1 values(128); -- 越界插入，报错
> ERROR 1264 (22003): Out of range value for column 'num' at row 1
> 
> mysql> select * from tt1;
> +------+
> | num |
> +------+
> | 1 |
> +------+
> 1 row in set (0.00 sec)
> ~~~
>
> 说明:
>
> - 在MySQL中，整型可以指定是有符号的和无符号的，默认是有符号的。 
> - 可以通过UNSIGNED来说明某个字段是无符号的 
> - 无符号案例
>
> ~~~mysql
> mysql> create table tt2(num tinyint unsigned);
> 
> mysql> insert into tt2 values(-1); -- 无符号，范围是： 0 - 255
> ERROR 1264 (22003): Out of range value for column 'num' at row 1
> 
> mysql> insert into tt2 values(255);
> Query OK, 1 row affected (0.02 sec)
> 
> mysql> select * from tt2;
> +------+
> | num |
> +------+
> | 255 |
> +------+
> 1 row in set (0.00 sec)
> ~~~
>
> 

bit类型

> ~~~mysql
> bit[(M)] : 位字段类型。M表示每个值的位数，范围从1到64。如果M被忽略，默认为1。
> ~~~
>
> ~~~MYSQL
> mysql> create table tt4 ( id int, a bit(8));
> Query OK, 0 rows affected (0.01 sec)
> 
> mysql> insert into tt4 values(10, 10);
> Query OK, 1 row affected (0.01 sec)
> 
> mysql> select * from tt4; #发现很怪异的现象，a的数据10没有出现
> +------+------+
> | id | a |
> +------+------+
> | 10 | |
> +------+------+
> 1 row in set (0.00 sec)
> ~~~
>
> bit使用的注意事项：
>
> - bit字段在显示时，是按照ASCII码对应的值显示。
>
> ~~~MYSQL
> mysql> insert into tt4 values(65, 65);
> 
> mysql> select * from tt4;
> +------+------+
> | id | a |
> +------+------+
> | 10 | |
> | 65 | A |
> +------+------+
> 
> ~~~
>
> - 如果我们有这样的值，只存放0或1，这时可以定义bit(1)。这样可以节省空间。
>
> ~~~mysql
> mysql> create table tt5(gender bit(1));
> 
> mysql> insert into tt5 values(0);
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into tt5 values(1);
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into tt5 values(2); -- 当插入2时，已经越界了
> ERROR 1406 (22001): Data too long for column 'gender' at row 1
> ~~~
>
> 

float

> 语法：
>
> ~~~mysql
> float[(m, d)] [unsigned] : M指定显示长度，d指定小数位数，占用空间4个字节
> ~~~
>
> 案例：
>
> 小数：float(4,2)表示的范围是-99.99 ~ 99.99，MySQL在保存值时会进行四舍五入。
>
> ~~~mysql
> mysql> create table tt6(id int, salary float(4,2));
> Query OK, 0 rows affected (0.01 sec)
> 
> mysql> insert into tt6 values(100, -99.99);
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into tt6 values(101, -99.991); #多的这一点被拿掉了
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> select * from tt6;
> +------+--------+
> | id | salary |
> +------+--------+
> | 100 | -99.99 |
> | 101 | -99.99 |
> +------+--------+
> 2 rows in set (0.00 sec)
> ~~~
>
> 如果定义的是float(4,2) unsigned 这时，因为把它指定为无符号的数，范围是 0 ~ 99.99
>
> ~~~mysql
> mysql> create table tt7(id int, salary float(4,2) unsigned);
> Query OK, 0 rows affected (0.01 sec)
> 
> mysql> insert into tt7 values(100, -0.1);
> Query OK, 1 row affected, 1 warning (0.00 sec)
> 
> mysql> show warnings;
> +---------+------+-------------------------------------------------+
> | Level | Code | Message |
> +---------+------+-------------------------------------------------+
> | Warning | 1264 | Out of range value for column 'salary' at row 1 |
> +---------+------+-------------------------------------------------+
> 1 row in set (0.00 sec)
> 
> mysql> insert into tt7 values(100, -0);
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into tt7 values(100, 99.99);
> Query OK, 1 row affected (0.00 sec)
> ~~~
>
> 

decimal

> 语法：
>
> ~~~mysql
> decimal(m, d) [unsigned] : 定点数m指定长度，d表示小数点的位数
> ~~~
>
> - decimal(5,2) 表示的范围是 -999.99 ~ 999.99
>
> - decimal(5,2) unsigned 表示的范围 0 ~ 999.99
>
> - decimal和float很像，但是有区别: float和decimal表示的精度不一样
>
>     ~~~mysql
>     mysql> create table tt8 ( id int, salary float(10,8), salary2 decimal(10,8));
>                                 
>     mysql> insert into tt8 values(100,23.12345612, 23.12345612);
>     Query OK, 1 row affected (0.00 sec)
>                                 
>     mysql> select * from tt8;
>     +------+-------------+-------------+
>     | id | salary | salary2 |
>     +------+-------------+-------------+
>     | 100 | 23.12345695 | 23.12345612 | 
>     # 发现decimal的精度更准确，因此如果我们希望某个数据表示高精度，选择decimal
>     +------+-------------+-------------+
>     ~~~
>
> **说明：**float表示的精度大约是7位。 decimal整数最大位数m为65。支持小数最大位数d是30。如果d被省略，默认为0.如果m被省略，默认是10。

char

> 语法：
>
> ~~~mysql
> char(L): 固定长度字符串，L是可以存储的长度，单位为字符，最大长度值可以为255
> 
> L代表可以存储 <= L个字符， 直接开辟L字符的空间
> 
> 字符不等于字节
> ~~~
>
> 案例：
>
> ~~~mysql
> mysql> create table tt9(id int, name char(2));
> Query OK, 0 rows affected (0.00 sec)
> 
> mysql> insert into tt9 values(100, 'ab');
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into tt9 values(101, '中国');
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> select * from tt9;
> +------+--------+
> | id | name |
> +------+--------+
> | 100 | ab |
> | 101 | 中国 |
> +------+--------+
> ~~~
>
> 说明：
>
> char(2) 表示可以存放两个字符，可以是字母或汉字，但是不能超过2个， 最多只能是255
>
> ~~~mysql
> mysql> create table tt10(id int ,name char(256));
> ERROR 1074 (42000): Column length too big for column 'name' (max = 255); use BLOB or TEXT instead
> ~~~
>
> 

varchar

> 语法： 
>
> ~~~mysql
> varchar(L): 可变长度字符串，L表示字符长度，最大长度65535个字节
> 
> L代表可以存储 <= L个字符， 用多少开辟多少
> 
> 字符不等于字节
> ~~~
>
> 案例：
>
> ~~~mysql
> mysql> create table tt10(id int ,name varchar(6)); --表示这里可以存放6个字符
> 
> mysql> insert into tt10 values(100, 'hello');
> mysql> insert into tt10 values(100, '我爱你，中国');
> 
> mysql> select * from tt10;
> +------+--------------------+
> | id | name |
> +------+--------------------+
> | 100 | hello |
> | 100 | 我爱你，中国 |
> +------+--------------------+
> ~~~
>
> 说明： 
>
> 关于varchar(len),len到底是多大，这个len值，和表的编码密切相关： 
>
> - varchar长度可以指定为0到65535之间的值，但是有1 - 3 个字节用于记录数据大小，所以说有**效字节数是 65532**。 
>
> - 当我们的表的编码是utf8时，varchar(n)的参数n最大值是65532/3=21844（因为utf中，一个字符占用3个字 节），如果编码是gbk，varchar(n)的参数n最大是65532/2=32766（因为gbk中，一个字符占用2字节）。
>
>     ~~~mysql
>     mysql> create table tt11(name varchar(21845))charset=utf8; -- 验证了utf8确实是不能超过21844
>     ERROR 1118 (42000): Row size too large. The maximum row size for the used table type,not counting BLOBs, is 65535. You have to change some columns to TEXT or BLOBs
>                                 
>     mysql> create table tt11(name varchar(21844)) charset=utf8;
>     Query OK, 0 rows affected (0.01 sec)
>     ~~~
>
> 

varchar 与 char

> ![image-20230220193613446](%E5%9B%BE%E7%89%87/MySQL/image-20230220193613446.png)
>
> ~~~
> 4是字符个数， 3是在对应编码规则下，一个字符需要的字节个数
> ~~~
>
> 如何选择定长或变长字符串？ 
>
> - 如果数据确定长度都一样，就使用定长（char），比如：身份证，手机号，md5 
> - 如果数据长度有变化,就使用变长(varchar), 比如：名字，地址，但是你要保证最长的能存的进去。 
> - 定长的磁盘空间比较浪费，但是效率高。 
> - 变长的磁盘空间比较节省，但是效率低。 
> - 定长的意义是，直接开辟好对应的空间 
> - 变长的意义是，在不超过自定义范围的情况下，用多少，开辟多少。

日期和时间类型

> 常用的日期有如下三个： 
>
> - date :日期 'yyyy-mm-dd' ，占用三字节 
> - datetime 时间日期格式 'yyyy-mm-dd HH:ii:ss' 表示范围从 1000 到 9999 ，占用八字节 
> - timestamp ：时间戳，从1970年开始的 yyyy-mm-dd HH:ii:ss 格式和 datetime 完全一致，占用四字节
>
> 案例：
>
> ~~~mysql
> //创建表
> mysql> create table birthday (t1 date, t2 datetime, t3 timestamp);
> Query OK, 0 rows affected (0.01 sec)
> 
> //插入数据：
> mysql> insert into birthday(t1,t2) values('1997-7-1','2008-8-8 12:1:1'); -- 插入两种时间
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> select * from birthday;
> +------------+---------------------+---------------------+
> | t1 | t2 | t3 |
> +------------+---------------------+---------------------+
> | 1997-07-01 | 2008-08-08 12:01:01 | 2017-11-12 18:28:55 | -- 添加数据时，时间戳自动补上当前时间
> +------------+---------------------+---------------------+
> 
> //更新数据：
> mysql> update birthday set t1='2000-1-1';
> Query OK, 1 row affected (0.00 sec)
> Rows matched: 1 Changed: 1 Warnings: 0
> 
> mysql> select * from birthday;
> +------------+---------------------+---------------------+
> | t1 | t2 | t3 |
> +------------+---------------------+---------------------+
> | 2000-01-01 | 2008-08-08 12:01:01 | 2017-11-12 18:32:09 | -- 更新数据，时间戳会更新成当前时间
> +------------+---------------------+---------------------+
> ~~~
>
> 

enum和set

> 语法： 
>
> - enum：枚举，“单选”类型； 
>
>     enum('选项1','选项2','选项3',...); 
>
> 该设定只是提供了若干个选项的值，最终一个单元格中，实际只存储了其中一个值；而且出于效率考虑，这些值实际 存储的是“数字”，因为这些选项的每个选项值依次**对应如下数字：1,2,3,....最多65535个；**当我们添加枚举值时，也可 以添加对应的数字编号。
>
> - set：集合，“多选”类型； 
>
>     set('选项值1','选项值2','选项值3', ...); 
>
> 该设定只是提供了若干个选项的值，最终一个单元格中，设计可存储了其中任意多个值；而且出于效率考虑，这些值 实际存储的是“数字”，因为这些选项的每个选项值依次**对应如下数字：1,2,4,8,16,32，.... 最多64个。**
>
> 说明：不建议在添加枚举值，集合值的时候采用数字的方式，因为不利于阅读。
>
> 案例：有一个调查表votes，需要调查人的喜好， 比如（登山，游泳，篮球，武术）中去选择（可以多选），（男，女）（单选）
>
> ~~~mysql
>mysql> create table votes(
>     username varchar(30),
>     hobby set('登山','游泳','篮球','武术'), -- 注意：使用数字标识每个爱好的时候，想想Linux权限，采用比特位位置来个set中的爱好对应起来
>     gender enum('男','女')); -- 注意：使用数字标识的时候，就是正常的数组下标
> Query OK, 0 rows affected (0.02 sec)
> ~~~
> 
> 插入数据
> 
>~~~mysql
> insert into votes values('雷锋', '登山,武术', '男');
>insert into votes values('Juse','登山,武术',2);
> 
> select * from votes where gender=2;
> +----------+---------------+--------+
> | username | hobby | gender |
> +----------+---------------+--------+
> | Juse | 登山,武术 |女 |
> +----------+---------------+--------+
> 
> ~~~
> 
> 有如下数据，想查找所有喜欢登山的人：
>
> ~~~mysql
>+-----------+---------------+--------+
> | username | hobby | gender |
> +-----------+---------------+--------+
> | 雷锋 | 登山,武术 | 男 |
> | Juse | 登山,武术 | 女 |
> | LiLei | 登山 | 男 |
> | LiLei | 篮球 | 男 |
> | HanMeiMei | 游泳 | 女 |
> +-----------+---------------+--------+
> ~~~
> 
> 使用如下查询语句：
>
> ~~~mysql
>mysql> select * from votes where hobby='登山';
> +----------+--------+--------+
> | username | hobby | gender |
> +----------+--------+--------+
> | LiLei | 登山 | 男 |
> +----------+--------+--------+
> ~~~
> 
> 不能查询出所有，有爱好为登山的人。
>
> **集合查询使用find_ in_ set函数：**
>
> find_in_set(sub,str_list) ：如果 sub 在 str_list 中，则返回下标；如果不在，返回0； str_list 用逗号分 隔的字符串。
>
> ~~~mysql
>mysql> select find_in_set('a', 'a,b,c');
> +---------------------------+
> | find_in_set('a', 'a,b,c') |
> +---------------------------+
> | 1 |
> +---------------------------+
> mysql> select find_in_set('d', 'a,b,c');
> +---------------------------+
> | find_in_set('d', 'a,b,c') |
> +---------------------------+
> | 0 |
> +---------------------------+
> 
> ~~~
> 
> 查询爱好登山的人：
>
> ~~~mysql
>mysql> select * from votes where find_in_set('登山', hobby);
> +----------+---------------+--------+
> | username | hobby | gender |
> +----------+---------------+--------+
> | 雷锋 | 登山,武术 | 男 |
> | Juse | 登山,武术 | 女 |
> | LiLei | 登山 | 男 |
> +----------+---------------+--------+
> ~~~
> 
> 

总结：

> 数据类型本质上是一种约束，如果插入的数据不满足类型要求，则会直接终止SQL语句。

## 表的约束

真正约束字段的是数据类型，但是数据类型约束很单一，需要有一些额外的约束，更好的保证数据的合法性，从业务逻辑角度保证数据的正确性。比如有一个字段是email，要求是唯一的。 

表的约束很多，这里主要介绍如下几个： null/not null,default, comment, zerofill，primary key， auto_increment，unique key 。

空属性

> - 两个值：null（默认的）和not null(不为空) 
>
> - 数据库默认字段基本都是字段为空，但是实际开发时，尽可能保证字段不为空，因为数据为空没办法参与运算。
>
>     ~~~mysql
>     mysql> select null;
>     +------+
>     | NULL |
>     +------+
>     | NULL |
>     +------+
>     1 row in set (0.00 sec)
>             
>     mysql> select 1+null;
>     +--------+
>     | 1+null |
>     +--------+
>     | NULL |
>     +--------+
>     1 row in set (0.00 sec)
>             
>     ~~~
>
> 案例： 创建一个班级表，包含班级名和班级所在的教室。 
>
> 站在正常的业务逻辑中：
>
> - 如果班级没有名字，你不知道你在哪个班级 
> -  如果教室名字可以为空，就不知道在哪上课
>
> 所以我们在设计数据库表的时候，一定要在表中进行限制，满足上面条件的数据就不能插入到表中。这就是“约束”。
>
> ~~~mysql
> mysql> create table myclass(
>     class_name varchar(20) not null,
>     class_room varchar(10) not null);
> Query OK, 0 rows affected (0.02 sec)
> 
> mysql> desc myclass;
> +------------+-------------+------+-----+---------+-------+
> | Field | Type | Null | Key | Default | Extra |
> +------------+-------------+------+-----+---------+-------+
> | class_name | varchar(20) | NO | | NULL | |
> | class_room | varchar(10) | NO | | NULL | |
> +------------+-------------+------+-----+---------+-------+
> 
> //插入数据时,没有给教室数据插入失败：
> mysql> insert into myclass(class_name) values('class1');
> ERROR 1364 (HY000): Field 'class_room' doesn't have a default value
> 
> ~~~
>
> 

默认值

> 默认值：某一种数据会经常性的出现某个具体的值，可以在一开始就指定好，在需要真实数据的时候，用户可以选择 性的使用默认值。
>
> ~~~mysql
> mysql> create table tt10 (
>     name varchar(20) not null,
>     age tinyint unsigned default 0,
>     sex char(2) default '男');
> Query OK, 0 rows affected (0.00 sec)
> 
> mysql> desc tt10;
> +-------+---------------------+------+-----+---------+-------+
> | Field | Type | Null | Key | Default | Extra |
> +-------+---------------------+------+-----+---------+-------+
> | name | varchar(20) | NO | | NULL | |
> | age | tinyint(3) unsigned | YES | | 0 | |
> | sex | char(2) | YES | | 男 | |
> +-------+---------------------+------+-----+---------+-------+
> ~~~
>
> 默认值的生效：数据在插入的时候不给该字段赋值，就使用默认值
>
> ~~~mysql
> mysql> insert into tt10(name) values('zhangsan');
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> select * from tt10;
> +----------+------+------+
> | name | age | sex |
> +----------+------+------+
> | zhangsan | 0 | 男 |
> +----------+------+------+
> -- 注意：只有设置了default的列，才可以在插入值的时候，对列进行省略
> ~~~
>
> 

列描述

> 列描述：comment，没有实际含义，专门用来描述字段，会根据表创建语句保存，用来给程序员或DBA来进行了解，使用show create查询。
>
> ~~~mysql
> mysql> create table tt12 (
> -> name varchar(20) not null comment '姓名',
> -> age tinyint unsigned default 0 comment '年龄',
> -> sex char(2) default '男' comment '性别'
> -> );
> -- 注意：not null和defalut一般不需要同时出现，因为default本身有默认值，不会为空
> ~~~
>
> 通过desc查看不到注释信息：
>
> ~~~mysql
> mysql> desc tt12;
> +-------+---------------------+------+-----+---------+-------+
> | Field | Type | Null | Key | Default | Extra |
> +-------+---------------------+------+-----+---------+-------+
> | name | varchar(20) | NO | | NULL | |
> | age | tinyint(3) unsigned | YES | | 0 | |
> | sex | char(2) | YES | | 男 | |
> +-------+---------------------+------+-----+---------+-------+
> ~~~
>
> 通过show可以看到：
>
> ~~~mysql
> mysql> show create table tt12\G
> *************************** 1. row ***************************
> Table: tt12
> Create Table: CREATE TABLE `tt12` (
> `name` varchar(20) NOT NULL COMMENT '姓名',
> `age` tinyint(3) unsigned DEFAULT '0' COMMENT '年龄',
> `sex` char(2) DEFAULT '男' COMMENT '性别'
> ) ENGINE=MyISAM DEFAULT CHARSET=gbk
> 1 row in set (0.00 sec)
> 
> ~~~
>
> 

zerofill

> 刚开始学习数据库时，很多人对数字类型后面的长度很迷茫。通过show看看tt3表的建表语句：
>
> ~~~mysql
> mysql> show create table tt3\G
> ***************** 1. row *****************
> Table: tt3
> Create Table: CREATE TABLE `tt3` (
> `a` int(10) unsigned DEFAULT NULL,
> `b` int(10) unsigned DEFAULT NULL
> ) ENGINE=MyISAM DEFAULT CHARSET=gbk
> 1 row in set (0.00 sec)
> ~~~
>
> 可以看到int(10),这个代表什么意思呢？整型不是4字节码？这个10又代表什么呢？其实没有zerofill这个属性，括号内 的数字是毫无意义的。a和b列就是前面插入的数据，如下：
>
> ~~~mysql
> mysql> insert into tt3 values(1,2);
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> select * from tt3;
> +------+------+
> | a | b |
> +------+------+
> | 1 | 2 |
> +------+------+
> ~~~
>
> 但是对列添加了zerofill属性后，显示的结果就有所不同了。修改tt3表的属性：
>
> ```mysql
> mysql> alter table tt3 change a a int(5) unsigned zerofill;
> mysql> show create table tt3\G
> *************************** 1. row ***************************
> Table: tt3
> Create Table: CREATE TABLE `tt3` (
> `a` int(5) unsigned zerofill DEFAULT NULL, --具有了zerofill
> `b` int(10) unsigned DEFAULT NULL
> ) ENGINE=MyISAM DEFAULT CHARSET=gbk
> 1 row in set (0.00 sec)
> ```
>
> 对a列添加了zerofill属性，再进行查找，返回如下结果：
>
> ~~~mysql
> mysql> select * from tt3;
> +-------+------+
> | a | b |
> +-------+------+
> | 00001 | 2 |
> +-------+------+
> ~~~
>
> 这次可以看到a的值由原来的1变成00001，这就是zerofill属性的作用，如果宽度小于设定的宽度（这里设置的是 5），自动填充0。要注意的是，这**只是最后显示的结果，在MySQL中实际存储的还是1**。为什么是这样呢？我们可以 用hex函数来证明。
>
> ~~~mysql
> mysql> select a, hex(a) from tt3;
> +-------+--------+
> | a | hex(a) |
> +-------+--------+
> | 00001 | 1 |
> +-------+--------+
> ~~~
>
> 可以看出数据库内部存储的还是1,00001只是设置了zerofill属性后的一种格式化输出而已。

主键

> 主键：primary key用来唯一的约束该字段里面的数据，不能重复，不能为空，一张表中最多只能有一个主键；主键所在的列通常是整数类型。
>
> 案例：
>
> - 创建表的时候直接在字段上指定主键
>
>     ~~~mysql
>     mysql> create table tt13 (
>         id int unsigned primary key comment '学号不能为空',
>         name varchar(20) not null);
>     Query OK, 0 rows affected (0.00 sec)
>     
>     mysql> desc tt13;
>     +-------+------------------+------+-----+---------+-------+
>     | Field | Type | Null | Key | Default | Extra |
>     +-------+------------------+------+-----+---------+-------+
>     | id | int(10) unsigned | NO | PRI | NULL | | <= key 中 pri表示该字段是主键
>     | name | varchar(20) | NO | | NULL | |
>     +-------+------------------+------+-----+---------+-------+
>     ~~~
>
> - 主键约束：主键对应的字段中不能重复，一旦重复，操作失败。
>
>     ~~~mysql
>     mysql> insert into tt13 values(1, 'aaa');
>     Query OK, 1 row affected (0.00 sec)
>
>     mysql> insert into tt13 values(1, 'aaa');
>     ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
>     ~~~
>
> - 当表创建好以后但是没有主键的时候，可以再次追加主键
>
>     ~~~mysql
>     alter table 表名 add primary key(字段列表)
>     -- 追加字段不能有重复值
>     ~~~
>
> - 删除主键
>
>     ~~~mysql
>     alter table 表名 drop primary key;
>
>     mysql> alter table tt13 drop primary key;
>     mysql> desc tt13;
>     +-------+------------------+------+-----+---------+-------+
>     | Field | Type | Null | Key | Default | Extra |
>     +-------+------------------+------+-----+---------+-------+
>     | id | int(10) unsigned | NO | | NULL | |
>     | name | varchar(20) | NO | | NULL | |
>     +-------+------------------+------+-----+---------+-------+
>     ~~~
>
> - 复合主键
>
>     在创建表的时候，在所有字段之后，使用primary key(主键字段列表)来创建主键，如果有多个字段作为主键， 可以使用复合主键。
>
>     ~~~mysql
>     mysql> create table tt14(
>         id int unsigned,
>         course char(10) comment '课程代码',
>         score tinyint unsigned default 60 comment '成绩',
>         primary key(id, course) -- id和course为复合主键
>     	);
>     Query OK, 0 rows affected (0.01 sec)
>             
>     mysql> desc tt14;
>     +--------+---------------------+------+-----+---------+-------+
>     | Field | Type | Null | Key | Default | Extra |
>     +--------+---------------------+------+-----+---------+-------+
>     | id | int(10) unsigned | NO | PRI | 0 | | <= 这两列合成主键
>     | course | char(10) | NO | PRI | | |
>     | score | tinyint(3) unsigned | YES | | 60 | |
>     +--------+---------------------+------+-----+---------+-------+
>             
>     mysql> insert into tt14 (id,course)values(1, '123');
>     Query OK, 1 row affected (0.02 sec)
>             
>     mysql> insert into tt14 (id,course)values(1, '123');
>     ERROR 1062 (23000): Duplicate entry '1-123' for key 'PRIMARY' -- 主键冲突
>     ~~~
>
> 

自增长

> auto_increment：当对应的字段，不给值，会自动的被系统触发，系统会从当前字段中已经有的最大值+1操作（不太准确），得到一个新的不同的值。通常和主键搭配使用，作为逻辑主键。 
>
> 自增长的特点: 
>
> - 任何一个字段要做自增长，前提是本身是一个**索引**（key一栏有值） 
> - 自增长字段必须是整数 
> - 一张表最多**只能有一个自增长**
>
> 案例：
>
> ~~~mysql
> mysql> create table tt21(
>     id int unsigned primary key auto_increment,
>     name varchar(10) not null default ''
> 	);
> 	
> mysql> insert into tt21(name) values('a');
> mysql> insert into tt21(name) values('b');
> 
> mysql> select * from tt21;
> +----+------+
> | id | name |
> +----+------+
> | 1 | a |
> | 2 | b |
> +----+------+
> ~~~
>
> 在插入后获取上次插入的 AUTO_INCREMENT 的值（批量插入获取的是第一个值）
>
> ~~~mysql
> mysql > select last_insert_id();
> +------------------+
> | last_insert_id() |
> +------------------+
> | 2 |
> +------------------+
> ~~~
>
> **索引：**
>
> 在关系数据库中，索引是一种单独的、物理的对数据库表中一列或多列的值进行排序的一种存储结构，它是某个表中 一列或若干列值的集合和相应的指向表中物理标识这些值的数据页的逻辑指针清单。索引的作用相当于图书的目录， 可以根据目录中的页码快速找到所需的内容。 
>
> 索引提供指向存储在表的指定列中的数据值的指针，然后根据您指定的排序顺序对这些指针排序。数据库使用索引以找到特定值，然后顺指针找到包含该值的行。这样可以使对应于表的SQL语句执行得更快，可快速访问数据库表中的特定信息。

唯一键

> 一张表中有往往有很多字段需要唯一性，数据不能重复，但是一张表中只能有一个主键：唯一键就可以解决表中有多 个字段需要唯一性约束的问题。
>
> 唯一键的本质和主键差不多，**唯一键允许为空，而且可以多个为空，空字段不做唯一性比较**。 
>
> 关于唯一键和主键的区别： 我们可以简单理解成，主键更多的是标识唯一性的。而唯一键更多的是保证在业务上，不要和别的信息出现重复。乍 一听好像没啥区别，我们举一个例子
>
> ~~~
> 假设一个场景(当然，具体可能并不是这样，仅仅为了帮助大家理解)
> 比如在公司，我们需要一个员工管理系统，系统中有一个员工表，员工表中有两列信息，一个身份证号码，一个是员工工号，
> 我们可以选择身份号码作为主键。
> 而我们设计员工工号的时候，需要一种约束：而所有的员工工号都不能重复。
> 具体指的是在公司的业务上不能重复，我们设计表的时候，需要这个约束，那么就可以将员工工号设计成为唯一键。
> 一般而言，我们建议将主键设计成为和当前业务无关的字段，这样，当业务调整的时候，我们可以尽量不会对主键做过大的调整。
> 
> 员工必须有身份证号，可以没有业务，有业务必须唯一
> ~~~
>
> 案例：
>
> ~~~mysql
> mysql> create table student (
> -> id char(10) unique comment '学号，不能重复，但可以为空',
> -> name varchar(10)
> -> );
> Query OK, 0 rows affected (0.01 sec)
> 
> mysql> insert into student(id, name) values('01', 'aaa');
> Query OK, 1 row affected (0.00 sec)
> 
> mysql> insert into student(id, name) values('01', 'bbb'); -- 唯一约束不能重复
> ERROR 1062 (23000): Duplicate entry '01' for key 'id'
> mysql> insert into student(id, name) values(null, 'bbb'); -- 但可以为空
> Query OK, 1 row affected (0.00 sec)
> mysql> select * from student;
> +------+------+
> | id | name |
> +------+------+
> | 01 | aaa |
> | NULL | bbb |
> +------+------+
> ~~~
>
> 

外键

> 外键用于定义**主表和从表**之间的关系：外键约束主要定义在从表上，主表则必须是有主键约束或unique约束。当定义外键后，要求外**键列数据必须在主表的主键列存在或为null**。
>
> 语法：
>
> ~~~mysql
> foreign key (字段名) references 主表(列)
> ~~~
>
> 案例：
>
> ![image-20230221170900241](%E5%9B%BE%E7%89%87/MySQL/image-20230221170900241.png)
>
> 对上面的示意图进行设计：
>
> - 先创建主键表
>
>     ~~~mysql
>     create table myclass (
>     id int primary key,
>     name varchar(30) not null comment'班级名'
>     );
>
> - 再创建从表
>
>     ~~~mysql
>     create table stu (
>     id int primary key,
>     name varchar(30) not null comment '学生名',
>     class_id int,
>     foreign key (class_id) references myclass(id)
>     );
>
> - 正常插入数据
>
>     ~~~mysql
>     mysql> insert into myclass values(10, 'C++大牛班'),(20, 'java大神班');
>     Query OK, 2 rows affected (0.03 sec)
>     Records: 2 Duplicates: 0 Warnings: 0
>     ~~~
>     
>     ~~~mysql
>     mysql> insert into stu values(100, '张三', 10),(101, '李四',20);
>     Query OK, 2 rows affected (0.01 sec)
>     Records: 2 Duplicates: 0 Warnings: 0
>     
> - 插入一个班级号为30的学生，因为没有这个班级，所以插入不成功
>
>     ~~~mysql
>     mysql> insert into stu values(102, 'wangwu',30);
>     ERROR 1452 (23000): Cannot add or update a child row:a foreign key constraint fails (mytest.stu, CONSTRAINT stu_ibfk_1 FOREIGN KEY (class_id) REFERENCES myclass (id))
>     
> - 插入班级id为null，比如来了一个学生，目前还没有分配班级
>
>     ~~~mysql
>     mysql> insert into stu values(102, 'wangwu', null);
>
> - 如何理解外键约束
>
>     ~~~mysql~~~
>     首先我们承认，这个世界是数据很多都是相关性的。
>     理论上，上面的例子，我们不创建外键约束，就正常建立学生表，以及班级表，该有的字段我们都有。
>     此时，在实际使用的时候，可能会出现什么问题？
>     有没有可能插入的学生信息中有具体的班级，但是该班级却没有在班级表中？
>     比如比特只开了比特100班，比特101班，但是在上课的学生里面竟然有比特102班的学生(这个班目前并不存在)，这很明显是有问题的。
>     因为此时两张表在业务上是有相关性的，但是在业务上没有建立约束关系，那么就可能出现问题。
>     解决方案就是通过外键完成的。建立外键的本质其实就是把相关性交给mysql去审核了，提前告诉mysql表之间的约束关系，那么当用户插入不符合业务逻辑的数据的时候，mysql不允许你插入。
>     ~~~
>
>     ![image-20230221172537933](%E5%9B%BE%E7%89%87/MySQL/image-20230221172537933.png)



## 表的增删改查

创建 create

> ~~~mysql
> create table student(
>     id unsigned primary key auto_increment,
>     sn int not null unique,
>     name varchar(20) not null,
>     ......
>     qq varchar(20) unique
> )
> ~~~
>
> 

### 插入 

insert

> ​	单行 + 全列插入 
>
> ~~~MYSQL
> insert into student (id, sn, name, qq) values(1, 202052340112, '李四', '415258758');
> 
> insert into student values(1, 112, '李四', '415258758');
> ~~~
>
> 单行 + 指定列插入
>
> ~~~mysql
> insert into student (id, sn, name) values(1, 112, '李四');
> ~~~
>
> 多行
>
> ~~~mysql
> insert into student (id, sn, name, qq) values
> (1, 112, '李四', '415258758'),
> (2, 113, '张三', '483796323');
> ~~~
>
> 若存在冲突进行更新 **ON DUPLICATE KEY**
>
> ~~~mysql
> insert into student (id, sn, name, qq) values(1, 202052340112, '李四', '415258758') on duplicate key update sn = 112, name = '李四', qq = '4152587589';
> ~~~
>
> ~~~mysql
> INSERT INTO students (id, sn, name) VALUES (100, 10010, '唐大师')
> ON DUPLICATE KEY UPDATE sn = 10010, name = '唐大师';
> Query OK, 2 rows affected (0.47 sec)
> 
> -- 0 row affected: 表中有冲突数据，但冲突数据的值和 update 的值相等
> -- 1 row affected: 表中没有冲突数据，数据被插入
> -- 2 row affected: 表中有冲突数据，并且数据已经被更新
> -- 通过 MySQL 函数获取受到影响的数据行数
> 
> SELECT ROW_COUNT();
> +-------------+
> | ROW_COUNT() |
> +-------------+
> | 2 |
> +-------------+
> 1 row in set (0.00 sec)
> -- ON DUPLICATE KEY 当发生重复key的时候
> 
> ~~~

 替换 replace

> ~~~mysql
> -- 主键 或者 唯一键 没有冲突，则直接插入；
> -- 主键 或者 唯一键 如果冲突，则删除后再插入
> 
> REPLACE INTO students (sn, name) VALUES (20001, '曹阿瞒');
> Query OK, 2 rows affected (0.00 sec)
> 
> -- 1 row affected: 表中没有冲突数据，数据被插入
> -- 2 row affected: 表中有冲突数据，删除后重新插入
> ~~~
>
> 

### 查询 

select

> 案例：
>
> ~~~mysql
> -- 创建表结构
> CREATE TABLE exam_result (
> id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
> name VARCHAR(20) NOT NULL COMMENT '同学姓名',
> chinese float DEFAULT 0.0 COMMENT '语文成绩',
> math float DEFAULT 0.0 COMMENT '数学成绩',
> english float DEFAULT 0.0 COMMENT '英语成绩'
> );
> 
> -- 插入测试数据
> INSERT INTO exam_result (name, chinese, math, english) VALUES
> ('唐三藏', 67, 98, 56),
> ('孙悟空', 87, 78, 77),
> ('猪悟能', 88, 98, 90),
> ('曹孟德', 82, 84, 67),
> ('刘玄德', 55, 85, 45),
> ('孙权', 70, 73, 78),
> ('宋公明', 75, 65, 30);
> Query OK, 7 rows affected (0.00 sec)
> Records: 7 Duplicates: 0 Warnings: 0
> ~~~
>
> 
>
> 全列查询
>
> ~~~mysql
> -- 通常情况下不建议使用 * 进行全列查询
> -- 1. 查询的列越多，意味着需要传输的数据量越大；
> -- 2. 可能会影响到索引的使用。（索引待后面课程讲解）
> 
> SELECT * FROM exam_result;
> +----+-----------+-------+--------+--------+
> | id | name | chinese | math | english |
> +----+-----------+-------+--------+--------+
> | 1 | 唐三藏 | 67 | 98 | 56 |
> | 2 | 孙悟空 | 87 | 78 | 77 |
> | 3 | 猪悟能 | 88 | 98 | 90 |
> | 4 | 曹孟德 | 82 | 84 | 67 |
> | 5 | 刘玄德 | 55 | 85 | 45 |
> | 6 | 孙权 | 70 | 73 | 78 |
> | 7 | 宋公明 | 75 | 65 | 30 |
> +----+-----------+-------+--------+--------+
> 7 rows in set (0.00 sec)
> ~~~
>
> 指定列查询
>
> ~~~mysql
> -- 指定列的顺序不需要按定义表的顺序来
> SELECT id, name, english FROM exam_result;
> +----+-----------+--------+
> | id | name | english |
> +----+-----------+--------+
> | 1 | 唐三藏 | 56 |
> | 2 | 孙悟空 | 77 |
> | 3 | 猪悟能 | 90 |
> | 4 | 曹孟德 | 67 |
> | 5 | 刘玄德 | 45 |
> | 6 | 孙权 | 78 |
> | 7 | 宋公明 | 30 |
> +----+-----------+--------+
> 7 rows in set (0.00 sec)
> 
> ~~~
>
> 查询字段为表达式
>
> ~~~mysql
> -- 表达式不包含字段
> SELECT id, name, 10 FROM exam_result;
> +----+-----------+----+
> | id | name | 10 |
> +----+-----------+----+
> | 1 | 唐三藏 | 10 |
> | 2 | 孙悟空 | 10 |
> | 3 | 猪悟能 | 10 |
> | 4 | 曹孟德 | 10 |
> | 5 | 刘玄德 | 10 |
> | 6 | 孙权 | 10 |
> | 7 | 宋公明 | 10 |
> +----+-----------+----+
> 7 rows in set (0.00 sec)
> ~~~
>
> ~~~mysql
> 
> -- 表达式包含一个字段
> SELECT id, name, english + 10 FROM exam_result;
> +----+-----------+-------------+
> | id | name | english + 10 |
> +----+-----------+-------------+
> | 1 | 唐三藏 | 66 |
> | 2 | 孙悟空 | 87 |
> | 3 | 猪悟能 | 100 |
> | 4 | 曹孟德 | 77 |
> | 5 | 刘玄德 | 55 |
> | 6 | 孙权 | 88 |
> | 7 | 宋公明 | 40 |
> +----+-----------+-------------+
> 7 rows in set (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 表达式包含多个字段
> SELECT id, name, chinese + math + english FROM exam_result;
> +----+-----------+-------------------------+
> | id | name | chinese + math + english |
> +----+-----------+-------------------------+
> | 1 | 唐三藏 | 221 |
> | 2 | 孙悟空 | 242 |
> | 3 | 猪悟能 | 276 |
> | 4 | 曹孟德 | 233 |
> | 5 | 刘玄德 | 185 |
> | 6 | 孙权 | 221 |
> | 7 | 宋公明 | 170 |
> +----+-----------+-------------------------+
> 7 rows in set (0.00 sec)
> 
> -- 
> SELECT id, name, chinese + math + english 总分 FROM exam_result;
> chinese + math + english 起别名为 总分
> ~~~
>
> 为查询结果**指定别名**
>
> ~~~mysql
> SELECT id, name, chinese + math + english 总分 FROM exam_result;
> +----+-----------+--------+
> | id | name | 总分 |
> +----+-----------+--------+
> | 1 | 唐三藏 | 221 |
> | 2 | 孙悟空 | 242 |
> | 3 | 猪悟能 | 276 |
> | 4 | 曹孟德 | 233 |
> | 5 | 刘玄德 | 185 |
> | 6 | 孙权 | 221 |
> | 7 | 宋公明 | 170 |
> +----+-----------+--------+
> 7 rows in set (0.00 sec)
> ~~~
>
> 结果去重 **distinct**
>
> ~~~mysql
> -- 98 分重复了
> SELECT math FROM exam_result;
> +--------+
> | math |
> +--------+
> | 98 |
> | 78 |
> | 98 |
> | 84 |
> | 85 |
> | 73 |
> | 65 |
> +--------+
> 7 rows in set (0.00 sec)
> ~~~
>
> ~~~mysql
> -- 去重结果
> SELECT DISTINCT math FROM exam_result;
> +--------+
> | math |
> +--------+
> | 98 |
> | 78 |
> | 84 |
> | 85 |
> | 73 |
> | 65 |
> +--------+
> 6 rows in set (0.00 sec)
> 
> ~~~
>
> 升序
>
> ~~~mysql
> ~~~
>
> 降序
>
> ~~~mysql
> ~~~
>
> 

条件查询 where

> 比较运算符：
>
> ![image-20230221185754725](%E5%9B%BE%E7%89%87/MySQL/image-20230221185754725.png)
>
> 逻辑运算符：
>
> ![image-20230221185822041](%E5%9B%BE%E7%89%87/MySQL/image-20230221185822041.png)
>
> 案例：
>
> 英语不及格的同学及英语成绩 ( < 60 )
>
> ~~~mysql
> -- 基本比较
> SELECT name, english FROM exam_result WHERE english < 60;
> +-----------+--------+
> | name | english |
> +-----------+--------+
> | 唐三藏 | 56 |
> | 刘玄德 | 45 |
> | 宋公明 | 30 |
> +-----------+--------+
> 3 rows in set (0.01 sec)
> 
> ~~~
>
> 语文成绩在 [80, 90] 分的同学及语文成绩
>
> ~~~mysql
> -- 使用 AND 进行条件连接
> SELECT name, chinese FROM exam_result WHERE chinese >= 80 AND chinese <= 90;
> +-----------+-------+
> | name | chinese |
> +-----------+-------+
> | 孙悟空 | 87 |
> | 猪悟能 | 88 |
> | 曹孟德 | 82 |
> +-----------+-------+
> 3 rows in set (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 使用 BETWEEN ... AND ... 条件
> SELECT name, chinese FROM exam_result WHERE chinese BETWEEN 80 AND 90;
> +-----------+-------+
> | name | chinese |
> +-----------+-------+
> | 孙悟空 | 87 |
> | 猪悟能 | 88 |
> | 曹孟德 | 82 |
> +-----------+-------+
> 3 rows in set (0.00 sec)
> ~~~
>
> 数学成绩是 58 或者 59 或者 98 或者 99 分的同学及数学成绩
>
> ~~~mysql
> -- 使用 OR 进行条件连接
> SELECT name, math FROM exam_result
> WHERE math = 58
> OR math = 59
> OR math = 98
> OR math = 99;
> +-----------+--------+
> | name | math |
> +-----------+--------+
> | 唐三藏 | 98 |
> | 猪悟能 | 98 |
> +-----------+--------+
> 2 rows in set (0.01 sec)
> ~~~
>
> ~~~mysql
> -- 使用 IN 条件
> SELECT name, math FROM exam_result WHERE math IN (58, 59, 98, 99);
> +-----------+--------+
> | name | math |
> +-----------+--------+
> | 唐三藏 | 98 |
> | 猪悟能 | 98 |
> +-----------+--------+
> 2 rows in set (0.00 sec)
> ~~~
>
> 姓孙的同学 及 孙某同学
>
> ~~~mysql
> -- % 匹配任意多个（包括 0 个）任意字符
> SELECT name FROM exam_result WHERE name LIKE '孙%';
> +-----------+
> | name |
> +-----------+
> | 孙悟空 |
> | 孙权 |
> +-----------+
> 2 rows in set (0.00 sec)
> ~~~
>
> ~~~mysql
> -- _ 匹配严格的一个任意字符
> SELECT name FROM exam_result WHERE name LIKE '孙_';
> +--------+
> | name |
> +--------+
> | 孙权 |
> +--------+
> 1 row in set (0.00 sec)
> 
> ~~~
>
> 语文成绩好于英语成绩的同学
>
> ~~~mysql
> 
> -- WHERE 条件中比较运算符两侧都是字段
> SELECT name, chinese, english FROM exam_result WHERE chinese > english;
> +-----------+-------+--------+
> | name | chinese | english |
> +-----------+-------+--------+
> | 唐三藏 | 67 | 56 |
> | 孙悟空 | 87 | 77 |
> | 曹孟德 | 82 | 67 |
> | 刘玄德 | 55 | 45 |
> | 宋公明 | 75 | 30 |
> +-----------+-------+--------+
> 5 rows in set (0.00 sec)
> 
> ~~~
>
> 总分在 200 分以下的同学
>
> ~~~mysql
> -- WHERE 条件中使用表达式
> 
> -- 别名不能用在 WHERE 条件中
> 
> SELECT name, chinese + math + english 总分 FROM exam_result
> WHERE chinese + math + english < 200;
> +-----------+--------+
> | name | 总分 |
> +-----------+--------+
> | 刘玄德 | 185 |
> | 宋公明 | 170 |
> +-----------+--------+
> 2 rows in set (0.00 sec)
> 
> ~~~
>
> ![image-20230221195621173](%E5%9B%BE%E7%89%87/MySQL/image-20230221195621173.png)
>
> 语文成绩 > 80 并且不姓孙的同学
>
> ~~~mysql
> -- AND 与 NOT 的使用
> SELECT name, chinese FROM exam_result
> WHERE chinese > 80 AND name NOT LIKE '孙%';
> +----+-----------+-------+--------+--------+
> | id | name | chinese | math | english |
> +----+-----------+-------+--------+--------+
> | 3 | 猪悟能 | 88 | 98 | 90 |
> | 4 | 曹孟德 | 82 | 84 | 67 |
> +----+-----------+-------+--------+--------+
> 2 rows in set (0.00 sec)
> 
> ~~~
>
> 孙某同学，否则要求总成绩 > 200 并且 语文成绩 < 数学成绩 并且 英语成绩 > 80
>
> ~~~mysql
> -- 综合性查询
> SELECT name, chinese, math, english, chinese + math + english 总分 FROM exam_result WHERE name LIKE '孙_' OR ( chinese + math + english > 200 AND chinese < math AND english > 80);
> +-----------+-------+--------+--------+--------+
> | name | chinese | math | english | 总分 |
> +-----------+-------+--------+--------+--------+
> | 猪悟能 | 88 | 98 | 90 | 276 |
> | 孙权 | 70 | 73 | 78 | 221 |
> +-----------+-------+--------+--------+--------+
> 2 rows in set (0.00 sec)
> ~~~
>
> NULL 的查询
>
> ~~~mysql
> -- 查询 students 表
> +-----+-------+-----------+-------+
> | id | sn | name | qq |
> +-----+-------+-----------+-------+
> | 100 | 10010 | 唐大师 | NULL |
> | 101 | 10001 | 孙悟空 | 11111 |
> | 103 | 20002 | 孙仲谋 | NULL |
> | 104 | 20001 | 曹阿瞒 | NULL |
> +-----+-------+-----------+-------+
> 4 rows in set (0.00 sec)
> 
> -- 查询 qq 号已知的同学姓名
> SELECT name, qq FROM students WHERE qq IS NOT NULL;
> +-----------+-------+
> | name | qq |
> +-----------+-------+
> | 孙悟空 | 11111 |
> +-----------+-------+
> 1 row in set (0.00 sec)
> 
> -- NULL 和 NULL 的比较，= 和 <=> 的区别
> SELECT NULL = NULL, NULL = 1, NULL = 0;
> +-------------+----------+----------+
> | NULL = NULL | NULL = 1 | NULL = 0 |
> +-------------+----------+----------+
> | NULL | NULL | NULL |
> +-------------+----------+----------+
> 1 row in set (0.00 sec)
> 
> SELECT NULL <=> NULL, NULL <=> 1, NULL <=> 0;
> +---------------+------------+------------+
> | NULL <=> NULL | NULL <=> 1 | NULL <=> 0 |
> +---------------+------------+------------+
> | 1 | 0 | 0 |
> +---------------+------------+------------+
> 1 row in set (0.00 sec)
> 
> -- 推荐 is not null , is null 
> ~~~
>
> 

### 结果排序

> ~~~mysql
> -- ASC 为升序（从小到大）
> -- DESC 为降序（从大到小）
> -- 默认为 ASC
> SELECT ... FROM table_name [WHERE ...]
> ORDER BY column [ASC|DESC], [...];
> ~~~
>
> 注意：没有 ORDER BY 子句的查询，返回的顺序是未定义的，永远不要依赖这个顺序 
>
> 案例：
>
> 同学及数学成绩，按数学成绩升序显示
>
> ~~~mysql
> SELECT name, math FROM exam_result ORDER BY math;
> +-----------+--------+
> | name | math |
> +-----------+--------+
> | 宋公明 | 65 |
> | 孙权 | 73 |
> | 孙悟空 | 78 |
> | 曹孟德 | 84 |
> | 刘玄德 | 85 |
> | 唐三藏 | 98 |
> | 猪悟能 | 98 |
> +-----------+--------+
> 7 rows in set (0.00 sec)
> ~~~
>
>  同学及 qq 号，按 qq 号排序显示
>
> ~~~MYSQL
> -- NULL 视为比任何值都小，升序出现在最上面
> SELECT name, qq FROM students ORDER BY qq;
> +-----------+-------+
> | name | qq |
> +-----------+-------+
> | 唐大师 | NULL |
> | 孙仲谋 | NULL |
> | 曹阿瞒 | NULL |
> | 孙悟空 | 11111 |
> +-----------+-------+
> 4 rows in set (0.00 sec)
> ~~~
>
> ~~~mysql
> -- NULL 视为比任何值都小，降序出现在最下面
> SELECT name, qq FROM students ORDER BY qq DESC;
> +-----------+-------+
> | name | qq |
> +-----------+-------+
> | 孙悟空 | 11111 |
> | 唐大师 | NULL |
> | 孙仲谋 | NULL |
> | 曹阿瞒 | NULL |
> +-----------+-------+
> 4 rows in set (0.00 sec)
> 
> ~~~
>
> 查询同学各门成绩，依次按 数学降序，英语升序，语文升序的方式显示
>
> ~~~mysql
> -- 多字段排序，排序优先级随书写顺序
> SELECT name, math, english, chinese FROM exam_result ORDER BY math DESC, english, chinese;
> +-----------+--------+--------+-------+
> | name | math | english | chinese |
> +-----------+--------+--------+-------+
> | 唐三藏 | 98 | 56 | 67 |
> | 猪悟能 | 98 | 90 | 88 |
> | 刘玄德 | 85 | 45 | 55 |
> | 曹孟德 | 84 | 67 | 82 |
> | 孙悟空 | 78 | 77 | 87 |
> | 孙权 | 73 | 78 | 70 |
> | 宋公明 | 65 | 30 | 75 |
> +-----------+--------+--------+-------+
> 7 rows in set (0.00 sec)
> 
> ~~~
> 
>查询同学及总分，由高到低
> 
>~~~mysql
> -- ORDER BY 中可以使用表达式
> SELECT name, chinese + english + math FROM exam_result ORDER BY chinese + english + math DESC;
> +-----------+-------------------------+
> | name | chinese + english + math |
> +-----------+-------------------------+
> | 猪悟能 | 276 |
> | 孙悟空 | 242 |
> | 曹孟德 | 233 |
> | 唐三藏 | 221 |
> | 孙权 | 221 |
> | 刘玄德 | 185 |
> | 宋公明 | 170 |
> +-----------+-------------------------+
> 7 rows in set (0.00 sec)
> ~~~
> 
> ~~~mysql
>-- ORDER BY 子句中可以使用列别名
> SELECT name, chinese + english + math 总分 FROM exam_result ORDER BY 总分 DESC;
> +-----------+--------+
> | name | 总分 |
> +-----------+--------+
> | 猪悟能 | 276 |
> | 孙悟空 | 242 |
> | 曹孟德 | 233 |
> | 唐三藏 | 221 |
> | 孙权 | 221 |
> | 刘玄德 | 185 |
> | 宋公明 | 170 |
> +-----------+--------+
> 7 rows in set (0.00 sec)
> 
> ~~~
> 
> 查询姓孙的同学或者姓曹的同学数学成绩，结果按数学成绩由高到低显示
> 
>~~~mysql
> -- 结合 WHERE 子句 和 ORDER BY 子句
>SELECT name, math FROM exam_result WHERE name LIKE '孙%' OR name LIKE '曹%' ORDER BY math DESC;
> +-----------+--------+
> | name | math |
> +-----------+--------+
> | 曹孟德 | 84 |
> | 孙悟空 | 78 |
> | 孙权 | 73 |
> +-----------+--------+
> 3 rows in set (0.00 sec)
> ~~~
> 
> 

### 筛选分页结果

> ~~~mysql
> -- 起始下标为 0
> -- 从 0 开始，筛选 n 条结果
> SELECT ... FROM table_name [WHERE ...] [ORDER BY ...] LIMIT n;
> -- 从 s 开始，筛选 n 条结果
> SELECT ... FROM table_name [WHERE ...] [ORDER BY ...] LIMIT s, n;
> -- 从 s 开始，筛选 n 条结果，比第二种用法更明确，建议使用
> SELECT ... FROM table_name [WHERE ...] [ORDER BY ...] LIMIT n OFFSET s;
> 
> ~~~
>
> **建议：对未知表进行查询时，最好加一条 LIMIT 1，避免因为表中数据过大，查询全表数据导致数据库卡死 **
>
> 案例：
>
> 按 id 进行分页，每页 3 条记录，分别显示 第 1、2、3 页
>
> ~~~mysql
> -- 第 1 页
> SELECT id, name, math, english, chinese FROM exam_result ORDER BY id LIMIT 3 OFFSET 0;
> +----+-----------+--------+--------+-------+
> | id | name | math | english | chinese |
> +----+-----------+--------+--------+-------+
> | 1 | 唐三藏 | 98 | 56 | 67 |
> | 2 | 孙悟空 | 78 | 77 | 87 |
> | 3 | 猪悟能 | 98 | 90 | 88 |
> +----+-----------+--------+--------+-------+
> 3 rows in set (0.02 sec)
> 
> -- 第 2 页
> SELECT id, name, math, english, chinese FROM exam_result ORDER BY id LIMIT 3 OFFSET 3;
> +----+-----------+--------+--------+-------+
> | id | name | math | english | chinese |
> +----+-----------+--------+--------+-------+
> | 4 | 曹孟德 | 84 | 67 | 82 |
> | 5 | 刘玄德 | 85 | 45 | 55 |
> | 6 | 孙权 | 73 | 78 | 70 |
> +----+-----------+--------+--------+-------+
> 3 rows in set (0.00 sec)
> ~~~
> 
>~~~mysql
> -- 第 3 页，如果结果不足 3 个，不会有影响
> SELECT id, name, math, english, chinese FROM exam_result ORDER BY id LIMIT 3 OFFSET 6;
> +----+-----------+--------+--------+-------+
> | id | name | math | english | chinese |
> +----+-----------+--------+--------+-------+
> | 7 | 宋公明 | 65 | 30 | 75 |
> +----+-----------+--------+--------+-------+
> 1 row in set (0.00 sec)
> ~~~
> 
> 

### 更新

update

> ~~~mysql
> UPDATE table_name SET column = expr [, column = expr ...] [WHERE ...] [ORDER BY ...] [LIMIT ...]
> ~~~
>
> **对查询到的结果进行列值更新**
>
> 案例：
>
> 将孙悟空同学的数学成绩变更为 80 分
>
> ~~~mysql
> -- 更新值为具体值
> -- 查看原数据
> SELECT name, math FROM exam_result WHERE name = '孙悟空';
> +-----------+--------+
> | name | math |
> +-----------+--------+
> | 孙悟空 | 78 |
> +-----------+--------+
> 1 row in set (0.00 sec)
> 
> -- 数据更新
> UPDATE exam_result SET math = 80 WHERE name = '孙悟空';
> Query OK, 1 row affected (0.04 sec)
> Rows matched: 1 Changed: 1 Warnings: 0
> 
> -- 查看更新后数据
> SELECT name, math FROM exam_result WHERE name = '孙悟空';
> +-----------+--------+
> | name | math |
> +-----------+--------+
> | 孙悟空 | 80 |
> +-----------+--------+
> 1 row in set (0.00 sec)
> 
> ~~~
>
> 将曹孟德同学的数学成绩变更为 60 分，语文成绩变更为 70 分
>
> ~~~mysql
> -- 一次更新多个列
> -- 查看原数据
> SELECT name, math, chinese FROM exam_result WHERE name = '曹孟德';
> +-----------+--------+-------+
> | name | math | chinese |
> +-----------+--------+-------+
> | 曹孟德 | 84 | 82 |
> +-----------+--------+-------+
> 1 row in set (0.00 sec)
> 
> -- 数据更新
> UPDATE exam_result SET math = 60, chinese = 70 WHERE name = '曹孟德';
> Query OK, 1 row affected (0.14 sec)
> Rows matched: 1 Changed: 1 Warnings: 0
> 
> -- 查看更新后数据
> SELECT name, math, chinese FROM exam_result WHERE name = '曹孟德';
> +-----------+--------+-------+
> | name | math | chinese |
> +-----------+--------+-------+
> | 曹孟德 | 60 | 70 |
> +-----------+--------+-------+
> 1 row in set (0.00 sec)
> 
> ~~~
>
> 将总成绩倒数前三的 3 位同学的数学成绩加上 30 分
>
> ~~~mysql
> -- 更新值为原值基础上变更
> -- 查看原数据
> -- 别名可以在ORDER BY中使用
> SELECT name, math, chinese + math + english 总分 FROM exam_result ORDER BY 总分 LIMIT 3;
> +-----------+--------+--------+
> | name | math | 总分 |
> +-----------+--------+--------+
> | 宋公明 | 65 | 170 |
> | 刘玄德 | 85 | 185 |
> | 曹孟德 | 60 | 197 |
> +-----------+--------+--------+
> 3 rows in set (0.00 sec)
> 
> -- 数据更新，不支持 math += 30 这种语法
> UPDATE exam_result SET math = math + 30
> ORDER BY chinese + math + english LIMIT 3;
> 
> -- 查看更新后数据
> -- 思考：这里还可以按总分升序排序取前 3 个么？
> SELECT name, math, chinese + math + english 总分 FROM exam_result
> WHERE name IN ('宋公明', '刘玄德', '曹孟德');
> +-----------+--------+--------+
> | name | math | 总分 |
> +-----------+--------+--------+
> | 曹孟德 | 90 | 227 |
> | 刘玄德 | 115 | 215 |
> | 宋公明 | 95 | 200 |
> +-----------+--------+--------+
> 3 rows in set (0.00 sec)
> 
> -- 按总成绩排序后查询结果
> SELECT name, math, chinese + math + english 总分 FROM exam_result
> ORDER BY 总分 LIMIT 3;
> +-----------+--------+--------+
> | name | math | 总分 |
> +-----------+--------+--------+
> | 宋公明 | 95 | 200 |
> | 刘玄德 | 115 | 215 |
> | 唐三藏 | 98 | 221 |
> +-----------+--------+--------+
> 3 rows in set (0.00 sec)
> 
> ~~~
>
> 将所有同学的语文成绩更新为原来的 2 倍（**注意：更新全表的语句慎用！**）
>
> ~~~mysql
> -- 没有 WHERE 子句，则更新全表
> -- 查看原数据
> SELECT * FROM exam_result;
> +----+-----------+-------+--------+--------+
> | id | name | chinese | math | english |
> +----+-----------+-------+--------+--------+
> | 1 | 唐三藏 | 67 | 98 | 56 |
> | 2 | 孙悟空 | 87 | 80 | 77 |
> | 3 | 猪悟能 | 88 | 98 | 90 |
> | 4 | 曹孟德 | 70 | 90 | 67 |
> | 5 | 刘玄德 | 55 | 115 | 45 |
> | 6 | 孙权 | 70 | 73 | 78 |
> | 7 | 宋公明 | 75 | 95 | 30 |
> +----+-----------+-------+--------+--------+
> 7 rows in set (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 数据更新
> UPDATE exam_result SET chinese = chinese * 2;
> Query OK, 7 rows affected (0.00 sec)
> Rows matched: 7 Changed: 7 Warnings: 0
> 
> -- 查看更新后数据
> SELECT * FROM exam_result;
> +----+-----------+-------+--------+--------+
> | id | name | chinese | math | english |
> +----+-----------+-------+--------+--------+
> | 1 | 唐三藏 | 134 | 98 | 56 |
> | 2 | 孙悟空 | 174 | 80 | 77 |
> | 3 | 猪悟能 | 176 | 98 | 90 |
> | 4 | 曹孟德 | 140 | 90 | 67 |
> | 5 | 刘玄德 | 110 | 115 | 45 |
> | 6 | 孙权 | 140 | 73 | 78 |
> | 7 | 宋公明 | 150 | 95 | 30 |
> +----+-----------+-------+--------+--------+
> 7 rows in set (0.00 sec)
> ~~~
>
> 

### 删除

删除数据 delete

> ~~~mysql
> DELETE FROM table_name [WHERE ...] [ORDER BY ...] [LIMIT ...]
> ~~~
>
> 案例：
>
> 删除孙悟空同学的考试成绩
>
> ~~~mysql
> -- 查看原数据
> SELECT * FROM exam_result WHERE name = '孙悟空';
> +----+-----------+-------+--------+--------+
> | id | name | chinese | math | english |
> +----+-----------+-------+--------+--------+
> | 2 | 孙悟空 | 174 | 80 | 77 |
> +----+-----------+-------+--------+--------+
> 1 row in set (0.00 sec)
> 
> -- 删除数据
> DELETE FROM exam_result WHERE name = '孙悟空';
> Query OK, 1 row affected (0.17 sec)
> 
> -- 查看删除结果
> SELECT * FROM exam_result WHERE name = '孙悟空';
> Empty set (0.00 sec)
> 
> ~~~
>
> 删除整张表数据（**注意：删除整表操作要慎用！**）
>
> ~~~mysql
> -- 准备测试表
> CREATE TABLE for_delete (
> id INT PRIMARY KEY AUTO_INCREMENT,
> name VARCHAR(20)
> );
> Query OK, 0 rows affected (0.16 sec)
> 
> -- 插入测试数据
> INSERT INTO for_delete (name) VALUES ('A'), ('B'), ('C');
> Query OK, 3 rows affected (1.05 sec)
> Records: 3 Duplicates: 0 Warnings: 0
> 
> -- 查看测试数据
> SELECT * FROM for_delete;
> +----+------+
> | id | name |
> +----+------+
> | 1 | A |
> | 2 | B |
> | 3 | C |
> +----+------+
> 3 rows in set (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 删除整表数据
> DELETE FROM for_delete;
> Query OK, 3 rows affected (0.00 sec)
> 
> -- 查看删除结果
> SELECT * FROM for_delete;
> Empty set (0.00 sec)
> ~~~
>
> ~~~mysql
> -- 再插入一条数据，自增 id 在原值上增长
> INSERT INTO for_delete (name) VALUES ('D');
> Query OK, 1 row affected (0.00 sec)
> 
> -- 查看数据
> SELECT * FROM for_delete;
> +----+------+
> | id | name |
> +----+------+
> | 4 | D |
> +----+------+
> 1 row in set (0.00 sec)
> 
> -- 查看表结构，会有 AUTO_INCREMENT=n 项
> SHOW CREATE TABLE for_delete\G
> *************************** 1. row ***************************
> Table: for_delete
> Create Table: CREATE TABLE `for_delete` (
> `id` int(11) NOT NULL AUTO_INCREMENT,
> `name` varchar(20) DEFAULT NULL,
> PRIMARY KEY (`id`)
> ) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8 -- auto字段
> 1 row in set (0.00 sec)
> ~~~
>
> 

截断表 truncate

> ~~~mysql
> TRUNCATE [TABLE] table_name
> ~~~
>
> **注意：这个操作慎用**
>
> 1. 只能对整表操作，不能像 DELETE 一样针对部分数据操作； 
> 2. 实际上 **MySQL 不对数据操作**，所以比 DELETE 更快，但是TRUNCATE在删除数据的时候，并不经过真正的事务，所以**无法回滚** 
> 3. 会**重置 AUTO_INCREMENT 项**
>
> ~~~mysql
> -- 准备测试表
> CREATE TABLE for_truncate (
> id INT PRIMARY KEY AUTO_INCREMENT,
> name VARCHAR(20)
> );
> Query OK, 0 rows affected (0.16 sec)
> 
> -- 插入测试数据
> INSERT INTO for_truncate (name) VALUES ('A'), ('B'), ('C');
> Query OK, 3 rows affected (1.05 sec)
> Records: 3 Duplicates: 0 Warnings: 0
> 
> -- 查看测试数据
> SELECT * FROM for_truncate;
> +----+------+
> | id | name |
> +----+------+
> | 1 | A |
> | 2 | B |
> | 3 | C |
> +----+------+
> 3 rows in set (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 截断整表数据，注意影响行数是 0，所以实际上没有对数据真正操作
> TRUNCATE for_truncate;
> Query OK, 0 rows affected (0.10 sec)
> 
> -- 查看删除结果
> SELECT * FROM for_truncate;
> Empty set (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 再插入一条数据，自增 id 在重新增长
> INSERT INTO for_truncate (name) VALUES ('D');
> Query OK, 1 row affected (0.00 sec)
> 
> ~~~
>
> ~~~mysql
> -- 查看数据
> SELECT * FROM for_truncate;
> +----+------+
> | id | name |
> +----+------+
> | 1 | D |
> +----+------+
> 1 row in set (0.00 sec)
> 
> -- 查看表结构，会有 AUTO_INCREMENT=2 项
> SHOW CREATE TABLE for_truncate\G
> *************************** 1. row ***************************
> Table: for_truncate
> Create Table: CREATE TABLE `for_truncate` (
> `id` int(11) NOT NULL AUTO_INCREMENT,
> `name` varchar(20) DEFAULT NULL,
> PRIMARY KEY (`id`)
> ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8
> 1 row in set (0.00 sec)
> 
> ~~~
>
> ![image-20230221221530559](%E5%9B%BE%E7%89%87/MySQL/image-20230221221530559.png)

### 插入查询结果

语法：

~~~mysql
INSERT INTO table_name [(column [, column ...])] SELECT ...
~~~

案例：删除表中的的重复复记录，重复的数据只能有一份

~~~mysql
-- 创建原数据表
CREATE TABLE duplicate_table (id int, name varchar(20));
Query OK, 0 rows affected (0.01 sec)

-- 插入测试数据
INSERT INTO duplicate_table VALUES
(100, 'aaa'),
(100, 'aaa'),
(200, 'bbb'),
(200, 'bbb'),
(200, 'bbb'),
(300, 'ccc');
Query OK, 6 rows affected (0.00 sec)
Records: 6 Duplicates: 0 Warnings: 0
~~~

思路：

~~~mysql
-- 创建一张空表 no_duplicate_table，结构和 duplicate_table 一样
CREATE TABLE no_duplicate_table LIKE duplicate_table;
Query OK, 0 rows affected (0.00 sec)

-- 将 duplicate_table 的去重数据插入到 no_duplicate_table
INSERT INTO no_duplicate_table SELECT DISTINCT * FROM duplicate_table;
Query OK, 3 rows affected (0.00 sec)
Records: 3 Duplicates: 0 Warnings: 0

-- 通过重命名表，实现原子的去重操作
RENAME TABLE duplicate_table TO old_duplicate_table,
no_duplicate_table TO duplicate_table;
Query OK, 0 rows affected (0.00 sec)

-- 查看最终结果
SELECT * FROM duplicate_table;
+------+------+
| id | name |
+------+------+
| 100 | aaa |
| 200 | bbb |
| 300 | ccc |
+------+------+
3 rows in set (0.00 sec)

~~~



### 聚合函数

![image-20230222205659679](%E5%9B%BE%E7%89%87/MySQL/image-20230222205659679.png)

案例：

统计班级共有多少同学

~~~mysql
-- 使用 * 做统计，不受 NULL 影响
SELECT COUNT(*) FROM students;
+----------+
| COUNT(*) |
+----------+
| 4 |
+----------+
1 row in set (0.00 sec)

-- 使用表达式做统计
SELECT COUNT(1) FROM students;
+----------+
| COUNT(1) |
+----------+
| 4 |
+----------+
1 row in set (0.00 sec)
~~~

 统计班级收集的 qq 号有多少

~~~mysql
-- NULL 不会计入结果
SELECT COUNT(qq) FROM students;
+-----------+
| COUNT(qq) |
+-----------+
| 1 |
+-----------+
1 row in set (0.00 sec)
~~~

统计本次考试的数学成绩分数个数

~~~mysql
-- COUNT(math) 统计的是全部成绩
SELECT COUNT(math) FROM exam_result;
+---------------+
| COUNT(math) |
+---------------+
| 6 |
+---------------+
1 row in set (0.00 sec)

-- COUNT(DISTINCT math) 统计的是去重成绩数量
SELECT COUNT(DISTINCT math) FROM exam_result;
+------------------------+
| COUNT(DISTINCT math) |
+------------------------+
| 5 |
+------------------------+
1 row in set (0.00 sec)
~~~

统计数学成绩总分

~~~mysql
SELECT SUM(math) FROM exam_result;
+-------------+
| SUM(math) |
+-------------+
| 569 |
+-------------+
1 row in set (0.00 sec)

-- 不及格 < 60 的总分，没有结果，返回 NULL
SELECT SUM(math) FROM exam_result WHERE math < 60;
+-------------+
| SUM(math) |
+-------------+
| NULL |
+-------------+
1 row in set (0.00 sec)
~~~

统计平均总分

~~~mysql
SELECT AVG(chinese + math + english) 平均总分 FROM exam_result;
+--------------+
| 平均总分 |
+--------------+
| 297.5 |
+--------------+
~~~

返回英语最高分

~~~mysql
SELECT MAX(english) FROM exam_result;
+-------------+
| MAX(english) |
+-------------+
| 90 |
+-------------+
1 row in set (0.00 sec)
~~~

返回 > 70 分以上的数学最低分

~~~mysql
SELECT MIN(math) FROM exam_result WHERE math > 70;
+-------------+
| MIN(math) |
+-------------+
| 73 |
+-------------+
1 row in set (0.00 sec)

~~~



### group by 字句

**在select中使用group by 子句可以对指定列进行分组查询**

~~~mysql
select column1, column2, .. from table group by column;
~~~

案例： 

- 准备工作，创建一个雇员信息表（来自oracle 9i的经典测试表） 

    - EMP员工表 
    - DEPT部门表 
    - SALGRADE工资等级表 

- 如何显示每个部门的平均工资和最高工资

    ~~~mysql
    select deptno,avg(sal),max(sal) from EMP group by deptno;
    ~~~

    

- 显示每个部门的每种岗位的平均工资和最低工资

    ~~~mysql
    select avg(sal),min(sal),job, deptno from EMP group by deptno, job;
    ~~~

    

- 显示平均工资低于2000的部门和它的平均工资

    - 统计各个部门的平均工资

        ~~~mysql
        select avg(sal) from EMP group by deptno
        ~~~

    - **having**和group by配合使用，对group by结果进行过滤

        ~~~mysql
        select avg(sal) as myavg from EMP group by deptno having myavg<2000;
        --having经常和group by搭配使用，作用是对分组进行筛选，作用有些像where。
        ~~~

        

### OJ

- https://www.nowcoder.com/practice/51c12cea6a97468da149c04b7ecf362e?tpId=82&tqId=29802&tPage=2&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/ae51e6d057c94f6d891735a48d1c2397?tpId=82&tqId=29760&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/218ae58dfdcd4af195fff264e062138f?tpId=82&tqId=29753&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/ec1ca44c62c14ceb990c3c40def1ec6c?tpId=82&tqId=29754&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/4c8b4a10ca5b44189e411107e1d8bec1?tpId=82&tqId=29761&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/72ca694734294dc78f513e147da7821e?tpId=82&tqId=29765&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://leetcode.com/problems/duplicate-emails/description/
- https://leetcode.com/problems/big-countries/description/
- https://leetcode.com/problems/nth-highest-salary/description/

> ~~~mysql
> 面试题：
> SQL查询中各个关键字的执行先后顺序 ：from > on> join > where > group by > with > having > select> distinct > order by > limit
> ~~~

## 内置函数

### 日期函数

![image-20230222211129103](%E5%9B%BE%E7%89%87/MySQL/image-20230222211129103.png)

- 获得年月日

    ~~~mysql
    select current_date();
    +----------------+
    | current_date() |
    +----------------+
    | 2017-11-19 |
    +----------------+
    ~~~

- 获得时分秒

    ~~~mysql
    select current_time();
    +----------------+
    | current_time() |
    +----------------+
    | 13:51:21 |
    +----------------+
    

- 获得时间戳

    ~~~mysql
    select current_timestamp();
    +---------------------+
    | current_timestamp() |
    +---------------------+
    | 2017-11-19 13:51:48 |
    +---------------------+
    

- 在日期的基础上加上日期

    ~~~mysql
    select date_add('2017-10-28', interval 10 day);
    +-----------------------------------------+
    | date_add('2017-10-28', interval 10 day) |
    +-----------------------------------------+
    | 2017-11-07 |
    +-----------------------------------------+

- 在日期的基础上减去日期

    ~~~mysql
    select date_sub('2017-10-1', interval 2 day);
    +---------------------------------------+
    | date_sub('2017-10-1', interval 2 day) |
    +---------------------------------------+
    | 2017-09-29 |
    +---------------------------------------+

- 计算两个日期直接相差多少天

    ~~~mysql
    select datediff('2017-10-10', '2016-9-1');
    +------------------------------------+
    | datediff('2017-10-10', '2016-9-1') |
    +------------------------------------+
    | 404 |
    +------------------------------------+
    ~~~


**案例：**

创建一张表，记录生日

> ~~~mysql
> create table tmp(
> id int primary key auto_increment,
> birthday date
> );
> 
> ~~~
>
> 添加当前日期：
>
> ~~~mysql
> insert into tmp(birthday) values(current_date());
> ~~~
>
> ~~~mysql
> mysql> select * from tmp;
> +----+------------+
> | id | birthday |
> +----+------------+
> | 1 | 2017-11-19 |
> +----+------------+
> ~~~
>
> 

​    创建一个留言板

> ~~~mysql
> mysql> create table msg (
> -> id int primary key auto_increment,
> -> content varchar(30) not null,
> -> sendtime datetime
> -> );
> ~~~
>
> 插入数据
>
> ~~~mysql
> mysql> insert into msg(content,sendtime) values('hello1', now());
> mysql> insert into msg(content,sendtime) values('hello2', now());
> mysql> select * from msg;
> +----+---------+---------------------+
> | id | content | sendtime |
> +----+---------+---------------------+
> | 1 | hello1 | 2017-11-19 14:12:20 |
> | 2 | hello2 | 2017-11-19 14:13:21 |
> +----+---------+---------------------+
> 
> ~~~
>
> 显示所有留言信息，发布日期只显示日期，不用显示时间
>
> ~~~mysql
> select content,date(sendtime) from msg;
> ~~~
>
> 请查询在2分钟内发布的帖子
>
> ~~~mysql
> select * from msg where date_add(sendtime, interval 2 minute) > now();
> 
> ~~~



### 字符串函数

![image-20230222211729342](%E5%9B%BE%E7%89%87/MySQL/image-20230222211729342.png)

案例:

- 获取emp表的ename列的字符集

    ~~~mysql
    select charset(ename) from EMP;
    
    ~~~

    

- 要求显示student表中的信息，显示格式：“XXX的语文是XXX分，数学XXX分，英语XXX分”

    ~~~mysql
    select concat(name, '的语文是',chinese,'分，数学是',math,'分') as '分数' from student;
    

- 求学生表中学生姓名占用的字节数

    ~~~mysql
    select length(name), name from student;
    ~~~

    > 注意：length函数返回字符串长度，以字节为单位。如果是多字节字符则计算多个字节数；如果是单字 节字符则算作一个字节。比如：字母，数组算作一个字节，中文表示多个字节数（与字符集编码有关）

- 将EMP表中所有名字中有S的替换成'上海'

    ~~~mysql
    select replace(ename, 'S', '上海') ,ename from EMP;

- 截取EMP表中ename字段的第二个到第三个字符

    ~~~mysql
    select substring(ename, 2, 2), ename from EMP;
    

- 以首字母小写的方式显示所有员工的姓名

    ~~~mysql
    select concat(lcase(substring(ename, 1, 1)),substring(ename,2)) from EMP;

### 数学函数

![image-20230222212308593](%E5%9B%BE%E7%89%87/MySQL/image-20230222212308593.png)

案例：

- 绝对值

    ~~~mysql
    select abs(-100.2);

- 向上取整

    ~~~mysql
    select ceiling(23.04);
    

- 向下取整

    ~~~mysql
    select floor(23.7);

- 保留n位小数

    ~~~mysql
    select format(12.3456, 2);

- 随机数

    ~~~mysql
    select rand();

### 其他函数

- user() 查询当前用户

    ~~~mysql
    select user();
    
    ~~~

    

- md5(str)对一个字符串进行md5摘要，摘要后得到一个32位字符串

    ~~~mysql
    select md5('admin')
    ~~~

    ~~~mysql
    +----------------------------------+
    | md5('admin') |
    +----------------------------------+
    | 21232f297a57a5a743894a0e4a801fc3 |
    +----------------------------------+

- database()显示当前正在使用的数据库

    ~~~mysql
    select database();
    
    ~~~

    

- password()函数，MySQL数据库使用该函数对用户加密

    ~~~mysql
    select password('root');
    +-------------------------------------------+
    | password('root') |
    +-------------------------------------------+
    | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
    +-------------------------------------------+

- ifnull（val1， val2） 如果val1为null，返回val2，否则返回val1的值

    ~~~mysql
    select ifnull('abc', '123');
    +----------------------+
    | ifnull('abc', '123') |
    +----------------------+
    | abc |
    +----------------------+
    1 row in set (0.01 sec)
    select ifnull(null, '123');
    +---------------------+
    | ifnull(null, '123') |
    +---------------------+
    | 123 |
    +---------------------+
    1 row in set (0.00 sec)

https://www.nowcoder.com/practice/e3870bd5d6744109a902db43c105bd50?tpId=82&tqId=29819&rp=10&ru=%2Factivity%2Foj&qru=%2Fta%2Fsql%2Fquestion-ranking&tPage=3

## 复合查询

![image-20230223222447995](%E5%9B%BE%E7%89%87/MySQL/image-20230223222447995.png)![image-20230223222453660](%E5%9B%BE%E7%89%87/MySQL/image-20230223222453660.png)![image-20230223222459083](%E5%9B%BE%E7%89%87/MySQL/image-20230223222459083.png)

### 基本查询测试

- 查询工资高于500或岗位为MANAGER的雇员，同时还要满足他们的姓名首字母为大写的J
    ~~~mysql
    select * from EMP where (sal>500 or job='MANAGER') and ename like 'J%';
    ~~~

- 按照部门号升序而雇员的工资降序排序
    ~~~mysql
    select * from EMP order by deptno, sal desc;

- 使用年薪进行降序排序
    ~~~mysql
    select ename, sal*12+ifnull(comm,0) as '年薪' from EMP order by 年薪 desc;

- 显示工资最高的员工的名字和工作岗位
    ~~~mysql
    select ename, job from EMP where sal = (select max(sal) from EMP);

- 显示工资高于平均工资的员工信息

    ~~~mysql
    select ename, sal from EMP where sal>(select avg(sal) from EMP);

- 显示每个部门的平均工资和最高工资
    ~~~mysql
    select deptno, format(avg(sal), 2) , max(sal) from EMP group by deptno;

- 显示平均工资低于2000的部门号和它的平均工资
    ~~~mysql
    select deptno, avg(sal) as avg_sal from EMP group by deptno having avg_sal<2000;

- 显示每种岗位的雇员总数，平均工资
    ~~~mysql
    select job,count(*), format(avg(sal),2) from EMP group by job;
    

### 多表查询

实际开发中往往数据来自不同的表，所以需要多表查询。本节我们用一个简单的公司管理系统，有三张表 EMP,DEPT,SALGRADE来演示如何进行多表查询。 

案例：

- 显示雇员名、雇员工资以及所在部门的名字因为上面的数据来自EMP和DEPT表，因此要联合查询
    ![image-20230222213351011](%E5%9B%BE%E7%89%87/MySQL/image-20230222213351011.png)
    其实我们只要emp表中的deptno = dept表中的deptno字段的记录
    ~~~mysql
    select EMP.ename, EMP.sal, DEPT.dname from EMP, DEPT where EMP.deptno = DEPT.deptno;

- 显示部门号为10的部门名，员工名和工资
    ~~~mysql
    select ename, sal,dname from EMP, DEPT where EMP.deptno=DEPT.deptno and DEPT.deptno = 10;

- 显示各个员工的姓名，工资，及工资级别
    ~~~mysql
    select ename, sal, grade from EMP, SALGRADE where EMP.sal between losal and hisal;

![image-20230222192712644](%E5%9B%BE%E7%89%87/MySQL/image-20230222192712644.png)

### 自连接

自连接是指在同一张表连接查询 

案例： **显示员工FORD的上级领导的编号和姓名（mgr是员工领导的编号--empno）**

- 使用子查询

    ~~~mysql
    select empno,ename from emp where emp.empno=(select mgr from emp where ename='FORD');
    
    ~~~

    

- 使用多表查询

    ~~~mysql
    -- 使用到表的别名
    -- from emp leader, emp worker 给自己的表起别名，因为要先做笛卡尔积，所以别名可以先识别
    select leader.empno,leader.ename from emp leader, emp worker where leader.empno =worker.mgr and worker.ename='FORD';
    ~~~



### 子查询

子查询是指嵌入在其他sql语句中的select语句，也叫嵌套查询

#### 单行子查询

返回一行记录的子查询 

案例：显示SMITH同一部门的员工

~~~mysql
select * from EMP WHERE deptno = (select deptno from EMP where ename='smith');
~~~

#### 多行子查询

返回多行记录的子查询

案例：

- in关键字；查询和10号部门的工作岗位相同的雇员的名字，岗位，工资，部门号，但是不包含10自己的
    ~~~mysql
    select ename,job,sal,deptno from emp where job in (select distinct job from emp where deptno=10) and deptno<>10;
    ~~~
    
    
    
- all关键字；显示工资比部门30的所有员工的工资高的员工的姓名、工资和部门号
    ~~~mysql
    select ename, sal, deptno from EMP where sal > all(select sal from EMP where
    deptno=30);

- any关键字；显示工资比部门30的任意员工的工资高的员工的姓名、工资和部门号（包含自己部门的员工）
    ~~~mysql
    select ename, sal, deptno from EMP where sal > any(select sal from EMP where
    deptno=30);

#### 多列子查询

单行子查询是指子查询只返回单列，单行数据；多行子查询是指返回单列多行数据，都是针对单列而言的，而多列子 查询则是指查询返回多个列数据的子查询语句 

案例：**查询和SMITH的部门和岗位完全相同的所有雇员，不含SMITH本人**

~~~mysql
mysql> select ename from EMP where (deptno, job)=(select deptno, job from EMP where
ename='SMITH') and ename <> 'SMITH';
+-------+
| ename |
+-------+
| ADAMS |
+-------+

~~~

#### 在from子句中使用子查询

子查询语句出现在from子句中。这里要用到数据查询的技巧，把一个子查询当做一个临时表使用。 

案例：

- 显示每个高于自己部门平均工资的员工的姓名、部门、工资、平均工资
    ~~~mysql
    //获取各个部门的平均工资，将其看作临时表
    select ename, deptno, sal, format(asal,2) from EMP, 
    (select avg(sal) asal, deptno dt from EMP group by deptno) tmp 
    where EMP.sal > tmp.asal and EMP.deptno=tmp.dt;

- 查找每个部门工资最高的人的姓名、工资、部门、最高工资
    ~~~mysql
    select emp.ename, emp.sal,emp.deptno, ms from emp,
    (select max(sal) ms, deptno from emp group by deptno) tmp
    where emp.deptno=tmp.deptno and emp.sal=tmp.ms;

- 显示每个部门的信息（部门名，编号，地址）和人员数量

    - 方法1：使用多表
        ~~~mysql
        select dept.dname, dept.deptno, dept.loc,count(*) '部门人数' from emp, dept
        where emp.deptno=dept.deptno
        group by dept.deptno,dept.dname,dept.loc;
        
    - 方法2：使用子查询
        ~~~mysql
        -- 1. 对EMP表进行人员统计
        select count(*), deptno from EMP group by deptno;
        -- 2. 将上面的表看作临时表
        select DEPT.deptno, dname, mycnt, loc from DEPT,
        (select count(*) mycnt, deptno from EMP group by deptno) tmp
        where DEPT.deptno=tmp.deptno;

#### 合并查询

在实际应用中，为了合并多个select的执行结果，可以使用集合操作符 union，union all

##### union

该操作符用于取得两个结果集的并集。当使用该操作符时，会自动去掉结果集中的重复行。 

案例：将工资大于2500或职位是MANAGER的人找出来

~~~mysql
mysql> select ename, sal, job from EMP where sal>2500 union
-> select ename, sal, job from EMP where job='MANAGER';--去掉了重复记录
+-------+---------+-----------+
| ename | sal | job |
+-------+---------+-----------+
| JONES | 2975.00 | MANAGER |
| BLAKE | 2850.00 | MANAGER |
| SCOTT | 3000.00 | ANALYST |
| KING | 5000.00 | PRESIDENT |
| FORD | 3000.00 | ANALYST |
| CLARK | 2450.00 | MANAGER |
+-------+---------+-----------+
~~~

##### union all

该操作符用于取得两个结果集的并集。当使用该操作符时，不会去掉结果集中的重复行。 

案例：将工资大于25000或职位是MANAGER的人找出来

~~~mysql
mysql> select ename, sal, job from EMP where sal>2500 union all
-> select ename, sal, job from EMP where job='MANAGER';
+-------+---------+-----------+
| ename | sal | job |
+-------+---------+-----------+
| JONES | 2975.00 | MANAGER |
| BLAKE | 2850.00 | MANAGER |
| SCOTT | 3000.00 | ANALYST |
| KING | 5000.00 | PRESIDENT |
| FORD | 3000.00 | ANALYST |
| JONES | 2975.00 | MANAGER |
| BLAKE | 2850.00 | MANAGER |
| CLARK | 2450.00 | MANAGER |
+-------+---------+-----------+

~~~



### OJ

- https://www.nowcoder.com/practice/23142e7a23e4480781a3b978b5e0f33a?tpId=82&tqId=29758&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/355036f7f0c8429a85281f7ac05b457a?tpId=82&tqId=29817&tPage=3&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/32c53d06443346f4a2f2ca733c19660c?tpId=82&tqId=29762&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking
- https://www.nowcoder.com/practice/e50d92b8673a440ebdf3a517b5b37d62?tpId=82&tqId=29763&tPage=1&rp=&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking

## 内外链接

表的连接分为内连和外连

### 内连接

我们前面学习的查询都是隐式内连接，前面的内连接实际上就是利用where子句对两种表形成的笛卡儿积进行筛选，而join语法的显式内连接是先匹配再链接，比where效率高，也是在开发过程中使用的最多的连接查询。 

语法：

~~~mysql
select 字段 from 表1 inner join 表2 on 连接条件 and 其他条件；
~~~

> 备注：前面学习的都是隐式内连接

案例：显示SMITH的名字和部门名称

~~~Mysql
-- 用前面的写法
select ename, dname from EMP, DEPT where EMP.deptno=DEPT.deptno and ename='SMITH';
-- 用标准的内连接写法
select ename, dname from EMP inner join DEPT on EMP.deptno=DEPT.deptno and
ename='SMITH';
~~~

### 外连接

外连接分为左外连接和右外连接

#### 左外连接

如果联合查询，左侧的表完全显示我们就说是左外连接。 

语法：

~~~mysql
select 字段名 from 表名1 left join 表名2 on 连接条件
~~~

案例：

> ~~~mysql
> -- 建两张表
> create table stu (id int, name varchar(30)); -- 学生表
> insert into stu values(1,'jack'),(2,'tom'),(3,'kity'),(4,'nono');
> create table exam (id int, grade int); -- 成绩表
> insert into exam values(1, 56),(2,76),(11, 8);
> ~~~
>
> 查询所有学生的成绩，如果这个学生没有成绩，也要将学生的个人信息显示出来
>
> ~~~mysql
> -- 当左边表和右边表没有匹配时，也会显示左边表的数据
> select * from stu left join exam on stu.id=exam.id;
> ~~~
>
> 

#### 右外连接

如果联合查询，右侧的表完全显示我们就说是右外连接。 

语法：

~~~mysql
select 字段 from 表名1 right join 表名2 on 连接条件；
~~~

案例： 

> 对stu表和exam表联合查询，把所有的成绩都显示出来，即使这个成绩没有学生与它对应，也要显示出来
>
> ~~~mysql
> select * from stu right join exam on stu.id=exam.id;
> ~~~



练习： 列出部门名称和这些部门的员工信息，同时列出没有员工的部门

~~~mysql
方法一：
select d.dname, e.* from dept d left join emp e on d.deptno=e.deptno;
方法二：
select d.dname, e.* from emp e right join dept d on d.deptno=e.deptno;
~~~



### OJ

- https://leetcode.com/problems/rank-scores/
- https://leetcode.com/problems/exchange-seats/description/

## 索引

### 原理

#### 1. 没有索引，可能会有什么问题

**索引**：提高数据库的性能，索引是物美价廉的东西了。不用加内存，不用改程序，不用调sql，只要执行正确的 create index ，查询速度就可能提高成百上千倍。但是天下没有免费的午餐，查询速度的提高是以插入、更新、删除的速度为代价的，这些写操作，增加了大量的IO。所以它的价值，在于提高一个海量数据的检索速度。 

常见索引分为： 

- 主键索引(primary key) 
- 唯一索引(unique) 
- 普通索引(index) 
- 全文索引(fulltext)--解决中子文索引问题。

案例： 

> 先整一个海量表，在查询的时候，看看没有索引时有什么问题？
>
> ~~~mysql
> --构建一个8000000条记录的数据
> --构建的海量表数据需要有差异性，所以使用存储过程来创建， 拷贝下面代码就可以了，暂时不用理解
> -- 产生随机字符串
> delimiter $$
> create function rand_string(n INT)
> returns varchar(255)
> begin
> declare chars_str varchar(100) default
> 'abcdefghijklmnopqrstuvwxyzABCDEFJHIJKLMNOPQRSTUVWXYZ';
> declare return_str varchar(255) default '';
> declare i int default 0;
> while i < n do
> set return_str =concat(return_str,substring(chars_str,floor(1+rand()*52),1));
> set i = i + 1;
> end while;
> return return_str;
> end $$
> delimiter ;
> --产生随机数字
> delimiter $$
> create function rand_num()
> returns int(5)
> begin
> declare i int default 0;
> set i = floor(10+rand()*500);
> return i;
> end $$
> delimiter ;
> --创建存储过程，向雇员表添加海量数据
> delimiter $$
> create procedure insert_emp(in start int(10),in max_num int(10))
> begin
> declare i int default 0;
> set autocommit = 0;
> repeat
> set i = i + 1;
> insert into EMP values ((start+i)
> ,rand_string(6),'SALESMAN',0001,curdate(),2000,400,rand_num());
> until i = max_num
> end repeat;
> commit;
> end $$
> delimiter ;
> -- 执行存储过程，添加8000000条记录
> call insert_emp(100001, 8000000);
> ~~~
>
> 到此，已经创建出了海量数据的表了。
>
> - 查询员工编号为998877的员工
>     ~~~mysql
>     select * from EMP where empno=998877;
>     ~~~
>
>     可以看到耗时4.93秒，这还是在本机一个人来操作，在实际项目中，如果放在公网中，假如同时有1000个人并 发查询，那很可能就死机。
>
> - 解决方法，创建索引
>     ~~~mysql
>     alter table EMP add index(empno);
>     ~~~
>
>     
>
> - 换一个员工编号，测试看看查询时间
>     ~~~mysql
>     select * from EMP where empno=123456;
>     ~~~
>
>     

#### 2. 认识磁盘

**MySQL与存储：** 

MySQL 给用户提供存储服务，而存储的都是数据，数据在磁盘这个外设当中。磁盘是计算机中的一个机械设备，相 比于计算机其他电子元件，磁盘效率是比较低的，在加上IO本身的特征，可以知道，如何提交效率，是 MySQL 的一 个重要话题。

**先来研究一下磁盘：**

![image-20230224214448148](%E5%9B%BE%E7%89%87/MySQL/image-20230224214448148.png)

**在看看磁盘中一个盘片：**

![image-20230224214525853](%E5%9B%BE%E7%89%87/MySQL/image-20230224214525853.png)

**扇区：**

数据库文件，本质其实就是保存在磁盘的盘片当中。也就是上面的一个个小格子中，就是我们经常所说的扇区。当 然，数据库文件很大，也很多，一定需要占据多个扇区。 

题外话：

> - 从上图可以看出来，在半径方向上，距离圆心越近，扇区越小，距离圆心越远，扇区越大
> - 那么，所有扇区都是默认512字节吗？目前是的，我们也这样认为。因为保证一个扇区多大，是由比特位密度决 定的。
> - 不过最新的磁盘技术，已经慢慢的让扇区大小不同了，不过我们现在暂时不考虑。

我们在使用Linux，所看到的大部分目录或者文件，其实就是保存在硬盘当中的。(当然，有一些内存文件系统，如： proc ， sys 之类，我们不考虑)

~~~mysql
#数据库文件，本质其实就是保存在磁盘的盘片当中，就是一个一个的文件
[root@VM-0-3-centos ~]# ls /var/lib/mysql -l #我们目前MySQL中的文件
total 319592
drwxr-x--- 2 mysql mysql 4096 Apr 15 21:46 57test
-rw-r----- 1 mysql mysql 56 Apr 12 15:27 auto.cnf
drwxr-x--- 2 mysql mysql 4096 May 17 13:52 bit_index
-rw------- 1 mysql mysql 1676 Apr 12 15:27 ca-key.pem
-rw-r--r-- 1 mysql mysql 1112 Apr 12 15:27 ca.pem
drwx------ 2 mysql mysql 4096 Apr 13 21:26 ccdata_pro
-rw-r--r-- 1 mysql mysql 1112 Apr 12 15:27 client-cert.pem
-rw------- 1 mysql mysql 1680 Apr 12 15:27 client-key.pem
-rw-r----- 1 mysql mysql 16958 Jun 8 15:46 ib_buffer_pool
-rw-r----- 1 mysql mysql 213909504 Jun 8 16:02 ibdata1
-rw-r----- 1 mysql mysql 50331648 Jun 8 16:02 ib_logfile0
-rw-r----- 1 mysql mysql 50331648 Jun 8 16:02 ib_logfile1
-rw-r----- 1 mysql mysql 12582912 Jun 8 15:46 ibtmp1
drwxr-x--- 2 mysql mysql 4096 Apr 28 14:11 musicserver
drwxr-x--- 2 mysql mysql 4096 May 9 09:47 mysql
srwxrwxrwx 1 mysql mysql 0 Jun 8 15:46 mysql.sock
-rw------- 1 mysql mysql 5 Jun 8 15:46 mysql.sock.lock
drwxr-x--- 2 mysql mysql 4096 Apr 12 15:27 performance_schema
-rw------- 1 mysql mysql 1676 Apr 12 15:27 private_key.pem
-rw-r--r-- 1 mysql mysql 452 Apr 12 15:27 public_key.pem
drwxr-x--- 2 mysql mysql 4096 May 9 09:46 scott
-rw-r--r-- 1 mysql mysql 1112 Apr 12 15:27 server-cert.pem
-rw------- 1 mysql mysql 1676 Apr 12 15:27 server-key.pem
drwxr-x--- 2 mysql mysql 12288 Apr 12 15:27 sys
drwxr-x--- 2 mysql mysql 4096 Jun 5 17:13 test # 自己定义的数据库，里面有数据表
~~~

所以，最基本的，找到一个文件的全部，本质，就是在磁盘找到所有保存文件的扇区。 

而我们能够定位任何一个扇区，那么便能找到所有扇区，因为查找方式是一样的。

**定位扇区：**

![image-20230224214754717](%E5%9B%BE%E7%89%87/MySQL/image-20230224214754717.png)

- 柱面(磁道): 多盘磁盘，每盘都是双面，大小完全相等。那么同半径的磁道，整体上便构成了一个柱面 
- 每个盘面都有一个磁头，那么磁头和盘面的对应关系便是1对1的 
- 所以，我们只需要知道，磁头（Heads）、柱面(Cylinder)(等价于磁道)、扇区(Sector)对应的编号。即可在磁盘 上定位所要访问的扇区。这种磁盘数据定位方式叫做 CHS 。
- 不过实际**系统软件使用的并不是 CHS （但是硬件 是），而是 LBA** ，一种线性地址，可以想象成虚拟地址与物理地址。系统将 LBA 地址最后会转化成为 CHS ，交 给磁盘去进行数据读取。不过，我们现在不关心转化细节，知道这个东西，让我们逻辑自洽起来即可。

**结论：**

我们现在已经能够在硬件层面定位任何一个基本数据块了(扇区)。那么在系统软件上，就直接按照扇区(512字节， 部分4096字节),进行IO交互吗？**不是**

> - 如果操作系统直接使用硬件提供的数据大小进行交互，那么系统的IO代码，就和硬件强相关，换言之，如果硬件发生变化，系统必须跟着变化 
> - 从目前来看，单次IO 512字节，还是太小了。IO单位小，意味着读取同样的数据内容，需要进行多次磁盘访 问，会带来效率的降低。 
> - 之前学习文件系统，就是在磁盘的基本结构下建立的，文件系统读取基本单位，就不是扇区，而是数据块。

故，系统读取磁盘，是以块为单位的，基本单位是 4KB 。

**磁盘随机访问(Random Access)与连续访问(Sequential Access)：**

> 随机访问：本次IO所给出的扇区地址和上次IO给出扇区地址不连续，这样的话磁头在两次IO操作之间需要作比较大 的移动动作才能重新开始读/写数据。 
>
> 连续访问：如果当次IO给出的扇区地址与上次IO结束的扇区地址是连续的，那磁头就能很快的开始这次IO操作，这 样的多个IO操作称为连续访问。
>
> 因此尽管相邻的两次IO操作在同一时刻发出，但如果它们的请求的扇区地址相差很大的话也只能称为随机访问，而非 连续访问。 
>
> 磁盘是通过机械运动进行寻址的，随机访问不需要过多的定位，故效率比较高。



#### 3. MySQL 与磁盘交互基本单位

而 MySQL 作为一款应用软件，可以想象成一种特殊的文件系统。它有着更高的IO场景，所以，为了提高基本的IO效 率， MySQL 进行IO的基本单位是 16KB (后面统一使用 InnoDB 存储引擎讲解)

~~~mysql
mysql> SHOW GLOBAL STATUS LIKE 'innodb_page_size';
+------------------+-------+
| Variable_name | Value |
+------------------+-------+
| Innodb_page_size | 16384 | -- 16*1024=16384
+------------------+-------+
1 row in set (0.01 sec)
~~~

也就是说，磁盘这个硬件设备的基本单位是 512 字节，而 MySQL InnoDB引擎 使用 16KB 进行IO交互。即， MySQL 和 磁盘进行数据交互的基本单位是 16KB 。这个基本数据单元，在 MySQL 这里叫做page（注意和系统的page区分）

#### 4. 建立共识

- MySQL 中的数据文件，是以page为单位保存在磁盘当中的。 
- MySQL 的 CURD 操作，都需要通过计算，找到对应的插入位置，或者找到对应要修改或者查询的数据。 
- 而只要涉及计算，就需要CPU参与，而为了便于CPU参与，一定要能够先将数据移动到内存当中。 
- 所以在特定时间内，数据一定是磁盘中有，内存中也有。后续操作完内存数据之后，以特定的刷新策略，刷新 到磁盘。而这时，就涉及到磁盘和内存的数据交互，也就是IO了。而此时IO的基本单位就是Page。 
- 为了更好的进行上面的操作， MySQL 服务器在内存中运行的时候，在服务器内部，就申请了被称为 Buffer Pool 的的大内存空间，来进行各种缓存。其实就是很大的内存空间，来和磁盘数据进行IO交互。 
- 为了更高的效率，一定要尽可能的减少系统和磁盘IO的次数

#### 5. 索引的理解

##### 测试

建立测试表

~~~mysql
create table if not exists user (
id int primary key, --一定要添加主键哦，只有这样才会默认生成主键索引
age int not null,
name varchar(16) not null
);

mysql> show create table user \G
*************************** 1. row ***************************
Table: user
Create Table: CREATE TABLE `user` (
`id` int(11) NOT NULL,
`age` int(11) NOT NULL,
`name` varchar(16) NOT NULL,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 --默认就是InnoDB存储引擎
1 row in set (0.00 sec)

~~~

插入多条记录

~~~mysql
-- 插入多条记录，注意，我们并没有按照主键的大小顺序插入哦

mysql> insert into user (id, age, name) values(3, 18, '杨过');
Query OK, 1 row affected (0.01 sec)

mysql> insert into user (id, age, name) values(4, 16, '小龙女');
Query OK, 1 row affected (0.00 sec)

mysql> insert into user (id, age, name) values(2, 26, '黄蓉');
Query OK, 1 row affected (0.01 sec)

mysql> insert into user (id, age, name) values(5, 36, '郭靖');
Query OK, 1 row affected (0.00 sec)

mysql> insert into user (id, age, name) values(1, 56, '欧阳锋');
Query OK, 1 row affected (0.00 sec)

~~~

查看插入结果

~~~mysql
mysql> select * from user; -- 发现竟然默认是有序的！是谁干的呢？排序有什么好处呢？
+----+-----+-----------+
| id | age | name |
+----+-----+-----------+
| 1 | 56 | 欧阳锋 |
| 2 | 26 | 黄蓉 |
| 3 | 18 | 杨过 |
| 4 | 16 | 小龙女 |
| 5 | 36 | 郭靖 |
+----+-----+-----------+
5 rows in set (0.00 sec)
~~~

##### 中断一下---为何IO交互是 Page

> 为何MySQL和磁盘进行IO交互的时候，要采用Page的方案进行交互呢?用多少，加载多少不香吗? 
>
> 
>
> 如上面的5条记录，如果MySQL要查找id=2的记录，第一次加载id=1，第二次加载id=2，一次一条记录，那么就需要2次IO。 如果要找id=5，那么就需要5次IO。
>
> 但，如果这5条(或者更多)都被保存在一个Page中(16KB，能保存很多记录),那么第一次IO查找id=2的时候，整个Page会被 加载到MySQL的Buffer Pool中，这里完成了一次IO。但是往后如果在查找id=1,3,4,5等，完全不需要进行IO了，而是直 接在内存中进行了。所以，就在单Page里面，大大减少了IO的次数。 
>
> 你怎么保证，用户一定下次找的数据，就在这个Page里面？我们不能严格保证，但是有很大概率，因为有局部性原理。 往往IO效率低下的最主要矛盾不是IO单次数据量的大小，而是IO的次数。

##### 理解单个Page

MySQL 中要管理很多数据表文件，而要管理好这些文件，就需要 先描述，在组织 ,我们目前可以简单理解成一个个独 立文件是有一个或者多个Page构成的。

![image-20230224215521529](%E5%9B%BE%E7%89%87/MySQL/image-20230224215521529.png)



不同的 Page ，在 MySQL 中，都是 16KB ，使用 prev 和 next 构成双向链表 

因为有主键的问题， MySQL 会默认按照主键给我们的数据进行排序，从上面的Page内数据记录可以看出，数据是有序且彼此关联的。

~~~mysql
为什么数据库在插入数据时要对其进行排序呢？我们按正常顺序插入数据不是也挺好的吗？
插入数据时排序的目的，就是优化查询的效率。
页内部存放数据的模块，实质上也是一个链表的结构，链表的特点也就是增删快，查询修改慢，所以优化查询的效率是必须的。
正式因为有序，在查找的时候，从头到后都是有效查找，没有任何一个查找是浪费的，而且，如果运气好，是可以提前结束查找过程的。
~~~

##### 理解多个Page

- 通过上面的分析，我们知道，上面页模式中，只有一个功能，就是在查询某条数据的时候直接将一整页的数据 加载到内存中，以减少硬盘IO次数，从而提高性能。但是，我们也可以看到，现在的页模式内部，实际上是采 用了链表的结构，前一条数据指向后一条数据，本质上还是通过数据的逐条比较来取出特定的数据。 
- 如果有1千万条数据，一定需要多个Page来保存1千万条数据，多个Page彼此使用双链表链接起来，而且每个 Page内部的数据也是基于链表的。那么，查找特定一条记录，也一定是线性查找。这效率也太低了。

![image-20230224215630675](%E5%9B%BE%E7%89%87/MySQL/image-20230224215630675.png)

##### 页目录

我们在看《谭浩强C程序设计》这本书的时候，如果我们要看<指针章节>，找到该章节有两种做法 

- 从头逐页的向后翻，直到找到目标内容 
- 通过书提供的目录，发现指针章节在234页(假设)，那么我们便直接翻到234页。同时，查找目录的方案，可以 顺序找，不过因为目录肯定少，所以可以快速提高定位 
- 本质上，书中的目录，是多花了纸张的，但是却提高了效率 
- 所以，目录，是一种“空间换时间的做法”

##### 单页情况 

针对上面的单页Page，我们能否也引入目录呢？当然可以

![image-20230224215816248](%E5%9B%BE%E7%89%87/MySQL/image-20230224215816248.png)

那么当前，在一个Page内部，我们引入了目录。比如，我们要查找id=4记录，之前必须线性遍历4次，才能拿到结 果。现在直接通过目录2[3]，直接进行定位新的起始位置，提高了效率。现在我们可以再次正式回答上面的问题了， 为何通过键值 MySQL 会自动排序？**可以很方便引入目录**

##### 多页情况

MySQL 中每一页的大小只有 16KB ，单个Page大小固定，所以随着数据量不断增大， 16KB 不可能存下所有的数据， 那么必定会有多个页来存储数据。

![image-20230224215900534](%E5%9B%BE%E7%89%87/MySQL/image-20230224215900534.png)



在单表数据不断被插入的情况下， MySQL 会在容量不足的时候，自动开辟新的Page来保存新的数据，然后通过指针 的方式，将所有的Page组织起来。 

需要注意，上面的图，是理想结构，大家也知道，目前要保证整体有序，那么新插入的数据，不一定会在新Page上 面，这里仅仅做演示。

这样，我们就可以通过多个Page遍历，Page内部通过目录来快速定位数据。可是，貌似这样也有效率问题，在Page 之间，也是需要 MySQL 遍历的，遍历意味着依旧需要进行大量的IO，将下一个Page加载到内存，进行线性检测。这 样就显得我们之前的Page内部的目录，有点杯水车薪了。 

那么如何解决呢？解决方案，其实就是我们之前的思路，给Page也带上目录。

> - 使用一个目录项来指向某一页，而这个目录项存放的就是将要指向的页中存放的最小数据的键值。 
> - 和页内目录不同的地方在于，这种目录管理的级别是页，而页内目录管理的级别是行。
> -  其中，每个目录项的构成是：键值+指针。图中没有画全。
>     ![image-20230224215952190](%E5%9B%BE%E7%89%87/MySQL/image-20230224215952190.png)



存在一个目录页来管理页目录，目录页中的数据存放的就是指向的那一页中最小的数据。有数据，就可通过比较，找 到该访问那个Page，进而通过指针，找到下一个Page。

其实**目录页的本质也是页，普通页中存的数据是用户数据，而目录页中存的数据是普通页的地址。** 

可是，我们每次检索数据的时候，该从哪里开始呢？虽然顶层的目录页少了，但是还要遍历啊？不用担心，可以在加 目录页

![image-20230224220115683](%E5%9B%BE%E7%89%87/MySQL/image-20230224220115683.png)

这货就是传说中的B+树啊！没错，至此，我们已经给我们的表user构建完了主键索引。 

随便找一个id=？我们发现，现在查找的Page数一定减少了，也就意味着IO次数减少了，那么效率也就提高了。

##### 复盘一下

> Page分为目录页和数据页。目录页只放各个下级Page的最小键值。 
>
> 查找的时候，自定向下找，只需要加载部分目录页到内存，即可完成算法的整个查找过程，大大减少了IO次数

##### InnoDB 在建立索引结构来管理数据的时候，其他数据结构为何不行？

- 链表？线性遍历 
- 二叉搜索树？退化问题，可能退化成为线性结构 
- AVL &&红黑树？虽然是平衡或者近似平衡，但是毕竟是二叉结构，相比较多阶B+，意味着树整体过高，大家都 是自顶向下找，层高越低，意味着系统与硬盘更少的IO Page交互。虽然你很秀，但是有更秀的。
-  Hash？官方的索引实现方式中， MySQL 是支持HASH的，不过 InnoDB 和 MyISAM 并不支持.Hash根据其算法特征，决定了虽然有时候也很快(O(1))，不过，在面对范围查找就明显不行，另外还有其他差别，有兴趣可以查一 下。
    ![image-20230224220239494](%E5%9B%BE%E7%89%87/MySQL/image-20230224220239494.png)
- B树？最值得比较的是 InnoDB 为何不用B树作为底层索引？

##### B VS B+

B

> ![image-20230224220324502](%E5%9B%BE%E7%89%87/MySQL/image-20230224220324502.png)

B+

> ![image-20230224220338047](%E5%9B%BE%E7%89%87/MySQL/image-20230224220338047.png)

上面的图，是在网上找的，大家也可以搜一下。

目前这两棵树，对我们最有意义的区别是：

- B树节点，既有数据，又有Page指针，而B+，只有叶子节点有数据，其他目录页，只有键值和Page指针 
- B+叶子节点，全部相连，而B没有

为何选择B+

- 节点不存储data，这样一个节点就可以存储更多的key。可以使得树更矮，所以IO操作次数更少。 
- 叶子节点相连，更便于进行范围查找

##### 聚簇索引 VS 非聚簇索引

聚簇索引：数据附着于索引结构

非聚簇索引：数据与索引结构分离

MyISAM 存储引擎-主键索引 

MyISAM 引擎同样使用B+树作为索引结果，叶节点的data域存放的是数据记录的地址。下图为 MyISAM 表的主索引， Col1 为主键。

![image-20230225215355845](%E5%9B%BE%E7%89%87/MySQL/image-20230225215355845.png)

其中， MyISAM 最大的特点是，将索引Page和数据Page分离，也就是叶子节点没有数据，只有对应数据的地址。 

相较于 InnoDB 索引， InnoDB 是将索引和数据放在一起的。

~~~mysql
-- 终端A
mysql> create database myisam_test; --创建数据库
Query OK, 1 row affected (0.00 sec)

mysql> use myisam_test;
Database changed

mysql> create table mtest(
-> id int primary key,
-> name varchar(11) not null
-> )engine=MyISAM; --使用engine=MyISAM
Query OK, 0 rows affected (0.01 sec)


-- 终端B
[root@VM-0-3-centos mysql]# ls myisam_test/ -al --mysql数据目录下
total 28
drwxr-x--- 2 mysql mysql 4096 Jun 13 13:33 .
drwxr-x--x 13 mysql mysql 4096 Jun 13 13:32 ..
-rw-r----- 1 mysql mysql 61 Jun 13 13:32 db.opt
-rw-r----- 1 mysql mysql 8586 Jun 13 13:33 mtest.frm -- 表结构数据
-rw-r----- 1 mysql mysql 0 Jun 13 13:33 mtest.MYD -- 该表对应的数据，当前没有数据，所以是0
-rw-r----- 1 mysql mysql 1024 Jun 13 13:33 mtest.MYI -- 该表对应的主键索引数据

~~~

其中， MyISAM 这种用户数据与索引数据分离的索引方案，叫做非聚簇索引

~~~mysql
-- 终端A
mysql> create database innodb_test; --创建数据库
Query OK, 1 row affected (0.00 sec)

mysql> use innodb_test;
Database changed

mysql> create table itest(
-> id int primary key,
-> name varchar(11) not null
-> )engine=InnoDB; --使用engine=InnoDB
Query OK, 0 rows affected (0.02 sec)


-- 终端B
[root@VM-0-3-centos mysql]# ls innodb_test/ -al
total 120
drwxr-x--- 2 mysql mysql 4096 Jun 13 13:39 .
drwxr-x--x 14 mysql mysql 4096 Jun 13 13:38 ..
-rw-r----- 1 mysql mysql 61 Jun 13 13:38 db.opt
-rw-r----- 1 mysql mysql 8586 Jun 13 13:39 itest.frm -- 表结构数据
-rw-r----- 1 mysql mysql 98304 Jun 13 13:39 itest.ibd -- 该表对应的主键索引和用户数据，虽然现在一行数据没有，但是该表并不为0，因为有主键索引数据
~~~

其中， InnoDB 这种用户数据与索引数据在一起索引方案，叫做聚簇索引 



当然， MySQL 除了默认会建立主键索引外，我们用户也有可能建立按照其他列信息建立的索引，一般这种索引可以 叫做辅助（普通）索引。 

对于 MyISAM ,建立辅助（普通）索引和主键索引没有差别，无非就是主键不能重复，而非主键可重复。 

下图就是基于 MyISAM 的 Col2 建立的索引，和主键索引没有差别

![image-20230225215526560](%E5%9B%BE%E7%89%87/MySQL/image-20230225215526560.png)

同样， InnoDB 除了主键索引，用户也会建立辅助（普通）索引，我们以上表中的 Col3 建立对应的辅助索引如下 图：

![image-20230225215608867](%E5%9B%BE%E7%89%87/MySQL/image-20230225215608867.png)

可以看到， InnoDB 的非主键索引中叶子节点并没有数据，而只有对应记录的key值。 所以通过辅助（普通）索引，找到目标记录，需要两遍索引：首先检索辅助索引获得主键，然后用主键到主索引中检 索获得记录。这种过程，就叫做回表查询 为何 InnoDB 针对这种辅助（普通）索引的场景，不给叶子节点也附上数据呢？原因就是太浪费空间了。

总结：

> - 如何理解硬盘 
> - 如何理解柱面，磁道，扇区，磁头 
> - InnoDB 主键索引和普通索引 
> - MyISAM 主键索引和普通索引 
> - 其他数据结构为何不能作为索引结构，尤其是B+和B 
> - 聚簇索引 VS 非聚簇索引



### 操作

#### 创建主键索引

- 第一种方式

    ~~~mysql
    -- 在创建表的时候，直接在字段名后指定 primary key
    create table user1(id int primary key, name varchar(30));

- 第二种方式
    ~~~mysql
    -- 在创建表的最后，指定某列或某几列为主键索引
    create table user2(id int, name varchar(30), primary key(id));
    

- 第三种方式

    ~~~mysql
    create table user3(id int, name varchar(30));
    -- 创建表以后再添加主键
    alter table user3 add primary key(id);
    ~~~

**主键索引的特点：** 

    > - 一个表中，最多有一个主键索引，当然可以使符合主键 
    > - 主键索引的效率高（主键不可重复） 
    > - 创建主键索引的列，它的值不能为null，且不能重复 主
    > - 键索引的列基本上是int

#### 唯一索引的创建

- 第一种方式

    ~~~mysql
    -- 在表定义时，在某列后直接指定unique唯一属性。
    create table user4(id int primary key, name varchar(30) unique);
    ~~~

- 第二种方式

    ~~~mysql
    -- 创建表时，在表的后面指定某列或某几列为unique
    create table user5(id int primary key, name varchar(30), unique(name));
    ~~~

- 第三种方式

    ~~~mysql
    create table user6(id int primary key, name varchar(30)）；
    -- 创建表以后再添加唯一键
    alter table user6 add unique(name);
    ~~~

**唯一索引的特点：** 

> - 一个表中，可以有多个唯一索引 
> - 查询效率高 
> - 如果在某一列建立唯一索引，必须保证这列不能有重复数据 
> - 如果一个唯一索引上指定not null，等价于主键索引

#### 普通索引的创建

- 第一种方式 
    ~~~mysql
    create table user8(id int primary key,
    name varchar(20),
    email varchar(30),
    index(name) --在表的定义最后，指定某列为索引
    );
    
    ~~~

    

- 第二种方式
    ~~~mysql
    create table user9(id int primary key, name varchar(20), email varchar(30));
    alter table user9 add index(name); --创建完表以后指定某列为普通索引
    
    ~~~

    

- 第三种方式
    ~~~mysql
    create table user10(id int primary key, name varchar(20), email varchar(30));
    -- 创建一个索引名为 idx_name 的索引
    create index idx_name on user10(name);
    ~~~

普通索引的特点： 

> - 一个表中可以有多个普通索引，普通索引在实际开发中用的比较多 
>
> - 如果某列需要创建索引，但是该列有重复的值，那么我们就应该使用普通索引

#### 全文索引的创建

当对文章字段或有大量文字的字段进行检索时，会使用到全文索引。MySQL提供全文索引机制，但是有要求，要求 表的存储引擎必须是MyISAM，而且默认的全文索引支持英文，不支持中文。如果对中文进行全文检索，可以使用 sphinx的中文版(coreseek)。

~~~mysql
CREATE TABLE articles (
id INT UNSIGNED AUTO_INCREMENT NOT NULL PRIMARY KEY,
title VARCHAR(200),
body TEXT,
FULLTEXT (title,body)
)engine=MyISAM;
INSERT INTO articles (title,body) VALUES
('MySQL Tutorial','DBMS stands for DataBase ...'),
('How To Use MySQL Well','After you went through a ...'),
('Optimizing MySQL','In this tutorial we will show ...'),
('1001 MySQL Tricks','1. Never run mysqld as root. 2. ...'),
('MySQL vs. YourSQL','In the following database comparison ...'),
('MySQL Security','When configured properly, MySQL ...');

~~~

**查询有没有database数据**

> 如果使用如下查询方式，虽然查询出数据，但是没有使用到全文索引
>
> ~~~mysql
> mysql> select * from articles where body like '%database%';
> +----+-------------------+------------------------------------------+
> | id | title | body |
> +----+-------------------+------------------------------------------+
> | 1 | MySQL Tutorial | DBMS stands for DataBase ... |
> | 5 | MySQL vs. YourSQL | In the following database comparison ... |
> +----+-------------------+------------------------------------------+
> 
> ~~~
>
> 可以用explain工具看一下，是否使用到索引
>
> ~~~mysql
> mysql> explain select * from articles where body like '%database%'\G
> *************************** 1. row ***************************
> id: 1
> select_type: SIMPLE
> table: articles
> type: ALL
> possible_keys: NULL
> key: NULL <== key为null表示没有用到索引
> key_len: NULL
> ref: NULL
> rows: 6
> Extra: Using where
> 1 row in set (0.00 sec)
> 
> ~~~
>
> 

**如何使用全文索引呢？**

> ~~~mysql
> mysql> SELECT * FROM articles
> -> WHERE MATCH (title,body) AGAINST ('database');
> +----+-------------------+------------------------------------------+
> | id | title | body |
> +----+-------------------+------------------------------------------+
> | 5 | MySQL vs. YourSQL | In the following database comparison ... |
> | 1 | MySQL Tutorial | DBMS stands for DataBase ... |
> +----+-------------------+------------------------------------------+
> ~~~
>
> 

**通过explain来分析这个sql语句**

> ~~~mysql
> mysql> explain SELECT * FROM articles WHERE MATCH (title,body) AGAINST ('database')\G
> *************************** 1. row ***************************
> id: 1
> select_type: SIMPLE
> table: articles
> type: fulltext
> possible_keys: title
> key: title <= key用到了title
> key_len: 0
> ref:
> rows: 1
> Extra: Using where
> ~~~
>
> 

#### 查询索引

- 第一种方法： show keys from 表名
    ~~~mysql
    mysql> show keys from goods\G
    *********** 1. row ***********
    Table: goods <= 表名
    Non_unique: 0 <= 0表示唯一索引
    Key_name: PRIMARY <= 主键索引  此字段代表索引的真是名字
    Seq_in_index: 1
    Column_name: goods_id <= 索引在哪列
    Collation: A
    Cardinality: 0
    Sub_part: NULL
    Packed: NULL
    Null:
    Index_type: BTREE <= 以二叉树形式的索引
    Comment:
    1 row in set (0.00 sec)
    ~~~

-  show index from 表名

- 第三种方法（信息比较简略）： desc 表名；

#### 删除索引

- 第一种方法-删除主键索引： alter table 表名 drop primary key; 
- 第二种方法-其他索引的删除： alter table 表名 drop index 索引名； 索引名就是show keys from 表名中的 Key_name 字段 mysql> alter table user10 drop index idx_name; 
- 第三种方法方法： drop index 索引名 on 表名 mysql> drop index name on user8;

#### 索引创建原则 

- 比较频繁作为查询条件的字段应该创建索引 
- 唯一性太差的字段不适合单独创建索引，即使频繁作为查询条件 
- 更新非常频繁的字段不适合作创建索引
- 不会出现在where子句中的字段不该创建索引

#### 其他概念--自行了解： 

> - 复合索引 
> - 索引最左匹配原则 
> - 索引覆盖





### 笔记

~~~
mysql引擎会按照一定规则，描述组织包装成‘page’
将数据保存在page内部，并保证数据整体有序
在page里建立目录，目录对有序的数据进行映射
则查找某条数据时，先查在哪个目录，在对目录内部的数据线性检测

page内部的查询不需要io，增加效率，而在查询数据在哪一个page时，如果使用线性检测，则每次查询都需要io操作
类比，我们也给page建立目录， 。。


b+树
~~~

![image-20230224174109110](%E5%9B%BE%E7%89%87/MySQL/image-20230224174109110.png)

![image-20230224175311595](%E5%9B%BE%E7%89%87/MySQL/image-20230224175311595.png)





## 事务

### CURD不加控制，会有什么问题？

![image-20230225220411407](%E5%9B%BE%E7%89%87/MySQL/image-20230225220411407.png)

**CURD满足什么属性，能解决上述问题？**

> 1. 买票的过程得是原子的吧 
> 2. 买票互相应该不能影响吧 
> 3. 买完票应该要永久有效吧 
> 4. 买前，和买后都要是确定的状态吧

### 什么是事务？

事务就是一组DML语句组成，这些语句在逻辑上存在相关性，这一组DML语句要么全部成功，要么全部失败，是一 个整体。MySQL提供一种机制，保证我们达到这样的效果。事务还规定不同的客户端看到的数据是不相同的。 

事务就是要做的或所做的事情，主要用于处理操作量大，复杂度高的数据。假设一种场景：你毕业了，学校的教务系 统后台 MySQL 中，不在需要你的数据，要删除你的所有信息(一般不会:) ), 那么要删除你的基本信息(姓名，电话，籍 贯等)的同时，也删除和你有关的其他信息，比如：你的各科成绩，你在校表现，甚至你在论坛发过的文章等。这 样，就需要多条 MySQL 语句构成，那么所有这些操作合起来，就构成了一个事务。 

正如我们上面所说，一个 MySQL 数据库，可不止你一个事务在运行，同一时刻，甚至有大量的请求被包装成事务， 在向 MySQL 服务器发起事务处理请求。而每条事务至少一条 SQL ，最多很多 SQL ,这样如果大家都访问同样的表数 据，在不加保护的情况，就绝对会出现问题。甚至，因为事务由多条 SQL 构成，那么，也会存在执行到一半出错或者 不想再执行的情况，那么已经执行的怎么办呢？ 

所有，一个完整的事务，绝对不是简单的 sql 集合，还需要满足如下四个属性：

> - **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环 节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过 一样。
> - **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有 的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。 
> - **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时 由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（ Read uncommitted ）、读提交 （ read committed ）、可重复读（ repeatable read ）和串行化（ Serializable ） 
> - **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

上面四个属性，可以简称为 ACID 。

> 原子性（Atomicity，或称不可分割性） 
>
> 一致性（Consistency） 
>
> 隔离性（Isolation，又称独立性） 
>
> 持久性（Durability）。

### 为什么会出现事务

事务被 MySQL 编写者设计出来,本质是为了当应用程序访问数据库的时候,事务能够简化我们的编程模型,不需要我们去 考虑各种各样的潜在错误和并发问题.可以想一下当我们使用事务时,要么提交,要么回滚,我们不会去考虑网络异常了, 服务器宕机了,同时更改一个数据怎么办对吧?因此**事务本质上是为了应用层服务的**.而不是伴随着数据库系统天生就有 的. 

备注：我们后面把 MySQL 中的一行信息，称为一行记录

### 事务的版本支持

在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务， MyISAM 不支持。 

查看数据库引擎

~~~mysql
mysql> show engines; -- 表格显示
mysql> show engines \G -- 行显示
*************************** 1. row ***************************
Engine: InnoDB -- 引擎名称
Support: DEFAULT -- 默认引擎
Comment: Supports transactions, row-level locking, and foreign keys -- 描述
Transactions: YES -- 支持事务
XA: YES
Savepoints: YES -- 支持事务保存点
*************************** 2. row ***************************
Engine: MRG_MYISAM
Support: YES
Comment: Collection of identical MyISAM tables
Transactions: NO
XA: NO
Savepoints: NO
*************************** 3. row ***************************
Engine: MEMORY --内存引擎
Support: YES
Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
XA: NO
Savepoints: NO
*************************** 4. row ***************************
Engine: BLACKHOLE
Support: YES
Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
XA: NO
Savepoints: NO
*************************** 5. row ***************************
Engine: MyISAM
Support: YES
Comment: MyISAM storage engine
Transactions: NO -- MyISAM不支持事务
XA: NO
Savepoints: NO
*************************** 6. row ***************************
Engine: CSV
Support: YES
Comment: CSV storage engine
Transactions: NO
XA: NO
Savepoints: NO
*************************** 7. row ***************************
Engine: ARCHIVE
Support: YES
Comment: Archive storage engine
Transactions: NO
XA: NO
Savepoints: NO
*************************** 8. row ***************************
Engine: PERFORMANCE_SCHEMA
Support: YES
Comment: Performance Schema
Transactions: NO
XA: NO
Savepoints: NO
*************************** 9. row ***************************
Engine: FEDERATED
Support: NO
Comment: Federated MySQL storage engine
Transactions: NULL
XA: NULL
Savepoints: NULL
9 rows in set (0.00 sec)

~~~

### 事务提交方式

事务的提交方式常见的有两种： 

> 自动提交 
>
> 手动提交 

查看事务提交方式

~~~mysql
mysql> show variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit | ON |
+---------------+-------+
1 row in set (0.41 sec)

~~~

用 SET 来改变 MySQL 的自动提交模式:

~~~mysql
mysql> SET AUTOCOMMIT=0; #SET AUTOCOMMIT=0 禁止自动提交
Query OK, 0 rows affected (0.00 sec)
mysql> show variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit | OFF |
+---------------+-------+
1 row in set (0.00 sec)
~~~

~~~mysql
mysql> SET AUTOCOMMIT=1; #SET AUTOCOMMIT=1 开启自动提交
Query OK, 0 rows affected (0.00 sec)
mysql> show variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit | ON |
+---------------+-------+
1 row in set (0.01 sec)
~~~

### 事务常见操作方式 

简单银行用户表

> - 提前准备
>
>     ~~~mysql
>     ## Centos 7 云服务器，默认开启3306 mysqld服务
>     [whb@VM-0-3-centos ~]$ sudo netstat -nltp
>     [sudo] password for whb:
>     Active Internet connections (only servers)
>     Proto Recv-Q Send-Q Local Address Foreign Address State
>     PID/Program name
>     tcp6 0 0 :::3306 :::* LISTEN 30415/mysqld
>                                             
>     ## 使用win cmd远程访问Centos 7云服务器，mysqld服务(需要win上也安装了MySQL，这里看到结果即可)
>     ## 注意，使用本地mysql客户端，可能看不到链接效果，本地可能使用域间套接字，查不到链接
>     C:\Users\whb>mysql -uroot -p -h42.192.83.143
>     Enter password: ***********
>                                             
>     Welcome to the MySQL monitor. Commands end with ; or \g.
>     Your MySQL connection id is 3484
>     Server version: 5.7.33 MySQL Community Server (GPL)
>     Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.
>     Oracle is a registered trademark of Oracle Corporation and/or its
>     affiliates. Other names may be trademarks of their respective
>     owners.
>     Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
>     
>     
>     ## 使用netstat查看链接情况，可知：mysql本质是一个客户端进程
>     [whb@VM-0-3-centos ~]$ sudo netstat -ntp
>     Active Internet connections (w/o servers)
>     Proto Recv-Q Send-Q Local Address Foreign Address State PID/Program name
>     tcp6 0 0 172.17.0.3:3306 113.132.141.236:19354 ESTABLISHED  30415/mysqld
>                                             
>     -- 必须设置，否则后续实验无法进行
>     ## 为了便于演示，我们将mysql的默认隔离级别设置成读未提交。
>     ## 具体操作我们后面专门会讲，现在已使用为主。
>     mysql> set global transaction isolation level READ UNCOMMITTED;
>     Query OK, 0 rows affected (0.00 sec)
>                                             
>     mysql> quit
>     Bye
>     ##需要重启终端，进行查看
>     mysql> select @@tx_isolation;
>     +------------------+
>     | @@tx_isolation |
>     +------------------+
>     | READ-UNCOMMITTED |
>     +------------------+
>     1 row in set, 1 warning (0.00 sec)
>     ~~~
>
> - 创建测试表
>     ~~~mysql
>     create table if not exists account(
>     id int primary key,
>     name varchar(50) not null default '',
>     blance decimal(10,2) not null default 0.0
>     )ENGINE=InnoDB DEFAULT CHARSET=UTF8;
>     ~~~
>
> - 正常演示 - 证明事务的开始与回滚
>     ~~~mysql
>     mysql> show variables like 'autocommit'; -- 查看事务是否自动提交。我们故意设置成自动提交，看看
>     该选项是否影响begin
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | ON |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> start transaction; -- 开始一个事务begin也可以，推荐begin
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> savepoint save1; -- 创建一个保存点save1
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> insert into account values (1, '张三', 100); -- 插入一条记录
>     Query OK, 1 row affected (0.05 sec)
>     mysql> savepoint save2; -- 创建一个保存点save2
>     Query OK, 0 rows affected (0.01 sec)
>     mysql> insert into account values (2, '李四', 10000); -- 在插入一条记录
>     Query OK, 1 row affected (0.00 sec)
>     mysql> select * from account; -- 两条记录都在了
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> rollback to save2; -- 回滚到保存点save2
>     Query OK, 0 rows affected (0.03 sec)
>     mysql> select * from account; -- 一条记录没有了
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     mysql> rollback; -- 直接rollback，回滚在最开始
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> select * from account; -- 所有刚刚的记录没有了
>     Empty set (0.00 sec)
>                                             
>     ~~~
>
> - 非正常演示1 - 证明未commit，客户端崩溃，MySQL自动会回滚（隔离级别设置为读未提交）
>     ~~~mysql
>     -- 终端A
>     mysql> select * from account; -- 当前表内无数据
>     Empty set (0.00 sec)
>     mysql> show variables like 'autocommit'; -- 依旧自动提交
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | ON |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> begin; --开启事务
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> insert into account values (1, '张三', 100); -- 插入记录
>     Query OK, 1 row affected (0.00 sec)
>     mysql> select * from account; --数据已经存在，但没有commit，此时同时查看终端B
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     mysql> Aborted -- ctrl + \ 异常终止MySQL
>     --终端B
>     mysql> select * from account; --终端A崩溃前
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     mysql> select * from account; --数据自动回滚
>     Empty set (0.00 sec)
>     ~~~
>
> - 非正常演示2 - 证明commit了，客户端崩溃，MySQL数据不会在受影响，已经持久化
>     ~~~mysql
>     --终端 A
>     mysql> show variables like 'autocommit'; -- 依旧自动提交
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | ON |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> select * from account; -- 当前表内无数据
>     Empty set (0.00 sec)
>     mysql> begin; -- 开启事务
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> insert into account values (1, '张三', 100); -- 插入记录
>     Query OK, 1 row affected (0.00 sec)
>     mysql> commit; --提交事务
>     Query OK, 0 rows affected (0.04 sec)
>     mysql> Aborted -- ctrl + \ 异常终止MySQL
>     --终端 B
>     mysql> select * from account; --数据存在了，所以commit的作用是将数据持久化到MySQL中
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>                                             
>     ~~~
>
> - 非正常演示3 - 对比试验。证明begin操作会自动更改提交方式，不会受MySQL是否自动提交影响
>     ~~~mysql
>     -- 终端 A
>     mysql> select *from account; --查看历史数据
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     mysql> show variables like 'autocommit'; --查看事务提交方式
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | ON |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> set autocommit=0; --关闭自动提交
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> show variables like 'autocommit'; --查看关闭之后结果
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | OFF |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> begin; --开启事务
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> insert into account values (2, '李四', 10000); --插入记录
>     Query OK, 1 row affected (0.00 sec)
>     mysql> select *from account; --查看插入记录，同时查看终端B
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> Aborted --再次异常终止
>     -- 终端B
>     mysql> select * from account; --终端A崩溃前
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> select * from account; --终端A崩溃后，自动回滚
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     ~~~
>
> - 非正常演示4 - 证明单条 SQL 与事务的关系
>     ~~~mysql
>     --实验一
>     -- 终端A
>     mysql> select * from account;
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     mysql> show variables like 'autocommit';
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | ON |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> set autocommit=0; --关闭自动提交
>     Query OK, 0 rows affected (0.00 sec)
>     mysql> insert into account values (2, '李四', 10000); --插入记录
>     Query OK, 1 row affected (0.00 sec)
>     mysql> select *from account; --查看结果，已经插入。此时可以在查看终端B
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> ^DBye --ctrl + \ or ctrl + d,终止终端
>     --终端B
>     mysql> select * from account; --终端A崩溃前
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> select * from account; --终端A崩溃后
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     -- 实验二
>     --终端A
>     mysql> show variables like 'autocommit'; --开启默认提交
>     +---------------+-------+
>     | Variable_name | Value |
>     +---------------+-------+
>     | autocommit | ON |
>     +---------------+-------+
>     1 row in set (0.00 sec)
>     mysql> select * from account;
>     +----+--------+--------+
>     | id | name | blance |
>     +----+--------+--------+
>     | 1 | 张三 | 100.00 |
>     +----+--------+--------+
>     1 row in set (0.00 sec)
>     mysql> insert into account values (2, '李四', 10000);
>     Query OK, 1 row affected (0.01 sec)
>     mysql> select *from account; --数据已经插入
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> Aborted --异常终止
>     --终端B
>     mysql> select * from account; --终端A崩溃前
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     mysql> select * from account; --终端A崩溃后，并不影响，已经持久化。autocommit起作用
>     +----+--------+----------+
>     | id | name | blance |
>     +----+--------+----------+
>     | 1 | 张三 | 100.00 |
>     | 2 | 李四 | 10000.00 |
>     +----+--------+----------+
>     2 rows in set (0.00 sec)
>     ~~~
>
> - 结论
>
>     > 只要输入begin或者start transaction，事务便必须要通过commit提交，才会持久化，与是否设置set autocommit无关。 
>     >
>     > 事务可以手动回滚，同时，当操作异常，MySQL会自动回滚 
>     >
>     > 对于 InnoDB 每一条 SQL 语言都默认封装成事务，自动提交。（select有特殊情况，因为 MySQL 有 MVCC ） 
>     >
>     > 从上面的例子，我们能看到事务本身的原子性(回滚)，持久性(commit) 
>     >
>     > 那么隔离性？一致性？
>
> - 事务操作注意事项
>
>     > 如果没有设置保存点，也可以回滚，只能回滚到事务的开始。直接使用 rollback(前提是事务还没有提交) 
>     >
>     > 如果一个事务被提交了（commit），则不可以回退（rollback） 可以选择回退到哪个保存点 
>     >
>     > InnoDB 支持事务， MyISAM 不支持事务 
>     >
>     > 开始事务可以使 start transaction 或者 begin



### 事务隔离级别

#### 如何理解隔离性1

- MySQL服务可能会同时被多个客户端进程(线程)访问，访问的方式以事务方式进行  
- 一个事务可能由多条SQL构成，也就意味着，任何一个事务，都有执行前，执行中，执行后的阶段。而所谓的原 子性，其实就是让用户层，要么看到执行前，要么看到执行后。执行中出现问题，可以随时回滚。所以单个事 务，对用户表现出来的特性，就是原子性。 
- 但，毕竟所有事务都要有个执行过程，那么在多个事务各自执行多个SQL的时候，就还是有可能会出现互相影响 的情况。比如：多个事务同时访问同一张表，甚至同一行数据。 
- 就如同你妈妈给你说：你要么别学，要学就学到最好。至于你怎么学，中间有什么困难，你妈妈不关心。那么 你的学习，对你妈妈来讲，就是原子的。那么你学习过程中，很容易受别人干扰，此时，就需要将你的学习隔 离开，保证你的学习环境是健康的。 
- 数据库中，为了保证事务执行过程中**尽量不受干扰**，就有了一个重要特征：隔离性 
- 数据库中，允许事务受不同程度的干扰，就有了一种重要特征：隔离级别

#### 隔离级别

- **读未提交【Read Uncommitted】：** 在该隔离级别，所有的事务都可以看到其他事务没有提交的执行结果。 （实际生产中不可能使用这种隔离级别的），但是相当于没有任何隔离性，也会有很多并发问题，如**脏读，幻 读，不可重复读**等，我们上面为了做实验方便，用的就是这个隔离性。 
- **读提交【Read Committed】 ：**该隔离级别是大多数数据库的默认的隔离级别（不是 MySQL 默认的）。它满 足了隔离的简单定义:一个事务只能看到其他的已经提交的事务所做的改变。这种隔离级别会引起不可重复读， 即一个事务执行时，如果多次 select， 可能得到不同的结果。 
- **可重复读【Repeatable Read】：** 这是 MySQL 默认的隔离级别，它确保同一个事务，在执行中，多次读取操 作数据时，会看到同样的数据行。但是会有幻读问题。 
- **串行化【Serializable】:** 这是事务的最高隔离级别，它通过强制事务排序，使之不可能相互冲突，从而解决了 幻读的问题。它在每个读的数据行上面加上共享锁，。但是可能会导致超时和锁竞争（这种隔离级别太极端， 实际生产基本不使用）

~~~
脏读：一个事务在执行中，读到另一个执行中事务的更新(或其他操作)但是未commit的数据，这种现象叫做脏读(dirty read)。

读到了不应该读到的信息，甚至对这条信息进行了业务处理。比如，使用读未提交机制，用户1上传错了一条错误信息，在还未来得及删除时，用户2查询到了这条信息，并保存了下来，进行了处理。这条信息用户1删除之后，在用户2那里并不知道，并进行了处理。就是脏读
------
幻读：可重复读时，由于另一个事务进行了insert+commit，导致每次查询会多出来数据，就是幻读。

不可重复读：同一个事务内，同样的读取，在不同的时间段(依旧还在事务操作中！)，读取到了不同的值，这种现象叫做不可重复读(non reapeatable read)！！
~~~





隔离级别如何实现：隔离，基本都是通过锁实现的，不同的隔离级别，锁的使用是不同的。常见有，表锁，行锁，读 锁，写锁，间隙锁(GAP),Next-Key锁(GAP+行锁)等。不过，我们目前现有这个认识就行，先关注上层使用。

#### 查看与设置隔离性

~~~mysql
-- 查看
mysql> SELECT @@global.tx_isolation; --查看全局隔级别
+-----------------------+
| @@global.tx_isolation |
+-----------------------+
| REPEATABLE-READ |
+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @@session.tx_isolation; --查看会话(当前)全局隔级别
+------------------------+
| @@session.tx_isolation |
+------------------------+
| REPEATABLE-READ |
+------------------------+
1 row in set, 1 warning (0.00 sec)
mysql> SELECT @@tx_isolation; --默认同上
+-----------------+
| @@tx_isolation |
+-----------------+
| REPEATABLE-READ |
+-----------------+
1 row in set, 1 warning (0.00 sec)

-- 设置
-- 设置当前会话 or 全局隔离级别语法
SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED |
REPEATABLE READ | SERIALIZABLE}

-- 设置当前会话隔离性，另起一个会话，看不多，只影响当前会话
mysql> set session transaction isolation level serializable; -- 串行化
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@global.tx_isolation; -- 全局隔离性还是RR
+-----------------------+
| @@global.tx_isolation |
+-----------------------+
| REPEATABLE-READ |
+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @@session.tx_isolation; --会话隔离性成为串行化
+------------------------+
| @@session.tx_isolation |
+------------------------+
| SERIALIZABLE |
+------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @@tx_isolation; --同上
+----------------+
| @@tx_isolation |
+----------------+
| SERIALIZABLE |
+----------------+
1 row in set, 1 warning (0.00 sec)

-- 设置全局隔离性，另起一个会话，会被影响
mysql> set global transaction isolation level READ UNCOMMITTED;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@global.tx_isolation;
+-----------------------+
| @@global.tx_isolation |
+-----------------------+
| READ-UNCOMMITTED |
+-----------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT @@session.tx_isolation;
+------------------------+
| @@session.tx_isolation |
+------------------------+
| READ-UNCOMMITTED |
+------------------------+
1 row in set, 1 warning (0.00 sec)
mysql> SELECT @@tx_isolation;
+------------------+
| @@tx_isolation |
+------------------+
| READ-UNCOMMITTED |
+------------------+
1 row in set, 1 warning (0.00 sec)
-- 注意，如果没有现象，关闭mysql客户端，重新连接。
~~~

#### 读未提交【Read Uncommitted】

~~~mysql
-- 几乎没有加锁，虽然效率高，但是问题太多，严重不建议采用

-- 终端A
-- 设置隔离级别为 读未提交
mysql> set global transaction isolation level read uncommitted;
Query OK, 0 rows affected (0.00 sec)

-- 重启客户端
mysql> select @@tx_isolation;
+------------------+
| @@tx_isolation |
+------------------+
| READ-UNCOMMITTED |
+------------------+
1 row in set, 1 warning (0.00 sec)

mysql> select * from account;
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 100.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> begin; -- 开启事务
Query OK, 0 rows affected (0.00 sec)

mysql> update account set blance=123.0 where id=1; -- 更新指定行
Query OK, 1 row affected (0.05 sec)
Rows matched: 1 Changed: 1 Warnings: 0
-- 没有commit哦！！！


-- 终端B
mysql> begin;
mysql> select * from account;
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 123.00 | -- 读到终端A更新但是未commit的数据[insert，delete同样]
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)
-- 一个事务在执行中，读到另一个执行中事务的更新(或其他操作)但是未commit的数据，这种现象叫做脏读(dirty read)

~~~

#### 读提交【Read Committed】

~~~mysql
-- 终端A

mysql> set global transaction isolation level read committed;
Query OK, 0 rows affected (0.00 sec)


-- 重启客户端
mysql> select * from account; --查看当前数据
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 123.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> begin; -- 手动开启事务，同步的开始终端B事务
Query OK, 0 rows affected (0.00 sec)

mysql> update account set blance=321.0 where id=1; -- 更新张三数据
Query OK, 1 row affected (0.00 sec)
Rows matched: 1 Changed: 1 Warnings: 0

-- 切换终端到终端B，查看数据。

mysql> commit; -- commit提交！
Query OK, 0 rows affected (0.01 sec)

-- 切换终端到终端B，再次查看数据。


-- 终端B
mysql> begin; -- 手动开启事务，和终端A一前一后
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; -- 终端A commit之前，查看不到
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 123.00 | --老的值
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

-- 终端A commit之后，看到了！

-- but，此时还在当前事务中，并未commit，那么就造成了，同一个事务内，同样的读取，在不同的时间段(依旧还在事务操作中！)，读取到了不同的值，这种现象叫做不可重复读(non reapeatable read)！！（这个是问题吗？？）
mysql> select *from account;
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 321.00 | --新的值
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)
~~~

#### 可重复读【Repeatable Read】

~~~mysql
-- 终端A
mysql> set global transaction isolation level repeatable read; -- 设置全局隔离级别RR
Query OK, 0 rows affected (0.01 sec)

-- 关闭终端重启
mysql> select @@tx_isolation;
+-----------------+
| @@tx_isolation |
+-----------------+
| REPEATABLE-READ | -- 隔离级别RR
+-----------------+
1 row in set, 1 warning (0.00 sec)

mysql> select *from account; -- 查看当前数据
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> begin; -- 开启事务，同步的，终端B也开始事务
Query OK, 0 rows affected (0.00 sec)

mysql> update account set blance=4321.0 where id=1; -- 更新数据
Query OK, 1 row affected (0.00 sec)
Rows matched: 1 Changed: 1 Warnings: 0

-- 切换到终端B，查看另一个事务是否能看到

mysql> commit; -- 提交事务
-- 切换终端到终端B，查看数据。

-- 终端B
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; -- 终端A中事务 commit之前，查看当前表中数据，数据未更新
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> select * from account; -- 终端A中事务 commit 之后，查看当前表中数据，数据未更新
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)
-- 可以看到，在终端B中，事务无论什么时候进行查找，看到的结果都是一致的，这叫做可重复读！

mysql> commit; -- 结束事务
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; -- 再次查看，看到最新的更新数据
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

----------------------------------------------------------------
-- 如果将上面的终端A中的update操作，改成insert操作，会有什么问题？？

-- 终端A
mysql> select *from account;
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> begin; -- 开启事务，终端B同步开启
Query OK, 0 rows affected (0.00 sec)

mysql> insert into account (id,name,blance) values(3, '王五', 5432.0);
Query OK, 1 row affected (0.00 sec)

-- 切换到终端B，查看另一个事务是否能看到

mysql> commit; -- 提交事务
Query OK, 0 rows affected (0.00 sec)

-- 切换终端到终端B，查看数据。

mysql> select * from account;
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
| 3 | 王五 | 5432.00 |
+----+--------+----------+
3 rows in set (0.00 sec) 


-- 终端B
mysql> begin; -- 开启事务
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; -- 终端A commit前 查看
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> select * from account; -- 终端A commit后 查看
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> select * from account; 
-- 多次查看，发现终端A在对应事务中insert的数据，在终端B的事务周期中，也没有什么影响，也符合可重复的特点。但是，一般的数据库在可重复读情况的时候，无法屏蔽其他事务insert的数据(为什么？-----快照读）因为隔离性实现是对数据加锁完成的，而insert待插入的数据因为并不存在，那么一般加锁无法屏蔽这类问题),会造成虽然大部分内容是可重复读的，但是insert的数据在可重复读情况被读取出来，导致多次查找时，会多查找出来新的记录，就如同产生了幻觉。这种现象，叫做幻读(phantom read)。很明显，MySQL在RR级别的时候，是解决了幻读问题的(解决的方式是用Next-Key锁(GAP+行锁)解决的。这块比较难，有兴趣同学了解一下)。
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
+----+--------+----------+
2 rows in set (0.00 sec)

mysql> commit; -- 结束事务
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; --看到更新
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
| 3 | 王五 | 5432.00 |
+----+--------+----------+
3 rows in set (0.00 sec)
~~~

#### 串行化【serializable】

~~~mysql
-- 对所有操作全部加锁，进行串行化，不会有问题，但是只要串行化，效率很低，几乎完全不会被采用

-- 终端A

mysql> set global transaction isolation level serializable;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@tx_isolation;
+----------------+
| @@tx_isolation |
+----------------+
| SERIALIZABLE |
+----------------+
1 row in set, 1 warning (0.00 sec)

mysql> begin; -- 开启事务，终端B同步开启
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; -- 两个读取不会串行化，共享锁
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
| 3 | 王五 | 5432.00 |
+----+--------+----------+
3 rows in set (0.00 sec)

mysql> update account set blance=1.00 where id=1; -- 终端A中有更新或者其他操作，会阻塞。直到终端B事务提交。
Query OK, 1 row affected (18.19 sec)
Rows matched: 1 Changed: 1 Warnings: 0
 
 
-- 终端B

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from account; -- 两个读取不会串行化
+----+--------+----------+
| id | name | blance |
+----+--------+----------+
| 1 | 张三 | 4321.00 |
| 2 | 李四 | 10000.00 |
| 3 | 王五 | 5432.00 |
+----+--------+----------+
3 rows in set (0.00 sec)

mysql> commit; -- 提交之后，终端A中的update才会提交。
Query OK, 0 rows affected (0.00 sec)
~~~

![image-20230226124210739](%E5%9B%BE%E7%89%87/MySQL/image-20230226124210739.png)

#### 总结 

> - 其中隔离级别越严格，安全性越高，但数据库的并发性能也就越低，往往需要在两者之间找一个平衡点。 
> - 不可重复读的重点是修改和删除：同样的条件, 你读取过的数据,再次读取出来发现值不一样了 幻读的重点在于 新增：同样的条件, 第1次和第2次读出来的记录数不一样 
> - 说明： mysql 默认的隔离级别是可重复读,一般情况下不要修改 
> - 上面的例子可以看出，事务也有长短事务这样的概念。事务间互相影响，指的是事务在并行执行的时候，即都 没有commit的时候，影响会比较大。
> - ![image-20230226124255338](%E5%9B%BE%E7%89%87/MySQL/image-20230226124255338.png)

#### 一致性(Consistency) 

> - 事务执行的结果，必须使数据库从一个一致性状态，变到另一个一致性状态。当数据库只包含事务成功提交的 结果时，数据库处于一致性状态。如果系统运行发生中断，某个事务尚未完成而被迫中断，而改未完成的事务 对数据库所做的修改已被写入数据库，此时数据库就处于一种不正确（不一致）的状态。因此一致性是通过原 子性来保证的。
> -  其实一致性和用户的业务逻辑强相关，一般MySQL提供技术支持，但是一致性还是要用户业务逻辑做支撑，也 就是，一致性，是由用户决定的。 
> - 而技术上，通过AID保证C

#### 推荐阅读

~~~mysql
https://www.jianshu.com/p/398d788e1083
https://tech.meituan.com/2014/08/20/innodb-lock.html
https://www.cnblogs.com/aspirant/p/9177978.html


备注：
基本上，了解了上面的知识，在MySQL事务使用上，肯定没有问题。不过，这块设计很优秀，也是面试中可能被问到的，一般学生，如果能说出上面的内容，就已经不错了。但是如果我们能更详细，更深入的谈论这个问题，那么对我们的面试与学习肯定是大大的有帮助。
不过接下来的内容，会比较难一些，听的不明白，也没有太大问题。


如果有时间，可以现场给学生演示一下，在RR级别的时候，多个事务的update，多个事务的insert,多个事务的delete，是否会有加锁现象。
现象结果是，update，insert，delete之间是会有加锁现象的，但是select和这些操作是不冲突的。这就是通过读写锁(锁有行锁或者表锁)+MVCC完成隔离性。
~~~



### 如何理解隔离性2

数据库并发的场景有三种： 

> - 读-读 ：不存在任何问题，也不需要并发控制 
> - 读-写 ：有线程安全问题，可能会造成事务隔离性问题，可能遇到脏读，幻读，不可重复读 
> - 写-写 ：有线程安全问题，可能会存在更新丢失问题，比如第一类更新丢失，第二类更新丢失(后面补充)

### 读-写

多版本并发控制（ MVCC ）是一种用来解决 读-写冲突 的**无锁并发控制** 

为事务分配单向增长的事务ID，为每个修改保存一个版本，版本与事务ID关联，读操作只读该事务开始前的数据库的 快照。 所以 MVCC 可以为数据库解决以下问题 

- 在并发读写数据库时，可以做到在读操作时不用阻塞写操作，写操作也不用阻塞读操作，提高了数据库并发读 写的性能 
- 同时还可以解决脏读，幻读，不可重复读等事务隔离问题，但**不能解决更新丢失**问题 

理解 MVCC 需要知道三个前提知识： 

- 3个记录隐藏字段 
- undo 日志 
- Read View

#### 3个记录隐藏列字段

- DB_TRX_ID ：6 byte，最近修改( 修改/插入 )事务ID，记录创建这条记录/最后一次修改该记录的事务ID 
- DB_ROLL_PTR : 7 byte，回滚指针，指向这条记录的上一个版本（简单理解成，指向历史版本就行，这些数据一 般在 undo log 中） 比特科技 name age DB_TRX_ID(创建该记录的事务 ID) 
- DB_ROW_ID(隐式主 键) DB_ROLL_PTR(回滚指 针) 张三 28 null 1 null DB_ROW_ID : 6 byte，隐含的自增ID（隐藏主键），如果数据表没有主键， InnoDB 会自动以 DB_ROW_ID 产生一 个聚簇索引 
- 补充：实际还有一个删除flag隐藏字段, 既记录被更新或删除并不代表真的删除，而是删除flag变了

假设测试表结构是：

~~~mysql
mysql> create table if not exists student(
name varchar(11) not null,
age int not null
);

mysql> insert into student (name, age) values ('张三', 28);
Query OK, 1 row affected (0.05 sec)

mysql> select * from student;
+--------+-----+
| name | age |
+--------+-----+
| 张三 | 28 |
+--------+-----+
1 row in set (0.00 sec)

~~~

上面的描述的意思是：

![image-20230226125445385](%E5%9B%BE%E7%89%87/MySQL/image-20230226125445385.png)

我们目前并不知道创建该记录的事务ID，隐式主键，我们就默认设置成null，1。第一条记录也没有其他版本，我们 设置回滚指针为null。

#### undo日志

这里不想细讲，但是有一件事情得说清楚， MySQL 将来是以服务进程的方式，在内存中运行。我们之前所讲的所有 机制：索引，事务，隔离性，日志等，都是在内存中完成的，即在 MySQL 内部的相关缓冲区中，保存相关数据，完 成各种判断操作。然后在合适的时候，将相关数据刷新到磁盘当中的。 

所以，我们这里理解undo log，简单理解成，就是 MySQL 中的一段内存缓冲区，用来保存日志数据的就行。

#### 模拟 MVCC

现在有一个事务10(仅仅为了好区分)，对student表中记录进行修改(update)：将name(张三)改成name(李四)。 

> - 事务10,因为要修改，所以要先给该记录加行锁。 
> - 修改前，现将改行记录拷贝到undo log中，所以，undo log中就有了一行副本数据。(原理就是写时拷贝) 
> - 所以现在 MySQL 中有两行同样的记录。现在修改原始记录中的name，改成 '李四'。并且修改原始记录的隐藏字 段 DB_TRX_ID 为当前 事务10 的ID, 我们默认从 10 开始，之后递增。而原始记录的回滚指针 DB_ROLL_PTR 列， 里面写入undo log中副本数据的地址，从而指向副本记录，既表示我的上一个版本就是它。 
> - 事务10提交，释放锁。
>
> ![image-20230226125743222](%E5%9B%BE%E7%89%87/MySQL/image-20230226125743222.png)

备注：此时，最新的记录是’李四‘那条记录。 

现在又有一个事务11，对student表中记录进行修改(update)：将age(28)改成age(38)。 

> - 事务11,因为也要修改，所以要先给该记录加行锁。（该记录是那条？）
>
> - 修改前，现将改行记录拷贝到undo log中，所以，undo log中就又有了一行副本数据。此时，新的副本，我们 采用头插方式，插入undo log。 
>
> - 现在修改原始记录中的age，改成 38。并且修改原始记录的隐藏字段 DB_TRX_ID 为当前 事务11 的ID。而原始记 录的回滚指针 DB_ROLL_PTR 列，里面写入undo log中副本数据的地址，从而指向副本记录，既表示我的上一个 版本就是它。 
>
> - 事务11提交，释放锁。
>
> ![image-20230226125914294](%E5%9B%BE%E7%89%87/MySQL/image-20230226125914294.png)

这样，我们就有了一个基于链表记录的历史版本链。所谓的回滚，无非就是用历史数据，覆盖当前数据。 

上面的一个一个版本，我们可以称之为一个一个的快照。

~~~~mysql
##一些思考
上面是以更新（`upadte`）主讲的,如果是`delete`呢？一样的，别忘了，删数据不是清空，而是设置flag为删除即可。也可以形成版本。
如果是`insert`呢？因为`insert`是插入，也就是之前没有数据，那么`insert`也就没有历史版本。但是一般为了回滚操作，insert的数据也是要被放入undo log中，如果当前事务commit了，那么这个undo log 的历史insert记录就可以被清空了。
总结一下，也就是我们可以理解成，`update`和`delete`可以形成版本链，`insert`暂时不考虑。
那么`select`呢？
首先，`select`不会对数据做任何修改，所以，为`select`维护多版本，没有意义。不过，此时有个问题，就是：
select读取，是读取最新的版本呢？还是读取历史版本？

当前读：读取最新的记录，就是当前读。增删改，都叫做当前读，select也有可能当前读，比如：select lock in sharemode(共享锁), select for update （这个好理解，我们后面不讨论）
快照读：读取历史版本(一般而言)，就叫做快照读。(这个我们后面重点讨论)

我们可以看到，在多个事务同时删改查的时候，都是当前读，是要加锁的。那同时有select过来，如果也要读取最新版(当前读)，那么也就需要加锁，这就是串行化。
但如果是快照读，读取历史版本的话，是不受加锁限制的。也就是可以并行执行！换言之，提高了效率，即MVCC的意义所在。
那么，是什么决定了，select是当前读，还是快照读呢？隔离级别!
那为什么要有隔离级别呢？
事务都是原子的。所以，无论如何，事务总有先有后。
但是经过上面的操作我们发现，事务从begin->CURD->commit，是有一个阶段的。也就是事务有执行前，执行中，执行后的阶段。但，不管怎么启动多个事务，总是有先有后的。
那么多个事务在执行中，CURD操作是会交织在一起的。那么，为了保证事务的“有先有后”，是不是应该让不同的事务看到它该看到的内容，这就是所谓的隔离性与隔离级别要解决的问题。
先来的事务，应不应该看到后来的事务所做的修改呢?
~~~~

那么，如何保证，不同的事务，看到不同的内容呢？也就是如何如何实现隔离级别？

#### Read View

Read View就是事务进行 快照读 操作的时候生产的 读视图 (Read View)，在该事务执行的快照读的那一刻，会生成数据库系统当前的一个快照，记录并维护系统当前活跃事务的ID(当每个事务开启时，都会被分配一个ID, 这个ID是递增 的，所以最新的事务，ID值越大) 

Read View 在 MySQL 源码中,就是一个类，本质是用来进行可见性判断的。 即当我们某个事务执行快照读的时候，对 该记录创建一个 Read View 读视图，把它比作条件,用来判断当前事务能够看到哪个版本的数据，既可能是当前最新 的数据，也有可能是该行记录的 undo log 里面的某个版本的数据。

下面是 ReadView 结构,但为了减少同学们负担，我们简化一下

~~~mysql
class ReadView {
// 省略...
private:
/** 高水位，大于等于这个ID的事务均不可见*/
trx_id_t m_low_limit_id
/** 低水位：小于这个ID的事务均可见 */
trx_id_t m_up_limit_id;
/** 创建该 Read View 的事务ID*/
trx_id_t m_creator_trx_id;
/** 创建视图时的活跃事务id列表*/
ids_t m_ids;
/** 配合purge，标识该视图不需要小于m_low_limit_no的UNDO LOG，
* 如果其他视图也不需要，则可以删除小于m_low_limit_no的UNDO LOG*/
trx_id_t m_low_limit_no;
/** 标记视图是否被关闭*/
bool m_closed;
// 省略...
};
~~~

~~~mysql
m_ids; //一张列表，用来维护Read View生成时刻，系统正活跃的事务ID
up_limit_id; //记录m_ids列表中事务ID最小的ID(没有写错)
low_limit_id; //ReadView生成时刻系统尚未分配的下一个事务ID，也就是目前已出现过的事务ID的最大值+1(也没
有写错)
creator_trx_id //创建该ReadView的事务ID
~~~

我们在实际读取数据版本链的时候，是能读取到每一个版本对应的事务ID的，即：当前记录的 DB_TRX_ID 。 

那么，我们现在手里面有的东西就有，当前快照读的 ReadView 和 版本链中的某一个记录的 DB_TRX_ID 。 

所以现在的问题就是，当前快照读，应不应该读到当前版本记录。一张图，解决所有问题！

![image-20230226131343521](%E5%9B%BE%E7%89%87/MySQL/image-20230226131343521.png)



对应源码策略：

![image-20230226131402595](%E5%9B%BE%E7%89%87/MySQL/image-20230226131402595.png)

如果查到不应该看到当前版本，接下来就是遍历下一个版本，直到符合条件，即可以看到。上面的 readview 是当你 进行select的时候，会自动形成。

#### 整体流程

**假设当前有条记录：**

![image-20230226131432690](%E5%9B%BE%E7%89%87/MySQL/image-20230226131432690.png)

**事务操作：**

![image-20230226131455811](%E5%9B%BE%E7%89%87/MySQL/image-20230226131455811.png)

> - 事务4：修改name(张三) 变成name(李四)
>
> - 当 事务2 对某行数据执行了 快照读 ，数据库为该行数据生成一个 Read View 读视图
>     ~~~mysql
>     //事务2的 Read View
>     m_ids; // 1,3
>     up_limit_id; // 1
>     low_limit_id; // 4 + 1 = 5，原因：ReadView生成时刻，系统尚未分配的下一个事务ID
>     creator_trx_id // 2
>     ~~~
>
>     

**此时版本链是：**

![image-20230226131612740](%E5%9B%BE%E7%89%87/MySQL/image-20230226131612740.png)

> - 有事务4修改过该行记录，并在事务2执行快照读前，就提交了事务。
>     ![image-20230226131636957](%E5%9B%BE%E7%89%87/MySQL/image-20230226131636957.png)
>
> - 我们的事务2在快照读该行记录的时候，就会拿该行记录的 DB_TRX_ID 去跟 up_limit_id,low_limit_id和活 跃事务ID列表(trx_list) 进行比较，判断当前事务2能看到该记录的版本。
>     ~~~mys
>     //事务2的 Read View
>     m_ids; // 1,3
>     up_limit_id; // 1
>     low_limit_id; // 4 + 1 = 5，原因：ReadView生成时刻，系统尚未分配的下一个事务ID
>     creator_trx_id // 2
>     //事务4提交的记录对应的事务ID
>     DB_TRX_ID=4
>     //比较步骤
>     DB_TRX_ID（4）< up_limit_id（1） ? 不小于，下一步
>     DB_TRX_ID（4）>= low_limit_id(5) ? 不大于，下一步
>     m_ids.contains(DB_TRX_ID) ? 不包含，说明，事务4不在当前的活跃事务中。
>     //结论
>     故，事务4的更改，应该看到。
>     所以事务2能读到的最新数据记录是事务4所提交的版本，而事务4提交的版本也是全局角度上最新的版本
>                                                     
>     ~~~
>
>     

### RR 与 RC的本质区别

#### 当前读和快照读在RR级别下的区别

下面的代码经过测试，是完全没有问题的。要不要现场测试，主要看上课的时间允不允许

> select * from user lock in share mode ,以加共享锁方式进行读取，对应的就是当前读。此处只作为测 试使用，不重讲。

测试表：

~~~mysql
--设置RR模式下测试
mysql> set global transaction isolation level REPEATABLE READ;
Query OK, 0 rows affected (0.00 sec)
--重启终端
mysql> select @@tx_isolation;
+-----------------+
| @@tx_isolation |
+-----------------+
| REPEATABLE-READ |
+-----------------+
1 row in set, 1 warning (0.00 sec)
--依旧用之前的表
create table if not exists account(
id int primary key,
name varchar(50) not null default '',
blance decimal(10,2) not null default 0.0
)ENGINE=InnoDB DEFAULT CHARSET=UTF8;

--插入一条记录，用来测试
mysql> insert into user (id, age, name) values (1, 15,'黄蓉');
Query OK, 1 row affected (0.00 sec)
~~~

测试用例1-表1：

![image-20230226132140858](%E5%9B%BE%E7%89%87/MySQL/image-20230226132140858.png)

测试用例2-表2：

![image-20230226132150895](%E5%9B%BE%E7%89%87/MySQL/image-20230226132150895.png)

> - 用例1与用例2：唯一区别仅仅是 表1 的事务B在事务A修改age前 快照读 过一次age数据 
>
> - 而 表2 的事务B在事务A修改age前没有进行过快照读。

结论：

> - 事务中快照读的结果是非常依赖该事务首次出现快照读的地方，即某个事务中首次出现快照读，决定该事务后 续快照读结果的能力 
> - delete同样如此

#### RR 与 RC的本质区别

- 正是Read View生成时机的不同，从而造成RC,RR级别下快照读的结果的不同 
- 在RR级别下的某个事务的对某条记录的第一次快照读会创建一个快照及Read View, 将当前系统活跃的其他事务 记录起来 
- 此后在调用快照读的时候，还是使用的是同一个Read View，所以只要当前事务在其他事务提交更新之前使用过 快照读，那么之后的快照读使用的都是同一个Read View，所以对之后的修改不可见； 
- 即RR级别下，快照读生成Read View时，Read View会记录此时所有其他活动事务的快照，这些事务的修改对于 当前事务都是不可见的。而早于Read View创建的事务所做的修改均是可见 
- 而在RC级别下的，事务中，每次快照读都会新生成一个快照和Read View, 这就是我们在RC级别下的事务中可以 看到别的事务提交的更新的原因 
- 总之在RC隔离级别下，是每个快照读都会生成并获取最新的Read View；而在RR隔离级别下，则是同一个事务 中的第一个快照读才会创建Read View, 之后的快照读获取的都是同一个Read View。 
- 正是RC每次快照读，都会形成Read View，所以，RC才会有不可重复读问题。

### 读-读

~~~mysql


~~~

### 写-写

现阶段，直接理解成都是当前读，当前不做深究

### 推荐阅读

~~~MYSQL
关于这块，有很好的文章，推荐大家阅读
https://blog.csdn.net/SnailMann/article/details/94724197
https://www.cnblogs.com/f-ck-need-u/archive/2018/05/08/9010872.html
https://blog.csdn.net/chenghan_yang/article/details/97630626
~~~





## 视图

视图是一个虚拟表，其内容由查询定义。同真实的表一样，视图包含一系列带有名称的列和行数据。视图的数据变 化会影响到基表，基表的数据变化也会影响到视图。

语法：

~~~mysql
create view 视图名 as select语句；
~~~

案例：

~~~mysql
create view v_ename_dname as select ename, dname
from EMP, DEPT where EMP.deptno=DEPT.deptno;
~~~

~~~mysql
select * from v_ename_dname;
+--------+------------+
| ename | dname |
+--------+------------+
| CLARK | ACCOUNTING |
| KING | ACCOUNTING |
| MILLER | ACCOUNTING |
| SMITH | RESEARCH |
| JONES | RESEARCH |
| SCOTT | RESEARCH |
| ADAMS | RESEARCH |
| FORD | RESEARCH |
| ALLEN | SALES |
| WARD | SALES |
| MARTIN | SALES |
| BLAKE | SALES |
| TURNER | SALES |
| JAMES | SALES |
+--------+------------+
~~~

**修改了视图，对基表数据有影响**

~~~mysql
update v_ename_dname set dname='sales' where ename='CLARK';
select * from EMP where ename='CLARK';

~~~

**修改了基表，对视图有影响**

~~~mysql
mysql> update EMP set deptno=20 where ename='JAMES'; -- 修改基表
Query OK, 1 row affected (0.00 sec)
Rows matched: 1 Changed: 1 Warnings: 0
mysql> select * from v_ename_dname where ename='JAMES';
+-------+----------+
| ename | dname |
+-------+----------+
| JAMES | RESEARCH | <== 视图中的数据也发生了变化

~~~

删除视图

~~~mysql
drop view 视图名；
~~~

**视图规则和限制**

> - 与表一样，必须唯一命名（不能出现同名视图或表名） 
> - 创建视图数目无限制，但要考虑复杂查询创建为视图之后的性能影响
> - 视图不能添加索引，也不能有关联的触发器或者默认值 
> - 视图可以提高安全性，必须具有足够的访问权限 
> - order by 可以用在视图中，但是如果从该视图检索数据 select 中也含有 order by ,那么该视图中的 order by 将被覆盖 
> - 视图可以和表一起使用

OJ

> - https://www.nowcoder.com/practice/b9db784b5e3d488cbd30bd78fdb2a862?tpId=82&&tqId=29806&rp=10&ru=/activity/oj&qru=/ta/sql/question-ranking



## 用户管理

![image-20230226133624865](%E5%9B%BE%E7%89%87/MySQL/image-20230226133624865.png)

### 	 用户

#### 用户信息

MySQL中的用户，都存储在系统数据库mysql的user表中

~~~mysql
mysql> use mysql;
Database changed
mysql> select host,user,authentication_string from user;
+-----------+---------------+-------------------------------------------+
| host | user | authentication_string |
+-----------+---------------+-------------------------------------------+
| localhost | root | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
| localhost | mysql.session | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| localhost | mysql.sys | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
+-----------+---------------+-------------------------------------------+
--可以通过desc user初步查看一下表结构

~~~

字段解释： 

- host： 表示这个用户可以从哪个主机登陆，如果是localhost，表示只能从本机登陆 
- user： 用户名
-  authentication_string： 用户密码通过password函数加密后的 
- *_priv： 用户拥有的权限

#### 创建用户

语法：

~~~mysql
create user '用户名'@'登陆主机/ip' identified by '密码';
~~~

案例：

~~~mysql
mysql> create user 'whb'@'localhost' identified by '12345678';
Query OK, 0 rows affected (0.06 sec)
mysql> select user,host,authentication_string from user;
+---------------+-----------+-------------------------------------------+
| user | host | authentication_string |
+---------------+-----------+-------------------------------------------+
| root | % | *A2F7C9D334175DE9AF4DB4F5473E0BD0F5FA9E75 |
| mysql.session | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| whb | localhost | *84AAC12F54AB666ECFC2A83C676908C8BBC381B1 | --新增用户
+---------------+-----------+-------------------------------------------+
4 rows in set (0.00 sec)
-- 此时便可以使用新账号新密码进行登陆啦
-- 备注：可能实际在设置密码的时候，因为mysql本身的认证等级比较高，一些简单的密码无法设置，会爆出如下报错：
-- ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
-- 解决方案：https://blog.csdn.net/zhanaolu4821/article/details/93622812
-- 查看密码设置相关要求：SHOW VARIABLES LIKE 'validate_password%';
-- 这个大家下来自己玩玩
-- 关于新增用户这里，需要大家注意，不要轻易添加一个可以从任意地方登陆的user。
~~~

#### 删除用户

语法：

~~~mysql
drop user '用户名'@'主机名'
~~~

示例：

~~~mysql
mysql> select user,host,authentication_string from user;
+---------------+-----------+-------------------------------------------+
| user | host | authentication_string |
+---------------+-----------+-------------------------------------------+
| root | % | *A2F7C9D334175DE9AF4DB4F5473E0BD0F5FA9E75 |
| mysql.session | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| whb | localhost | *84AAC12F54AB666ECFC2A83C676908C8BBC381B1 |
+---------------+-----------+-------------------------------------------+
4 rows in set (0.00 sec)
mysql> drop user whb; --尝试删除
ERROR 1396 (HY000): Operation DROP USER failed for 'whb'@'%' -- <= 直接给个用户名，不能删除，它默认是%，表示所有地方可以登陆的用户

mysql> drop user 'whb'@'localhost'; --删除用户
Query OK, 0 rows affected (0.00 sec)


mysql> select user,host,authentication_string from user;
+---------------+-----------+-------------------------------------------+
| user | host | authentication_string |
+---------------+-----------+-------------------------------------------+
| root | % | *A2F7C9D334175DE9AF4DB4F5473E0BD0F5FA9E75 |
| mysql.session | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
+---------------+-----------+-------------------------------------------+
3 rows in set (0.00 sec)

~~~

#### 修改用户密码

语法：

- 自己改自己密码
    ~~~mysql
    set password=password('新的密码');
    --自己下来试试
    
    ~~~

    

- root用户修改指定用户的密码
    ~~~mysql
    set password for '用户名'@'主机名'=password('新的密码')；
    
    ~~~

    ~~~mysql
    mysql> select host,user, authentication_string from user;
    +-----------+---------------+-------------------------------------------+
    | host | user | authentication_string |
    +-----------+---------------+-------------------------------------------+
    | % | root | *A2F7C9D334175DE9AF4DB4F5473E0BD0F5FA9E75 |
    | localhost | mysql.session | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
    | localhost | mysql.sys | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
    | localhost | whb | *84AAC12F54AB666ECFC2A83C676908C8BBC381B1 |
    +-----------+---------------+-------------------------------------------+
    4 rows in set (0.00 sec)
    mysql> set password for 'whb'@'localhost'=password('87654321');
    Query OK, 0 rows affected, 1 warning (0.00 sec)
    mysql> select host,user, authentication_string from user;
    +-----------+---------------+-------------------------------------------+
    | host | user | authentication_string |
    +-----------+---------------+-------------------------------------------+
    | % | root | *A2F7C9D334175DE9AF4DB4F5473E0BD0F5FA9E75 |
    | localhost | mysql.session | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
    | localhost | mysql.sys | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
    | localhost | whb | *5D24C4D94238E65A6407DFAB95AA4EA97CA2B199 |
    +-----------+---------------+-------------------------------------------+
    4 rows in set (0.00 sec)
    
    ~~~

    

### 数据库的权限

MySQL数据库提供的权限列表：

![image-20230226134044415](%E5%9B%BE%E7%89%87/MySQL/image-20230226134044415.png)

#### 给用户授权

刚创建的用户没有任何权限。需要给用户授权。

语法:

~~~mysql
grant 权限列表 on 库.对象名 to '用户名'@'登陆位置' [identified by '密码']
~~~

说明：

- 权限列表，多个权限用逗号分开
    ~~~mysql
    grant select on ...
    grant select, delete, create on ....
    grant all [privileges] on ... -- 表示赋予该用户在该对象上的所有权限
    
    ~~~

    

- \*.\* : 代表本系统中的所有数据库的所有对象（表，视图，存储过程等）

- 库.* : 表示某个数据库中的所有数据对象(表，视图，存储过程等)

- identified by可选。 如果用户存在，赋予权限的同时修改密码,如果该用户不存在，就是创建用户



案例：

~~~mysql
--使用root账号
--终端A
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| 57test |
| bit_index |
| ccdata_pro |
| innodb_test |
| musicserver |
| myisam_test |
| mysql |
| order_sys |
| performance_schema |
| scott |
| sys |
| test |
| vod_system |
+--------------------+
14 rows in set (0.00 sec)
mysql> use test;
Database changed
mysql> show tables;
+----------------+
| Tables_in_test |
+----------------+
| account |
| student |
| user |
+----------------+
3 rows in set (0.01 sec)
--给用户whb赋予test数据库下所有文件的select权限
mysql> grant select on test.* to 'whb'@'localhost';
Query OK, 0 rows affected (0.01 sec)
--使用whb账号
--终端B
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
+--------------------+
1 row in set (0.00 sec)
--暂停等root用户给whb赋完权之后，在查看
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| test | --赋完权之后，就能看到新的表
+--------------------+
2 rows in set (0.01 sec)
mysql> use test;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
mysql> show tables;
+----------------+
| Tables_in_test |
+----------------+
| account |
| student |
| user |
+----------------+
3 rows in set (0.00 sec)
mysql> select * from account;
+----+--------+---------+
| id | name | blance |
+----+--------+---------+
| 2 | 李四 | 321.00 |
| 3 | 王五 | 5432.00 |
| 4 | 赵六 | 543.90 |
| 5 | 赵六 | 543.90 |
+----+--------+---------+
4 rows in set (0.00 sec)
--没有删除权限
mysql> delete from account;
ERROR 1142 (42000): DELETE command denied to user 'whb'@'localhost' for table 'account'
备注：特定用户现有查看权限
mysql> show grants for 'whb'@'%';
+-----------------------------------------------+
| Grants for whb@% |
+-----------------------------------------------+
| GRANT USAGE ON *.* TO 'whb'@'%' |
| GRANT ALL PRIVILEGES ON `test`.* TO 'whb'@'%' |
+-----------------------------------------------+
2 rows in set (0.00 sec)
mysql> show grants for 'root'@'%';
+-------------------------------------------------------------+
| Grants for root@% |
+-------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION |
+-------------------------------------------------------------+
1 row in set (0.00 sec)
~~~

**注意：如果发现赋权限后，没有生效，执行如下指令：**

~~~mysql
flush privileges;

~~~

#### 回收权限

语法：

~~~Mysql
revoke 权限列表 on 库.对象名 from '用户名'@'登陆位置'；
~~~

示例：

~~~mysql
-- 回收whb对test数据库的所有权限
--root身份，终端A
mysql> revoke all on test.* from 'whb'@'localhost';
Query OK, 0 rows affected (0.00 sec)
--whb身份，终端B
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| test |
+--------------------+
2 rows in set (0.00 sec)
mysql> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
+--------------------+
1 row in set (0.00 sec)
~~~



















## C语言链接数据库



1：00：00
