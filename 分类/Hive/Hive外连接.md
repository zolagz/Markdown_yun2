# Hive外连接


select d.deptno, d.dname, count(e.empno) ---count(e.empno)聚合函数 计数 

from emp e, dept d where e.deptno = d.deptno group by d.deptno, d.dname;

-- 没有出现在聚合函数中的列 

等值连接：不成立，员工表中不存在的部门但是部门表中存在 

应该修改为外连接的形式： 通过外连接可以将对连接条件不成立的记录仍然包含在最后的结果中。 

左外连接： 

右外连接： 

select d.deptno, d.dname, count(e.empno) from emp e right outer join dept d on(e.deptno = d.deptno) group by d.deptno, d.dname;

<!--
create time: 2018-04-11 11:21:02
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

