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

     最后，词条按顺序通过每个token过滤器。这个过程可能会改变词条（例如，小写化Quick），删除词条（例如，像a，and，the等无用词），或者增加词条（例如，像jump和leap这种同义词）

6. 文档冲突

   Elasticsearch是分布式的。当文档创建、更新或删除时，新版本的文档必须复制到集群中的其他节点。Elasticsearch也是异步和并发的，这意味着这些复制请求被并行发送，并且到达目的地时也许顺序是乱的。Elasticsearch需要一种方法确保文档的旧版本不会覆盖新的版本。

   我们可以利用version号来确保应用中相互冲突的变更不会导致数据丢失。我们通过指定想要修改文档的version号来达到这个目的。如果该版本不是当前版本号，我们的请求将会失败。老的版本es使用version，但是新版本不支持了，会报下面的错误，提示我们用if_seq_no和if_primary_tem

   ```http
   [post]http://47.113.148.39:9200/user/_update/1001?if_seq_no=17&if_primary_term=5
   ```

   一个常见的设置是使用其它数据库作为主要的数据存储，使用Elasticsearch做数据检索，这意味着主数据库的所有更改发生时都需要被复制到Elasticsearch，如果多个进程负责这一数据同步，你可能遇到类似于之前描述的并发问题。如果你的主数据库已经有了版本号——或一个能作为版本号的字段值比如timestamp那么你就可以在Elasticsearch中通过增加version_type=external到查询字符串的方式重用这些相同的版本号，版本号必须是大于零的整数，且小于9.2E+18一一个Java中long类型的正值。

   ```http
   [get]http://47.113.148.39:9200/user/_doc/1001?version=6&version_type=external
   ```

7. 安装Kibana

   ```linux
   docker run \
   --name kibana \
   --privileged=true \
   -p 5601:5601 \
   -v /home/wgx/kibana/config:/usr/share/kibana/config \
   -d kibana:7.8.0
   ```

   修改配置文件，设置中文

   ![](F:\截图\屏幕截图 2023-08-08 171809.png)

# 第三章 框架集成

## 一，SpringData

1. 框架介绍

   Spring Data是一个用于简化数据库、非关系型数据库、索引访问，并支持云服务的开源框架。其主要目标是使得对数据的访问变得方便快捷，并支持map-reduce框架和云计算数据服务。Spring Data可以极大的简化JPA(Elasticsearch…)的写法，可以在几乎不用写实现的情况下，实现对数据的访问和操作。除了CRUD外，还包括如分页、排序等一些常用的功能。

2. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.data</groupId>
       <artifactId>spring-data-elasticsearch</artifactId>
   </dependency>
   ```

3. 配置类

   ```java
   @Configuration
   @ConfigurationProperties(prefix = "elasticsearch")
   @Data
   public class ElasticSearchConfig extends AbstractElasticsearchConfiguration {
   
       private String host;
       private int port;
   
       @Override
       public RestHighLevelClient elasticsearchClient() {
           RestClientBuilder builder = RestClient.builder(new HttpHost(host, port));
           return new RestHighLevelClient(builder);
       }
   }
   ```

4. 实体类

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   @Accessors(chain = true)
   @Document(indexName = "product")
   public class Product {
       @Id
       private Long id;
       @Field(type = FieldType.Text)
       private String title;
       @Field(type = FieldType.Keyword)
       private String category;
       @Field(type = FieldType.Double)
       private Double price;
       @Field(type = FieldType.Keyword, index = false)
       private String images;
   }
   ```

5. dao

   ```java
   @Repository
   public interface ProductDao extends ElasticsearchRepository<Product, Long> {
   
   }
   ```

