# SparkSql从myslq加载数据


```
import java.util.Properties
import org.apache.spark.sql.{DataFrame, SparkSession}

//todo:利用sparksql从mysql中加载数据

object DataFromMysql {

  def main(args: Array[String]): Unit = {
    //创建sparkSession
    val spark: SparkSession = SparkSession.builder().appName("DataFromMysql").master("local[2]").getOrCreate()

//     通过sparkSession获取sql表中的数据
    
    //设置用户名和密码
    val properties = new Properties()
    properties.setProperty("user","root")
    properties.setProperty("password","admin")
    
    //jdbc:mysql://localhost:3306/数据库","表名"
    val dataframe: DataFrame = spark.read.jdbc("jdbc:mysql://localhost:3306/db1","emp",properties)

    //打印dataFrame schema信息
    dataframe.printSchema()

    //打印dataFrame中的数据
    dataframe.show()

    //关闭
    spark.stop()
  }
}

```

<!--
create time: 2018-03-29 10:15:34
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

