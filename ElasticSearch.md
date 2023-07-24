# 第一章 ElasticSearch入门

## 一，索引

1. 数据格式

   Elasticsearch是面向文档型数据库，一条数据在这里就是一个文档。

   与mysql概念对比

   ![](F:\截图\屏幕截图 2023-07-22 201531.png)

   ES里的Index可以看做一个库，而Types相当于表，Documents则相当于表的行。这里Types的概念己经被逐渐弱化，Elasticsearch6.X中，一个index下己经只能包含一个 Type，Elasticsearch7.X中，Type的概念己经被删除了。

2. 索引创建

   ```http
   [put]http://47.113.148.39:9200/shopping
   ```

3. 获取索引

   ```http
   [get]http://47.113.148.39:9200/shopping
   ```

4. 查看所有的索引

   ```http
   [get]http://47.113.148.39:9200/_cat/indices?v
   ```

5. 删除索引

   ```http
   [delete]http://47.113.148.39:9200/shopping
   ```

## 二，文档

1. 创建文档

   ```http
   [post]http://47.113.148.39:9200/shopping/_doc		//随机生成id
   [post]http://47.113.148.39:9200/shopping/_doc/1001	//设置指定id
   [post]http://47.113.148.39:9200/shopping/_create/1001	//也可以使用_create
   ```

   请求体

   ```json
   {
       "title": "小米手机",
       "price": 1999
   }
   ```

2. 查询文档

   ```http
   [get]http://47.113.148.39:9200/shopping/_doc/1001	//主键查询
   [get]http://47.113.148.39:9200/shopping/_search		//全部查询
   ```

3. 整体修改文档

   ```http
   [put]http://47.113.148.39:9200/shopping/_doc/1001
   ```

   请求体

   ```json
   {
       "title": "小米手机",
       "price": 2999
   }
   ```

4. 局部修改文档

   ```http
   [post]http://47.113.148.39:9200/shopping/_update/1001
   ```

   请求体

   ```json
   {
       "doc": {
           "title": "华为手机"
       }
   }
   ```

5. 删除文档

   ```http
   [delete]http://47.113.148.39:9200/shopping/_doc/1001
   ```

## 三，复杂查询

```http
[get]http://47.113.148.39:9200/shopping/_search
```

1. 条件查询

   ```json
   {
       "query": {
           //匹配查询
           "match": {
               "title": "华为手机"
           }
       }
   }
   ```

2. 分页查询
   ```json
   {
       "query": {
           "match_all": {
   
           }
       },
       //从第几页
       "from": 0,
       //查询几条
       "size": 1
   }
   ```

3. 查询指定字段

   ```json
   {
       "query": {
           "match_all": {
   
           }
       },
       //只查询title
       "_source": ["title"]
   }
   ```

4. 排序

   ```json
   {
       "query": {
           "match_all": {
   
           }
       },
       "sort": {
           //排序字段
           "price": {
               //升序或者降序
               "order": "desc"
           }
       }
   }
   ```

5. 多条件查询

   ```json
   {
       "query": {
           "bool": {
               //and，查询title为小米手机并且价格为1999
               "must": [
                   {
                       "match": {
                           "title": "小米手机"
                       }
                   },
                   {
                       "match": {
                           "price": 1999
                       }
                   }
               ]
           }
       }
   }
   ```

   ```json
   {
       "query": {
           "bool": {
               //or，查询title为小米手机或者为华为手机
               "should": [
                   {
                       "match": {
                           "title": "小米手机"
                       }
                   },
                   {
                       "match": {
                           "title": "华为手机"
                       }
                   }
               ]
           }
       }
   }
   ```

6. 范围查询

   ```json
   {
       "query": {
           "bool": {
               //过滤
               "filter": {
                   //范围查询
                   "range": {
                       //price大于2000
                       "price": {
                           "gt": 2000
                       }
                   }
               }
           }
       }
   }
   ```

7. 匹配查询

   ```json
   {
       "query": {
           "match": {
               //可以查出华为和小米，华米被拆分为华和米
               "title": "华米"
           }
       }
   }
   ```

8. 完全匹配

   ```json
   {
       "query": {
           "match_phrase": {
               //只能查出小米
               "title": "米"
           }
       }
   }
   ```

9. 高亮显示

   ```json
   {
       "query": {
           "match": {
               "title": "米"
           }
       },
       //对title字段高亮显示
       "highlight": {
           "fields": {
               "title": {}
           }
       }
   }
   ```

