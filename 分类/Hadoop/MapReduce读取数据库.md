# MapReduce读取数据库

![](http://p2ehgqigv.bkt.clouddn.com/18-4-11/58365921.jpg)
![](http://p2ehgqigv.bkt.clouddn.com/18-4-11/11093528.jpg)

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

编写DBMapper类，继承Mapper

```
public static class DBMapper extends Mapper<LongWritable,emp,LongWritable,Text>{

        @Override
        protected void map(LongWritable key, emp value, Context context) throws IOException, InterruptedException {

            context.write(new LongWritable(value.empno),new Text(value.toString()));
        }
    }
    
```


编写主程序

```
 private static final String[] RecodeNames = { "empno", "ename","job","hiredate" };

 public static void main(String[] args) throws Exception{

        Configuration conf = new Configuration();<p>

DBConfiguration.configureDB(conf,"com.mysql.jdbc.Driver","jdbc:mysql://localhost:3306/db1","root","admin");
        Job job = Job.getInstance(conf);

        job.setJarByClass(MRfromMysql.class);
        job.setMapperClass(DBMapper.class);

        job.setOutputKeyClass(LongWritable.class);
        job.setOutputValueClass(Text.class);

        DBInputFormat.setInput(job,emp.class,"emp",null,"empno",RecodeNames);
        FileOutputFormat.setOutputPath(job,new Path("/Users/alfred/Downloads/git下载/aaa/"));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
    
```



<!--
create time: 2018-04-11 09:55:27
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

