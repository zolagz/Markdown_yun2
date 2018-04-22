# Linux安装Kibana

###1、kibana简介

Kibana是一个开源的分析与可视化平台，设计出来用于和Elasticsearch一起使用的。你可以用kibana搜索、查看、交互存放在Elasticsearch索引里的数据，使用各种不同的图表、表格、地图等kibana能够很轻易地展示高级数据分析与可视化。

Kibana让我们理解大量数据变得很容易。它简单、基于浏览器的接口使你能快速创建和分享实时展现Elasticsearch查询变化的动态仪表盘。安装Kibana非常快，你可以在几分钟之内安装和开始探索你的Elasticsearch索引数据，不需要写任何代码，没有其他基础软件依赖。

下载并解压然后

2、修改配置文件

```
/opt/kibana/kibana-5.2.2-linux-x86/config/kibana.yml
server.port: 5601
server.host: "你自己主机的ip"
elasticsearch.url:  "http://你自己主机的ip:9200"
kibana.index: ".kibana"
```
4、启动

需要先启动es服务

>bin/kibana --f /config/kibana.yml


启动完成后，进入http:localhost:5601页面

点击devTool 

<!--
create time: 2018-04-21 19:33:19
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