10. 聚合查询

    ```json
    {
        //聚合操作
        "aggs": {
            //分组名称
            "price_group": {
                //分组
                "terms": {
                    //分组的字段
                    "field": "price"
                }
            }
        }
    }
    ```

    ```json
    {
        "aggs": {
            //求出所有价格的平均值
            "price_avg": {
                "avg": {
                    "field": "price"
                }
            }
        }
    }
    ```

## 四，映射

1. 创建映射

   ```http
   [post]http://47.113.148.39:9200/user/_mapping
   ```

   请求体

   ```json
   {
       "properties": {
           "name": {
               //文本类型
               "type": "text",
               //可以被索引
               "index": true
           },
           "sex": {
               //keyword类型
               "type": "keyword",
               //可以被索引
               "index": true
           },
           "tel": {
               //keyword类型
               "type": "keyword",
               //不可以被索引
               "index": false
           }
       }
   }
   ```

2. 查询映射

   ```http
   [get]http://47.113.148.39:9200/user/_mapping
   ```

## 五，JavaAPI

1. 创建连接

   ```java
   //创建ES客户端
   RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("47.113.148.39", 9200)));
   
   //关闭连接
   client.close();
   ```

2. 创建索引

   ```java
   //创建索引
   CreateIndexRequest book = new CreateIndexRequest("book");
   CreateIndexResponse response = client.indices().create(book, RequestOptions.DEFAULT);
   System.out.println(response.isAcknowledged());
   ```

3. 查询索引

   ```java
   //查询索引
   GetIndexRequest user = new GetIndexRequest("user");
   GetIndexResponse response = client.indices().get(user, RequestOptions.DEFAULT);
   System.out.println(response.getAliases());
   System.out.println(response.getMappings());
   System.out.println(response.getSettings());
   ```

4. 删除索引

   ```java
   //删除索引
   DeleteIndexRequest book = new DeleteIndexRequest("book");
   AcknowledgedResponse response = client.indices().delete(book, RequestOptions.DEFAULT);
   System.out.println(response.isAcknowledged());
   ```

5. 插入数据

   ```java
   //插入数据
   //绑定index
   IndexRequest request = new IndexRequest("user");
   //设置id
   request.id("1001");
   //准备数据
   User user = new User().setName("wgx").setSex("男").setAge(24);
   //转JSON
   ObjectMapper mapper = new ObjectMapper();
   String userJson = mapper.writeValueAsString(user);
   //设置请求体
   request.source(userJson, XContentType.JSON);
   //发送请求
   IndexResponse response = client.index(request, RequestOptions.DEFAULT);
   System.out.println(response.getResult());
   ```

6. 批量插入

   ```java
   //批量插入数据
   BulkRequest bulkRequest = new BulkRequest("user");
   //加入多个请求
   bulkRequest.add(new IndexRequest().id("1002").source(XContentType.JSON, "name", "wgx1"));
   bulkRequest.add(new IndexRequest().id("1003").source(XContentType.JSON, "name", "wgx2"));
   BulkResponse response = client.bulk(bulkRequest, RequestOptions.DEFAULT);
   for (BulkItemResponse item : response.getItems()) {
       System.out.println(item.getResponse().getResult());
   }
   ```

7. 更新数据

   ```java
   //更新数据
   //绑定index和id
   UpdateRequest request = new UpdateRequest("user", "1001");
   //指定更新的字段
   request.doc(XContentType.JSON, "tel", "15555664494");
   //发送请求
   UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
   System.out.println(response.getResult());
   ```

8. 查询数据

   ```java
   //查询数据
   //绑定index和id
   GetRequest request = new GetRequest("user", "1001");
   //发送请求
   GetResponse response = client.get(request, RequestOptions.DEFAULT);
   System.out.println(response.getSourceAsString());
   ```

9. 删除数据

   ```java
   //删除数据
   //绑定index和id
   DeleteRequest request = new DeleteRequest("user", "1001");
   //发送请求
   DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);
   System.out.println(response.getResult());
   ```

10. 批量删除

   ```java
   //批量删除数据
   BulkRequest bulkRequest = new BulkRequest("user");
   //加入多个请求
   bulkRequest.add(new DeleteRequest().id("1002"));
   bulkRequest.add(new DeleteRequest().id("1003"));
   BulkResponse response = client.bulk(bulkRequest, RequestOptions.DEFAULT);
   for (BulkItemResponse item : response.getItems()) {
       System.out.println(item.getResponse().getResult());
   }
   ```

11. 条件查询

    ```java
    //条件查询
    SearchRequest request = new SearchRequest("user");
    //查询name为wgx的数据
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.termQuery("name", "wgx"));
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getSourceAsString());
    }
    ```

