# Scala链接数据库

Scala语法

预装数据库Mysql,登录用户名密码为：root：root，建立数据库db1，建立数据表emp；

```
package guan.test.demo1

import java.sql.{Connection, DriverManager, PreparedStatement}

object DBTest {

  def main(args: Array[String]): Unit = {

    Class.forName("com.mysql.jdbc.Driver")

    val con :Connection= DriverManager.getConnection("jdbc:mysql://localhost:3306/db1","root","admin")

    val prep: PreparedStatement= con.prepareStatement("select * from emp")

    val resultSet = prep.executeQuery()

    while (resultSet.next()){
      println(  "\t" + resultSet.getString(1) + "\t" + resultSet.getString(2) + "\t" + resultSet.getString(3))
    }
    
    resultSet.close()
    
    con.close()
  }

}
```


<!--
create time: 2018-03-26 15:51:36
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

