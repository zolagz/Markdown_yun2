# Phoenix笔记

Phoenix 和 Hbase 映射关系

#### 如果HBase已经存在的表
* 通过映射视图（只读）
* 通过表映射 （读写）

#### 全新的表

* 在Phoenix中建表

#### 进入Phoenix命令行

sqlline.py zookeeper_address


### 查看有哪些存在的表

	$> !Table
	
#### 查看帮助信息
	
	$>!help

#### 查看表结构

	$> !describe "表名"
	
对于HBase已经存在的表只需要建立视图映射即可，如：在hbase建立一张表，并存入数据

```
create 'test1','cf1'

put 'test1','rk001','cf:name','zhangsan'
put 'test1','rk001','cf:age','25'

put 'test1','rk002','cf:name','lisi'
put 'test1','rk002','cf:age','20'

put 'test1','rk003','cf:name','wangwu'
put 'test1','rk003','cf:age','28'

```


在phoenix中创建视图

```
<!--phoenix 是区分大小写的，不加“”,会转换为大写，加上“”,就不会在转变为大写了-->

create view test1(
	user_id varchar primary key,
	'cf'.NAME varchar,
	'cf'."age"	varchar
);

```


<!--
create time: 2018-06-26 05:32:45
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

