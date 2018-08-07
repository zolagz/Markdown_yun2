# SparkSql2DF


### sparkSql创建DataFrame



```
scala> spark.sql("select * from users.test3").toDF()
res11: org.apache.spark.sql.DataFrame = [id: int, name: string ... 3 more fields]

```
###查看DataFrame内容

```

scala> res11.show
+---+----+------+-----+---------------+                                         
| id|name|course|score|processing_dttm|
+---+----+------+-----+---------------+
| 11|  李四|    语文|   90|  1532923312746|
| 15|  王五|    数学|   99|  1532923312746|
| 10|  张三|    语文|   80|  1532923312746|
| 18|  王五|    英语|   89|  1532923312746|
| 16|  张三|    英语|   90|  1532923312746|
| 12|  王五|    语文|   93|  1532923312746|
| 17|  李四|    英语|   50|  1532923312746|
| 14|  李四|    数学|   68|  1532923312746|
| 13|  张三|    数学|   77|  1532923312746|
+---+----+------+-----+---------------+

```

```
scala> res11.filter($"score" > 90).show
+---+----+------+-----+---------------+
| id|name|course|score|processing_dttm|
+---+----+------+-----+---------------+
| 15|  王五|    数学|   99|  1532923312746|
| 12|  王五|    语文|   93|  1532923312746|
+---+----+------+-----+---------------+

scala> res11.select($"name",$"score").show
+----+-----+
|name|score|
+----+-----+
|  李四|   90|
|  王五|   99|
|  张三|   80|
|  王五|   89|
|  张三|   90|
|  王五|   93|
|  李四|   50|
|  李四|   68|
|  张三|   77|
+----+-----+

```

###查看Schema

```
scala> res11.printSchema()
root
 |-- id: integer (nullable = true)
 |-- name: string (nullable = true)
 |-- course: string (nullable = true)
 |-- score: integer (nullable = true)
 |-- processing_dttm: string (nullable = true)

scala> 
``` 
### 写入hive

```
scala> res11.write.mode("append").saveAsTable("users.students")
```


![](http://p2ehgqigv.bkt.clouddn.com/18-7-30/11912191.jpg)

### GroupBy操作

```                                                                        scala>  res11.groupBy("name").count().show()
+----+-----+                                                                    
|name|count|
+----+-----+
|  王五|    3|
|  李四|    3|
|  张三|    3|
+----+-----+
```

### drop：去除指定字段，保留其他字段

```
scala> res11.drop("name").show()
+---+------+-----+---------------+                                              
| id|course|score|processing_dttm|
+---+------+-----+---------------+
| 11|    语文|   90|  1532923312746|
| 15|    数学|   99|  1532923312746|
| 10|    语文|   80|  1532923312746|
| 18|    英语|   89|  1532923312746|
| 16|    英语|   90|  1532923312746|
| 12|    语文|   93|  1532923312746|
| 17|    英语|   50|  1532923312746|
| 14|    数学|   68|  1532923312746|
| 13|    数学|   77|  1532923312746|
+---+------+-----+---------------+

```

###col：获取指定字段

只能获取一个字段，返回对象为Column类型。

```　
　　val idCol = res11.col(“id”)。
```

（4）apply：获取指定字段
只能获取一个字段，返回对象为Column类型
　　示例：

```
val idCol1 = res11.apply("id")
val idCol2 = res11("id")

```

### limit

limit方法获取指定DataFrame的前n行记录，得到一个新的DataFrame对象。和take与head不同的是，limit方法不是Action操作。

```
res11.limit(3).show( false)

scala> res11.limit(3).show( false)
+---+----+------+-----+---------------+                                         
|id |name|course|score|processing_dttm|
+---+----+------+-----+---------------+
|11 |李四  |语文    |90   |1532923312746  |
|15 |王五  |数学    |99   |1532923312746  |
|10 |张三  |语文    |80   |1532923312746  |
+---+----+------+-----+---------------+
```

### distinct
distinct：返回一个不包含重复记录的DataFrame

### dropDuplicates

dropDuplicates：根据指定字段去重

###union
unionAll方法：对两个DataFrame进行组合

类似于SQL中的UNION ALL操作。

###join

### withColumn
whtiColumn(colName: String , col: Column)方法根据指定colName往DataFrame中新增一列，如果colName已存在，则会覆盖当前列。 

```
scala> res11.withColumn("test",res11("name")).show()
+---+----+------+-----+---------------+----+                                    
| id|name|course|score|processing_dttm|test|
+---+----+------+-----+---------------+----+
| 11|  李四|    语文|   90|  1532923312746|  李四|
| 15|  王五|    数学|   99|  1532923312746|  王五|
| 10|  张三|    语文|   80|  1532923312746|  张三|
| 18|  王五|    英语|   89|  1532923312746|  王五|
| 16|  张三|    英语|   90|  1532923312746|  张三|
| 12|  王五|    语文|   93|  1532923312746|  王五|
| 17|  李四|    英语|   50|  1532923312746|  李四|
| 14|  李四|    数学|   68|  1532923312746|  李四|
| 13|  张三|    数学|   77|  1532923312746|  张三|
+---+----+------+-----+---------------+----+
```


<!--
create time: 2018-07-30 21:06:26
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

