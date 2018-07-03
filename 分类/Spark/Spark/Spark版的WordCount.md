# Spark版的WordCount

```
import org.apache.spark.rdd.RDD
import org.apache.spark.{SparkConf, SparkContext}

/**
  * 需求：利用scala语言实现spark wordcount程序
  */
object WordCount {
  def main(args: Array[String]): Unit = {
    // 1 创建saprkConf对象，设置appName和master,local[2]表示本地运行两个线程
    //.set("spark.driver.host", "localhost")
    val conf: SparkConf = new SparkConf().setAppName("WordCount").setMaster("local[2]")//.set("spark.driver.host", "localhost")

    //  2 创建sparkContext对象
    val sc = new SparkContext(conf)

    // 3  读取数据文件
    val data: RDD[String] = sc.textFile("/Users/alfred/Desktop/mydir/st_word/words.txt")

    // 4 切分文件中的每一行，返回文件所有单词
    val words: RDD[String] = data.flatMap(_.split(" "))

    // 每个单词为1 ，(单词，1)
    val wordAndOne: RDD[(String, Int)] = words.map((_,1))

    //  相同单词出现的次数累加
    val result: RDD[(String, Int)] = wordAndOne.reduceByKey((_+_))
    
    
    //    降序排列
    val sort2: RDD[(String, Int)] = result.sortBy(_._2,false)
    //  收集结果数据
    val finalResult1: Array[(String, Int)] = sort2.collect()
    
    
    //  收集结果数据
    val finalResult: Array[(String, Int)] = result.collect()
    //  8 打印结果数据
    finalResult.foreach(x => println(x))
    
    //  关闭sc
    sc.stop()
  }
}

```


<!--
create time: 2018-04-07 10:46:58
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

