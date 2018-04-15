# SparkSQL之SQL风格语法

DataFrame的一个强大之处就是我们可以将它看作是一个关系型数据表，然后可以通过在程序中使用spark.sql() 来执行SQL语句查询，结果返回一个DataFrame。
如果想使用SQL风格的语法，需要将DataFrame注册成表,采用如下的方式：
peopleDF.registerTempTable("t_person")


###查询年龄最大的前两名

spark.sql("select * from t_person order by age desc limit 2").show

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/18491546.jpg)

###显示表的Schema信息spark.sql("desc t_person").show```
scala> spark.sql("desc t_person").show
+--------+---------+-------+
|col_name|data_type|comment|
+--------+---------+-------+
|      id|      int|   null|
|    name|   string|   null|
|     age|      int|   null|
+--------+---------+-------+
```

###查询年龄大于20的人的信息spark.sql("select * from t_person where age > 20 ").show
```
scala> spark.sql("select * from t_person where age > 20 ").show
+---+--------+---+
| id|    name|age|
+---+--------+---+
| 14|zhangsan| 23|
| 12|  wangwu| 21|
| 23| zhaoliu| 22|
| 54|    heer| 26|
+---+--------+---+
```

<!--
create time: 2018-04-15 16:16:11
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

