# Spark-Shell操作hive上的数据

[官方文档](http://spark.apache.org/docs/latest/sql-programming-guide.html)

### 配置

Spark SQL also supports reading and writing data stored in Apache Hive. However, since Hive has a large number of dependencies, these dependencies are not included in the default Spark distribution. If Hive dependencies can be found on the classpath, Spark will load them automatically. Note that these Hive dependencies must also be present on all of the worker nodes, as they will need access to the Hive serialization and deserialization libraries (SerDes) in order to access data stored in Hive.

Configuration of Hive is done by placing your **hive-site.xml**, **core-site.xml** (for security configuration), and **hdfs-site.xml** (for HDFS configuration) file in conf/.




```
18/06/22 09:22:43 WARN util.Utils: Service 'SparkUI' could not bind on port 4040. Attempting port 4041.
18/06/22 09:22:43 WARN spark.SparkContext: Use an existing SparkContext, some configuration may not take effect.
Spark context Web UI available at http://192.168.25.130:4041
Spark context available as 'sc' (master = local[*], app id = local-1529630563268).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 2.0.2
      /_/
         
Using Scala version 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_162)
Type in expressions to have them evaluated.
Type :help for more information.

scala> val sqltext= new org.apache.spark.sql.hive.HiveContext(sc)

scala> sqltext.sql("select * from jd_data limit 2")
res1: org.apache.spark.sql.DataFrame = [id: int, name: string ... 6 more fields]

scala> res1.show
+-------+--------------------+--------------------+--------------------+-------+------------+------------+--------------+
|     id|                name|                 url|               title|  price|     maidian|      pinpai|        xinhao|
+-------+--------------------+--------------------+--------------------+-------+------------+------------+--------------+
|6733024| 商品名称：华为P20 Pro|https://item.jd.c...|华为 HUAWEI P20 Pro...|4988.00|商品编号：6733024|商品毛重：500.00g|品牌： 华为（HUAWEI）|
|5089273|商品名称：AppleiPhone ...|https://item.jd.c...|Apple iPhone 8 Pl...|5499.00|商品编号：5089273|商品毛重：480.00g|     品牌： Apple|
+-------+--------------------+--------------------+--------------------+-------+------------+------------+--------------+


scala> 


或者：

import org.apache.spark.sql.hive.HiveContext

val hiveContext = new HiveContext(sc)

hiveContext.sql("select * from jd_data limit 2").show()


scala> sqltext.sql("show tables").show
+---------+-----------+
|tableName|isTemporary|
+---------+-----------+
|  jd_data|      false|
|  user_t1|      false|
+---------+-----------+


<!--推荐-->
scala> spark.sql("show tables").show
+---------+-----------+
|tableName|isTemporary|
+---------+-----------+
|  jd_data|      false|
|  user_t1|      false|
+---------+-----------+

<!--推荐的方式-->
scala> spark.sql("select * from jd_data limit 2").show
+-------+--------------------+--------------------+--------------------+-------+------------+------------+--------------+
|     id|                name|                 url|               title|  price|     maidian|      pinpai|        xinhao|
+-------+--------------------+--------------------+--------------------+-------+------------+------------+--------------+
|6733024| 商品名称：华为P20 Pro|https://item.jd.c...|华为 HUAWEI P20 Pro...|4988.00|商品编号：6733024|商品毛重：500.00g|品牌： 华为（HUAWEI）|
|5089273|商品名称：AppleiPhone ...|https://item.jd.c...|Apple iPhone 8 Pl...|5499.00|商品编号：5089273|商品毛重：480.00g|     品牌： Apple|
+-------+--------------------+--------------------+--------------------+-------+------------+------------+--------------+

```


新版代码：

```
package com.dkl.leanring.spark.sql

import org.apache.spark.sql.SparkSession

/**
 * 新版本spark-hive测试
 */
object NewSparkHiveDemo {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession
      .builder()
      .appName("Spark Hive Example")
      .master("local")
      .config("spark.sql.warehouse.dir", "/user/hive/warehouse/")
      .enableHiveSupport()
      .getOrCreate()

    import spark.implicits._
    import spark.sql
    sql("CREATE TABLE IF NOT EXISTS src (key INT, value STRING)")
    val data = Array((1, "val1"), (2, "val2"), (3, "val3"))
    var df = spark.createDataFrame(data).toDF("key", "value")
    df.createOrReplaceTempView("temp_src")
    sql("insert into src select key,value from temp_src")
    sql("SELECT * FROM src").show()
  }
}
```

[参考链接](https://blog.csdn.net/dkl12/article/details/80248716)


<!--
create time: 2018-06-22 09:28:26
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

