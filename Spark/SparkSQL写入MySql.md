# SparkSQL写入MySql

person.txt文件内容如下

```
Michael 29
Andy 30
Justin 19
```
代码如下

```
package com.test.demo

import java.util.Properties
import org.apache.spark.SparkContext
import org.apache.spark.rdd.RDD
import org.apache.spark.sql.SparkSession

/*
todo:利用RDD转化DataFrame 利用反射机制
 */

// 定义样例类

case class Person(name:String,age:Int)

object CaseClassSchema {

  def main(args: Array[String]): Unit = {

    //todo:1、创建sparkSession对象

    val spark: SparkSession = SparkSession
      .builder().appName("CaseClassSchema").master("local[2]").getOrCreate()

    val sc: SparkContext = spark.sparkContext
    sc.setLogLevel("WARN")
    //todo:2、读取数据
    val dataRdd: RDD[String] = sc.textFile("/Users/alfred/Documents/people.txt")
    //todo:3、切分每一行,
    val lineArrayRDD: RDD[Array[String]] = dataRdd.map(_.split(" "))

    //todo:4、RDD关联Person
    val personRDD: RDD[Person] = lineArrayRDD.map(x=>Person(x(0),x(1).toInt))

    //todo:导入隐式转换
    import spark.implicits._

    //todo:5、将RDD转换成DataFrame
    val personDF = personRDD.toDF()



    //todo--------------------SQL操作风格 start-----------
    //todo:将DataFrame注册成表
    personDF.createOrReplaceTempView("t_person")
    //todo:传入sql语句，进行操作

    spark.sql("select * from t_person").show()

    spark.sql("select * from t_person where name='Andy'").show()

    spark.sql("select * from t_person order by age desc").show()

    //todo:8、把结果保存在mysql表中
    //todo:创建Properties对象，配置连接mysql的用户名和密码
    val prop =new Properties()
    prop.setProperty("user","root")
    prop.setProperty("password","admin")

    personDF.write.jdbc("jdbc:mysql://localhost:3306/db1","student1",prop)

    //todo:写入mysql时，可以配置插入mode，overwrite覆盖，append追加，ignore忽略，error默认表存在报错
    //resultDF.write.mode(SaveMode.Overwrite).jdbc("jdbc:mysql://localhost:3306/db1","student",prop)

    //todo-------------------DSL语法操作 start--------------
    //1、显示DataFrame的数据，默认显示20行
    personDF.show()
    //2、显示DataFrame的schema信息
    personDF.printSchema()
    //3、显示DataFrame记录数
    println(personDF.count())
    //4、显示DataFrame的所有字段
    personDF.columns.foreach(println)
    //5、取出DataFrame的第一行记录
    println(personDF.head())
    //6、显示DataFrame中name字段的所有值
    personDF.select("name").show()
    //7、过滤出DataFrame中年龄大于30的记录
    personDF.filter($"age" > 20).show()
    //8、统计DataFrame中年龄大于20的人数
    println(personDF.filter($"age">20).count())
    //9、统计DataFrame中按照年龄进行分组，求每个组的人数
    personDF.groupBy("age").count().show()
    
    sc.stop()
    spark.stop()
  }

}

```
pom依赖

```
<properties>
        <scala.version>2.11.8</scala.version>
        <hadoop.version>2.7.4</hadoop.version>
        <spark.version>2.0.2</spark.version>
    </properties>
    <dependencies>


        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.11</artifactId>
            <version>2.0.2</version>
        </dependency>

        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>${scala.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>${spark.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>${hadoop.version}</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.38</version>
        </dependency>
    </dependencies>

    <build>
        <sourceDirectory>src/main/scala</sourceDirectory>
        <testSourceDirectory>src/test/scala</testSourceDirectory>
        <plugins>
            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>3.2.2</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                            <goal>testCompile</goal>
                        </goals>
                        <configuration>
                            <args>
                                <arg>-dependencyfile</arg>
                                <arg>${project.build.directory}/.scala_dependencies</arg>
                            </args>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <filters>
                                <filter>
                                    <artifact>*:*</artifact>
                                    <excludes>
                                        <exclude>META-INF/*.SF</exclude>
                                        <exclude>META-INF/*.DSA</exclude>
                                        <exclude>META-INF/*.RSA</exclude>
                                    </excludes>
                                </filter>
                            </filters>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass></mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```


<!--
create time: 2018-04-15 17:29:51
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

