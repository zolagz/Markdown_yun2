# ES-Head插件安装部署 

###准备工作
1. 安装：nodejs,xz，

![](http://p2ehgqigv.bkt.clouddn.com/18-4-7/11824890.jpg)

下载下来的jar包是xz格式的，一般的linux可能不识别，还需要安装xz.（前面已经安装）

2 . 解压nodejs的安装包:

xz -d node-v6.10.1-linux-x64.tar.xz

tar -xvf node-v6.10.1-linux-x64.tar -C /usr/local/

3. 解压完node的安装文件后，需要配置下环境变量

[root@linux-node1 local]#  cat >> /etc/profile << END

# set node environmentexport

export NODE_HOME=/usr/local/node-v6.10.1-linux-x64

export PATH=$PATH:$NODE_HOME/bin

END

④别忘记立即执行以下

source /etc/profile

⑤这个时候可以测试一下node是否生效：


下载插件：git clone git://github.com/mobz/elasticsearch-head.git

进入elasticsearch-head目录下；执行npm install

###特别注意
需要修改es安装 目录下的conf/es.yml文件，加入如下配置

```
http.cors.enabled: true

http.cors.allow-origin: "*"

```


[参考链接：](https://blog.csdn.net/m0_37886429/article/details/68924087)

<!--
create time: 2018-04-07 09:31:34
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

