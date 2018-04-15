# Spark连接数据库

```
import java.util.Properties
import org.apache.spark.sql.{DataFrame, SparkSession}
import scala.tools.cmd.Property

object DataFromMysql {

  def main(args: Array[String]): Unit = {
    val spark: SparkSession = SparkSession.builder().appName("DataFromMysql").master("local[2]").getOrCreate()
    val properties = new Properties()
    properties.setProperty("user","root")
    properties.setProperty("password","admin")

    val dataFrame: DataFrame = spark.read.jdbc("jdbc:mysql://localhost:3306/db1","emp",properties)

    dataFrame.printSchema()

    dataFrame.show()

    spark.stop()
  }
}

```

<!--
create time: 2018-04-10 21:46:39
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

