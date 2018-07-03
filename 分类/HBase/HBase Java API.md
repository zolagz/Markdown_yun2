# HBase Java API

#### 4.1 导入pom依赖

```xml
 		<dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-client</artifactId>
            <version>1.3.1</version>
        </dependency>
```

#### 4.2 添加配置文件

在resource目录下创建hbase-site.xml

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zk01,zk02,zk03</value>
        <description>The directory shared by region servers.
        </description>
    </property>
</configuration>
```

#### 4.3 连接Hbase

```java
//  hbase的两种连接方式：1）读取配置文件 只需要配置zookeeper 
        Configuration config = HBaseConfiguration.create();
        Connection connection = ConnectionFactory.createConnection(config);
//2）通过代码配置
	    Configuration configuration = new Configuration();
        configuration.set("hbase.zookeeper.quorum", "zk01:2181,zk02:2181,zk03:2181");
        connection = ConnectionFactory.createConnection();
```

#### 4.4 创建表

```java
 public static void main(String[] args) throws IOException {
        // 1.连接HBase
        // 1.1 HBaseConfiguration.create(); 
        Configuration config = HBaseConfiguration.create();
        // 1.2 创建一个连接
        Connection connection = ConnectionFactory.createConnection(config);
        // 1.3 从连接中获得一个Admin对象
        Admin admin = connection.getAdmin();
        // 2.创建表
        // 2.1 判断表是否存在
        TableName tableName = TableName.valueOf("user");
        if (!admin.tableExists(tableName)) {
            //2.2 如果表不存在就创建一个表
            HTableDescriptor hTableDescriptor = new HTableDescriptor(tableName);
            hTableDescriptor.addFamily(new HColumnDescriptor("base_info"));
            admin.createTable(hTableDescriptor);
            System.out.println("创建表");
        }
    }
```

#### 4.5 打印表的信息

```java
@Before
public void initConnection() {
        try {
            connection = ConnectionFactory.createConnection(config);
        } catch (IOException e) {
            System.out.println("连接数据库失败");
        }
}

@Test 
public void tableInfo() throws IOException {
        // 1.定义表的名称
        TableName tableName = TableName.valueOf("user");
        // 2.获取表
        Table table = connection.getTable(tableName);
        // 3.获取表的描述信息
        HTableDescriptor tableDescriptor = table.getTableDescriptor();
        // 4.获取表的列簇信息
        HColumnDescriptor[] columnFamilies = tableDescriptor.getColumnFamilies();
        for (HColumnDescriptor columnFamily : columnFamilies) {
            // 5.获取表的columFamily的字节数组
            byte[] name = columnFamily.getName();
            // 6.使用hbase自带的bytes工具类转成string
            String value = Bytes.toString(name);
            // 7.打印
            System.out.println(value);
        }
    }
```

#### 4.6 添加数据(PUT)

![](http://p2ehgqigv.bkt.clouddn.com/18-3-18/76286533.jpg)

```java
@Before
public void initConnection() {
        try {
            connection = ConnectionFactory.createConnection(config);
        } catch (IOException e) {
            System.out.println("连接数据库失败");
        }
}
@Test
public void put() throws IOException {
        // 1.定义表的名称
        TableName tableName = TableName.valueOf("user");
        // 2.获取表
        Table table = connection.getTable(tableName);
        // 3.准备数据
        String rowKey = "rowkey_10";
        Put zhangsan = new Put(Bytes.toBytes(rowKey));
        zhangsan.addColumn(Bytes.toBytes("base_info"), Bytes.toBytes("username"), Bytes.toBytes("张三"));
        zhangsan.addColumn(Bytes.toBytes("base_info"), Bytes.toBytes("sex"), Bytes.toBytes("1"));
        zhangsan.addColumn(Bytes.toBytes("base_info"), Bytes.toBytes("address"), Bytes.toBytes("北京市"));
        zhangsan.addColumn(Bytes.toBytes("base_info"), Bytes.toBytes("birthday"), Bytes.toBytes("2014-07-10"));
        // 4. 添加数据
        table.put(zhangsan);
        table.close();
}
```

#### 4.7 获取数据(Get)

```java
 @Test
public void get() throws IOException {
        // 1.定义表的名称
        TableName tableName = TableName.valueOf("user");
        // 2.获取表
        Table table = connection.getTable(tableName);
        // 3.准备数据
        String rowKey = "rowkey_10";
        // 4.拼装查询条件
        Get get = new Get(Bytes.toBytes(rowKey));
        // 5.查询数据
        Result result = table.get(get);
        // 6.打印数据 获取所有的单元格
        List<Cell> cells = result.listCells();
        for (Cell cell : cells) {
            // 打印rowkey,family,qualifier,value
            System.out.println(Bytes.toString(CellUtil.cloneRow(cell))
                    + "==> " + Bytes.toString(CellUtil.cloneFamily(cell))
                    + "{" + Bytes.toString(CellUtil.cloneQualifier(cell))
                    + ":" + Bytes.toString(CellUtil.cloneValue(cell)) + "}");
        }
}
```

####  4.8 全表扫描（scan 慎用）

```java
@Test
public void scan() throws IOException {
        // 1.定义表的名称
        TableName tableName = TableName.valueOf("user");
        // 2.获取表
        Table table = connection.getTable(tableName);
        // 3.全表扫描
        Scan scan = new Scan();
        // 4.获取扫描结果
        ResultScanner scanner = table.getScanner(scan);
        Result result = null;
        // 5. 迭代数据
        while ((result = scanner.next()) != null) {
            // 6.打印数据 获取所有的单元格
            List<Cell> cells = result.listCells();
            for (Cell cell : cells) {
                // 打印rowkey,family,qualifier,value
                System.out.println(Bytes.toString(CellUtil.cloneRow(cell))
                        + "==> " + Bytes.toString(CellUtil.cloneFamily(cell))
                        + "{" + Bytes.toString(CellUtil.cloneQualifier(cell))
                        + ":" + Bytes.toString(CellUtil.cloneValue(cell)) + "}");
            }
        }
}
```
如果数据量巨大，这种方法非常消耗性能

一般配合区间范围获取数据  如下👇

#### 4.9 范围查询（开始行-结束行）

在4.8的基础上修改代码如下

```java
 Scan scan = new Scan();
 scan.setStartRow(Bytes.toBytes("rowkey_1"));
 scan.setStopRow(Bytes.toBytes("rowkey_2"));