12. 分页查询

    ```java
    //分页查询
    SearchRequest request = new SearchRequest("user");
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
    //从第几页
    query.from(0);
    //查询几条
    query.size(2);
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getSourceAsString());
    }
    ```

13. 排序

    ```java
    //排序
    SearchRequest request = new SearchRequest("user");
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
    //根据age升序排列
    query.sort("age", SortOrder.ASC);
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getSourceAsString());
    }
    ```

14. 过滤字段

    ```java
    //过滤字段
    SearchRequest request = new SearchRequest("user");
    SearchSourceBuilder query = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
    //只查询name字段
    query.fetchSource(new String[]{"name"}, new String[]{});
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getSourceAsString());
    }
    ```

15. 组合查询

    ```java
    //组合查询
    SearchRequest request = new SearchRequest("user");
    BoolQueryBuilder queryBuilder = QueryBuilders.boolQuery()
            //sex为男
            .must(QueryBuilders.matchQuery("sex", "男"))
            //并且age大于30
            .filter(QueryBuilders.rangeQuery("age").gt(30));
    SearchSourceBuilder query = new SearchSourceBuilder().query(queryBuilder);
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getSourceAsString());
    }
    ```

16. 模糊查询

    ```java
    //模糊查询
    SearchRequest request = new SearchRequest("user");
    FuzzyQueryBuilder queryBuilder = QueryBuilders
            //查询name包含wgx的
            .fuzzyQuery("name", "wgx")
            //只允许差一个字符
            .fuzziness(Fuzziness.ONE);
    SearchSourceBuilder query = new SearchSourceBuilder().query(queryBuilder);
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getSourceAsString());
    }
    ```

17. 高亮查询

    ```java
    //高亮查询
    SearchRequest request = new SearchRequest("user");
    QueryBuilder queryBuilder = QueryBuilders.termQuery("name", "wgx");
    SearchSourceBuilder query = new SearchSourceBuilder().query(queryBuilder);
    query.highlighter(new HighlightBuilder().field("name"));
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    for (SearchHit hit : response.getHits()) {
        System.out.println(hit.getHighlightFields());
    }
    ```

18. 聚合查询

    ```java
    //聚合查询，最大值
    SearchRequest request = new SearchRequest("user");
    SearchSourceBuilder query = new SearchSourceBuilder();
    query.aggregation(AggregationBuilders.max("maxAge").field("age"));
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    ParsedMax maxAge = response.getAggregations().get("maxAge");
    System.out.println(maxAge.getValue());
    ```

    ```java
    //聚合查询，分组
    SearchRequest request = new SearchRequest("user");
    SearchSourceBuilder query = new SearchSourceBuilder();
    query.aggregation(AggregationBuilders.terms("sexGroup").field("sex"));
    request.source(query);
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    ParsedTerms sexGroup = response.getAggregations().get("sexGroup");
    for (Terms.Bucket bucket : sexGroup.getBuckets()) {
        System.out.println(bucket.getKeyAsString() + ":" + 		bucket.getDocCount());
    }
    ```

# 第二章 ElasticSearch进阶

## 一，核心概念

1. 索引

   一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母），并且当我们要对这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。在一个集群中，可以定义任意多的索引。

   能搜索的数据必须索引，这样的好处是可以提高查询速度

2. 类型

   在一个索引中，你可以定义一种或多种类型。一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具有一组共同字段的文档定义一个类型。不同的版本，类型发生了不同的变化

3. 文档

   一个文档是一个可被索引的基础信息单元，也就是一条数据比如：你可以拥有某一个客户的文档，某一个产品的一个文档，当然，也可以拥有某个订单的一个文档。文档以JSON（Javascript Object Notation）格式来表示，而JSON是一个到处存在的互联网数据交互格式。

   在一个index/type里面，你可以存储任意多的文档。

4. 字段

   相当于是数据表的字段，对文档数据根据不同属性进行的分类标识。

5. 映射

   mapping是处理数据的方式和规则方面做一些限制，如：某个字段的数据类型、默认值、分析器、是否被索引等等。这些都是映射里面可以设置的，其它就是处理ES里面数据的一些使用规则设置也叫做映射，按着最优处理数据对性能提高很大，因此才需要建立映射，并且需要思考如何建立映射才能对性能更好。

