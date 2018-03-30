# 利用SparkSql操作Hivesql

pom依赖

```
    <properties>
        <scala.version>2.11.8</scala.version>
        <hadoop.version>2.7.4</hadoop.version>
        <spark.version>2.0.2</spark.version>
    </properties>
    <dependencies>
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

        <!--引入spark-sql的依赖-->
       <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.11</artifactId>
            <version>2.0.2</version>
        </dependency>

        <!--引入spark-hive的依赖-->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-hive_2.11</artifactId>
            <version>2.0.2</version>
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
创建data文件夹，并创建student.txt，内容如下
![](http://p2ehgqigv.bkt.clouddn.com/18-3-29/21631751.jpg)
###代码

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
create time: 2018-03-29 10:17:30
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

