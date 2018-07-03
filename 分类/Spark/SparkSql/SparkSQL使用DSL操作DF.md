# SparkSQL使用DSL操作DF

DataFrame可以是用DSL操作

```
scala> val rdd1=sc.textFile("/people.txt").map(_.split(" "))
rdd1: org.apache.spark.rdd.RDD[Array[String]] = MapPartitionsRDD[2] at map at <console>:24

scala> case class People(id:Int,name:String,age:Int)
defined class People

scala> val peopleRDD = rdd1.map(x => People(x(0).toInt,x(1),x(2).toInt))
peopleRDD: org.apache.spark.rdd.RDD[People] = MapPartitionsRDD[3] at map at <console>:28

scala> val peopleDF = peopleRDD.toDF

peopleDF: org.apache.spark.sql.DataFrame = [id: int, name: string ... 1 more field]

scala> peopleDF
res0: org.apache.spark.sql.DataFrame = [id: int, name: string ... 1 more field]

scala> peopleDF.collect
res1: Array[org.apache.spark.sql.Row] = Array([14,zhangsan,23], [12,wangwu,21], [23,zhaoliu,22], [54,heer,26])

scala>  peopleDF.show
+---+--------+---+
| id|    name|age|
+---+--------+---+
| 14|zhangsan| 23|
| 12|  wangwu| 21|
| 23| zhaoliu| 22|
| 54|    heer| 26|
+---+--------+---+
```
###select 

```
scala> peopleDF.select("name")
res13: org.apache.spark.sql.DataFrame = [name: string]

scala> peopleDF.select("name").show
+--------+
|    name|
+--------+
|zhangsan|
|  wangwu|
| zhaoliu|
|    heer|
+--------+
```

```
scala> peopleDF.select("name","age").show
+--------+---+
|    name|age|
+--------+---+
|zhangsan| 23|
|  wangwu| 21|
| zhaoliu| 22|
|    heer| 26|
+--------+---+
```

##给年龄+1 

```
scala> peopleDF.select($"name",$"age" + 1).show
+--------+---------+
|    name|(age + 1)|
+--------+---------+
|zhangsan|       24|
|  wangwu|       22|
| zhaoliu|       23|
|    heer|       27|
+--------+---------+

scala> 
```

###过滤age大于等于25的，使用filter方法

```
scala> peopleDF.filter($"age" > 25).show
+---+----+---+
| id|name|age|
+---+----+---+
| 54|heer| 26|
+---+----+---+
```
###统计年龄大于20的人数

```
scala> peopleDF.filter(col("age")>20).count()
res22: Long = 4

scala> 
```
###按年龄进行分组并统计相同年龄的人数

```
scala> peopleDF.groupBy("age").count().show
+---+-----+                                                                     
|age|count|
+---+-----+
| 26|    1|
| 22|    1|
| 23|    1|
| 21|    1|
+---+-----+
```





<!--
create time: 2018-04-15 16:03:49
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

