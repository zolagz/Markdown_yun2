# HBase示例（1）

HBase Java简单示例
HBase是一个分布式的、面向列的开源数据库，该技术来源于 Fay Chang 所撰写的Google论文“Bigtable：一个结构化数据的分布式存储系统”。就像Bigtable利用了Google文件系统（File System）所提供的分布式数据存储一样，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。

HBase – Hadoop Database，是一个高可靠性、高性能、面向列、可伸缩的分布式存储系统，利用HBase技术可在廉价PC Server上搭建起大规模结构化存储集群。

与FUJITSU Cliq等商用大数据产品不同，HBase是Google Bigtable的开源实现，类似Google Bigtable利用GFS作为其文件存储系统，HBase利用Hadoop HDFS作为其文件存储系统；Google运行MapReduce来处理Bigtable中的海量数据，HBase同样利用Hadoop MapReduce来处理HBase中的海量数据；Google Bigtable利用 Chubby作为协同服务，HBase利用Zookeeper作为对应。

![](http://a.hiphotos.baidu.com/baike/s%3D220/sign=4c1de59d7bcb0a4681228c3b5b62f63e/b3b7d0a20cf431ad8b01445e4b36acaf2fdd9881.jpg)

上图描述Hadoop EcoSystem中的各层系统。其中,HBase位于结构化存储层，Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供了高性能的计算能力，Zookeeper为HBase提供了稳定服务和failover机制。

此外，Pig和Hive还为HBase提供了高层语言支持，使得在HBase上进行数据统计处理变的非常简单。 Sqoop则为HBase提供了方便的RDBMS数据导入功能，使得传统数据库数据向HBase中迁移变的非常方便。
Hbase基本使用示例：

```
import java.io.IOException; 
import java.util.ArrayList; 
import java.util.List; 
import org.apache.hadoop.conf.Configuration; 
import org.apache.hadoop.hbase.HBaseConfiguration; 
import org.apache.hadoop.hbase.HColumnDescriptor; 
import org.apache.hadoop.hbase.HTableDescriptor; 
import org.apache.hadoop.hbase.KeyValue; 
import org.apache.hadoop.hbase.MasterNotRunningException; 
import org.apache.hadoop.hbase.ZooKeeperConnectionException; 
import org.apache.hadoop.hbase.client.Delete; 
import org.apache.hadoop.hbase.client.Get; 
import org.apache.hadoop.hbase.client.HBaseAdmin; 
import org.apache.hadoop.hbase.client.HTable; 
import org.apache.hadoop.hbase.client.HTablePool; 
import org.apache.hadoop.hbase.client.Put; 
import org.apache.hadoop.hbase.client.Result; 
import org.apache.hadoop.hbase.client.ResultScanner; 
import org.apache.hadoop.hbase.client.Scan; 
import org.apache.hadoop.hbase.filter.Filter; 
import org.apache.hadoop.hbase.filter.FilterList; 
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter; 
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp; 
import org.apache.hadoop.hbase.util.Bytes; 
 
public class HbaseTest { 
 
    public static Configuration configuration; 
    static { 
        configuration = HBaseConfiguration.create(); 
        configuration.set("hbase.zookeeper.property.clientPort", "2181"); 
        configuration.set("hbase.zookeeper.quorum", "192.168.1.100"); 
        configuration.set("hbase.master", "192.168.1.100:600000"); 
    } 
 
    public static void main(String[] args) { 
        // createTable("wujintao"); 
        // insertData("wujintao"); 
        // QueryAll("wujintao"); 
        // QueryByCondition1("wujintao"); 
        // QueryByCondition2("wujintao"); 
        //QueryByCondition3("wujintao"); 
        //deleteRow("wujintao","abcdef"); 
        deleteByCondition("wujintao","abcdef"); 
    } 
 
     
    public static void createTable(String tableName) { 
        System.out.println("start create table ......"); 
        try { 
            HBaseAdmin hBaseAdmin = new HBaseAdmin(configuration); 
            if (hBaseAdmin.tableExists(tableName)) {// 如果存在要创建的表，那么先删除，再创建 
                hBaseAdmin.disableTable(tableName); 
                hBaseAdmin.deleteTable(tableName); 
                System.out.println(tableName + " is exist,detele...."); 
            } 
            HTableDescriptor tableDescriptor = new HTableDescriptor(tableName); 
            tableDescriptor.addFamily(new HColumnDescriptor("column1")); 
            tableDescriptor.addFamily(new HColumnDescriptor("column2")); 
            tableDescriptor.addFamily(new HColumnDescriptor("column3")); 
            hBaseAdmin.createTable(tableDescriptor); 
        } catch (MasterNotRunningException e) { 
            e.printStackTrace(); 
        } catch (ZooKeeperConnectionException e) { 
            e.printStackTrace(); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
        System.out.println("end create table ......"); 
    } 
 
     
    public static void insertData(String tableName) { 
        System.out.println("start insert data ......"); 
        HTablePool pool = new HTablePool(configuration, 1000); 
        HTable table = (HTable) pool.getTable(tableName); 
        Put put = new Put("112233bbbcccc".getBytes());// 一个PUT代表一行数据，再NEW一个PUT表示第二行数据,每行一个唯一的ROWKEY，此处rowkey为put构造方法中传入的值 
        put.add("column1".getBytes(), null, "aaa".getBytes());// 本行数据的第一列 
        put.add("column2".getBytes(), null, "bbb".getBytes());// 本行数据的第三列 
        put.add("column3".getBytes(), null, "ccc".getBytes());// 本行数据的第三列 
        try { 
            table.put(put); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
        System.out.println("end insert data ......"); 
    } 
 
     
    public static void dropTable(String tableName) { 
        try { 
            HBaseAdmin admin = new HBaseAdmin(configuration); 
            admin.disableTable(tableName); 
            admin.deleteTable(tableName); 
        } catch (MasterNotRunningException e) { 
            e.printStackTrace(); 
        } catch (ZooKeeperConnectionException e) { 
            e.printStackTrace(); 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
 
    } 
     
     public static void deleteRow(String tablename, String rowkey)  { 
        try { 
            HTable table = new HTable(configuration, tablename); 
            List list = new ArrayList(); 
            Delete d1 = new Delete(rowkey.getBytes()); 
            list.add(d1); 
             
            table.delete(list); 
            System.out.println("删除行成功!"); 
             
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
         
 
    } 
 
      
     public static void deleteByCondition(String tablename, String rowkey)  { 
            //目前还没有发现有效的API能够实现根据非rowkey的条件删除这个功能能，还有清空表全部数据的API操作 
 
    } 
 
 
     
    public static void QueryAll(String tableName) { 
        HTablePool pool = new HTablePool(configuration, 1000); 
        HTable table = (HTable) pool.getTable(tableName); 
        try { 
            ResultScanner rs = table.getScanner(new Scan()); 
            for (Result r : rs) { 
                System.out.println("获得到rowkey:" + new String(r.getRow())); 
                for (KeyValue keyValue : r.raw()) { 
                    System.out.println("列：" + new String(keyValue.getFamily()) 
                            + "====值:" + new String(keyValue.getValue())); 
                } 
            } 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
 
     
    public static void QueryByCondition1(String tableName) { 
 
        HTablePool pool = new HTablePool(configuration, 1000); 
        HTable table = (HTable) pool.getTable(tableName); 
        try { 
            Get scan = new Get("abcdef".getBytes());// 根据rowkey查询 
            Result r = table.get(scan); 
            System.out.println("获得到rowkey:" + new String(r.getRow())); 
            for (KeyValue keyValue : r.raw()) { 
                System.out.println("列：" + new String(keyValue.getFamily()) 
                        + "====值:" + new String(keyValue.getValue())); 
            } 
        } catch (IOException e) { 
            e.printStackTrace(); 
        } 
    } 
 
     
    public static void QueryByCondition2(String tableName) { 
 
        try { 
            HTablePool pool = new HTablePool(configuration, 1000); 
            HTable table = (HTable) pool.getTable(tableName); 
            Filter filter = new SingleColumnValueFilter(Bytes 
                    .toBytes("column1"), null, CompareOp.EQUAL, Bytes 
                    .toBytes("aaa")); // 当列column1的值为aaa时进行查询 
            Scan s = new Scan(); 
            s.setFilter(filter); 
            ResultScanner rs = table.getScanner(s); 
            for (Result r : rs) { 
                System.out.println("获得到rowkey:" + new String(r.getRow())); 
                for (KeyValue keyValue : r.raw()) { 
                    System.out.println("列：" + new String(keyValue.getFamily()) 
                            + "====值:" + new String(keyValue.getValue())); 
                } 
            } 
        } catch (Exception e) { 
            e.printStackTrace(); 
        } 
 
    } 
 
     
    public static void QueryByCondition3(String tableName) { 
 
        try { 
            HTablePool pool = new HTablePool(configuration, 1000); 
            HTable table = (HTable) pool.getTable(tableName); 
 
            List<Filter> filters = new ArrayList<Filter>(); 
 
            Filter filter1 = new SingleColumnValueFilter(Bytes 
                    .toBytes("column1"), null, CompareOp.EQUAL, Bytes 
                    .toBytes("aaa")); 
            filters.add(filter1); 
 
            Filter filter2 = new SingleColumnValueFilter(Bytes 
                    .toBytes("column2"), null, CompareOp.EQUAL, Bytes 
                    .toBytes("bbb")); 
            filters.add(filter2); 
 
            Filter filter3 = new SingleColumnValueFilter(Bytes 
                    .toBytes("column3"), null, CompareOp.EQUAL, Bytes 
                    .toBytes("ccc")); 
            filters.add(filter3); 
 
            FilterList filterList1 = new FilterList(filters); 
 
            Scan scan = new Scan(); 
            scan.setFilter(filterList1); 
            ResultScanner rs = table.getScanner(scan); 
            for (Result r : rs) { 
                System.out.println("获得到rowkey:" + new String(r.getRow())); 
                for (KeyValue keyValue : r.raw()) { 
                    System.out.println("列：" + new String(keyValue.getFamily()) 
                            + "====值:" + new String(keyValue.getValue())); 
                } 
            } 
            rs.close(); 
 
        } catch (Exception e) { 
            e.printStackTrace(); 
        } 
 
    } 
 
} 

```
Hbase获取数据实例

```

/*
 * Need Packages:
 * commons-codec-1.4.jar
 *
 * commons-logging-1.1.1.jar
 *
 * hadoop-0.20.2-core.jar
 *
 * hbase-0.90.2.jar
 *
 * log4j-1.2.16.jar
 *
 * zookeeper-3.3.2.jar
 *
 */

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.KeyValue;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.filter.FilterList;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.util.Bytes;

public class HbaseSelecter
{
    public static Configuration configuration = null;
    static
    {
        configuration = HBaseConfiguration.create();
        //configuration.set("hbase.master", "192.168.0.201:60000");
        configuration.set("hbase.zookeeper.quorum", "idc01-hd-nd-03,idc01-hd-nd-04,idc01-hd-nd-05");
        //configuration.set("hbase.zookeeper.property.clientPort", "2181");
    }

    public static void selectRowKey(String tablename, String rowKey) throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Get g = new Get(rowKey.getBytes());
        Result rs = table.get(g);

        for (KeyValue kv : rs.raw())
        {
            System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
            System.out.println("Column Family: " + new String(kv.getFamily()));
            System.out.println("Column       :" + new String(kv.getQualifier()));
            System.out.println("value        : " + new String(kv.getValue()));
        }
    }

    public static void selectRowKeyFamily(String tablename, String rowKey, String family) throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Get g = new Get(rowKey.getBytes());
        g.addFamily(Bytes.toBytes(family));
        Result rs = table.get(g);
        for (KeyValue kv : rs.raw())
        {
            System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
            System.out.println("Column Family: " + new String(kv.getFamily()));
            System.out.println("Column       :" + new String(kv.getQualifier()));
            System.out.println("value        : " + new String(kv.getValue()));
        }
    }

    public static void selectRowKeyFamilyColumn(String tablename, String rowKey, String family, String column)
            throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Get g = new Get(rowKey.getBytes());
        g.addColumn(family.getBytes(), column.getBytes());

        Result rs = table.get(g);

        for (KeyValue kv : rs.raw())
        {
            System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
            System.out.println("Column Family: " + new String(kv.getFamily()));
            System.out.println("Column       :" + new String(kv.getQualifier()));
            System.out.println("value        : " + new String(kv.getValue()));
        }
    }

    public static void selectFilter(String tablename, List<String> arr) throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Scan scan = new Scan();// 实例化一个遍历器
        FilterList filterList = new FilterList(); // 过滤器List

        for (String v : arr)
        { // 下标0为列簇，1为列名，3为条件
            String[] wheres = v.split(",");

            filterList.addFilter(new SingleColumnValueFilter(// 过滤器
                    wheres[0].getBytes(), wheres[1].getBytes(),

                    CompareOp.EQUAL,// 各个条件之间是" and "的关系
                    wheres[2].getBytes()));
        }
        scan.setFilter(filterList);
        ResultScanner ResultScannerFilterList = table.getScanner(scan);
        for (Result rs = ResultScannerFilterList.next(); rs != null; rs = ResultScannerFilterList.next())
        {
            for (KeyValue kv : rs.list())
            {
                System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
                System.out.println("Column Family: " + new String(kv.getFamily()));
                System.out.println("Column       :" + new String(kv.getQualifier()));
                System.out.println("value        : " + new String(kv.getValue()));
            }
        }
    }

    public static void main(String[] args) throws Exception
    {
        if(args.length < 2){
            System.out.println("Usage: HbaseSelecter table key");
            System.exit(-1);
        }

        System.out.println("Table: " + args[0] + " , key: " + args[1]);
        selectRowKey(args[0], args[1]);

        /*
        System.out.println("------------------------行键  查询----------------------------------");
        selectRowKey("b2c", "yihaodian1002865");
        selectRowKey("b2c", "yihaodian1003396");

        System.out.println("------------------------行键+列簇 查询----------------------------------");
        selectRowKeyFamily("riapguh", "用户A", "user");
        selectRowKeyFamily("riapguh", "用户B", "user");

        System.out.println("------------------------行键+列簇+列名 查询----------------------------------");
        selectRowKeyFamilyColumn("riapguh", "用户A", "user", "user_code");
        selectRowKeyFamilyColumn("riapguh", "用户B", "user", "user_code");

        System.out.println("------------------------条件 查询----------------------------------");
        List<String> arr = new ArrayList<String>();
        arr.add("dpt,dpt_code,d_001");
        arr.add("user,user_code,u_0001");
        selectFilter("riapguh", arr);
        */
    }
}

```
Hbase 导出特定列 示例(小量数据):

```

/*
 * Need Packages:
 * commons-codec-1.4.jar
 *
 * commons-logging-1.1.1.jar
 *
 * hadoop-0.20.2-core.jar
 *
 * hbase-0.90.2.jar
 *
 * log4j-1.2.16.jar
 *
 * zookeeper-3.3.2.jar
 *
 * Example: javac -classpath ./:/data/chenzhenjing/code/panama/lib/hbase-0.90.2.jar:/data/chenzhenjing/code/panama/lib/hadoop-core-0.20-append-for-hbase.jar:/data/chenzhenjing/code/panama/lib/commons-logging-1.0.4.jar:/data/chenzhenjing/code/panama/lib/commons-lang-2.4.jar:/data/chenzhenjing/code/panama/lib/commons-io-1.2.jar:/data/chenzhenjing/code/panama/lib/zookeeper-3.3.2.jar:/data/chenzhenjing/code/panama/lib/log4j-1.2.15.jar:/data/chenzhenjing/code/panama/lib/commons-codec-1.3.jar   DiffHbase.java   
 */

import java.io.BufferedReader;
import java.io.File;
import java.io.IOException;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.io.StringReader;
import java.text.SimpleDateFormat;
import java.util.Date;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.KeyValue;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.filter.FilterList;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.util.Bytes;

class ColumnUtils {

    public static byte[] getFamily(String column){
        return getBytes(column, 0);
    }

    public static byte[] getQualifier(String column){
        return getBytes(column, 1);
    }

    private static byte[] getBytes(String column , int offset){
        String[] split = column.split(":");
        return Bytes.toBytes(offset > split.length -1 ? split[0] :split[offset]);
    }
}

public class DiffHbase
{
    public static Configuration configuration = null;
    static
    {
        configuration = HBaseConfiguration.create();
        configuration.set("hbase.zookeeper.quorum", "idc01-hd-ds-01,idc01-hd-ds-02,idc01-hd-ds-03");
    }

    public static void selectRowKey(String tablename, String rowKey) throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Get g = new Get(rowKey.getBytes());
        Result rs = table.get(g);

        for (KeyValue kv : rs.raw())
        {
            System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
            System.out.println("Column Family: " + new String(kv.getFamily()));
            System.out.println("Column       :" + new String(kv.getQualifier()) + "t");
            System.out.println("value        : " + new String(kv.getValue()));
        }
    }

    public static void selectRowKeyFamily(String tablename, String rowKey, String family) throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Get g = new Get(rowKey.getBytes());
        g.addFamily(Bytes.toBytes(family));
        Result rs = table.get(g);
        for (KeyValue kv : rs.raw())
        {
            System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
            System.out.println("Column Family: " + new String(kv.getFamily()));
            System.out.println("Column       :" + new String(kv.getQualifier()) + "t");
            System.out.println("value        : " + new String(kv.getValue()));
        }
    }

    public static void selectRowKeyFamilyColumn(String tablename, String rowKey, String family, String column)
        throws IOException
    {
        HTable table = new HTable(configuration, tablename);
        Get g = new Get(rowKey.getBytes());
        g.addColumn(family.getBytes(), column.getBytes());

        Result rs = table.get(g);

        for (KeyValue kv : rs.raw())
        {
            System.out.println("--------------------" + new String(kv.getRow()) + "----------------------------");
            System.out.println("Column Family: " + new String(kv.getFamily()));
            System.out.println("Column       :" + new String(kv.getQualifier()) + "t");
            System.out.println("value        : " + new String(kv.getValue()));
        }
    }



    private static final String USAGE = "Usage: DiffHbase [-o outfile] tablename infile filterColumns...";

    /**
     * Prints the usage message and exists the program.
     * 
     * @param message  The message to print first.
     */
    private static void printUsage(String message) {
        System.err.println(message);
        System.err.println(USAGE);
        throw new RuntimeException(USAGE);
    }

    private static void PrintId(String id, Result rs){
        String value = Bytes.toString( rs.getValue(ColumnUtils.getFamily("info:url"), ColumnUtils.getQualifier("info:url")));
        if(value == null){
            System.out.println( id + "\tNULL");
        }else{
            System.out.println( id + "\t" + value);
        }
    }

    private static void WriteId(String id, Result rs, FileOutputStream os){
        String value = Bytes.toString( rs.getValue(ColumnUtils.getFamily("info:url"), ColumnUtils.getQualifier("info:url")));
        try{
            if(value == null){
                os.write( (id + "\tNULL\n").getBytes());
            }else{
                os.write( (id + "\t" + value + "\n").getBytes());
            }
        }
        catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static void PrintRow(String id, Result rs){

        System.out.println("--------------------" + id + "----------------------------");
        for (KeyValue kv : rs.raw())
        {
            System.out.println(new String(kv.getFamily()) + ":" + new String(kv.getQualifier()) + " : " + new String(kv.getValue()));
        }
    }

    public static void main(String[] args) throws Exception
    { 
        if (args.length < 3) {
            printUsage("Too few arguments");
        }

        String outfile = null;
        String tablename = args[0];
        String dictfile  = args[1];
        int skilLen = 2;

        if( args[0].equals("-o")){
            outfile = args[1];
            tablename = args[2];
            dictfile  = args[3];
            skilLen = 4;
        }

        HTable table = new HTable(configuration, tablename);

        String[] filterColumns = new String[args.length - skilLen];
        System.arraycopy(args, skilLen, filterColumns, 0, args.length - skilLen);

        System.out.println("filterColumns: ");
        for(int i=0; i<filterColumns.length; ++i){
            System.out.println("\t" + filterColumns[i]);
        }

        FileOutputStream os = null;
        if(outfile != null){
            os = new FileOutputStream(outfile);
        }
        
        int count = 0;
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//设置日期格式

        File srcFile = new File(dictfile);
        FileInputStream in = new FileInputStream(srcFile);
        InputStreamReader isr = new InputStreamReader(in);
        BufferedReader br = new BufferedReader(isr);
        String read = null;
        while ((read = br.readLine()) != null) {
            String[] split = read.trim().split("\\s");   // space split
            if( split.length < 1 ){
                System.out.println("Error line: " + read);
                continue;
            }

            if( ++count % 1000 == 0){
                System.out.println(df.format(new Date()) + " : " + count + " rows processed." );  // new Date()为获取当前系统时间
            }
            // System.out.println("ROWKEY:" + split[0]);

            Get g = new Get(split[0].getBytes());
            Result rs = table.get(g);
            if( rs == null){
                System.out.println("No Result for " + split[0]);
                continue;
            }

            for(int i=0; i<filterColumns.length; ++i){
                String value = Bytes.toString(rs.getValue(ColumnUtils.getFamily(filterColumns[i]), ColumnUtils.getQualifier(filterColumns[i])));
                if(value == null){
                    if( os == null){
                        PrintId(split[0], rs);
                    }else{
                        WriteId(split[0], rs, os);
                    }

                    // PrintRow(split[0], rs);
                    break;
                }
            }
        }

        br.close();
        isr.close();
        in.close();

    }
}

```
Hbase Mapreduce示例：全库扫描(大量数据)：

```
package com.hbase.mapreduce;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HConstants;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.mapreduce.IdentityTableMapper;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.util.GenericOptionsParser;

import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;                                                                      
import org.apache.hadoop.hbase.filter.CompareFilter;                                                                                
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;                                                                      
import org.apache.hadoop.hbase.filter.BinaryComparator;                                                                             
import org.apache.hadoop.hbase.util.Bytes; 

import com.goodhope.utils.ColumnUtils;

public class ExportHbase {
    private static final String INFOCATEGORY = "info:storecategory";

    private static final String USAGE = "Usage: ExportHbase " +
        "-r <numReduceTasks> -indexConf <iconfFile>\n" +
        "-indexDir <indexDir> -webSite <amazon> [-needupdate <true> -isVisible -startTime <long>] -table <tableName> -columns <columnName1> " +
        "[<columnName2> ...]";

    /**
     * Prints the usage message and exists the program.
     * 
     * @param message  The message to print first.
     */
    private static void printUsage(String message) {
        System.err.println(message);
        System.err.println(USAGE);
        throw new RuntimeException(USAGE);
    }

    /**
     * Creates a new job.
     * @param conf 
     * 
     * @param args  The command line arguments.
     * @throws IOException When reading the configuration fails.
     */
    public static Job createSubmittableJob(Configuration conf, String[] args) 
        throws IOException {
        if (args.length < 7) {
            printUsage("Too few arguments");
        }

        int numReduceTasks = 1;
        String iconfFile = null;
        String indexDir = null;
        String tableName = null;
        String website = null;
        String needupdate = "";
        String expectShopGrade = "";
        String dino = "6";
        String isdebug = "0";
        long debugThreshold = 10000;
        String debugThresholdStr = Long.toString(debugThreshold);
        String queue = "offline";

        long endTime =  Long.MAX_VALUE;
        int maxversions = 1;
        long startTime = System.currentTimeMillis() - 28*24*60*60*1000l;
        long distartTime = System.currentTimeMillis() - 30*24*60*60*1000l;
        long diusedTime = System.currentTimeMillis() - 30*24*60*60*1000l;
        String startTimeStr = Long.toString(startTime);
        String diusedTimeStr = Long.toString(diusedTime);
        String quorum = null;

        String isVisible = "";
        List<String> columns = new ArrayList<String>() ;  

        boolean bFilter = false;

        // parse args
        for (int i = 0; i < args.length - 1; i++) {
            if ("-r".equals(args[i])) {
                numReduceTasks = Integer.parseInt(args[++i]);
            } else if ("-indexConf".equals(args[i])) {
                iconfFile = args[++i];
            } else if ("-indexDir".equals(args[i])) {
                indexDir = args[++i];
            } else if ("-table".equals(args[i])) {
                tableName = args[++i];
            } else if ("-webSite".equals(args[i])) {
                website = args[++i];
            } else if ("-startTime".equals(args[i])) {
                startTimeStr = args[++i];
                startTime = Long.parseLong(startTimeStr);
            } else if ("-needupdate".equals(args[i])) {
                needupdate = args[++i];
            } else if ("-isVisible".equals(args[i])) {
                isVisible = "true";
            } else if ("-shopgrade".equals(args[i])) {
                expectShopGrade = args[++i]; 
            } else if ("-queue".equals(args[i])) {
                queue = args[++i];
            } else if ("-dino".equals(args[i])) {
                dino = args[++i];
            } else if ("-maxversions".equals(args[i])) {
                maxversions = Integer.parseInt(args[++i]);
            } else if ("-distartTime".equals(args[i])) {
                distartTime = Long.parseLong(args[++i]); 
            } else if ("-diendTime".equals(args[i])) {
                endTime = Long.parseLong(args[++i]);
            } else if ("-diusedTime".equals(args[i])) {
                diusedTimeStr = args[++i];
                diusedTime = Long.parseLong(diusedTimeStr);
            } else if ("-quorum".equals(args[i])) {
                quorum = args[++i];
            } else if ("-filter".equals(args[i])) {
                bFilter = true;
            } else if ("-columns".equals(args[i])) {
                columns.add(args[++i]);
                while (i + 1 < args.length && !args[i + 1].startsWith("-")) {
                    String columnname = args[++i];
                    columns.add(columnname);
                    System.out.println("args column----: " + columnname);
                }
            } else if ("-debugThreshold".equals(args[i])) {
                isdebug = "1";
                debugThresholdStr = args[++i];
                debugThreshold =  Long.parseLong( debugThresholdStr );
            }
            else {
                printUsage("Unsupported option " + args[i]);
            }
        }

        if (distartTime > endTime) {
            printUsage("distartTime must <= diendTime");  
        }

        if (indexDir == null || tableName == null || columns.isEmpty()) {
            printUsage("Index directory, table name and at least one column must " +
                    "be specified");
        }

        if (iconfFile != null) {
            // set index configuration content from a file
            String content = readContent(iconfFile);
            conf.set("hbase.index.conf", content);
            conf.set("hbase.website.name", website);
            conf.set("hbase.needupdate.productDB", needupdate);
            conf.set("hbase.expect.shopgrade", expectShopGrade);
            conf.set("hbase.di.no", dino);
            conf.set("hbase.expect.item.visible", isVisible);
            conf.set("hbase.index.startTime", startTimeStr);
            conf.set("hbase.index.diusedTime", diusedTimeStr);
            conf.set("hbase.index.debugThreshold", debugThresholdStr);
            conf.set("hbase.index.debug", isdebug);
            if (quorum != null) {
                conf.set("hbase.zookeeper.quorum", quorum);
            }
            String temp = "";
            for (String column : columns) {
                temp = temp + column + "|";
            }
            temp = temp.substring(0, temp.length() - 1);
            conf.set("hbase.index.column", temp);
            System.out.println("hbase.index.column: " + temp);
        }


        Job job = new Job(conf, "export data from table " + tableName);
        ((JobConf) job.getConfiguration()).setQueueName(queue);

        // number of indexes to partition into
        job.setNumReduceTasks(numReduceTasks);
        Scan scan = new Scan();
        scan.setCacheBlocks(false);

        // limit scan range
        scan.setTimeRange(distartTime, endTime);
        //  scan.setMaxVersions(maxversions);
        scan.setMaxVersions(1);

        /* limit scan columns */
        for (String column : columns) {
            scan.addColumn(ColumnUtils.getFamily(column), ColumnUtils.getQualifier(column));
            scan.addFamily(ColumnUtils.getFamily(column));
        }

        // set filter
        if( bFilter ){
            System.out.println("only export guangtaobao data. ");
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
                    Bytes.toBytes("info"),
                    Bytes.toBytes("producttype"),
                    CompareFilter.CompareOp.EQUAL,
                    new BinaryComparator(Bytes.toBytes("guangtaobao")) );
            filter.setFilterIfMissing(true);
            scan.setFilter(filter);
        }

        TableMapReduceUtil.initTableMapperJob(tableName, scan, ExportHbaseMapper.class,
                Text.class, Text.class, job);
        // job.setReducerClass(ExportHbaseReducer.class);
        FileOutputFormat.setOutputPath(job, new Path(indexDir));


        return job;
    }

    /**
     * Reads xml file of indexing configurations.  The xml format is similar to
     * hbase-default.xml and hadoop-default.xml. For an example configuration,
     * see the <code>createIndexConfContent</code> method in TestTableIndex.
     * 
     * @param fileName  The file to read.
     * @return XML configuration read from file.
     * @throws IOException When the XML is broken.
     */
    private static String readContent(String fileName) throws IOException {
        File file = new File(fileName);
        int length = (int) file.length();
        if (length == 0) {
            printUsage("Index configuration file " + fileName + " does not exist");
        }

        int bytesRead = 0;
        byte[] bytes = new byte[length];
        FileInputStream fis = new FileInputStream(file);

        try {
            // read entire file into content
            while (bytesRead < length) {
                int read = fis.read(bytes, bytesRead, length - bytesRead);
                if (read > 0) {
                    bytesRead += read;
                } else {
                    break;
                }
            }
        } finally {
            fis.close();
        }

        return new String(bytes, 0, bytesRead, HConstants.UTF8_ENCODING);
    }

    /**
     * The main entry point.
     * 
     * @param args  The command line arguments.
     * @throws Exception When running the job fails.
     */
    public static void main(String[] args) throws Exception {
        Configuration conf = HBaseConfiguration.create();
        String[] otherArgs = 
            new GenericOptionsParser(conf, args).getRemainingArgs();
        Job job = createSubmittableJob(conf, otherArgs);
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }

}

//////////////////////////////////////////////////////////

package com.hbase.mapreduce;

import java.io.IOException;
import java.util.List;
import java.util.ArrayList;
import java.lang.String;
import java.lang.StringBuffer;

import org.apache.hadoop.io.Text;
import org.apache.hadoop.conf.Configurable;
import org.apache.hadoop.conf.Configuration;
import org.apache.commons.lang.StringUtils;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.hbase.KeyValue;

import com.goodhope.utils.ColumnUtils;


/**
 * Pass the given key and record as-is to the reduce phase.
 */
@SuppressWarnings("deprecation")
public class ExportHbaseMapper extends TableMapper<Text,Text> implements Configurable {
    private static final Text keyTEXT = new Text();
    private static final Text SENDTEXT = new Text();

    private Configuration conf = null;

    private long startTime = 0;
    List<String> columnMap = null;

    private long rCount = 0;
    private long errCount = 0;
    private int  debug  = 0;
    private long thresCount  = 10000;

    public void map(ImmutableBytesWritable key, Result value, Context context) throws IOException, InterruptedException {

        rCount++;

        String itemid = Bytes.toString(key.get());
        if (itemid.contains("&")) {
            context.getCounter("Error", "rowkey contains \"&\"").increment(1);
            return;
        }

        StringBuffer outstr = new StringBuffer();
        for (String col : columnMap) {

            String tmp = Bytes.toString(value.getValue(ColumnUtils.getFamily(col), ColumnUtils.getQualifier(col)));
            if (tmp == null){
                context.getCounter("Error", col+" No value in hbase").increment(1);
                
                errCount++;
                if( debug > 0 && (errCount % thresCount == 0)){
                    System.err.println( itemid + ": doesn't has " + col + " data!");
                }

                outstr.append("NULL" + "\t");
            }else{
                if( tmp.contains("guangtaobao") ){
                    outstr.append("1" + "\t");
                }else{
                    outstr.append(tmp.trim() + "\t");
                }
            }
        }

        if ( ! outstr.toString().isEmpty() ) {

            SENDTEXT.set( outstr.toString() );
            keyTEXT.set(itemid);
            context.write(keyTEXT, SENDTEXT);

            if( debug > 0 && (rCount % thresCount*10000 == 0)){
                System.out.println( SENDTEXT.toString() + keyTEXT.toString() );
            }
        }
        else
        {
            context.getCounter("Error", "No Colume output").increment(1);
            return;
        }
    }

    /**
     * Returns the current configuration.
     * 
     * @return The current configuration.
     * @see org.apache.hadoop.conf.Configurable#getConf()
     */
    @Override
        public Configuration getConf() {
            return conf;
        }

    /**
     * Sets the configuration. This is used to set up the index configuration.
     * 
     * @param configuration
     *            The configuration to set.
     * @see org.apache.hadoop.conf.Configurable#setConf(org.apache.hadoop.conf.Configuration)
     */
    @Override
        public void setConf(Configuration configuration) {
            this.conf = configuration;
            
            startTime = Long.parseLong(conf.get("hbase.index.startTime"));
            thresCount = Long.parseLong(conf.get("hbase.index.debugThreshold"));
            debug = Integer.parseInt(conf.get("hbase.index.debug"));

            String[] columns = conf.get("hbase.index.column").split("\\|");

            columnMap = new ArrayList<String>();
            for (String column : columns) {
                System.out.println("Output column: " + column);

                columnMap.add(column);
            }

        }

}

//////////////////////////////////////////////////////////

package com.hbase.utils;

import org.apache.hadoop.hbase.util.Bytes;

public class ColumnUtils {

        public static byte[] getFamily(String column){
                return getBytes(column, 0);
        }

        public static byte[] getQualifier(String column){
                return getBytes(column, 1);
        }

        private static byte[] getBytes(String column , int offset){
                String[] split = column.split(":");
                return Bytes.toBytes(offset > split.length -1 ? split[0] :split[offset]);
        }
}

```



<!--
create time: 2018-04-18 21:04:58
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

