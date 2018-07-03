# DataSet操作


---
title: dataSet 操作
date: 2018-06-26 15:39:23
tags: spark dataSet
---

sales.csv 文件

```
transactionId,customerId,itemId,amountPaid
111,1,1,100.0
112,2,2,505.0
113,3,3,510.0
114,4,4,600.0
115,1,2,500.0
116,1,2,500.0
117,1,2,500.0
118,1,2,500.0
119,2,3,500.0
120,1,2,500.0
121,1,4,500.0
122,1,2,500.0
123,1,4,500.0
124,1,2,500.0
```


```
package com.boy.spark

import org.apache.spark.sql.SparkSession

/**
  * Dataset操作
  */
object DatasetApp {

  def main(args: Array[String]): Unit = {

    val spark = SparkSession.builder().appName("DatasetApp").master("local[2]").getOrCreate()
    //注意：需要导入隐式转换
    import spark.implicits._
    val path = "src/main/data/sales.csv"

    //spark如何解析csv文件？  CSV ==> DataFrame
    val df = spark.read.option("header", "true").option("inferSchema", "true").csv(path)
    df.show()
    //DataFrame ==>  Dataset
    val ds = df.as[Sales]
    ds.map(line => line.itemId).show()
    spark.stop()

  }
  
  case class Sales(transactionId: Int, customerId: Int, itemId: Int, amountPaid: Double)
  
}

```

<!--
create time: 2018-06-26 15:42:46
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

