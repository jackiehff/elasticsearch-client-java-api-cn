########################################
搜索API
########################################


搜索 API 允许用户执行搜索查询并返回匹配查询的搜索命中结果。它可以跨一个或多个索引以及跨一个或多个类型执行。可以使用<< java-query-dsl，Java 查询 API >> 提供的查询功能。搜索请求的正文是使用 `SearchSourceBuilder` 对象来构建的。下面是一个代码示例:

.. code-block:: java

    import org.elasticsearch.action.search.SearchResponse;
    import org.elasticsearch.action.search.SearchType;
    import org.elasticsearch.index.query.QueryBuilders.*;

.. code-block:: java

    SearchResponse response = client.prepareSearch("index1", "index2")
            .setTypes("type1", "type2")
            .setSearchType(SearchType.DFS_QUERY_THEN_FETCH)
            .setQuery(QueryBuilders.termQuery("multi", "test"))                 // Query
            .setPostFilter(QueryBuilders.rangeQuery("age").from(12).to(18))     // Filter
            .setFrom(0).setSize(60).setExplain(true)
            .get();

请注意, 所有的参数都是可选的。下面是可以写出来的最短的搜索调用代码:

.. code-block:: java

    // MatchAll on the whole cluster with all default options
    SearchResponse response = client.prepareSearch().get();

.. note::  尽管 Java API 定义了额外的搜索类型 QUERY_AND_FETCH 和 DFS_QUERY_AND_FETCH, 这些模式也都经过了内部优化, API的使用者不需要显示地指定搜索类型.

有关搜索操作的更多信息, 请查看 REST https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search.html[搜索] 文档。


****************************************
在 Java 中使用滚动
****************************************

首先请阅读 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-request-scroll.html[滚动文档]!

.. code-block:: java

    import static org.elasticsearch.index.query.QueryBuilders.*;

    QueryBuilder qb = termQuery("multi", "test");

    SearchResponse scrollResp = client.prepareSearch(test)
            .addSort(FieldSortBuilder.DOC_FIELD_NAME, SortOrder.ASC)
            .setScroll(new TimeValue(60000))
            .setQuery(qb)
            .setSize(100).get(); //max of 100 hits will be returned for each scroll
    //Scroll until no hits are returned
    do {
        for (SearchHit hit : scrollResp.getHits().getHits()) {
            //Handle the hit...
        }

        scrollResp = client.prepareSearchScroll(scrollResp.getScrollId()).setScroll(new TimeValue(60000)).execute().actionGet();
    } while(scrollResp.getHits().getHits().length != 0); // Zero hits mark the end of the scroll and the while loop.


****************************************
MultiSearch API
****************************************

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-multi-search.html[MultiSearch API 查询]文档

.. code-block:: java

    SearchRequestBuilder srb1 = client
        .prepareSearch().setQuery(QueryBuilders.queryStringQuery("elasticsearch")).setSize(1);
    SearchRequestBuilder srb2 = client
        .prepareSearch().setQuery(QueryBuilders.matchQuery("name", "kimchy")).setSize(1);

    MultiSearchResponse sr = client.prepareMultiSearch()
            .add(srb1)
            .add(srb2)
            .get();

    // You will get all individual responses from MultiSearchResponse#getResponses()
    long nbHits = 0;
    for (MultiSearchResponse.Item item : sr.getResponses()) {
        SearchResponse response = item.getResponse();
        nbHits += response.getHits().getTotalHits();
    }


****************************************
使用聚合
****************************************

下面的代码展示了如何在搜索中添加两个聚合操作:

.. code-block:: java

    SearchResponse sr = client.prepareSearch()
        .setQuery(QueryBuilders.matchAllQuery())
        .addAggregation(
                AggregationBuilders.terms("agg1").field("field")
        )
        .addAggregation(
                AggregationBuilders.dateHistogram("agg2")
                        .field("birth")
                        .dateHistogramInterval(DateHistogramInterval.YEAR)
        )
        .get();

    // Get your facet results
    Terms agg1 = sr.getAggregations().get("agg1");
    DateHistogram agg2 = sr.getAggregations().get("agg2");

