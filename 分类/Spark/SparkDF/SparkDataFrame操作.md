---
title: DataFrame API操作
date: 2018-06-26 15:39:23
tags: spark rdd dataFrame
---



people.json

```
{"name":"zhangsan", "age":30}
{"name":"Michael"}
{"name":"Andy", "age":30}
{"name":"Justin", "age":19}
```

```


import org.apache.spark.sql.SparkSession

/**
  * DataFrame API基本操作
  */
object DataFrameApp {

  def main(args: Array[String]): Unit = {

    val spark = SparkSession.builder().appName("DataFrameApp").master("local[2]").getOrCreate()

    //将json文件加载成一个dataframe
    val peopleDF = spark.read.format("json").load("src/main/data/people.json")

    //输出dataframe对应的schema信息
    peopleDF.printSchema()

    //输出数据集的前20条
    peopleDF.show()

    //查询某列所有的数据： select name from table
    peopleDF.select("name").show()

    //查询某几列所有的数据，并对列进行计算： select name,age+10 as age2 from table
    peopleDF.select(peopleDF.col("name"), (peopleDF.col("age") + 10).as("age2")).show()

    //根据某一列的值进行过滤： select * from table where age>19
    peopleDF.filter(peopleDF.col("age") > 19).show()

    //根据某一行进行分组，然后再进行聚合操作： select age,count(1) from table group by age
    peopleDF.groupBy("age").count().show()

    spark.close()

  }
}

```

###student.data

```
1|Burke|1-300-746-8446|ullamcorper.velit.in@ametnullaDonec.co.uk
2|Kamal|1-668-571-5046|pede.Suspendisse@interdumenim.edu
3|Olga|1-956-311-1686|Aenean.eget.metus@dictumcursusNunc.edu
4|Belle|1-246-894-6340|vitae.aliquet.nec@neque.co.uk
5|Trevor|1-300-527-4967|dapibus.id@acturpisegestas.net
6|Laurel|1-691-379-9921|adipiscing@consectetueripsum.edu
7|Sara|1-608-140-1995|Donec.nibh@enimEtiamimperdiet.edu
8|Kaseem|1-881-586-2689|cursus.et.magna@euismod.org
9|Lev|1-916-367-5608|Vivamus.nisi@ipsumdolor.com
10|Maya|1-271-683-2698|accumsan.convallis@ornarelectusjusto.edu
11|Emi|1-467-270-1337|est@nunc.com
12|Caleb|1-683-212-0896|Suspendisse@Quisque.edu
13|Florence|1-603-575-2444|sit.amet.dapibus@lacusAliquamrutrum.ca
14|Anika|1-856-828-7883|euismod@ligulaelit.co.uk
15|Tarik|1-398-171-2268|turpis@felisorci.com
16|Amena|1-878-250-3129|lorem.luctus.ut@scelerisque.com
17|Blossom|1-154-406-9596|Nunc.commodo.auctor@eratSed.co.uk
18|Guy|1-869-521-3230|senectus.et.netus@lectusrutrum.com
19|Malachi|1-608-637-2772|Proin.mi.Aliquam@estarcu.net
20|Edward|1-711-710-6552|lectus@aliquetlibero.co.uk
21||1-711-710-6552|lectus@aliquetlibero.co.uk
22||1-711-710-6552|lectus@aliquetlibero.co.uk
23|NULL|1-711-710-6552|lectus@aliquetlbero.co.uk

```

```
import org.apache.spark.sql.SparkSession
/**
  * DataFrame中的操作
  */
object DataFrameCase {
  def main(args: Array[String]): Unit = {

    val spark = SparkSession.builder().appName("DataFrameCase").master("local[2]").getOrCreate()

    val rdd = spark.sparkContext.textFile("src/main/data/student.data")

    import spark.implicits._
    val studentDF = rdd.map(_.split("\\|")).map(line => Student(line(0).toInt, line(1), line(2), line(3))).toDF()

    studentDF.show(30, false)
    //前10条
    studentDF.take(10).foreach(println)
    //第一条记录
    studentDF.first()
    //前三条
    studentDF.head(3)
    //指定的列
    studentDF.select("name", "email").show(30, false)
    //过滤name=''
    studentDF.filter("name='' OR name='NULL'").show()

    //name以M开头的人
    studentDF.filter("SUBSTR(name,0,1)='M'").show()
    //查询内置函数
    spark.sql("show functions").show(1000)

    // //name以M开头的人
    studentDF.filter("substring(name,0,1)='M'").show()

    //排序
    studentDF.sort(studentDF("name")).show()
    studentDF.sort(studentDF("name").desc).show()
    studentDF.sort("name", "id").show()
    studentDF.sort(studentDF("name").asc, studentDF("id").desc).show()

    //重命名
    studentDF.select(studentDF("name").as("student_name")).show()

    //join操作
    val studentDF2 = rdd.map(_.split("\\|")).map(line => Student(line(0).toInt, line(1), line(2), line(3))).toDF()
    studentDF.join(studentDF2, studentDF.col("id") === studentDF2.col("id")).show()

    spark.stop()
  }

  case class Student(id: Int, name: String, phone: String, email: String)
}

```


```
1,zhangsan,20
2,lisi,30
3,wangwu,40

```


```
package com.boy.spark

import org.apache.spark.sql.types.{IntegerType, StringType, StructField, StructType}
import org.apache.spark.sql.{Row, SparkSession}

/**
  * DataFrame和RDD的互操作
  * 两种编程方式：
  * a:基于DataFrame API
  * b:基于sql API
  */
object DataFrameRDDApp {


  def main(args: Array[String]): Unit = {

    val spark = SparkSession.builder().appName("DataFrameRDDApp").master("local[2]").getOrCreate()

    //第一种方式：反射
    //inferReflection(spark)

    /** 第二种方式：编程
      * Create an RDD of Rows from the original RDD;
      * Create the schema represented by a StructType matching the structure of Rows in the RDD created in Step 1;
      * Apply the schema to the RDD of Rows via createDataFrame method provided by SparkSession.
      */
    program(spark)

    spark.stop()
  }

  //相当于一个java bean
  case class info(id: Int, name: String, age: Int)

  def program(spark: SparkSession): Unit = {
    //RDD ==> DataFrame
    val rdd = spark.sparkContext.textFile("src/main/data/infos.txt")

    //定义RDD
    val infoRDD = rdd.map(_.split(",")).map(line => Row(line(0).toInt, line(1), line(2).toInt))

    //定义结构
    val structType = StructType(Array(StructField("id", IntegerType, true),
      StructField("name", StringType, true),
      StructField("age", IntegerType, true)))

    val infoDF = spark.createDataFrame(infoRDD, structType)

    infoDF.printSchema()
    infoDF.show()

    //通过df的api进行操作
    infoDF.filter(infoDF.col("age") > 30).show()

    //通过sql的方式进行操作
    infoDF.createOrReplaceTempView("infos")
    spark.sql("select * from infos where age > 30").show()
  }


  private def inferReflection(spark: SparkSession) = {
    //RDD ==> DataFrame
    val rdd = spark.sparkContext.textFile("src/main/data/infos.txt")

    //注意：需要导入隐式转换
    import spark.implicits._
    val infoDF = rdd.map(_.split(",")).map(line => info(line(0).toInt, line(1), line(2).toInt)).toDF()

    infoDF.show()

    infoDF.filter(infoDF.col("age") > 30).show()

    infoDF.createOrReplaceTempView("infos")

    spark.sql("select * from infos where age > 30").show()
  }

}
```