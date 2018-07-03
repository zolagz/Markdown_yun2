# MySql2HDFS

mysql数据通过MapReduce编程方式上传到HDFS

更简洁的是用sqoop

![](http://p2ehgqigv.bkt.clouddn.com/18-4-23/7428301.jpg)

根据表结构创建dao对象，并实现Writable,DBWritable接口

```
    public static class emp implements Writable,DBWritable{
        int empno;
        String ename;
        String job;
        String hiredate;

        public emp(){}

        @Override
        public void write(DataOutput output) throws IOException {

            output.writeInt(this.empno);
            output.writeUTF(this.ename);
            output.writeUTF(this.job);
            output.writeUTF(this.hiredate);
        }

        @Override
        public void readFields(DataInput input) throws IOException {

            this.empno = input.readInt();
            this.ename = input.readUTF();
            this.job = input.readUTF();
            this.hiredate = input.readUTF();
        }

        @Override
        public void write(PreparedStatement statement) throws SQLException {

            statement.setInt(1,this.empno);
            statement.setString(2,this.ename);
            statement.setString(3,this.job);
            statement.setString(4,this.hiredate);
        }

        @Override
        public void readFields(ResultSet resultSet) throws SQLException {

            this.empno = resultSet.getInt(1);
            this.ename = resultSet.getString(2);
            this.job = resultSet.getString(3);
            this.hiredate = resultSet.getString(4);
        }

        @Override
        public String toString() {
            return
                    "empno=" + empno +
                            ", ename='" + ename + '\'' +
                            ", job='" + job + '\'' +
                            ", hiredate='" + hiredate + '\'';
        }
    }

```

代码 如下

```
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

import java.io.IOException;

public class mysql2hdfs {

    public static class mysqlMapper extends Mapper<LongWritable,emp,Text,Text>{

        @Override
        protected void map(LongWritable key, emp value, Context context) throws IOException, InterruptedException {

            context.write(new Text(value.ename),new Text(value.toString()));
        }

    }

    public static void main(String[] args) throws  Exception{

       String[] fields = {"empno","ename","job","hiredate"};

        Configuration conf = new Configuration();

        org.apache.hadoop.mapred.lib.db.DBConfiguration.configureDB(conf,"com.mysql.jdbc.Driver","jdbc:mysql://localhost:3306/db1","root","admin");
        Job job = Job.getInstance(conf);

        job.setJarByClass(mysql2hdfs.class);
        job.setMapperClass(mysqlMapper.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);

        org.apache.hadoop.mapreduce.lib.db.DBInputFormat.setInput(job,emp.class,"emp",null,"empno",fields);

        FileOutputFormat.setOutputPath(job,new Path("/Users/alfred/Downloads/git下载/b/"));

        System.exit(job.waitForCompletion(true) ? 0 : 1);


    }
}

```






<!--
create time: 2018-04-23 12:55:52
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