6. 测试

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest(classes = ElasticSearchApplication.class)
   public class SpringDataEsTest {
       @Autowired
       private ProductDao productDao;
   
       @Autowired
       private ElasticsearchRestTemplate template;
   
       @Test
       public void save() {
           productDao.save(new Product(1L, "小米", "手机", 1999D, ""));
       }
   
       @Test
       public void update() {
           productDao.save(new Product(1L, "小米", "手机", 2999D, ""));
       }
   
       @Test
       public void findById() {
           Product product = productDao.findById(1L).get();
           System.out.println(product);
       }
   
       @Test
       public void findAll() {
           Iterable<Product> products = productDao.findAll();
           for (Product product : products) {
               System.out.println(product);
           }
       }
   
       @Test
       public void findByPageable() {
           Sort sort = Sort.by(Sort.Direction.DESC, "price");//排序
           int curPage = 1;//当前页码
           int pageSize = 1;//每页显示多少条记录
           PageRequest page = PageRequest.of(curPage, pageSize, sort);
           Page<Product> productPage = productDao.findAll(page);
           productPage.stream().forEach(System.out::println);
       }
   
       @Test
       public void search() {
           NativeSearchQueryBuilder nativeQueryBuilder = new NativeSearchQueryBuilder();
           BoolQueryBuilder q = QueryBuilders.boolQuery().must(QueryBuilders.matchQuery("title", "小米"));
           nativeQueryBuilder.withQuery(q);
           IndexCoordinates indexCoordinates = IndexCoordinates.of("product");
           SearchHits<Product> res = template.search(nativeQueryBuilder.build(), Product.class, indexCoordinates);
           for (SearchHit<Product> product : res) {
               System.out.println(product);
           }
       }
   }
   ```

# 第四章 优化

## 一，硬件选择

Elasticsearch的基础是Lucene，所有的索引和文档数据是存储在本地的磁盘中，具体的路径可在ES的配置文件../config/elasticsearch.yml中配置

磁盘在现代服务器上通常都是瓶颈。Elasticsearch重度使用磁盘，你的磁盘能处理的吞吐量越大，你的节点就越稳定。

1. 使用SSD，SSD比机械磁盘优秀多了。
2. 使用RAID0。条带化RAID会提高磁盘I/O,代价显然就是当一块硬盘故障时整个就故障了。不要使用镜像或者奇偶校验RAID因为副本已经提供了这个功能。
3. 另外，使用多块硬盘，并允许Elasticsearch通过多个path.data目录配置把数据条带化分配到它们上面。
4. 不要使用远程挂载的存储，比如NFS或者SMB/CFS。这个引入的延迟对性能来说完全是背道而驰的。

## 二，分片策略

分片和副本的设计为ES提供了支持分布式和故障转移的特性，但并不意味着分片和副本是可以无限分配的。而且索引的分片完成分配后由于索引的路由机制，我们是不能重新修改分片数的。

1. 一个分片的底层即为一个Lucene索引，会消耗一定文件句柄、内存、以及CPU运转。
2. 每一个搜索请求都需要命中索引中的每一个分片，如果每一个分片都处于不同的节点还好，但如果多个分片都需要在同一个节点上竞争使用相同的资源就有些槽糕了。
3. 用于计算相关度的词项统计信息是基于分片的。如果有许多分片，每一个都只有很少的数据会导致很低的相关度。

一个业务索引具体需要分配多少分片可能需要架构师和技术人员对业务的增长有个预先的判断，横向扩展应当分阶段进行。为下一阶段准备好足够的资源。只有当你进入到下一个阶段，你才有时间思考需要作出哪些改变来达到这个阶段。一般来说，我们遵循一些原则：

1. 控制每个分片占用的硬盘容量不超过ES的最大JVM的堆空间设置（一般设置不超过32G，参考下文的JVM设置原则)，因此，如果素引的总容量在500G左右，那分片大小在16个左右即可；当然，最好同时考虑原则2。
2. 考虑一下node数量，一般一个节点有时候就是一台物理机，如果分片数过多，大大超过了节点数，很可能会导致一个节点上存在多个分片，一旦该节点故障，即使保持了1个以上的副本，同样有可能会导致数据丢失，集群无法恢复。所以，一股都设置分片数不超过节点数的3倍。
3. 主分片，副本和节点最大数之间数量，我们分配的时候可以参考：节点数<=主分片数*（副本数+1）

对于节点瞬时中断的问题，默认情况，集群会等待一分钟来查看节点是否会重新加入，如果这个节点在此期间重新加入，重新加入的节点会保持其现有的分片数据，不会触发新的分片分配。这样就可以减少ES在自动再平衡可用分片时所带来的极大开销。

通过修改参数delayed_timeout，可以延长再均衡的时间，可以全局设置也可以在索引级别进行修改：

```http
[put] /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

## 三，路由选择

当我们查询文档的时候，Elasticsearch如何知道一个文档应该存放到哪个分片中呢？它其实是通过下面这个公式来计算出来：

shard = hash(routing) % number_of_primary_shards

routing默认值是文档的id,也可以采用自定义值，比如用户id。

## 四，写入速度优化

ES的默认配置，是综合了数据可靠性、写入速度、搜索实时性等因素。实际使用时，我们需要根据公司要求，进行偏向性的优化。

针对于搜索性能要求不高，但是对写入要求较高的场景，我们需要尽可能的选择恰当写优化策略。综合来说，可以考虑以下几个方面来提升写索引的性能：

1. 加大Translog Flush，目的是降低Iops、Writeblock。
2. 增加Index Refresh间隔，目的是减少Segment Merge的次数。
3. 调整Bulk线程池和队列。
4. 优化节点间的任务分布。
5. 优化Lucene层的索引建立，目的是降低CPU及IO。

## 五，批量数据的提交

ES提供了Bulk API支持批量操作，当我们有大量的写任务时，可以使用Bulk来进行批量写入。

Bulk默认设置批量提交的数据量不能超过100M。数据条数一般是根据文档的大小和服务器性能而定的，但是单次批处理的数据大小应从5MB~15MB逐渐增加，当性能没有提升时，把这个数据量作为最大值。

## 六，合理使用合并

Lucene以段的形式存储数据。当有新的数据写入索引时，Lucen就会自动创建一个新的段。

随着数据量的变化，段的数量会越来越多，消耗的多文件句柄数及CPU就越多，查询效率就会下降。

由于Lucene段合并的计算量庞大，会消耗大量的I/O，所以ES默认采用较保守的策略，让后台定期进行段合并

