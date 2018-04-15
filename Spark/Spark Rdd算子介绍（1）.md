# Spark Rdd算子介绍（1）


###parallelize

调用SparkContext 的 parallelize()，将一个存在的集合，变成一个RDD，这种方式试用于学习spark和做一些spark的测试

scala版本
def parallelize[T](seq: Seq[T], numSlices: Int = defaultParallelism)(implicit arg0: ClassTag[T]): RDD[T]
- 第一个参数一是一个 Seq集合
- 第二个参数是分区数
- 返回的是RDD[T]

```
scala> sc.parallelize(List("shenzhen", "is a beautiful city"))
res1: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[1] at parallelize at <console>:22
```

###makeRDD

只有scala版本的才有makeRDD
def makeRDD[T](seq : scala.Seq[T], numSlices : scala.Int = { /* compiled code */ })
跟parallelize类似

```
sc.makeRDD(List("shenzhen", "is a beautiful city"))
```

###textFile
调用SparkContext.textFile()方法，从外部存储中读取数据来创建 RDD
例如在我本地F:\dataexample\wordcount\input下有个sample.txt文件，文件随便写了点内容，我需要将里面的内容读取出来创建RDD
scala版本

```
var lines = sc.textFile("F:\\dataexample\\wordcount\\input") 
```

注: textFile支持分区，支持模式匹配，例如把F:\dataexample\wordcount\目录下inp开头的给转换成RDD

	var lines = sc.textFile("F:\\dataexample\\wordcount\\inp*")

多个路径可以使用逗号分隔，例如

	var lines = sc.textFile("dir1,dir2",3)


###filter

举例，在F:\sparktest\sample.txt 文件的内容如下

```
aa bb cc aa aa aa dd dd ee ee ee ee 
ff aa bb zks
ee kks
ee  zz zks
```
我要将包含zks的行的内容给找出来
scala版本

    val lines = sc.textFile("F:\\sparktest\\sample.txt").filter(line=>line.contains("zks"))
    //打印内容
    lines.collect().foreach(println(_));

```
-------------输出------------------
ff aa bb zks
ee  zz zks
```

###map

map() 接收一个函数，把这个函数用于 RDD 中的每个元素，将函数的返回结果作为结果RDD编程 
RDD 中对应元素的值 map是一对一的关系
举例，在F:\sparktest\sample.txt 文件的内容如下

```
aa bb cc aa aa aa dd dd ee ee ee ee 
ff aa bb zks
ee kks
ee  zz zks
```
把每一行变成一个数组
scala版本

```
//读取数据
scala> val lines = sc.textFile("F:\\sparktest\\sample.txt")
//用map，对于每一行数据，按照空格分割成一个一个数组，然后返回的是一对一的关系
scala> var mapRDD = lines.map(line => line.split("\\s+"))
---------------输出-----------
res0: Array[Array[String]] = Array(Array(aa, bb, cc, aa, aa, aa, dd, dd, ee, ee, ee, ee), Array(ff, aa, bb, zks), Array(ee, kks), Array(ee, zz, zks))

//读取第一个元素
scala> mapRDD.first
---输出----
res1: Array[String] = Array(aa, bb, cc, aa, aa, aa, dd, dd, ee, ee, ee, ee)

```

flatMap

有时候，我们希望对某个元素生成多个元素，实现该功能的操作叫作 flatMap()
faltMap的函数应用于每一个元素，对于每一个元素返回的是多个元素组成的迭代器(想要了解更多，请参考[scala的flatMap和map用法)](https://blog.csdn.net/t1dmzks/article/details/69858060#t23)

例如我们将数据切分为单词
scala版本

```
    scala>  val lines = sc.textFile("F:\\sparktest\\sample.txt")
    scala> val flatMapRDD = lines.flatMap(line=>line.split("\\s"))
    scala> flatMapRDD.first() 
	---输出----
	res0: String = aa

```
<!--
create time: 2018-03-31 11:09:44
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

