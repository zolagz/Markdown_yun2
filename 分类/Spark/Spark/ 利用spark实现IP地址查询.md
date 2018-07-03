#  利用spark实现IP地址查询


```
package cn.itcast.rdd.shizhan

import java.sql.{Connection, DriverManager, PreparedStatement}

import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.broadcast.Broadcast
import org.apache.spark.rdd.RDD

//todo:需求：利用spark实现ip地址查询
object IpLocation {

  //将ip地址转化为Long   192.168.200.100
  def ip2Long(ip: String): Long = {
    //将IP地址转为Long,这里有固定的算法
      val ips: Array[String] = ip.split("\\.")
      var ipNum:Long=0L
      for(i <- ips){
        ipNum=i.toLong | ipNum <<8L
      }
    ipNum
  }

  //利用二分法查询，查询到long类型的数字在数组的下标
  def binarySearch(ipNum: Long, broadcastValue: Array[(String, String, String, String)]): Int = {
       //开始下标
        var start=0
      //结束下标
        var end =broadcastValue.length-1

      while(start <=end){
          var middle=(start+end)/2
        if(ipNum>=broadcastValue(middle)._1.toLong && ipNum<=broadcastValue(middle)._2.toLong){
            return  middle
        }
        if(ipNum < broadcastValue(middle)._1.toLong){
          end=middle
        }
        if(ipNum> broadcastValue(middle)._2.toLong){
          start=middle
        }
      }
    -1
  }

    //将数据写入到mysql表中
  def data2mysql(iter:Iterator[((String,String), Int)]) = {
     //定义数据库连接
      var conn:Connection=null
    //定义PreparedStatement
      var ps:PreparedStatement=null
    //编写sql语句,?表示占位符
      val sql="insert into iplocation(longitude,latitude,total_count) values(?,?,?)"
    //获取数据库连接
    conn=DriverManager.getConnection("jdbc:mysql://192.168.200.100:3306/spark","root","123456")
    ps=conn.prepareStatement(sql)

    //遍历迭代器
      iter.foreach(line=>{
        //对sql语句中的?占位符赋值
        ps.setString(1,line._1._1)
        ps.setString(2,line._1._2)
        ps.setInt(3,line._2)
        //执行sql语句
        ps.execute()
      })

  }

  def main(args: Array[String]): Unit = {
    //1、创建sparkConf
    val sparkConf: SparkConf = new SparkConf().setAppName("IpLocation").setMaster("local[2]")
    //2、创建sparkContext
    val sc = new SparkContext(sparkConf)
    //3、读取城市ip信息，获取ip开始数字，ip结束数字，经度，维度
    val city_ip_rdd: RDD[(String, String, String, String)] = sc.textFile("D:\\data\\ip.txt").map(_.split("\\|")).map(x => (x(2), x(3), x(13), x(14)))
    //将城市ip信息广播到每一个worker节点
    val cityIpBroadcast: Broadcast[Array[(String, String, String, String)]] = sc.broadcast(city_ip_rdd.collect())

    //4、获取日志数据,获取所有ip地址
    val destData: RDD[String] = sc.textFile("D:\\data\\20090121000132.394251.http.format").map(_.split("\\|")(1))

    //5、遍历所有IP地址，去广播变量中进行匹配，获取对应的经度和维度
    val result: RDD[((String, String), Int)] = destData.mapPartitions(iter => {
      //获取广播变量的值
      val broadcastValue: Array[(String, String, String, String)] = cityIpBroadcast.value
      //遍历迭代器获取每一个ip地址
      iter.map(ip => {
        //将IP地址转化为Long
        val ipNum: Long = ip2Long(ip)
        //拿到long类型数字去广播变量中进行匹配，利用二分查询
        val index: Int = binarySearch(ipNum, broadcastValue)
        //返回结果数据  ((经度，维度)，1)
        ((broadcastValue(index)._3, broadcastValue(index)._4), 1)

      })
    })
   //6、把相同经度和维度出现的次数累加
    val finalResult: RDD[((String, String), Int)] = result.reduceByKey(_+_)

    //7、打印输出结果
      finalResult.collect().foreach(x=>println(x))

    //保存结果数据到mysql表中
    finalResult.foreachPartition(data2mysql)

    //8、关闭sc
    sc.stop()
  }
}

```


<!--
create time: 2018-04-08 17:11:11
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

