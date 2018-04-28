
### 01 下载安装包
```shell
wget http://mirrors.hust.edu.cn/apache/hbase/1.3.1/hbase-1.3.1-bin.tar.gz
tar -zxvf hbase-1.3.1-bin.tar.gz -C /export/servers/
cd ../servers/
mv hbase-1.3.1 hbase
vi /etc/profile
-
export HBASE_HOME=/export/servers/hbase
export PATH=${HBASE_HOME}/bin:$PATH
-
source /etc/profile
```
### 02 修改配置文件
```shell
cd /export/servers/hbase/conf/
```
```shell
vi regionservers 
-
node02
node03
```
```shell
vi hbase-site.xml 
-
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://ns1/hbase</value>
  </property>
  <property>
     <name>hbase.cluster.distributed</name>
     <value>true</value>
  </property>
  <property>
     <name>hbase.master.port</name>
     <value>16000</value>
  </property>
   <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/export/data/zk/</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>node01,node02,node03</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```
```shell
vi hbase-env.sh
-
export JAVA_HOME=/export/servers/jdk
export HBASE_MANAGES_ZK=false

```
```shell
拷贝hadoop的配置文件到hbase的配置文件目录
```

### 03 分发安装文件并启动
```shell
scp -r /export/servers/hbase/ node02:/export/servers/
scp -r /export/servers/hbase/ node03:/export/servers/
start-hbase.sh 
```