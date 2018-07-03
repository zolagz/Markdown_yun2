# Spark操作mysql

```
import java.util.Properties
import org.apache.spark.sql.{DataFrame, SQLContext, SparkSession}

/**
  * 使用外部数据源综合查询Hive和MySQL的表数据
  */
object HiveMySQLApp {

  def main(args: Array[String]): Unit = {

    val spark: SparkSession = SparkSession.builder().appName("DataFromMysql").master("local[2]").getOrCreate()
    val properties = new Properties()
    properties.setProperty("user","root")
    properties.setProperty("password","admin")

    val dataFrame: DataFrame = spark.read.jdbc("jdbc:mysql://localhost:3306/exam","emp",properties)

    dataFrame.printSchema()

    dataFrame.show()

    spark.stop()
  }
}
```


<!--
create time: 2018-06-26 15:48:08
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

