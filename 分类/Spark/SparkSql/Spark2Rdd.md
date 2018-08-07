# Spark2Rdd


###sparksql转RDD

```
scala> spark.sql("select * from users.test3").rdd
res18: org.apache.spark.rdd.RDD[org.apache.spark.sql.Row] = MapPartitionsRDD[84] at rdd at <console>:24

scala> res18.take(10)
```

### 获取RDD数据

res18.take(10)

```
scala> res18.take(10)
res19: Array[org.apache.spark.sql.Row] = Array([11,李四,语文,90,1532923312746], [15,王五,数学,99,1532923312746], [10,张三,语文,80,1532923312746], [18,王五,英语,89,1532923312746], [16,张三,英语,90,1532923312746], [12,王五,语文,93,1532923312746], [17,李四,英语,50,1532923312746], [14,李四,数学,68,1532923312746], [13,张三,数学,77,1532923312746])

scala> 
```

### 迭代打印出来

res18.take(10).foreach(println)

```
scala> res18.take(10).foreach(println)
[11,李四,语文,90,1532923312746]                                                 
[15,王五,数学,99,1532923312746]
[10,张三,语文,80,1532923312746]
[18,王五,英语,89,1532923312746]
[16,张三,英语,90,1532923312746]
[12,王五,语文,93,1532923312746]
[17,李四,英语,50,1532923312746]
[14,李四,数学,68,1532923312746]
[13,张三,数学,77,1532923312746]

scala> 

```

###RDD 转 DataFrame

![](http://p2ehgqigv.bkt.clouddn.com/18-7-30/77925841.jpg)

![](http://p2ehgqigv.bkt.clouddn.com/18-7-30/67480138.jpg)


<!--
create time: 2018-07-30 21:32:27
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

