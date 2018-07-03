# hive常用命令

[来自网络](http://longliqiang88.github.io/2015/08/11/Hive%E5%AD%A6%E4%B9%A02%EF%BC%9A%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E6%80%BB%E7%BB%93/)

本文只是总结一些在Hive中常用的命令，并且假设需要的目录或者数据已经存在。

创建表，\t作为列的分隔符

```
create table trade_detail (id bigint,income double,expenses double,time string) row formate delimited fields terminated by '\t';

```

```	
create table user_info(id bigint, account string, name string, age int) row format delimited fields terminated by '\t';

```
接下来是稍复杂的语句，创建表的的同时进行赋值

```
create table result row format delimited fields terminated by '\t' as select t1.account, t1.income, t1.expenses, t1.surplus, t2.name from user_info t2 join (select account, sum(income) as income, sum(expenses) as expenses, sum(income-expenses) as surplus from trade_detail group by account) t1 on(t1.account = t2.account);

```
加载本地文件到数据表中

```
load data local inpath '/home/hadoop/data/student.txt' overwrite into table student;
```
```
load data local inpath '/home/hadoop/data/user_info.doc' overwrite into table user_info;
```

创建外部表 ，创建外部表的一般情况指的是：先有文件存放着数据，之后我们再来创建表，也就是说创建一张表，然后指向这个有数据的目录。以后只要是向这个目录中上传符合格式的数据会被自动装在到数据库表中，因为在metastore（元数据）会记录这些信息

```
create external table t_detail(id bigint, account string, income double, expenses double, time string) ) row format delimited fields terminated by '\t' location '/hive/td_partition';
```
创建分区表，一般用于数据量比较大的情况下， partitioned by (logdate string)用来指定按照什么进行分区

```
create external table t_detail(id bigint, account string, income double, expenses double, time string)  row format delimited fields terminated by '\t' location '/hive/td_partition' partitioned by (logdate string);

```
将mysql中的数据直接保存到Hive中

```
sqoop export --connect jdbc:mysql://192.168.8.103:3306/hmbbs --username root --password hadoop --export-dir '/user/hive/warehouse/pv_2013_05_31/000000_0' --table pv
```
基本的插入语法

```
insert overwrite table tablename [partiton(partcol1=val1,partclo2=val2)]select_statement from t_statement
insert overwrite table test_insert select * from test_table;
```
更新表的名称

```
hive> alter table source RENAME TO target;
```
添加新一列

```
alter table invites add columns (new_col2 INT COMMENT 'a comment');

```
删除表：

```
DROP TABLE records;

```
删除表中数据，但要保持表的结构定义
```
dfs -rmr /user/hive/warehouse/records;
```
显示所有函数

```
show functions;
```
查看函数用法

```
describe function substr;
```
内连接

```
SELECT sales.*, things.* FROM sales JOIN things ON (sales.id = things.id);
```
查看hive为某个查询使用多少个MapReduce作业

```
Explain SELECT sales.*, things.* FROM sales JOIN things ON (sales.id = things.id);
```
外连接

```
SELECT sales.*, things.* FROM sales LEFT OUTER JOIN things ON (sales.id = things.id);
SELECT sales.*, things.* FROM sales RIGHT OUTER JOIN things ON (sales.id = things.id);
SELECT sales.*, things.* FROM sales FULL OUTER JOIN things ON (sales.id = things.id);
```
创建视图

```
hive> CREATE VIEW valid_records AS SELECT * FROM records2 WHERE temperature !=9999;
```
查看视图详细信息

```
hive> DESCRIBE EXTENDED valid_records;
```
<!--
create time: 2018-04-12 09:46:55
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

