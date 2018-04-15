# Spark连接Hive


```
import org.apache.spark.SparkContext
import org.apache.spark.sql.SparkSession

object HiveSupport {
  def main(args: Array[String]): Unit = {
    // 1   创建sparkSession
    val spark: SparkSession = SparkSession.builder()
                                          .appName("HiveSupport")
                                          .master("local[2]")
                                          .config("spark.sql.warehouse.dir","/Users/alfred/Desktop/mydir/st_word/spark-warehouse")
                                          .enableHiveSupport()  //开启对hive的支持
                                          .getOrCreate()

    //    获取sparkContext
    val sc: SparkContext = spark.sparkContext
    sc.setLogLevel("WARN")
    
    spark.sql("create table if not exists student(id int,name string,age int)" +
    "row format delimited fields terminated by ','")
    //加载数据到hive表里面
    spark.sql("load data local inpath './data/student.txt' into table student")

    //查询表的结果数据
    spark.sql("select * from student").show()
    //关闭
    sc.stop()
    
    spark.stop()
  }
}

```

<!--
create time: 2018-04-10 21:48:37
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