## 七，减少Refresh的次数

Lucene在新增数据时，采用了延迟写入的策略，默认情况下索引的refresh_interval为 1秒。

Lucene将待写入的数据先写到内存中，超过1秒（默认）时就会触发一次Refresh，然后Refresh会把内存中的的数据刷新到操作系统的文件缓存系统中。

如果我们对搜索的实效性要求不高，可以将Refresh周期延长，例如30秒。这样还可以有效地减少段刷新次数，但这同时意味着需要消耗更多的Heap内存。

## 八，加大Flush设置

Flush的主要目的是把文件缓存系统中的段持久化到硬盘，当Translog的数据量达到 512MB或者30分钟时，会触发一次Fush。

index.translog.flush_threshold_size参数的默认值是512MB，我们进行修改。

增加参数值意味着文件缓存系统中可能需要存储更多的数据，所以我们需要为操作系统的文件缓存系统留下足够的空间。

## 九，减少副本的数量

ES为了保证集群的可用性，提供了Replicas(副本)支持，然而每个副本也会执行分析、索引及可能的合并过程，所以Replicas的数量会严重影响写索引的效率。

当写索引时，需要把写入的数据都同步到副本节点，副本节点越多，写索引的效率就越慢。

如果我们需要大批量进行写入操作，可以先禁止Replica复制，设置 index.number_of_replicas:0关闭副本。在写入完成后，Replica修改回正常的状态。

## 十，内存设置

ES默认安装后设置的内存是1GB，对于任何一个现实业务来说，这个设置都太小了。如果是通过解压安装的ES,则在ES安装文件中包含一个jvm.option文件，添加如下命令来设置ES的堆大小，Xms表示堆的初始大小，Xmx表示可分配的最大内存，都是1GB。

确保Xmx和Xms的大小是相同的，其目的是为了能够在Java垃圾回收机制清理完堆区后不需要重新分隔计算堆区的大小而浪费资源，可以减轻伸缩堆大小带来的压力。

ES堆内存的分配需要满足一下两个原则

1. 不要超过物理内存的50%：Lucene的设计目的是把底层OS里的数据缓存到内存中。

    Lucene的段是分别存储到单个文件中的，这些文件都是不会变化的，所以很利于缓存，同时操作系统也会把这些段文件缓存起来，以便更快的访问。

   如果我们设置的堆内存过大， Lucene可用的内存将会减少，就会严重影响降低Lucene的全文本查询性能。

2. 堆内存的大小最好不要超过32GB：在Java中，所有对象都分配在堆上，然后有一个Klass Pointer指针指向它的类元数据。

   这个指针在64位的操作系统上为64位，64位的操作系统可以使用更多的内存(2\^64)。在32位的系统上为32位，32位的操作系统的最大寻址空间为4GB(2\^32)。

   但是64位的指针意味着更大的浪费，因为你的指针本身大了。浪费内存不算，更糟糕的是，更大的指针在主内存和缓存器（例如LLC，L1等）之间移动数据的时候，会占用更多的带宽。

## 十一，重要配置项

| 参数名                             | 参数值        | 说明                                                         |
| :--------------------------------- | ------------- | ------------------------------------------------------------ |
| cluster.name                       | elasticsearch | 配置ES的集群名称，默认值是ES，建议改成与所存数据相关的名称，ES会自动发现在同一网段下的集群名称相同的节点 |
| node.name                          | node-1        | 集群中的节点名，在同一个集群中不能重复。节点的名称一旦设置，就不能再改变了。当然，也可以设置成服务器的主机名称，例如 node.name:$ {HOSTNAME} |
| node.master                        | true          | 指定该节点是否有资格被选举成为Master节点，默认是True，如果被设置为Tue,则只是有资格成为 Master节点，具体能否成为Master节点，需要通过选举产生。 |
| node.data                          | true          | 指定该节点是否存储索引数据，默认为True。数据的增、删、改、查都是在Data节点完成的。 |
| index.number_of_shards             | 1             | 设置都索引分片个数，默认是1片。也可以在创建索引时设置该值，具体设置为多大都值要根据数据量的大小来定。如果数据量不大，则设置成1时效率最高 |
| index.number_of_replicas           | 1             | 设置默认的索引副本个数，默认为1个。副本数越多，集群的可用性越好，但是写索引时需要同步的数据越多。 |
| transport.tcp.compress             | true          | 设置在节点间传输数据时是否压缩，默认为False，不压缩          |
| discovery.zen.minimum_master_nodes | 1             | 设置在选举Master节点时需要参与的最少的候选主节点数，默认为1。如果使用默认值，则当网络不稳定时有可能会出现脑裂。合理的数值为(master_eligible_nodes/2)+1，其中 master_eligible_nodes表示集群中的候选主节点数 |
| discovery.zen.ping.timeout         | 3s            | 设置在集群中自动发现其他节点时Ping连接的超时时间，默认为3秒。在较差的网络环境下需要设置得大一点，防止因误判该节点的存活状态而导致分片的转移 |

