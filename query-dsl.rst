.. _query_dsl:

########################################
查询DSL
########################################

Elasticsearch 以类似REST`查询DSL <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl.html>`_ 的方式提供了一套完整的Java查询DSL。查询构造器工厂是 `QueryBuilders`。一旦查询准备好之后, 你可以使用 :ref:`search_api`。

要使用 `QueryBuilders`, 只需要在你的类中引入:

.. code-block:: java

    import static org.elasticsearch.index.query.QueryBuilders.*;

请注意, 你可以在 `QueryBuilder` 对象上使用 `toString()` 方法轻松地打印(即 debug)出生成的JSON查询。

接着 `QueryBuilder` 就可以用于任何接受像 `count` 和 `search` 查询的API。


****************************************
Match All Query
****************************************

参见 `Match All Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-match-all-query.html>`_ 。

.. code-block:: java

    matchAllQuery();


.. _full_text_query:

****************************************
全文查询
****************************************

高级的全文查询通常用于在像电子邮件正文这样的全文本字段上运行全文查询。他们了解如何分析被查询的字段, 并在执行之前将每个字段的 `analyzer`（或 `search_analyzer`）应用于查询字符串。

该分组中的查询有:

`match` 查询

    执行全文查询的标准查询, 包括模糊匹配以及短语或邻近查询。

`multi_match` 查询

    `match` 查询的多字段版本。

`common_terms` 查询

    一个更专门的查询, 它偏好不常见的单词。

`query_string` 查询

    支持紧凑的 Lucene 查询字符串语法, 允许你在单个查询字符串中指定 AND|OR|NOT 条件和多字段搜索。仅限专家用户。

`simple_query_string`

    一个更加简单, 更加健壮版本的 `query_string` 语法, 适合直接暴露给用户。


匹配查询
========================================

参见 `匹配查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-match-query.html>`_ 。

.. code-block:: java

    matchQuery(
            "name",                    <1>
            "kimchy elasticsearch");   <2>

<1> 字段
<2> 文本


Multi Match Query
========================================

参见 `Multi Match Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-multi-match-query.html>`_ 。

.. code-block:: java

    multiMatchQuery(
            "kimchy elasticsearch",           <1>
            "user", "message");               <2>


<1> 文本
<2> 字段


通用词条查询
========================================

参见 `通用词条查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-common-terms-query.html>`_ 。

.. code-block:: java

    commonTermsQuery("name",        <1>
                     "kimchy");     <2>

<1> 字段
<2> 值

查询字符串查询
========================================

参见 `查询字符串查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-query-string-query.html>`_ 。

.. code-block:: java

    queryStringQuery("+kimchy -elasticsearch");


简单查询字符串查询
========================================

参见 `简单查询字符串查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-simple-query-string-query.html>`_ 。

.. code-block:: java

    simpleQueryStringQuery("+kimchy -elasticsearch");


****************************************
词条级别的查询
****************************************

:ref:`full_text_query` 在执行查询前会对查询字符串进行分词, 而 term-level 查询操作于存储在倒排索引中的每个词条上。

这些查询通常用于数字、日期以及枚举之类的结构化的数据, 而不是全文本字段。Alternatively, they allow you to craft
low-level queries, foregoing the analysis process.

该分组中的查询有:

`term` 查询

    查询指定字段中精确包含指定词条的文档

`terms` 查询

    Find documents which contain any of the exact terms specified in the field
    specified.

`range` 查询

    查询指定字段中包含指定范围值(日期、数字或字符串)的文档。

`exists` 查询

    查询指定字段中包含任意非空值的文档。

`prefix` 查询

    Find documents where the field specified contains terms which being with
    the exact prefix specified.

`wildcard` 查询

    Find documents where the field specified contains terms which match the
    pattern specified, where the pattern supports single character wildcards
    (`?`) and multi-character wildcards (`*`)

`regexp` 查询

    查询指定字段中包含匹配指定正则表达式的词条的文档。

`fuzzy` 查询

    Find documents where the field specified contains terms which are fuzzily
    similar to the specified term.  Fuzziness is measured as a `莱文斯坦编辑距离 <http://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance>`_ of 1 or 2.

`type` 查询

    查询指定类型的文档。

`ids` 查询

    查询指定类型和文档ID列表的文档。


词条查询
========================================

参见 `词条查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-term-query.html>`_ 。

