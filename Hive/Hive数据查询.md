# Hive数据查询

###简单查询和fetch task

select *from emp; ---并没有转成mapreduce查询，把hdfs中对应的文件直接加载 

select empno,ename,sal from emp; --- 转换层mapreduce任务 不能对表进行全表扫描 需要对数据进行分析 速度慢的原因（1）和机器的配置有关（2）hive操作数据仓库，在海量数据下。。。

支持算术表达式： 

select empno,ename,sal，sal*12 from emp;

select empno,ename,sal，sal*12, comm,sal*12+comm from emp; 

涉及到NULL值，表达式中含有NULL值，整个表达式都是NULL值 把奖金为空（NULL）转换为0--> 
select empno,ename,sal，sal*12，comm，sal*12+nvl(comm,0) from emp; 

查询奖金为NULL的员工： select * from emp where comm is null; 
判断一个值是否为NULL，不能用= ！= 用is 

使用distinct 去掉重复的记录 

select distinct depno from emp; 

select distinct depno,job from emp; //组合去重 

Fetch Task功能： 不使用MR作业来查询，hive命令行中执行：

set hive.fetch.task.conversion = more; 

配置hive的参数并开启hive：hive --hiveconf hive.fetch.task.conversion = more 

修改xml配置,参数如图所示：hive-site.xml

###Hive中使用过滤查询

在查询中使用过滤：where条件 

selec * from emp where deptno=10; --- 开启了fecth task 功能 

select * from emp where enmae='KING'; 

select * from emp where enmae='king'; --- 严格区分字符串的大小写的 

select * from emp where deptno=10 and sal<2000; --- 使用and连接 

具体的执行过程：执行计划--->explain 读的顺序从右往左 从下往上 

---模糊查询 
select empno,ename,sal from emp where name like 'S%'; 

select empno,ename,sal from emp where name like '%\\_%'; 

-- _代表任意的字符，本身需要用转义字符


<!--
create time: 2018-03-06 21:38:37
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

