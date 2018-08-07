# Phoenix安装以及jdbc实现

### [安装](http://phoenix.apache.org/installation.html)

* Download and expand the latest phoenix-[version]-bin.tar.
* Add the phoenix-[version]-server.jar to the classpath of all 	HBase region server and master and remove any previous 	version. An easy way to do this is to copy it into the HBase 	lib directory (use phoenix-core-[version].jar for Phoenix 3.x)
* Restart HBase.
* Add the phoenix-[version]-client.jar to the classpath of any Phoenix client.



jdbc 依赖：

```
        <!-- https://mvnrepository.com/artifact/org.apache.phoenix/phoenix-core -->
        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.14.0-HBase-1.3</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.lmax/disruptor -->
        <dependency>
            <groupId>com.lmax</groupId>
            <artifactId>disruptor</artifactId>
            <version>3.4.2</version>
        </dependency>
```

代码实现

```

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class PhenixCli {
    public static void main(String[] args) throws  Exception {
        Class.forName("org.apache.phoenix.jdbc.PhoenixDriver");
        Connection connect = DriverManager.getConnection("jdbc:phoenix:node1,node2,node3:2181");
        PreparedStatement statement = connect.prepareStatement("SELECT  * FROM HELLO");
        ResultSet result = statement.executeQuery();

        while (result.next()){
            String name = result.getString("name");
            String age = result.getString("age");
            String address = result.getString("address");
            System.out.println("name : " + name + "age :" + age + "address:" + address) ;
        }
        statement.close();
        connect.close();
    }
}
```

[在SQUIRREL中使用PHOENIX操作HBASE——创建表和视图](http://www.daniubiji.cn/archives/111)

<!--
create time: 2018-07-05 21:19:43
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

