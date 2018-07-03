# ES之Java API操作

[参考链接](http://blog.java1234.com/index.html)


1、	需要新建 maven项目 
 
2、	基于maven的pom 导入坐标依赖 

```
  <dependencies>
  	<dependency>
  		<groupId>org.elasticsearch</groupId>
  		<artifactId>elasticsearch</artifactId>
  		<version>2.4.0</version>
  	</dependency>
  	<dependency>
  		<groupId>junit</groupId>
  		<artifactId>junit</artifactId>
  		<version>4.12</version>
  	</dependency>
  </dependencies>

```
ElasticSearch2.4.0 依赖 lucene5.5.2 版本 
 
   当直接在ElasticSearch 建立文档对象时，如果索引不存在的，默认会自动创建，映射采用默认方式 
   
*	ElasticSearch 服务默认端口 9300 
* 	Web 管理平台端口 9200 
  
    获取 Transport Client调用
  
    对于演示应用程序，（通过 App.java 中执行的初始化）选择 Transport Client，并保持 Elasticsearch 执行最低级别的处理：
  
    这里注意：如果连接到一个 Elasticsearch 集群，构建器可以接受多个地址。（在本课程中，您只有一个 localhost 节点。）连接到端口 9300，而不是像之前在 REST API 的 CURL 中一样连接到 9200。Java 客户端将会使用这个特殊端口，使用端口 9200将不起作用。
  
    使用org.elasticsearch.client.Client连接服务器。所以任何操作都需要用上，我们把它的创建放置到@Before中，操作最后别忘记执行client.close()方法关闭。

```
   private Client client;
	/** 获取client */
	@Before
	public void getClient() throws Exception {
		client = TransportClient.builder().build()
				.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));
	}

```


3、	建立文档， 自动创建索引 
方式一：拼装json的字符串。

```
	@Test
	public void createIndexNoMapping() {
		String json = "{" +
		        "\"id\":\"1\"," +
		        "\"title\":\"基于Lucene的搜索服务器\"," +
		        "\"content\":\"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口\"" +
		    "}";
		IndexResponse indexResponse = this.client.prepareIndex("blog", "article", "1").setSource(json).execute().actionGet();
		// 结果获取
		String index = indexResponse.getIndex();
		String type = indexResponse.getType();
		String id = indexResponse.getId();
		long version = indexResponse.getVersion();
		boolean created = indexResponse.isCreated();
		System.out.println(index + " : " + type + ": " + id + ": " + version + ": " + created);
		// 关闭连接
		client.close();
	}

```


方式二：使用Map集合

```
// ElasticSearch 测试程序 
public class ElasticSearchTest {

    /**创建索引、类型、文档*/
	@Test
	public void createIndexNoMapping1() {
		Map<String, Object> json = new HashMap<String, Object>();
		json.put("id", "2");
		json.put("title", "基于Lucene的搜索服务器");
		json.put("content", "它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口");
		IndexResponse indexResponse = this.client.prepareIndex("blog", "article", "2").setSource(json).execute().actionGet();
		// 结果获取
		String index = indexResponse.getIndex();
		String type = indexResponse.getType();
		String id = indexResponse.getId();
		long version = indexResponse.getVersion();
		boolean created = indexResponse.isCreated();
		System.out.println(index + " : " + type + ": " + id + ": " + version + ": " + created);
		// 关闭连接
		client.close();
	}
	
```

###方式三：使用es的帮助类，创建json对象

```
	@Test
	public void createIndexNoMapping2() throws Exception{
		// 使用es的帮助类，创建一个json方式的对象
		/**
		 * 描述json 数据
		 * {id:xxx, title:xxx, content:xxx}
		 */
		XContentBuilder sourceBuilder = XContentFactory.jsonBuilder()
			.startObject()
				.field("id", 3)
				.field("title", "ElasticSearch是一个基于Lucene的搜索服务器")
				.field("content",
						"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。")
			.endObject();
		// 创建索引
		IndexResponse indexResponse = client.prepareIndex("blog", "article", "3").setSource(sourceBuilder).get();
		// 结果获取
		String index = indexResponse.getIndex();
		String type = indexResponse.getType();
		String id = indexResponse.getId();
		long version = indexResponse.getVersion();
		boolean created = indexResponse.isCreated();
		System.out.println(index + " : " + type + ": " + id + ": " + version + ": " + created);
        // 关闭连接
		client.close();
	}
}
```
没有映射创建，自动创建索引 和 映射
名称为blog
 
自动创建索引映射

文档数据 （type 文档类型 ）
 
###6.2． 搜索文档数据（单个索引）

```
使用GetResponse查询
    /**
     * get API 获取指定文档信息
     */
    @Test
    public void testGetData() throws Exception {
    	Client client = TransportClient.builder().build()
				.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));
        GetResponse response = client.prepareGet("blog", "article", "1")
                .setOperationThreaded(false)    // 线程安全
                .get();
        System.out.println(response.getSourceAsString());
		// 关闭连接
		client.close();
}

```


6.3． 搜索文档数据（多个索引）

/**
     * 测试multi get
     * 搜索
     * 从不同的index, type, 和id中获取
     */
    @Test
    public void testMultiGet() {
        MultiGetResponse multiGetResponse = client.prepareMultiGet()
        .add("blog", "article", "1")
        .add("blog", "article", "2", "3", "4")
        .add("blog", "article", "2")
        .get();
        
        for (MultiGetItemResponse itemResponse : multiGetResponse) {
            GetResponse response = itemResponse.getResponse();
            if (response.isExists()) {
                String sourceAsString = response.getSourceAsString();
                System.out.println(sourceAsString);
            }
        }
        client.close();
    }


6.4． 更新文档数据
【更新方式一】

```

/**
	 * 测试更新 update API 使用 updateRequest 对象
	 */
	@Test
	public void testUpdate() throws Exception {
		UpdateRequest updateRequest = new UpdateRequest();
		updateRequest.index("blog");
		updateRequest.type("article");
		updateRequest.id("1");
		updateRequest.doc(XContentFactory.jsonBuilder().startObject()
				// 对没有的字段添加, 对已有的字段替换
				.field("title", "ElasticSearch是一个基于Lucene的搜索服务器")
				.field("content",
						"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。")
				.field("createDate", "2018-10-11").endObject());
		UpdateResponse response = client.update(updateRequest).get();

		// 打印
		String index = response.getIndex();
		String type = response.getType();
		String id = response.getId();
		long version = response.getVersion();
		System.out.println(index + " : " + type + ": " + id + ": " + version);
		// 关闭连接
		client.close();
	}

```

###【更新方式二】

```
    /**
	 * 测试更新 update API 使用 updateRequest 对象
	 */
	@Test
	public void testUpdate2() throws Exception {
		// 使用updateRequest对象及documents进行更新
		UpdateResponse response = client
				.update(new UpdateRequest("blog", "article", "1").doc(XContentFactory.jsonBuilder().startObject()
						.field("title", "什么是Elasticsearch，ElasticSearch是一个基于Lucene的搜索服务器").endObject()))
				.get();
		// 打印
		String index = response.getIndex();
		String type = response.getType();
		String id = response.getId();
		long version = response.getVersion();
		System.out.println(index + " : " + type + ": " + id + ": " + version);
		// 关闭连接
		client.close();
	}

```
【更新方式三】

```
/**
     * 测试upsert方法
     */
    @Test
    public void testUpsert() throws Exception {
        // 设置查询条件, 查找不到则添加
        IndexRequest indexRequest = new IndexRequest("blog", "article", "1")
            .source(XContentFactory.jsonBuilder()
                .startObject()
                .field("title", "搜索服务器")
				.field("content",
						"它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。")
                .endObject());
        // 设置更新, 查找到更新下面的设置
        UpdateRequest upsert = new UpdateRequest("blog", "article", "1")
            .doc(XContentFactory.jsonBuilder()
                    .startObject()
                        .field("user", "李四")
                    .endObject())
            .upsert(indexRequest);
        
        client.update(upsert).get();
        client.close();
    }

```
###6.5． 删除文档数据

```
    /**
     * 测试 delete
     */
    @Test
    public void testDelete() {
        DeleteResponse response = client.prepareDelete("blog", "article", "1")
                .get();
        String index = response.getIndex();
        String type = response.getType();
        String id = response.getId();
        long version = response.getVersion();
        System.out.println(index + " : " + type + ": " + id + ": " + version);
        client.close();
    }

```

###7． 条件查询QueryBuilder

到目前为止，我们使用了REST API和简单查询或GET请求来搜索数据。更改索引时，无论想执行的操作是更改映射还是文档索引化，都要用REST API向Elasticsearch发送JSON结构的数据。类似地，如果想发送的不是一个简单的查询，仍然把它封装为JSON结构并发送给Elasticsearch。这就是所谓的查询DSL。从更宏观的角度看，Elasticsearch支持两种类型的查询：基本查询和复合查询。
基本查询，如词条查询用于查询实际数据。
第二种查询为复合查询，如布尔查询，可以合并多个查询。
查询数据 主要依赖QueryBuilder对象 ，可以通过QueryBuilders获取各种查询 ：（基于lucene）
boolQuery() 布尔查询，可以用来组合多个查询条件 
fuzzyQuery() 相似度查询 
matchAllQuery() 查询所有数据 
regexpQuery() 正则表达式查询 
termQuery() 词条查询 
wildcardQuery() 模糊查询 
使用SearchResponse获取，支持各种查询：

```
    /**
     * 搜索在elasticSearch中创建的文档对象
     */
	@Test
	public void testSearch() throws Exception {
		// 搜索数据
		// get()方法 === execute().actionGet()
		// SearchResponse searchResponse = client.prepareSearch("blob1")
		// .setTypes("article").setQuery(QueryBuilders.matchAllQuery()).get();
		/**
		 * 1、ElasticSearch提供QueryBuileders.queryStringQuery(搜索内容)
		 * 查询方法，对所有字段进行分词查询
		 */
//		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
//				.setQuery(QueryBuilders.queryStringQuery("全面"))
//				.get();
		/**
		 * 2、 只想查询content里包含全文 ，使用QueryBuilders.wildcardQuery模糊查询 *任意字符串 ?任意单个字符
		 */
//		SearchResponse searchResponse = client.prepareSearch("blog").setTypes("article")
//				.setQuery(QueryBuilders.wildcardQuery("content", "*全文*")).get();
		/** 3、 查询content词条为“搜索” 内容，使用TermQuery */
		SearchResponse searchResponse = client.prepareSearch("blog")
				.setTypes("article")
				.setQuery(QueryBuilders.termQuery("content", "全文")).get();
		SearchHits hits = searchResponse.getHits(); // 获取命中次数，查询结果有多少对象
		System.out.println("查询结果有：" + hits.getTotalHits() + "条");
		Iterator<SearchHit> iterator = hits.iterator();
		while (iterator.hasNext()) {
			SearchHit searchHit = iterator.next(); // 每个查询对象
			System.out.println(searchHit.getSourceAsString()); // 获取字符串格式打印
			System.out.println("id:" + searchHit.getSource().get("id"));
			System.out.println("title:" + searchHit.getSource().get("title"));
			System.out.println("content:" + searchHit.getSource().get("content"));
			for (Iterator<SearchHitField> ite = searchHit.iterator(); ite.hasNext();) {
				SearchHitField next = ite.next();
				System.out.println(next.getValues());
			}
		}
		// 关闭连接
		client.close();
	}

```
<!--
create time: 2018-03-16 17:56:15
Author: Alfred

This file is created by Marboo<http://marboo.io> template file $MARBOO_HOME/.media/starts/default.md
本文件由 Marboo<http://marboo.io> 模板文件 $MARBOO_HOME/.media/starts/default.md 创建
-->