.. code-block:: java

    termQuery(
            "name",      <1>
            "kimchy");   <2>

<1> 字段
<2> 文本


多词条查询
========================================

参见 `多词条查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-terms-query.html>`_ 。

.. code-block:: java

    termsQuery("tags",                          <1>
            "blue", "pill");                    <2>

<1> 字段
<2> 值


范围查询
========================================

参见 `范围查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-range-query.html>`_ 。

.. code-block:: java

    rangeQuery("price")                     <1>
        .from(5)                            <2>
        .to(10)                             <3>
        .includeLower(true)                 <4>
        .includeUpper(false);               <5>

<1> 字段
<2> from
<3> to
<4> 包含较低值意味着为 `false` 时 `from` 是 `gt` 而为 `true' 时 `from` 是 `gte`。
<5> 包含较高值意味着为 `false` 时 `to` 是 `lt` 而为 `false` 时 `to` 是 `lte`。

.. code-block:: java

    // A simplified form using gte, gt, lt or lte
    rangeQuery("age")                     <1>
        .gte("10")                        <2>
        .lt("20");                        <3>

<1> 字段
<2> 设置 `from` 值为 10,  `includeLower` 值为 `true`
<3> 设置 `to` 值为 20, `includeUpper` 值为 `false`


存在查询
========================================

参见 `存在查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-exists-query.html>`_ 。

.. code-block:: java

    existsQuery("name");       <1>

<1> 字段


前缀查询
========================================

参见 `前缀查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-prefix-query.html>`_ 。

.. code-block:: java

    prefixQuery(
            "brand",        <1>
            "heine");       <2>


<1> 字段
<2> 前缀


通配符查询
========================================

参见 `通配符查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-wildcard-query.html>`_ 。

.. code-block:: java

    wildcardQuery(
            "user",           <1>
            "k?mch*");        <2>

<1> 字段
<2> 通配符表达式


正则表达式查询
========================================

参见 `正则表达式查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-regexp-query.html>`_ 。

.. code-block:: java

    regexpQuery(
            "name.first",        <1>
            "s.*y");             <2>

<1> 字段
<2> 正则表达式


模糊查询
========================================

参见 `模糊查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-fuzzy-query.html>`_ 。

.. code-block:: java

    fuzzyQuery(
            "name",       <1>
            "kimzhy");    <2>


<1> 字段
<2> 文本


类型查询
========================================

参见 `类型查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-type-query.html>`_ 。

.. code-block:: java

    typeQuery("my_type");   <1>

<1> 类型名称


文档ID查询
========================================


参见 `文档ID查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-ids-query.html>`_ 。

.. code-block:: java

    idsQuery("my_type", "type2")
            .addIds("1", "4", "100");

    idsQuery()                                  <1>
            .addIds("1", "4", "100");

<1> 类型是可选的


****************************************
复合查询
****************************************

Compound queries wrap other compound or leaf queries, either to combine their results and scores, to change their behaviour, or to switch from query to
filter context.

该分组中的查询有:

`constant_score` 查询

A query which wraps another query, but executes it in filter context.  All
matching documents are given the same ``constant'' `_score`.

`bool` 查询

The default query for combining multiple leaf or compound query clauses, as
`must`, `should`, `must_not`, or `filter` clauses.  The `must` and `should`
clauses have their scores combined -- the more matching clauses, the better --
while the `must_not` and `filter` clauses are executed in filter context.

`dis_max` 查询

A query which accepts multiple queries, and returns any documents which match
any of the query clauses.  While the `bool` query combines the scores from all
matching queries, the `dis_max` query uses the score of the single best-
matching query clause.

`function_score` 查询

Modify the scores returned by the main query with functions to take into
account factors like popularity, recency, distance, or custom algorithms
implemented with scripting.

`boosting` 查询

Return documents which match a `positive` query, but reduce the score of
documents which also match a `negative` query.

`indices` 查询

    对指定的索引执行一个查询，为其他索引执行另一个查询。


Constant Score Query
========================================

参见 `Constant Score Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-constant-score-query.html>`_ 。

.. code-block:: java

    constantScoreQuery(
            termQuery("name","kimchy"))     <1>
        .boost(2.0f);                       <2>

<1> 查询
<2> 查询分数


布尔查询
========================================

参见 `布尔查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-bool-query.html>`_ 。

.. code-block:: java

    boolQuery()
            .must(termQuery("content", "test1"))    <1>
            .must(termQuery("content", "test4"))    <2>
            .mustNot(termQuery("content", "test2")) <3>
            .should(termQuery("content", "test3"))  <4>
            .filter(termQuery("content", "test5")); <5>

<1> must 查询
<2>
<3> must not 查询
<4> should 查询
<5> 必须出现在匹配文档中但不对评分有贡献的查询。


Dis Max Query
========================================

参见 `Dis Max Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-dis-max-query.html>`_ 。

.. code-block:: java

    disMaxQuery()
            .add(termQuery("name", "kimchy"))               <1>
            .add(termQuery("name", "elasticsearch"))        <2>
            .boost(1.2f)                                    <3>
            .tieBreaker(0.7f);                              <4>

<1> 添加查询
<2> 添加查询
<3> boost factor
<4> tie breaker


Function Score Query
========================================

参见 `Function Score Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-function-score-query.html>`_ 。

要使用 `ScoreFunctionBuilders`, 只需要在你的类中引入它们:

.. code-block:: java

    import static org.elasticsearch.index.query.functionscore.ScoreFunctionBuilders.*;

.. code-block:: java

    FilterFunctionBuilder[] functions = {
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                    matchQuery("name", "kimchy"),                 <1>
                    randomFunction()),                            <2>
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                    exponentialDecayFunction("age", 0L, 1L))      <3>
    };
    functionScoreQuery(functions);

<1> 基于查询添加第一个函数
<2> 基于给定的种子随机化评分
<3> 基于 age 字段添加另一个函数


Boosting Query
========================================

参见 `Boosting Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-boosting-query.html>`_ 。

.. code-block:: java

    boostingQuery(
                termQuery("name","kimchy"),         <1>
                termQuery("name","dadoonet"))       <2>
            .negativeBoost(0.2f);                   <3>

<1> 提升文档的查询
<2> 降级文档的查询
<3> negative boost


****************************************
连接查询
****************************************

在像 Elasticsearch 这样的分布式系统中执行完全 SQL 风格的连接查询, 代价是非常昂贵的。相反, Elasticsearch 提供了两种形式的连接, 它们主要设计用于水平扩展。

嵌套查询

    文档可能包含 `nested` 类型的字段。这些字段用于索引对象数组, 其中每个对象可以作为一个独立的文本进行查询(使用嵌套查询)。

`has_child` 和 `has_parent`

    单个索引中的两种类型的文档之间可以存在父子关系。因为子文档匹配特定的查询, `has_child` 查询会返回父文档, 而因为父文档匹配特定的查询, `has_parent` 查询会返回子文档。


嵌套查询
========================================

参见 `嵌套查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-nested-query.html>`_ 。

.. code-block:: java

    nestedQuery(
            "obj1",                                             <1>
            boolQuery()                                         <2>
                    .must(matchQuery("obj1.name", "blue"))
                    .must(rangeQuery("obj1.count").gt(5)),
            ScoreMode.Avg);                                     <3>

<1> 嵌套文档路径
<2> 你的查询. 查询中引用的任何字段都必须使用完整的路径(全限定的).
<3> 评分模式可以是 `ScoreMode.Max`, `ScoreMode.Min`, `ScoreMode.Total`, `ScoreMode.Avg` 或 `ScoreMode.None`


Has Child查询
========================================

参见 `Has Child Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-has-child-query.html>`_ 。

当使用 has_child 查询时, 使用 PreBuiltTransportClient 而不是常规客户端是很重要的:

.. code-block:: java

    Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
    TransportClient client = new PreBuiltTransportClient(settings);
    client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

否则的话 parent-join 模块不会被加载并且传输客户端无法使用 has_child 查询。

.. code-block:: java

    JoinQueryBuilders.hasChildQuery(
            "blog_tag",                         <1>
            termQuery("tag","something"),       <2>
            ScoreMode.None);                    <3>

<1> 要查询的子类型
<2> 查询
<3> 评分模式可以是 `ScoreMode.Avg`, `ScoreMode.Max`, `ScoreMode.Min`, `ScoreMode.None` 或 `ScoreMode.Total`


Has Parent Query
========================================

参见 `Has Parent Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-has-parent-query.html>`_ 。

当使用 has_parent 查询时, 使用 PreBuiltTransportClient 而不是常规客户端是很重要的:

.. code-block:: java

    Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
    TransportClient client = new PreBuiltTransportClient(settings);
    client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

否则的话 parent-join 模块不会被加载并且传输客户端无法使用 has_parent 查询。

.. code-block:: java

    JoinQueryBuilders.hasParentQuery(
        "blog",                             <1>
        termQuery("tag","something"),       <2>
        false);                             <3>

<1> 要查询的父类型
<2> 查询
<3> whether the score from the parent hit should propogate to the child hit


****************************************
地理查询
****************************************

Elasticsearch 支持两种类型的地理数据:支持 lat/lon 对的 `geo_point` 字段和支持点、线、circles、多边形, multi-polygons等的 `geo_shape` 字段。

该分组中的查询有:

`geo_shape` 查询

    Find document with geo-shapes which either intersect, are contained by, or
    do not intersect with the specified geo-shape。

`geo_bounding_box` 查询

    Finds documents with geo-points that fall into the specified rectangle。

`geo_distance` 查询

    Finds document with geo-points within the specified distance of a central point。

`geo_polygon` 查询

    Find documents with geo-points within the specified polygon。


地理形状查询
========================================

参见 `地理形状查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-geo-shape-query.html>`_ 。

注意: `geo_shape` 类型使用了 `Spatial4J` 和 `JTS`, 它们都是可选的依赖。因此为了使用这种类型, 你必须要将 `Spatial4J` 和 `JTS` 依赖添加到你的类路径中:

.. code-block:: xml

    <dependency>
        <groupId>org.locationtech.spatial4j</groupId>
        <artifactId>spatial4j</artifactId>
        <version>0.6</version>                        <1>
    </dependency>

    <dependency>
        <groupId>com.vividsolutions</groupId>
        <artifactId>jts</artifactId>
        <version>1.13</version>                         <2>
        <exclusions>
            <exclusion>
                <groupId>xerces</groupId>
                <artifactId>xercesImpl</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

<1> 在 `Maven 中央仓库 <http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.locationtech.spatial4j%22%20AND%20a%3A%22spatial4j%22>`_ 中检查更新
<2> 在 `Maven 中央仓库 <http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.vividsolutions%22%20AND%20a%3A%22jts%22>`_ 中检查更新

.. code-block:: java

    // Import ShapeRelation and ShapeBuilder
    import org.elasticsearch.common.geo.ShapeRelation;
    import org.elasticsearch.common.geo.builders.ShapeBuilder;

.. code-block:: java

    GeoShapeQueryBuilder qb = geoShapeQuery(
            "pin.location",                             <1>
            ShapeBuilders.newMultiPoint(                <2>
                    new CoordinatesBuilder()
                .coordinate(0, 0)
                .coordinate(0, 10)
                .coordinate(10, 10)
                .coordinate(10, 0)
                .coordinate(0, 0)
                .build()));
    qb.relation(ShapeRelation.WITHIN);                  <3>

<1> 字段
<2> 形状
<3> relation 可以是 `ShapeRelation.CONTAINS`, `ShapeRelation.WITHIN`, `ShapeRelation.INTERSECTS` 或 `ShapeRelation.DISJOINT`

.. code-block:: java

    // Using pre-indexed shapes
    GeoShapeQueryBuilder qb = geoShapeQuery(
                "pin.location",                         <1>
                "DEU",                                  <2>
                "countries");                           <3>
    qb.relation(ShapeRelation.WITHIN)                   <4>
        .indexedShapeIndex("shapes")                    <5>
        .indexedShapePath("location");                  <6>

<1> 字段
<2> The ID of the document that containing the pre-indexed shape.
<3> Index type where the pre-indexed shape is.
<4> relation
<5> Name of the index where the pre-indexed shape is. Defaults to 'shapes'.
<6> The field specified as path containing the pre-indexed shape. Defaults to 'shape'.


Geo Bounding Box Query
========================================

参见 `Geo Bounding Box Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-geo-bounding-box-query.html>`_ 。

.. code-block:: java

    geoBoundingBoxQuery("pin.location")                   <1>
        .setCorners(40.73, -74.1,                         <2>
                    40.717, -73.99);                      <3>

<1> 字段
<2> 边界框顶部左边点
<3> 边界框底部右边点


地理距离查询
========================================

参见 `地理距离查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-geo-distance-query.html>`_ 。

.. code-block:: java

    geoDistanceQuery("pin.location")  <1>
        .point(40, -70)                                 <2>
        .distance(200, DistanceUnit.KILOMETERS);        <3>

<1> 字段
<2> 中心点
<3> 到中心点的距离


地理多变形查询
========================================

参见 `地理多变形查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-geo-polygon-query.html>`_ 。

.. code-block:: java

    List<GeoPoint> points = new ArrayList<>();             <1>
    points.add(new GeoPoint(40, -70));
    points.add(new GeoPoint(30, -80));
    points.add(new GeoPoint(20, -90));

    geoPolygonQuery("pin.location", points);               <2>

<1> 添加文档应落入的多边形的点
<2> 使用字段和点初始化查询


****************************************
Specialized queries
****************************************

This group contains queries which do not fit into the other groups:

`more_like_this` 查询

    This query finds documents which are similar to the specified text, document, or collection of documents.

`script` 查询

    This query allows a script to act as a filter.  Also see the <<java-query-dsl-function-score-query,`function_score` query>>.

`percolate` 查询

    This query finds percolator queries based on documents.


More Like This Query (mlt)
========================================

参见 `More Like This Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-mlt-query.html>`_ 。

.. code-block:: java

    String[] fields = {"name.first", "name.last"};                 <1>
    String[] texts = {"text like this one"};                       <2>
    Item[] items = null;

    moreLikeThisQuery(fields, texts, items)
        .minTermFreq(1)                                            <3>
        .maxQueryTerms(12);                                        <4>

<1> 字段
<2> 文本
<3> 忽略阈值
<4> 生成的查询中词条的最大数量

脚本查询
========================================

参见 `脚本查询 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-script-query.html>`_ 。

.. code-block:: java

    scriptQuery(
            new Script("doc['num1'].value > 1")     <1>
    );

<1> 内联脚本


如果你在每个数据节点上都存储了一个名为 `myscript.painless` 的脚本, 脚本内容如下:

.. code-block:: text

    doc['num1'].value > params.param1

那么你可以像下面这样使用它:

.. code-block:: java

    Map<String, Object> parameters = new HashMap<>();
    parameters.put("param1", 5);
    scriptQuery(new Script(
            ScriptType.STORED,                                  <1>
            null,                                               <2>
            "myscript",                                         <3>
            singletonMap("param1", 5)));                        <4>

<1> 脚本类型: `ScriptType.FILE`, `ScriptType.INLINE` 或 `ScriptType.INDEXED`
<2> 脚本引擎
<3> 脚本名称
<4> 参数是 `<String, Object>` 类型的 `Map`


Percolate Query
========================================

参见 `Percolate Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-percolate-query.html>`_ 。

.. code-block:: java

    Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
    TransportClient client = new PreBuiltTransportClient(settings);
    client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

Before the `percolate` query can be used an `percolator` mapping should be added and a document containing a percolator query should be indexed:

.. code-block:: java

    // create an index with a percolator field with the name 'query':
    client.admin().indices().prepareCreate("myIndexName")
                            .addMapping("query", "query", "type=percolator")
                            .addMapping("docs", "content", "type=text")
                            .get();

    //This is the query we're registering in the percolator
    QueryBuilder qb = termQuery("content", "amazing");

    //Index the query = register it in the percolator
    client.prepareIndex("myIndexName", "query", "myDesignatedQueryName")
        .setSource(jsonBuilder()
            .startObject()
                .field("query", qb) // Register the query
            .endObject())
        .setRefreshPolicy(RefreshPolicy.IMMEDIATE) // Needed when the query shall be available immediately
        .get();

This indexes the above term query under the name *myDesignatedQueryName*.

In order to check a document against the registered queries, 使用下面的代码:

.. code-block:: java

    //Build a document to check against the percolator
    XContentBuilder docBuilder = XContentFactory.jsonBuilder().startObject();
    docBuilder.field("content", "This is amazing!");
    docBuilder.endObject(); //End of the JSON root object

    PercolateQueryBuilder percolateQuery = new PercolateQueryBuilder("query", "docs", docBuilder.bytes());

    // Percolate, by executing the percolator query in the query dsl:
    SearchResponse response = client().prepareSearch("myIndexName")
            .setQuery(percolateQuery))
            .get();
    //Iterate over the results
    for(SearchHit hit : response.getHits()) {
        // Percolator queries as hit
    }


****************************************
Span queries
****************************************

Span queries are low-level positional queries which provide expert control over the order and proximity of the specified terms. These are typically used
to implement very specific queries on legal documents or patents.

Span queries cannot be mixed with non-span queries (with the exception of the `span_multi` query).

该分组中的查询有:

`span_term` 查询

    The equivalent of the <<java-query-dsl-term-query,`term` query>> but for use with other span queries.

`span_multi` 查询

    Wraps a <<java-query-dsl-term-query,`term`>>, <<java-query-dsl-range-query,`range`>>,
<<java-query-dsl-prefix-query,`prefix`>>, <<java-query-dsl-wildcard-query,`wildcard`>>,
<<java-query-dsl-regexp-query,`regexp`>>, or <<java-query-dsl-fuzzy-query,`fuzzy`>> query.

`span_first` 查询

    Accepts another span query whose matches must appear within the first N  positions of the field.

`span_near` 查询

    Accepts multiple span queries whose matches must be within the specified distance of each other, and possibly in the same order.

`span_or` 查询

    Combines multiple span queries -- returns documents which match any of the specified queries.

`span_not` 查询

    Wraps another span query, and excludes any documents which match that query.

`span_containing` 查询

    Accepts a list of span queries, but only returns those spans which also match a second span query.

`span_within` 查询

    The result from a single span query is returned as long is its span falls within the spans returned by a list of other span queries.


Span Term Query
========================================

参见 `Span Term Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-term-query.html>`_ 。

.. code-block:: java

    spanTermQuery(
            "user",                                     <1>
            "kimchy");                                  <2>

<1> field
<2> value


Span Multi Term Query
========================================

参见 `Span Multi Term Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-multi-term-query.html>`_ 。

.. code-block:: java

    spanMultiTermQueryBuilder(
            prefixQuery("user", "ki"));                   <1>

<1> 可以是任何继承了 `MultiTermQueryBuilder` 类的生成器。例如: `FuzzyQueryBuilder`, `PrefixQueryBuilder`, `RangeQueryBuilder`, `RegexpQueryBuilder` 或者 `WildcardQueryBuilder`.


Span First Query
========================================

参见 `Span First Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-first-query.html>`_ 。

.. code-block:: java

    spanFirstQuery(
            spanTermQuery("user", "kimchy"),            <1>
            3                                           <2>
    );

<1> 查询
<2> 最大结束位置


Span Near Query
========================================

参见 `Span Near Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-near-query.html>`_ 。

.. code-block:: java

    spanNearQuery(
            spanTermQuery("field","value1"),                <1>
            12)                                             <2>
                .addClause(spanTermQuery("field","value2")) <3>
                .addClause(spanTermQuery("field","value3")) <4>
                .inOrder(false);                            <5>

<1> span term queries
<3>
<4>
<2> slop factor: the maximum number of intervening unmatched positions
<5> whether matches are required to be in-order


Span Or Query
========================================

参见 `Span Or Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-or-query.html>`_ 。

.. code-block:: java

    spanOrQuery(spanTermQuery("field","value1"))       <1>        <1>
        .addClause(spanTermQuery("field","value2"))    <2>
        .addClause(spanTermQuery("field","value3"));   <3>

<1> span term queries
<2>
<3>

Span Not Query
========================================

参见 `Span Not Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-not-query.html>`_ 。

.. code-block:: java

    spanNotQuery(
            spanTermQuery("field","value1"),   <1>
            spanTermQuery("field","value2"));  <2>

<1> 匹配结果被过滤的span query
<2> span query whose matches must not overlap those returned


Span Containing Query
========================================

参见 `Span Containing Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-containing-query.html>`_ 。

.. code-block:: java

    spanContainingQuery(
            spanNearQuery(spanTermQuery("field1","bar"), 5) <1>
                .addClause(spanTermQuery("field1","baz"))
                .inOrder(true),
            spanTermQuery("field1","foo"));                 <2>

<1> `big` 部分
<2> `little` 部分


Span Within Query
========================================

参见 `Span Within Query <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/query-dsl-span-within-query.html>`_ 。

.. code-block:: java

    spanWithinQuery(
            spanNearQuery(spanTermQuery("field1", "bar"), 5)  <1>
                .addClause(spanTermQuery("field1", "baz"))
                .inOrder(true),
            spanTermQuery("field1", "foo"));                  <2>

<1> `big` part
<2> `little` part
