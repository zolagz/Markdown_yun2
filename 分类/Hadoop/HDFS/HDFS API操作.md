# HDFS API操作

新建Maven工程，引入pom依赖

```

<dependency>  
            <groupId>org.apache.hadoop</groupId>  
            <artifactId>hadoop-common</artifactId>  
            <version>2.7.4</version>  
        </dependency>  
        <dependency>  
            <groupId>org.apache.hadoop</groupId>  
            <artifactId>hadoop-hdfs</artifactId>  
            <version>2.7.4</version>  
        </dependency>  
        <dependency>  
            <groupId>org.apache.hadoop</groupId>  
            <artifactId>hadoop-client</artifactId>  
            <version>2.7.4</version>  
 </dependency> 
        
```

###创建一个文件

```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;

public class hdfs {

    public static void main(String[] args) throws  Exception {

        Configuration conf = new Configuration();
        <!--这里指定使用的 hdfs 的文件系统-->
        conf.set("fs.defaultFS","hdfs://node1:9000");
        <!--指定java客户端访问hdfs文件系统的方式-->
        System.setProperty("HADOOP_USER_NAME","root");
        FileSystem fs = FileSystem.get(conf);
        fs.create(new Path("/Hdfs_java"),false);;
        
        <!--创建一个目录-->
        //fs.mkdirs(new Path("/myTest2"));
        
        <!--删除一个目录-->
        //fs.delete(new Path("/myTest2"),true);
        
		<!--文件下载-->
		
//		fs.copyToLocalFile(new Path("要下载文件路径"),new Path("下载保存到本地目录"))


        
        fs.close();
        
        
        
    }
}

```

[更多操作参考](http://blog.csdn.net/daxiang12092205/article/details/52717470)


<!--
create time: 2018-03-20 14:36:25
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

