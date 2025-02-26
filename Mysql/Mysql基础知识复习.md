# Mysql基础知识复习

## 一. 关键词

```
select 选择，from 从...，insert插入 （into 到..），update 修改、更新，delete 删除，table 表，
database 数据库，create创建，drop 丢弃， values 值（复数），primary key 主键，foreign key 
外键，reference 引用，where 哪里，inner 内部的 join 连接 =》内连接，left join 左连接，right 
join 右连接，group（组，分组） by.. 根据..进行分组，having 有...，order by.. 根据..进行排序，
desc（descending） 降序，asc（ascending）升序，auto_increment自增长，default 默认,null 空，
not null非空，unique 唯一的，set 设置（值），distinct 确定的（去除重复数据），between ... and 
... 在..和..之间，in 在..里面，like 像..一样(%,_)，limit 限制（分页时使用）
(position,pageSize)，show 展示,use 使用,exists 存在,count统计，sum求和，avg平均值，max最大值，
min最小值, describe描述
```

## 二. 数据类型

### 1. 整数

| MySQL数据类型 | 含义（有符号）                        |
| ------------- | ------------------------------------- |
| tinyint       | 1个字节  范围(-128~127)               |
| smallint      | 2个字节  范围(-32768~32767)           |
| mediumint     | 3个字节  范围(-8388608~8388607)       |
| int           | 4个字节  范围(-2147483648~2147483647) |
| bigint        | 8个字节  范围(+-9.22*10的18次方)      |

### 2. 小数

| MySQL数据类型 | 含义                                                         |
| ------------- | ------------------------------------------------------------ |
| float(m,d)    | 单精度浮点型    8位精度(4字节)     m总个数，d小数位          |
| double(m,d)   | 双精度浮点型    16位精度(8字节)    m总个数，d小数位          |
| decimal(m,d)  | 参数m<65 是总个数，d<30且 d<m 是小数位 ( 将来可能会看到numberic，其余decimal类似 ) |

设一个字段定义为float(6,3)，如果插入一个数123.45678,实际数据库里存的是123.457，但总个数还以实际为准，即6位。**整数部分最大是3位**，如果插入数12.123456，存储的是12.1234，如果插入12.12，存储的是12.1200

decimal(10,2)   123.45678=> 123.45  12.123456=>12.12

### 3. 字符串

| MySQL数据类型 | 含义                            |
| ------------- | ------------------------------- |
| char(n)       | 固定长度，最多255个字符         |
| varchar(n)    | 可变长度，最多65535个字符       |
| tinytext      | 可变长度，最多255个字符         |
| text          | 可变长度，最多65535个字符       |
| mediumtext    | 可变长度，最多2的24次方-1个字符 |
| longtext      | 可变长度，最多2的32次方-1个字符 |

### 4. 日期

| MySQL数据类型 | 含义                          |
| ------------- | ----------------------------- |
| date          | 日期 '2008-12-2'              |
| time          | 时间 '12:25:36'               |
| datetime      | 日期时间 '2008-12-2 22:06:44' |
| timestamp     | 自动存储记录修改时间戳        |

## 三. 数据库创建删除

### 1. 查看所有的数据库

```
show databases;   # sql语句  ；表示一条sql语句结束了
```

### 2. CREATE DATABASE [IF NOT EXISTS] 数据库名;

```
create database myschool;
create database if not exists myschool;
```

### 3. DROP DATABASE  [IF EXISTS] 数据库名;

```
drop database abcd;
drop database if exists abcd;
```

### 4. 使用(切换)数据库

USE 数据库名;

```
use train；
```

### 5. 查看某个库中的所有的表(注意:在操作表之前必须先使用数据库)

```
show tables;
```

## 四. 表创建修改删除

### 1. 创建表

    CREATE TABLE Student (
        Sno   varchar(9) ，                  
        Sname  varchar(20) ，    
        Ssex    varchar(2)，
        Sage   INT，
        Sdept  varchar(20)
    )； 

### 2. 修改表

```sql
#重命名表
alter table old_name rename new_name 
#增加字段
alter table student add number int
#删除字段
alter table student drop number
#修改字段名称
alter table student change number phonenumber int
```

### 3. 删除表

```
DROP TABLE Studnet;
```

## 五. 数据操作

### 1. 插入数据

```
insert into student (Sno,Sname,Ssex,Sdept,Sage)
values ('200215121','孙策','M','CS',20);
```

### 2. 查询数据

```
select * from studnent where Sname = '孙策'
```

### 3. 修改数据

```
update student set Sname = '孙权' where Sname = '孙策'
```

### 4. 删除数据

```
delete from student where Sname = '孙策'
```

### 5. 常用函数

| MySQL常用函数                | 含义                                          |
| ---------------------------- | --------------------------------------------- |
| ABS(VAL)                     | 绝对值                                        |
| MOD(V1,V2)                   | 取余                                          |
| FLOOR(X)                     | 取下整                                        |
| CEIL(X)                      | 取上整                                        |
| ROUND(X)                     | 四舍五入                                      |
| CONCAT(str1,str2,...)        | 可变长度，最多2的32次方-1个字符               |
| CONCAT                       | 返回来自于参数连结的字符串                    |
| LENGTH(str)                  | 返回字符串str的长度                           |
| LEFT(str,len)                | 返回字符串str的最左面len个字符                |
| RIGHT(str,len)               | 返回字符串str的最右面len个字符                |
| SUBSTRING(str,pos)           | 从字符串str的起始位置pos返回一个子串          |
| SUBSTRING(str,pos,len)       | 从字符串str的起始位置pos返回一个len长度的子串 |
| REVERSE(str)                 | 返回颠倒字符顺序的字符串str                   |
| LCASE(str)                   | 转换小写                                      |
| UCASE(str)                   | 转换大写                                      |
| TRIM(str)                    | 去除前后空格                                  |
| REPLACE(str,from_str,to_str) | 字符串替换                                    |
| NOW()                        | 系统时间日期                                  |
| CURDATE()                    | 系统日期                                      |
| CURTIME()                    | 系统时间                                      |
| DAYNAME(date)                | 日期对应的星期                                |
| DAYOFWEEK(date)              | 日期对应的星期的数字                          |

## 六. 范式

### 1. 第一范式（1NF）

数据库表的每一列都是不可分割的基本数据项，无重复的列。

> 无重复的列

### 2. 第二范式（2NF）

第二范式（2NF）是在第一范式（1NF）的基础上建立起来的，即满足第二范式（2NF）必须先满足第一范式。要求实体的属性完全依赖于主关键字。所谓完全依赖是指不能存在仅依赖主关键字一部分的属性。

> 非主属性完全依赖于主关键字

### 3. 第三范式（3NF）

满足第三范式（3NF） [2]  必须先满足第二范式（2NF）。简而言之，第三范式（3NF）要求一个数据库表中不包含已在其它表中已包含的非主关键字信息。

> 属性不依赖于其它非主属性