# SparkSQL读取json创建DF

在spark安装目录下有一个json文件
![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/54596020.jpg)

spark中读取本地文件

```
scala> val df1 = spark.read.json("file:///usr/local/dev/spark-2.0.2/examples/src/main/resources/people.json")
df1: org.apache.spark.sql.DataFrame = [age: bigint, name: string]               

scala> 
```

通过DataFrame中show方法查看内容

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/49818701.jpg)

查看schema

```
scala>  df1.printSchema
root
 |-- age: long (nullable = true)
 |-- name: string (nullable = true)


scala> 
```

###通过读取parquet文件创建DataFrame
![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/17920519.jpg)


<!--
create time: 2018-04-15 15:45:05
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