6. 分片

   一个索引可以存储超出单个节点硬件限制的大量数据。比如，一个具有10亿文档数据的索引占据1TB的磁盘空间，而任一节点都可能没有这样大的磁盘空间。或者单个节点处理搜索请求，响应太慢。为了解决这个问题，Elasticsearch提供了将索引划分成多份的能力，每一份就称之为分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。

   分片很重要，主要有两方面的原因：

   - 允许你水平分割/扩展你的内容容量。
   - 允许你在分片之上进行分布式的、并行的操作，进而提高性能/吞吐量。

   至于一个分片怎样分布，它的文档怎样聚合和搜索请求，是完全由Elasticsearch管理的，对于作为用户的你来说，这些都是透明的，无需过分关心。

7. 分配

   将分片分配给某个节点的过程，包括分配主分片或者副本。如果是副本，还包含从主分片复制数据的过程。这个过程是由master节点完成的。

## 二，集群相关

1. 设置分片和副本

   ```http
   [put]http://47.113.148.39:9200/user
   ```

   请求体

   ```json
   {
       //3个主分片，3个副本，共6个分片
       "settings": {
           //分片
           "number_of_shards": 3,
           //副本
           "number_of_replicas": 1
       }
   }
   ```

2. 故障转移

   当集群中只有一个节点在运行时，意味着会有一个单点故障问题一一没有冗余。幸运的是，我们只需再启动一个节点即可防止数据丢失。当你在同一台机器上启动了第二个节点时，只要它和第一个节点有同样的cluster.name配置，它就会自动发现集群并加入到其中。但是在不同机器上启动节点的时候，为了加入到同一集群，你需要配置一个可连接到的单播主机列表。之所以配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上运行的节点才会自动组成集群。

   如果启动了第二个节点，我们的集群将会拥有两个节点的集群：所有主分片和副本分片都已被分配

3. 水平扩容

   怎样为我们的正在增长中的应用程序按需扩容呢？当启动了第三个节点，我们的集群将会拥有三个节点的集群：为了分散负载而对分片进行重新分配

   Node1和Node2上各有一个分片被迁移到了新的Node3节点，现在每个节点上都拥有2个分片，而不是之前的3个。这表示每个节点的硬件资源(CPU，RAM，I/O)将被更少的分片所共享，每个分片的性能将会得到提升。分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力。我们这个拥有6个分片（3个主分片和3个副本分片）的索引可以最大扩容到6个节点，每个节点上存在一个分片，并且每个分片拥有所在节点的全部资源

   ==如果扩容超过6个节点==

   主分片的数目在索引创建时就己经确定了下来。实际上，这个数目定义了这个索引能够存储的最大数据量。（实际大小取决于你的数据、硬件和使用场景。）但是，读操作搜索和返回数据一一可以同时被主分片或副本分片所处理，所以当你拥有越多的副本分片时，也将拥有越高的吞吐量。在运行中的集群上是可以动态调整副本分片数目的，我们可以按需伸缩集群。可以把副本数从默认的1增加到2

   ```http
   [put]http://47.113.148.39:9200/user/_settings
   ```

   ```json
   {
       //更新副本数量为2
       "number_of_replicas": 2
   }
   ```

4. 路由计算

   hash(id) % 主分片数量

5. 分片控制

   轮询

6. consistency

   consistency，即一致性。在默认设置下，即使仅仅是在试图执行一个写操作之前，主分片都会要求必须要有规定数量（quorum）的分片副本处于活跃可用状态，才会去执行写操作（其中分片副本可以是主分片或者副本分片）。这是为了避免在发生网络分区故障（network partition）的时候进行写操作，进而导致数据不一致。

   规定数量即：$int (number\_of\_replicas / 2) + 1$

   consistency参数的值可以设为one（只要主分片状态ok就允许执行写操作），all（必须要主分片和所有副本分片的状态没问题才允许执行写操作）。或quorum，默认值为quorum，即大多数的分片副本状态没问题就允许执行写操作。

   注意，规定数量的计算公式中number_of_replicas指的是在索引设置中的设定副本分片数，而不是指当前处理活动状态的副本分片数。

7. timeout

   如果没有足够的副本分片时Elasticsearch会等待，希望更多的分片出现。默认情况下，它最多等待1分钟。如果你需要，你可以使用timeout参数使它更早终止：100是100毫秒，30s是30秒。

8. 分片原理

   分片是Elasticsearch最小的工作单元。传统的数据库每个字段存储单个值，但这对全文检索并不够。文本字段中的每个单词需要被搜索，对数据库意味着需要单个字段有索引多值的能力。最好的支持是一个字段多个值需求的数据结构是倒排索引。

## 三，文档相关

