# 1. 概念

## DB DBMS RDBMS

数据库管理系统（DBMS，Database Management System）：管理数据库的计算机系统

关系数据库管理系统（RDBMS）：关系数据库通过关系数据库管理系统进行管理

数据库（DB，Database）：数据合集

## DBMS种类

| 类型                                             | 特点                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| 层次数据库（Hierarchy Database，HDB）            | 数据通过层次结构（树形结构）表现出来                         |
| 关系数据库（Relational Database，RDB）           | 由行和列组成的二位表来管理数据，专门语言SQL（Structured Query Language，结构化查询语言） |
| 面向对象数据库（Object Oriented Database，OODB） | 把数据以及对数据的操作集合起来以对象为单位进行管理           |
| XML数据库（XML Database，XMLDB）                 | 对XML形式的数据进行高速处理                                  |
| 键值存储系统（Key-Value Store，KVS)              | 保存查询所用的主键和值的组合的数据库                         |

# 2. 数据库和SQL

## 常见系统结构

客户端/服务器端（C/S类型）

![image-20201016212044215](./images/image-20201016212044215.png)

## 常见表结构

用来管理数据的二维表在关系数据库中简称为表，查询语句返回的结果也是二维表的形式

![image-20201016214129489](./images/image-20201016214129489.png)

表的**列**成为**字段**

表的**行**称为**记录**，**数据库以行为单位进行读写**

![image-20201016215224168](./images/image-20201016215224168.png)

## SQL概要

SQL分为三类：

| 类型                                                | 说明                                                         | 指令                                                         | 对象                                   |
| --------------------------------------------------- | ------------------------------------------------------------ | :----------------------------------------------------------- | -------------------------------------- |
| **DDL（Data Definition Language，数据定义语言）**   | 创建或者删除存储数据的用的数据库以及数据库中的表等对象       | **create**：创建数据库和表等对象   **drop**:杀出数据库和表等对象  **alter**：修改数据库和表等对象的结构 | **数据库和表**                         |
| **DML（Data Manipulation Language，数据操纵语言）** | 查询和变更表中的记录                                         | **select**：查询表中的数据   **insert**：向表中插入新数据     **update**：更新表中的数据   **delete**：删除表中的数据 | **操作表中的数据**                     |
| **DCL（Data Control Language，数据控制语言）**      | 确认或取消对数据库中数据进行的变更，对RDBMS的用户是否有权限操作数据库中的对象（数据库表等）进行设定 | **commit**：确认对数据库中的数据进行的变更  **rollback**：取消对数据库中数据进行的变更     **grant**：赋予用户操作权限       **revoke**：取消用户的操作权限 | **数据变更的确认与回滚；用户操作权限** |

### SQL基本书写规则

1. 关键字（select、表名、列名等）不区分大小写，插入的数据区分大小写

2. 字符串和日期常熟需要使用单引号（'）括起来

3. 数字常量无需加注单引号（直接书写数字）
4. 单词之间使用半角空格或者换行进行分隔

## 数据库的创建

```mysql
create database <数据库名称>;
```

## 创建表

```mysql
create table <表名>
(列名1 <数据类型> <该列所需约束>，
列名2 <数据类型> <该列所需约束>，
.
.
.
<该表中的约束条件1>，<该表中的约束条件2>,...);
```

- 半角英文字母、数字、下划线（_）作为数据库、表和列的名称
- 在数据库中不能创建两个相同名称的表，在同一个表中也不能创建两个名称相同的列

### 数据类型

**数字型、字符型和日期型**

| 类型    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| Integer | 存储整数的列的数据类型                                       |
| Char    | 定长字符串，存储字符串的列的数据类型，char(10)、char(20）等，指定字符串的长度（最大长度） |
| varchar | 指定存储字符串的列的数据类型（字符串类型），可变长字符串的形式保存字符串 |
| date    | 指定存储日期（年月日）的列的数据类型                         |

### 约束的设置

除数据类型之外，对列中存储的数据进行限制或者追加条件的功能。

例如：

```mysql
not null约束
null约束
primary key(product_id)  //主键约束
```

**键**：指定特定数据时使用的列的组合

## 表的删除和更新

### 表的删除

```mysql
drop table 表名;
```

### 表定义的更新

- 添加列

  ```mysql
  alter table 表名 add column 列的定义;
  
  alter table product add column product_name_pinyin varchar(100) not null;
  ```

- 删除列

  ```mysql
  alter table 表名 drop column 列名;
  
  alter table product drop column product_name_pinyin;
  ```

## 插入数据

```mysql
insert into 表名 values(xxx,xxxx);

START TRANSACTION;
INSERT INTO Shohin VALUES ('0001', 'T恤' ,'衣服', 1000, 500, '2009-09-20');
INSERT INTO Shohin VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
COMMIT;
```

# 3. 查询基础

## 3.1 select基础

- 基础的select语句

```mysql
select 列名,列名,...
from 表名
```

查询结果中列的顺序和select子句中的顺序相同

查询全部列时，使用**代表所有列的星号 *** （但是结果的显示顺序就没有办法进行设定，只能按照create tabel的定义对列进行排序）

```mysql
select * from 表名
```

- 设定别名

  使用**as关键字**为**列设定别名**，使用中文的时候，使用双引号（“）括起来。

  ```mysql
  select product_id as id,  
  	   product_name as name,
  	   purchase_price as price
  from Product;
  
select product_id as "商品编号",  
  	   product_name as "商品名称",
  	   purchase_price as "进货单价" 
  from Product;
  ```
  
- 常数的查询

  select语句书写常数，该列的值展示为常数

  ```mysql
  select '商品' as string, 38 as number,'2009-02-24' as data,product_id,product_name
  from product;
  ```

  ![image-20201019103613766](./images/image-20201019103613766.png)

- distinct 

  **distinct关键字只能用在第一个列名之前**，不能使用```regist_date, distinct product_type```

  **distinct会将null视为一类数据**

  对一列数据使用distinct 	```select distinct(product_price) from product;```

  对多列数据使用distinct，将多列的数据进行组合	```select distinct product_type,regist_date from product```，product_type,regist_date相同的数据合并，只展示一条数据

  ![image-20201019111828739](./images/image-20201019111828739.png)

  ![image-20201019111757306](./images/image-20201019111757306.png)

- 注释的写法

  单行注释：--

  多行注释：/* */

## 3.2 算术运算符和比较运算符

### 算术运算符

| 类型 | 说明 |
| ---- | ---- |
| +    |      |
| -    |      |
| *    |      |
| /    |      |

```mysql
//SQL语句中使用运算表达式
select product_name, sale_price,sale_price*2 as 'sale_price_x2' from product;
```

数值大小比较的运算，返回结果的符号

**所有包含NULL的计算，结果肯定是NULL**，例如 5+NULL, 10-NULL, 1*NULL, NULL/9, NULL/0

### 比较运算符

比较运算符可以对字符、数字和日期等几乎所有数据类型的列和值进行比较。

<u>对字符串类型的数据进行大小比较时，使用的是和数字比较不同的规则</u>，典型规则包括：字典序顺序等，'1'>'10'>'11'>'2'>'222'>'3'。

| 类型            | 示例                                    |
| --------------- | --------------------------------------- |
| <>    不等于    | where sale_price <> 500; /* 不等于500*/ |
| =       等于    |                                         |
| \>=    大于等于 |                                         |
| \>      大于    |                                         |
| <=    小于等于  |                                         |
| <      小于     |                                         |

## 3.3 逻辑运算符





























