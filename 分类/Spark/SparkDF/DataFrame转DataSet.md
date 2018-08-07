# DataFrame转DataSet

```
val spark = SparkSession.builder().appName("DataFrameCase").master("local[2]").getOrCreate()
    val sc: SparkContext = spark.sparkContext
    sc.setLogLevel("WARN")
    val orderRdd: RDD[String] = spark.sparkContext.textFile("src/main/data/order.txt")
    import spark.implicits._
    val orderDF: DataFrame = orderRdd.map(_.split(",")).map(line => Order(line(0).toInt,line(1).toInt)).toDF()
    orderDF.show(20)
    val orderDS = orderDF.as[Order]
    orderDS.map(line => line.org_price).show()
    sc.stop()
  }

  case class Order(org_price :Int,price :Int)
}
```

<!--
create time: 2018-08-06 21:29:23
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