详情参见 <<java-aggs,Java 聚合 API>> 文档。


****************************************
之后终止
****************************************

当到达每个分片要收集文档的最大数量时, 查询执行操作将提前终止。如果设置了这个数量, 你可以在 `SearchResponse` 对象上调用 `isTerminatedEarly()` 方法来检查操作是否提前终止:

.. code-block:: java

    SearchResponse sr = client.prepareSearch(INDEX)
        .setTerminateAfter(1000)    <1>
        .get();

    if (sr.isTerminatedEarly()) {
        // We finished early
    }

<1> 到达1000个文档后结束


****************************************
搜索模板
****************************************

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-template.html[搜索模板] 文档

将你的模板参数定义成一个 `Map<String,Object>`:

.. code-block:: java

    Map<String, Object> template_params = new HashMap<>();
    template_params.put("param_gender", "male");

你可以使用存储在 `config/scripts` 中的搜索模板。例如, 如果你有一个名为 `config/scripts/template_gender.mustache` 的文件, 内容如下:

.. code-block:: json

    {
        "template" : {
            "query" : {
                "match" : {
                    "gender" : "{{param_gender}}"
                }
            }
        }
    }

// NOTCONSOLE

创建你的搜索模板请求:

.. code-block:: java

    SearchResponse sr = new SearchTemplateRequestBuilder(client)
        .setScript("template_gender")                 <1>
        .setScriptType(ScriptType.FILE) <2>
        .setScriptParams(template_params)             <3>
        .setRequest(new SearchRequest())              <4>
        .get()                                        <5>
        .getResponse();                               <6>

<1> 模板名称
<2> 存储于磁盘上 `gender_template.mustache` 文件中的模板
<3> 参数
<4> 设置执行上下文(即在这里定义索引名称)
<5> 执行并获取模板响应
<6> 从模板响应中获取搜索本身的响应

你还可以将模板存储在集群状态中:

.. code-block:: java

    client.admin().cluster().preparePutStoredScript()
        .setScriptLang("mustache")
        .setId("template_gender")
        .setSource(new BytesArray(
            "{\n" +
            "    \"template\" : {\n" +
            "        \"query\" : {\n" +
            "            \"match\" : {\n" +
            "                \"gender\" : \"{{param_gender}}\"\n" +
            "            }\n" +
            "        }\n" +
            "    }\n" +
            "}")).get();

要执行一个已存储的模板, 可以使用 `ScriptType.STORED`:

.. code-block:: java

    SearchResponse sr = new SearchTemplateRequestBuilder(client)
            .setScript("template_gender")                       <1>
            .setScriptType(ScriptType.STORED)     <2>
            .setScriptParams(template_params)                   <3>
            .setRequest(new SearchRequest())                    <4>
            .get()                                              <5>
            .getResponse();                                     <6>

<1> 模板名称
<2> 存储在集群状态中的模板
<3> 参数
<4> 设置执行上下文(即在这里定义索引名称)
<5> 执行并获取模板响应
<6> 从模板响应中获取搜索本身的响应

你还可以执行内联模板:

.. code-block:: java

    sr = new SearchTemplateRequestBuilder(client)
            .setScript("{\n" +                                  <1>
                    "        \"query\" : {\n" +
                    "            \"match\" : {\n" +
                    "                \"gender\" : \"{{param_gender}}\"\n" +
                    "            }\n" +
                    "        }\n" +
                    "}")
            .setScriptType(ScriptType.INLINE)    <2>
            .setScriptParams(template_params)                  <3>
            .setRequest(new SearchRequest())                   <4>
            .get()                                             <5>
            .getResponse();                                    <6>

<1> 模板名称
<2> 内联传递的模板
<3> 参数
<4> 设置执行上下文(即在这里定义索引名称)
<5> 执行并获取模板响应
<6> 从模板响应中获取搜索本身的响应

