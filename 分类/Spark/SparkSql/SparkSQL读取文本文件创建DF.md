# SparkSQL读取文本文件创建DF

首先在命令行下启动spark-shell

```
spark-shell --master local[2]
```

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/88757773.jpg)

通过Spark context来读取hdfs上的/people.txt

先查看一下

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/81264887.jpg)

读取文件并以空格切分

```
scala> val rdd1=sc.textFile("/people.txt").map(_.split(" "))
rdd1: org.apache.spark.rdd.RDD[Array[String]] = MapPartitionsRDD[2] at map at <console>:24

scala> 
```
![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/38614409.jpg)

转化为DF操作
![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/90232738.jpg)

###toDF转化为DF

```
scala> val peopleDF = peopleRDD.toDF

peopleDF: org.apache.spark.sql.DataFrame = [id: int, name: string ... 1 more field]

scala> peopleDF
res0: org.apache.spark.sql.DataFrame = [id: int, name: string ... 1 more field]

scala> peopleDF.collect
res1: Array[org.apache.spark.sql.Row] = Array([14,zhangsan,23], [12,wangwu,21], [23,zhaoliu,22], [54,heer,26])

```
通过printSchema方法查看schema

```

scala> peopleDF.printSchema
root
 |-- id: integer (nullable = false)
 |-- name: string (nullable = true)
 |-- age: integer (nullable = false)
```

###扩展 

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/39469776.jpg)


<!--
create time: 2018-04-15 15:09:50
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