1. 倒排索引

   Elasticsearch使用一种称为倒排索引的结构，它适用于快速的全文搜索。

   见其名，知其意，有倒排索引，肯定会对应有正向索引。正向索引(forward index)，反向索引(inverted index)更熟悉的名字是倒排索引。

   所谓的正向索引，就是搜索引擎会将待搜索的文件都对应一个文件ID，搜索时将这个ID和搜索关键字进行对应，形成K-V对，然后对关键字进行统计计数

   - 词条：索引中最小存储和查询单元
   - 词典：字典，词条的集合，B+数，HashMap

2. 文档搜索

   早期的全文检索会为整个文档集合建立一个很大的倒排索引并将其写入到磁盘。一旦新的索引就绪，旧的就会被其替换，这样最近的变化便可以被检索到。

   倒排索引被写入磁盘后是不可改变的：它永远不会修改。

   ==不变性有重要的价值：==

   - 不需要锁。如果你从来不更新索引，你就不需要担心多进程同时修改数据的问题。
   - 一旦索引被读入内核的文件系统缓存，便会留在哪里，由于其不变性。只要文件系统缓存中还有足够的空间，那么大部分读请求会直接请求内存，而不会命中磁盘。这提供了很大的性能提升。
   - 其它缓存（像filter缓存），在索引的生命周期内始终有效。它们不需要在每次数据改变时被重建，因为数据不会变化

   当然，一个不变的索引也有不好的地方，如果你需要让一个新的文档可以被搜索，你需要重建整个索引。这要么对一个索引所能包含的数据量造成了很大的限制，要么对索引可被更新的频率造成了很大的限制

3. 动态更新索引

   ==如何在保留不变性的前提下实现倒排索引的更新？==

   用更多的索引。通过增加新的补充索引来反映新近的修改，而不是直接重写整个倒排索引。每一个倒排索引都会被轮流查询到，从最早的开始查询完后再对结果进行合并。 Elasticsearch基于Lucene，这个java库引入了按段搜索的概念。每一段本身都是一个倒排索引，但索引在Lucene中除表示所有段的集合外，还增加了提交点的概念——一个列出了所有已知段的文件

   当一个查询被触发，所有已知的段按顺序被查询。词项统计会对所有段的结果进行聚合，以保证每个词和每个文档的关联都被准确计算。这种方式可以用相对较低的成本将新文档添加到索引。

   段是不可改变的，所以既不能从把文档从旧的段中移除，也不能修改旧的段来进行反映文档的更新。取而代之的是，每个提交点会包含一个.del文件，文件中会列出这些被删除文档的段信息。当一个文档被“删除”时，它实际上只是在.del文件中被标记删除。一个被标记删除的文档仍然可以被查询匹配到，但它会在最终结果被返回前从结果集中移除。

   文档更新也是类似的操作方式：当一个文档被更新时，旧版本文档被标记删除，文档的新版本被索引到一个新的段中。可能两个版本的文档都会被一个查询匹配到，但被删除的那个旧版本文档在结果集返回前就己经被移除。

4. 近实时索引

   随着按段（per-segment）搜索的发展，一个新的文档从索引到可被搜索的延迟显著降低了。新文档在几分钟之内即可被检索，但这样还是不够快。磁盘在这里成为了瓶颈。提交（Commiting）一个新的段到磁盘需要一个fsync来确保段被物理性地写入磁盘，这样在断电的时候就不会丢失数据。但是fsync操作代价很大；如果每次索引一个文档都去执行一次的话会造成很大的性能问题。

   我们需要的是一个更轻量的方式来使一个文档可被搜索，这意味着fsync要从整个过程中被移除。在Elasticsearch和磁盘之间是文件系统缓存。像之前描述的一样，在内存索引缓冲区中的文档会被写入到一个新的段中。但是这里新段会被先写入到文件系统缓存这一步代价会比较低，稍后再被刷新到磁盘一这一步代价比较高。不过只要文件已经在缓存中，就可以像其它文件一样被 打开和读取了。

5. 文档分析

   分析包含下面的过程：

   - 将一块文本分成适合于倒排索引的独立的词条
   - 将这些词条统一化为标准格式以提高它们的“可搜索性”，或者recall分析器执行上面的工作。

   分析器实际上是将三个功能封装到了一个包里：

   - 字符过滤器

     首先，字符串按顺序通过每个字符过滤器。他们的任务是在分词前整理字符串。一个字符过滤器可以用来去掉HTML，或者将&转化成and。

   - 分词器

     其次，字符串被分词器分为单个的词条。一个简单的分词器遇到空格和标点的时候，可能会将文本拆分成词条。

   - Token过滤器

     最后，词条按顺序通过每个tokn过滤器。这个过程可能会改变词条（例如，小写化Quick），删除词条（例如，像a，and，the等无用词），或者增加词条（例如，像jump和leap这种同义词）