```


### 5、使用过滤器进行查询

#### 5.1 比较过滤器有几种？

* RowFilter   基于RowKey的过滤
* FamilyFilter 基于列簇的过滤
* QualifierFilter 基于字段的过滤
* ValueFilter 基于值的过滤
* DependentColumnFilter 参考值过滤器

#### 5.2 比较运算符？

* LESS 匹配小于设定值的值
* LESS_OR_EQUAL 匹配小于或等于设定值的值
* EQUAL 匹配等于设定值的值
* NOT_EQUAL 匹配与设定值不相等的值
* GREATER_OR_EQUAL 匹配大于或等于设定值的值
* GREATER 匹配大于设定值的值
* NO_OP 排除一切值

#### 5.3 比较器有哪些？

* BinaryComparator 使用Bytes.compareTo()比较当前的阈值
* BinaryPrefixComparator 与上面的相似，使用Bytes.compareTo()进行匹配，但是是从左端开始前缀匹配
* NullComparator 不做匹配，只判断当前值不是null
* BitComparator 通过BitWiseOp类提供的按位与（AND）、或（OR）、异或（XOR）操作执行位级比较。
* RegexStringComparator 根据一个正则表达式，在实例化这个比较器的时候去匹配表中的数据。
* SubStringComparator 把阈值和表中数据String实例，同时通过contains()操作匹配字符串

#### 5.4 示例代码

```java
//创建RowFilter过滤器
 RowFilter rowFilter = new RowFilter(CompareFilter.CompareOp.EQUAL
                , new BinaryComparator("rowkey_10".getBytes()));
//创建familyFilter过滤器
FamilyFilter familyFilter = new FamilyFilter(CompareFilter.CompareOp.NO_OP,
                new BinaryComparator("base_Info".getBytes()));
//创建qualifierFilter过滤器
QualifierFilter qualifierFilter = new QualifierFilter(CompareFilter.CompareOp.EQUAL,
                new BinaryComparator("username".getBytes()));
        //创建valueFilter过滤器
ValueFilter valueFilter = new ValueFilter(CompareFilter.CompareOp.EQUAL,
                new BinaryComparator("张三".getBytes()));
```

#### 5.5、查询值等于张三的所有数据

```java
@Test
public void tesValueFilter() throws IOException {
        //1、创建过滤器
        ValueFilter filter = new ValueFilter(CompareFilter.CompareOp.EQUAL, new BinaryComparator("张三".getBytes()));
        //2、创建扫描器
        Scan scan = new Scan();
        //3、将过滤器设置到扫描器中
        scan.setFilter(filter);
        //4、获取HBase的表
        Table table = connection.getTable(TableName.valueOf("user"));
        //5、扫描HBase的表（注意过滤操作是在服务器进行的，也即是在regionServer进行的）
        ResultScanner scanner = table.getScanner(scan);
        for (Result result : scanner) {
            // 6.打印数据 获取所有的单元格
            List<Cell> cells = result.listCells();
            for (Cell cell : cells) {
                // 打印rowkey,family,qualifier,value
                System.out.println(Bytes.toString(CellUtil.cloneRow(cell))
                        + "==> " + Bytes.toString(CellUtil.cloneFamily(cell))
                        + "{" + Bytes.toString(CellUtil.cloneQualifier(cell))
                        + ":" + Bytes.toString(CellUtil.cloneValue(cell)) + "}");
            }
        }
}
```

### 7、扩展阅读

* [Hive与Hbase整合](https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration)
* [Hbase协处理器（一）](https://www.ibm.com/developerworks/cn/opensource/os-cn-hbase-coprocessor1/index.html)
* [Hbase协处理器（二）](https://www.ibm.com/developerworks/cn/opensource/os-cn-hbase-coprocessor2/index.html)
* [Hbase二级索引](https://www.cnblogs.com/MOBIN/p/5579088.html)
* [来自华为的 HBase 二级索引](https://mp.weixin.qq.com/s?__biz=MjM5NzM0MjcyMQ==&mid=200424745&idx=2&sn=7471bef16ee7f03d9ffae933a84f3cc2&scene=2&from=timeline&isappinstalled=0#rd)
* [Phoenix hbase](http://phoenix.apache.org/)






<!--
create time: 2018-03-18 15:38:10
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

