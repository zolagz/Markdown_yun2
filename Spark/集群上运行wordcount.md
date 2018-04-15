# 集群上运行wordcount


```
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.rdd.RDD

//todo:需求：利用scala语言实现spark wordcount程序

object WordCount_Online {
  def main(args: Array[String]): Unit = {
      //1、创建sparkConf对象,设置appName和master的地址
      val sparkConf: SparkConf = new SparkConf().setAppName("WordCount_Online")

       //2、创建sparkcontext对象
        val sc = new SparkContext(sparkConf)

      //设置日志输出级别
        sc.setLogLevel("WARN")

       // 3、读取数据文件
        val data: RDD[String] = sc.textFile(args(0))

       //4、切分文件中的每一行,返回文件所有单词
       val words: RDD[String] = data.flatMap(_.split(" "))

      //5、每个单词记为1，(单词，1)
      val wordAndOne: RDD[(String, Int)] = words.map((_,1))

      //6、相同单词出现的次数累加
      val result: RDD[(String, Int)] = wordAndOne.reduceByKey(_+_)

      //按照单词出现的次数降序排列
      val sortResult: RDD[(String, Int)] = result.sortBy(_._2,false)

      //7、结果数据保存在HDFS上
      sortResult.saveAsTextFile(args(1))

    //8、关闭sc
    sc.stop()
  }
}

```

###提交任务脚本

```
spark-submit --class cn.itcast.wordcount.WordCount_Online --master spark://node1:7077 --executor-memory 1G --total-executor-cores 2 /root/original-spark-2.0.jar /words.txt /out100
```



<!--
create time: 2018-04-07 11:06:04
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

