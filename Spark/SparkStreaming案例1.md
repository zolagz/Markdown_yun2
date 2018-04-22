# SparkStreaming案例1

### 1  DStream操作实战SparkStreaming接受socket数据，实现单词计数WordCount
####实现流程安装并启动生产者1.首先在linux服务器上用YUM安装nc工具，nc命令是netcat命令的简称,它是用来设置路由器。我们可以利用它向某个端口发送数据。
>yum install -y nc2.通过netcat工具向指定的端口发送数据
>nc -lk 9999 3.编写Spark Streaming程序

```
import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}

object StreamDemo {
  def main(args: Array[String]): Unit = {
    //配置sparkConf参数
    val conf: SparkConf = new SparkConf().setAppName("StreamDemo").setMaster("local[2]")
    //构建SparkContext对象
    val sc: SparkContext = new SparkContext(conf)
    sc.setLogLevel("WARN")
    // 构建StreamingContext对象，每个批处理的世界间隔
    val scc: StreamingContext = new StreamingContext(sc,Seconds(5))
    // 注册一个监听的IP地址和端口，用来收集数据
    val lines: ReceiverInputDStream[String] = scc.socketTextStream("192.168.25.130",9999)
//    切分每一行记录
    val words: DStream[String] = lines.flatMap(_.split(" "))
    //每个单词记为1
    val wordAndOne: DStream[(String, Int)] = words.map((_,1))
    //分组聚合
    val result: DStream[(String, Int)] = wordAndOne.reduceByKey(_+_)
    //打印数据
    result.print()
    scc.start()
    scc.awaitTermination()
  }
}

```
###先执行nc -lk 9999 
![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/12172454.jpg)
执行代码
![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/79212784.jpg)

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/65371623.jpg)

![](http://p2ehgqigv.bkt.clouddn.com/18-4-15/98574836.jpg)


### 2 修改单词统计累加(通过函数updateStateByKey实现)

```

import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}

/**  * sparkStreaming流式处理，接受socket数据，实现单词统计并且每个批次数据结果累加  */

object SteamDemo {

  //newValues 表示当前批次汇总成的(word,1)中相同单词的所有的1  //runningCount 历史的所有相同key的value总和
  def updateFunc(currentValues:Seq[Int],historyValues:Option[Int]):Option[Int] = {
    val result: Int = currentValues.sum+historyValues.getOrElse(0)
    Some(result)
  }

  def main(args: Array[String]): Unit = {
    //配置sparkConf参数
    val conf: SparkConf = new SparkConf().setAppName("SteamDemo").setMaster("local[2]")
    //构建SparkContext对象
    val sc: SparkContext = new SparkContext(conf)
    sc.setLogLevel("WARN")
    // 构建StreamingContext对象，每个批处理的世界间隔
    val scc: StreamingContext = new StreamingContext(sc,Seconds(5))
    scc.checkpoint("./ck")
    // 注册一个监听的IP地址和端口，用来收集数据
    val lines: ReceiverInputDStream[String] = scc.socketTextStream("192.168.25.130",9999)
//    切分每一行记录
    val words: DStream[String] = lines.flatMap(_.split(" "))
    //每个单词记为1
    val wordAndOne: DStream[(String, Int)] = words.map((_,1))
    //分组聚合

    val resultValue: DStream[(String, Int)] = wordAndOne.updateStateByKey(updateFunc)
//    val result: DStream[(String, Int)] = wordAndOne.reduceByKey(_+_)
    //打印数据
    resultValue.print()
    scc.start()
    scc.awaitTermination()
  }
}

```

通过函数updateStateByKey实现。根据key的当前值和key的之前批次值，对key进行更新，返回一个新状态的DStream

<!--
create time: 2018-04-15 22:01:14
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

