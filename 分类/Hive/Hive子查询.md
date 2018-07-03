# Hive子查询

```
select e.ename from emp e where e.deptno in (select d.deptno from dept d where d.dname = 'SALES' or d.dname = 'ACCOUNTING');

```

<!--
create time: 2018-03-06 21:40:05
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

