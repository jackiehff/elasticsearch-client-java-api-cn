########################################
查询DSL
########################################

Elasticsearch提供了一套完整的 Java查询 dsl  in a similar manner to the
REST https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl.html[Query DSL]. 查询构造器工厂是 `QueryBuilders`. 一旦查询准备好之后, 你可以使用<<java-search,搜索API>>.

要使用 `QueryBuilders`, 只需要在你的类中引入:

.. code-block:: java

    import static org.elasticsearch.index.query.QueryBuilders.*;

请注意, Note that you can easily print (aka debug) JSON generated queries using `toString()` method on `QueryBuilder` object.

The `QueryBuilder` can then be used with any API that accepts a query, such as `count` and `search`.


****************************************
Match All Query
****************************************

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-match-all-query.html[Match All Query]

.. code-block:: java

    matchAllQuery();


****************************************
全文查询
****************************************

高级的全文查询通常用于在像电子邮件正文这样的全文本字段上运行全文查询。他们了解如何分析被查询的字段, 并在执行之前将每个字段的 `analyzer`（或 `search_analyzer`）应用于查询字符串。

该组中的查询有:

<<java-query-dsl-match-query,`match` 查询>>::

    执行全文查询的标准查询, 包括模糊匹配以及短语或邻近查询。

<<java-query-dsl-multi-match-query,`multi_match` 查询>>::

    `match` 查询的多字段版本.

<<java-query-dsl-common-terms-query,`common_terms` 查询>>::

    一个更专门的查询, 它偏好不常见的单词。

<<java-query-dsl-query-string-query,`query_string` 查询>>::

    支持紧凑的 Lucene 查询字符串语法, 允许你在单个查询字符串中指定 AND|OR|NOT 条件和多字段搜索。仅限专家用户。

<<java-query-dsl-simple-query-string-query,`simple_query_string`>>::

    一个更加简单, 更加健壮版本的 `query_string` 语法, 适合直接暴露给用户。


==== 匹配查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-match-query.html[匹配查询]

.. code-block:: java

    QueryBuilder qb = matchQuery(
        "name",                  <1>
        "kimchy elasticsearch"   <2>
    );

<1> 字段
<2> 文本


==== Multi Match Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-multi-match-query.html[Multi Match Query]

.. code-block:: java

    QueryBuilder qb = multiMatchQuery(
        "kimchy elasticsearch", <1>
        "user", "message"       <2>
    );

<1> 文本
<2> 字段


==== 通用词条查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-common-terms-query.html[通用词条查询]

.. code-block:: java

    QueryBuilder qb = commonTermsQuery("name",    <1>
                                       "kimchy"); <2>

<1> 字段
<2> 值

==== 查询字符串查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-query-string-query.html[查询字符串查询]

.. code-block:: java

    QueryBuilder qb = queryStringQuery("+kimchy -elasticsearch");    <1>

<1> 文本


==== 简单查询字符串查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-simple-query-string-query.html[简单查询字符串查询]

.. code-block:: java

    QueryBuilder qb = simpleQueryStringQuery("+kimchy -elasticsearch");    <1>

<1> 文本


****************************************
词条级别的查询
****************************************

While the <<java-full-text-queries,full text queries>> will analyze the query
string before executing, the _term-level queries_ operate on the exact terms
that are stored in the inverted index.

这些查询通常用于数字、日期以及枚举之类的结构化的数据, 而不是全文本字段. Alternatively, they allow you to craft
low-level queries, foregoing the analysis process.

The queries in this group are:

<<java-query-dsl-term-query,`term`查询>>::

    查询特定字段中准确包含特定词条的文档

<<java-query-dsl-terms-query,`terms`查询>>::

    Find documents which contain any of the exact terms specified in the field
    specified.

<<java-query-dsl-range-query,`range`查询>>::

    Find documents where the field specified contains values (dates, numbers,
    or strings) in the range specified.

<<java-query-dsl-exists-query,`exists`查询>>::

    Find documents where the field specified contains any non-null value.

<<java-query-dsl-prefix-query,`prefix`查询>>::

    Find documents where the field specified contains terms which being with
    the exact prefix specified.

<<java-query-dsl-wildcard-query,`wildcard`查询>>::

    Find documents where the field specified contains terms which match the
    pattern specified, where the pattern supports single character wildcards
    (`?`) and multi-character wildcards (`*`)

<<java-query-dsl-regexp-query,`regexp`查询>>::

    Find documents where the field specified contains terms which match the
    regular expression specified.

<<java-query-dsl-fuzzy-query,`fuzzy`查询>>::

    Find documents where the field specified contains terms which are fuzzily
    similar to the specified term.  Fuzziness is measured as a
    http://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance[莱文斯坦编辑距离]
    of 1 or 2.

<<java-query-dsl-type-query,`type` query>>::

    查询指定类型的文档.

<<java-query-dsl-ids-query,`ids` query>>::

    查询指定类型和文档ID列表的文档.


==== 词条查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-term-query.html[词条查询]

.. code-block:: java

    QueryBuilder qb = termQuery(
        "name",    <1>
        "kimchy"   <2>
    );

<1> 字段
<2> 文本


==== 多词条查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-terms-query.html[多词条查询]

.. code-block:: java

    QueryBuilder qb = termsQuery("tags",    <1>
        "blue", "pill");                    <2>

<1> 字段
<2> 值


==== 范围查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-range-query.html[范围查询]

.. code-block:: java

    QueryBuilder qb = rangeQuery("price")   <1>
        .from(5)                            <2>
        .to(10)                             <3>
        .includeLower(true)                 <4>
        .includeUpper(false);               <5>

<1> 字段
<2> from
<3> to
<4> include lower value means that `from` is `gt` when `false` or `gte` when `true`
<5> include upper value means that `to` is `lt` when `false` or `lte` when `true`

.. code-block:: java

    // A simplified form using gte, gt, lt or lte
    QueryBuilder qb = rangeQuery("age")   <1>
        .gte("10")                        <2>
        .lt("20");                        <3>

<1> 字段
<2> set `from` to 10 and `includeLower` to `true`
<3> set `to` to 20 and `includeUpper` to `false`


==== 存在查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-exists-query.html[存在查询].

.. code-block:: java

    QueryBuilder qb = existsQuery("name");       <1>

<1> 字段


==== 前缀查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-prefix-query.html[前缀查询]

.. code-block:: java

    QueryBuilder qb = prefixQuery(
        "brand",    <1>
        "heine"     <2>
    );

<1> 字段
<2> 前缀


==== 通配符查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-wildcard-query.html[通配符查询]

.. code-block:: java

    QueryBuilder qb = wildcardQuery("user", "k?mc*");


==== 正则表达式查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-regexp-query.html[正则表达式查询]

.. code-block:: java

    QueryBuilder qb = regexpQuery(
        "name.first",        <1>
        "s.*y");             <2>

<1> 字段
<2> 正则表达式


==== 模糊查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-fuzzy-query.html[模糊查询]

.. code-block:: java

    QueryBuilder qb = fuzzyQuery(
        "name",     <1>
        "kimzhy"    <2>
    );

<1> 字段
<2> 文本



==== 类型查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-type-query.html[类型查询]

.. code-block:: java

    QueryBuilder qb = typeQuery("my_type"); <1>

<1> 类型名称


==== 文档 Id 查询


参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-ids-query.html[文档 Id 查询]

.. code-block:: java

    QueryBuilder qb = idsQuery("my_type", "type2")
        .addIds("1", "4", "100");

    QueryBuilder qb = idsQuery() <1>
        .addIds("1", "4", "100");

<1> 类型是可选的


****************************************
复合查询
****************************************

Compound queries wrap other compound or leaf queries, either to combine their
results and scores, to change their behaviour, or to switch from query to
filter context.

该组中的查询有:

<<java-query-dsl-constant-score-query,`constant_score` 查询>>::

A query which wraps another query, but executes it in filter context.  All
matching documents are given the same ``constant'' `_score`.

<<java-query-dsl-bool-query,`bool` 查询>>::

The default query for combining multiple leaf or compound query clauses, as
`must`, `should`, `must_not`, or `filter` clauses.  The `must` and `should`
clauses have their scores combined -- the more matching clauses, the better --
while the `must_not` and `filter` clauses are executed in filter context.

<<java-query-dsl-dis-max-query,`dis_max` 查询>>::

A query which accepts multiple queries, and returns any documents which match
any of the query clauses.  While the `bool` query combines the scores from all
matching queries, the `dis_max` query uses the score of the single best-
matching query clause.

<<java-query-dsl-function-score-query,`function_score` 查询>>::

Modify the scores returned by the main query with functions to take into
account factors like popularity, recency, distance, or custom algorithms
implemented with scripting.

<<java-query-dsl-boosting-query,`boosting` 查询>>::

Return documents which match a `positive` query, but reduce the score of
documents which also match a `negative` query.

<<java-query-dsl-indices-query,`indices` 查询>>::

对指定的索引执行一个查询，为其他索引执行另一个查询。


==== Constant Score Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-constant-score-query.html[Constant Score Query]

.. code-block:: java

    QueryBuilder qb = constantScoreQuery(
            termQuery("name","kimchy")      <1>
        )
        .boost(2.0f);                       <2>

<1> 查询
<2> 查询分数


==== 布尔查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-bool-query.html[布尔查询]

.. code-block:: java

    QueryBuilder qb = boolQuery()
        .must(termQuery("content", "test1"))    <1>
        .must(termQuery("content", "test4"))    <1>
        .mustNot(termQuery("content", "test2")) <2>
        .should(termQuery("content", "test3"))  <3>
        .filter(termQuery("content", "test5")); <4>

<1> must 查询
<2> must not 查询
<3> should 查询
<4> 必须出现在匹配文档中但不对评分有贡献的查询。


==== Dis Max Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-dis-max-query.html[Dis Max Query]

.. code-block:: java

    QueryBuilder qb = disMaxQuery()
        .add(termQuery("name", "kimchy"))        <1>
        .add(termQuery("name", "elasticsearch")) <2>
        .boost(1.2f)                             <3>
        .tieBreaker(0.7f);                       <4>

<1> 添加查询
<2> 添加查询
<3> boost factor
<4> tie breaker


==== Function Score Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-function-score-query.html[Function Score Query].

要使用 `ScoreFunctionBuilders`, 只需要在你的类中引入它们:

.. code-block:: java

    import static org.elasticsearch.index.query.functionscore.ScoreFunctionBuilders.*;

.. code-block:: java

    FilterFunctionBuilder[] functions = {
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                    matchQuery("name", "kimchy"),                 <1>
                    randomFunction("ABCDEF")),                    <2>
            new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                    exponentialDecayFunction("age", 0L, 1L))      <3>
    };
    QueryBuilder qb = QueryBuilders.functionScoreQuery(functions);

<1> 基于查询添加第一个函数
<2> 基于给定的种子随机化评分
<3> 基于 age 字段添加另一个函数


==== Boosting Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-boosting-query.html[Boosting Query]

.. code-block:: java

    QueryBuilder qb = boostingQuery(
            termQuery("name","kimchy"),    <1>
            termQuery("name","dadoonet"))  <2>
        .negativeBoost(0.2f);              <3>

<1> 提升文档的查询
<2> 降级文档的查询
<3> negative boost


==== 索引查询

过时[5.0.0, Search on the '_index' field instead]

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-indices-query.html[索引查询]

.. code-block:: java

    // Using another query when no match for the main one
    QueryBuilder qb = indicesQuery(
            termQuery("tag", "wow"),             <1>
            "index1", "index2"                   <2>
        ).noMatchQuery(termQuery("tag", "kow")); <3>

<1> 在选择的索引上执行的查询
<2> 选择的索引
<3> 非匹配索引上执行的查询

.. code-block:: java

    // Using all (match all) or none (match no documents)
    QueryBuilder qb = indicesQuery(
            termQuery("tag", "wow"),            <1>
            "index1", "index2"                  <2>
        ).noMatchQuery("all");                  <3>

<1> 在选择的索引上执行的查询
<2> 选择的索引
<3> `none`(to match no documents), and `all` (匹配所有文档). 默认值是 `all`。


****************************************
连接查询
****************************************

在像 Elasticsearch 这样的分布式系统中执行完全 SQL 风格的连接查询, 代价是非常昂贵的。相反, Elasticsearch 提供了两种形式的连接, 它们主要设计用于水平扩展。

<<java-query-dsl-nested-query,嵌套查询>>::

文档可能包含 `nested` 类型的字段。这些字段用于索引对象数组, 其中每个对象可以作为一个独立的文本进行查询(使用嵌套查询)。

<<java-query-dsl-has-child-query,`has_child`>> 和 <<java-query-dsl-has-parent-query,`has_parent`>> 查询::

单个索引中的两种类型的文档之间可以存在父子关系。因为子文档匹配特定的查询, `has_child` 查询会返回父文档, 而因为父文档匹配特定的查询, `has_parent` 查询会返回子文档。


==== 嵌套查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-nested-query.html[嵌套查询]

.. code-block:: java

    QueryBuilder qb = nestedQuery(
            "obj1",                       <1>
            boolQuery()                   <2>
                    .must(matchQuery("obj1.name", "blue"))
                    .must(rangeQuery("obj1.count").gt(5)),
            ScoreMode.Avg                 <3>
        );

<1> 嵌套文档路径
<2> 你的查询. 查询中引用的任何字段都必须使用完整的路径(全限定的).
<3> 评分模式可以是 `ScoreMode.Max`, `ScoreMode.Min`, `ScoreMode.Total`, `ScoreMode.Avg` 或 `ScoreMode.None`


==== Has Child Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-has-child-query.html[Has Child Query]

.. code-block:: java

    QueryBuilder qb = hasChildQuery(
        "blog_tag",                     <1>
        termQuery("tag","something"),   <2>
        ScoreMode.Avg                   <3>
    );

<1> 要查询的子类型
<2> 查询
<3> 评分模式可以是 `ScoreMode.Avg`, `ScoreMode.Max`, `ScoreMode.Min`, `ScoreMode.None` 或 `ScoreMode.Total`


==== Has Parent Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-has-parent-query.html[Has Parent]

.. code-block:: java

    QueryBuilder qb = hasParentQuery(
        "blog",                         <1>
        termQuery("tag","something"),   <2>
        false                           <3>
    );

<1> 要查询的父类型
<2> 查询
<3> whether the score from the parent hit should propogate to the child hit


****************************************
地理查询
****************************************

Elasticsearch 支持两种类型的地理数据:
`geo_point` fields which support lat/lon pairs, and
`geo_shape` fields, which support points, lines, circles, polygons, multi-polygons etc.

该组中的查询有:

<<java-query-dsl-geo-shape-query,`geo_shape`>> query::

    Find document with geo-shapes which either intersect, are contained by, or
    do not intersect with the specified geo-shape.

<<java-query-dsl-geo-bounding-box-query,`geo_bounding_box`>> query::

    Finds documents with geo-points that fall into the specified rectangle.

<<java-query-dsl-geo-distance-query,`geo_distance`>> query::

    Finds document with geo-points within the specified distance of a central
    point.

<<java-query-dsl-geo-polygon-query,`geo_polygon`>> query::

    Find documents with geo-points within the specified polygon.


==== 地理形状查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-geo-shape-query.html[地理形状查询]

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

<1> 在 http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.locationtech.spatial4j%22%20AND%20a%3A%22spatial4j%22[Maven 中央仓库]中检查更新
<2> 在 http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.vividsolutions%22%20AND%20a%3A%22jts%22[Maven 中央仓库]中检查更新

.. code-block:: java

    // Import ShapeRelation and ShapeBuilder
    import org.elasticsearch.common.geo.ShapeRelation;
    import org.elasticsearch.common.geo.builders.ShapeBuilder;

.. code-block:: java

    List<Coordinate> points = new ArrayList<>();
    points.add(new Coordinate(0, 0));
    points.add(new Coordinate(0, 10));
    points.add(new Coordinate(10, 10));
    points.add(new Coordinate(10, 0));
    points.add(new Coordinate(0, 0));

    QueryBuilder qb = geoShapeQuery(
            "pin.location",                         <1>
            ShapeBuilders.newMultiPoint(points)     <2>
            .relation(ShapeRelation.WITHIN);        <3>

<1> 字段
<2> 形状
<3> relation 可以是 `ShapeRelation.CONTAINS`, `ShapeRelation.WITHIN`, `ShapeRelation.INTERSECTS` 或 `ShapeRelation.DISJOINT`

.. code-block:: java

    // Using pre-indexed shapes
    QueryBuilder qb = geoShapeQuery(
            "pin.location",                  <1>
            "DEU",                           <2>
            "countries")                     <3>
            .relation(ShapeRelation.WITHIN)) <4>
            .indexedShapeIndex("shapes")     <5>
            .indexedShapePath("location");   <6>

<1> 字段
<2> The ID of the document that containing the pre-indexed shape.
<3> Index type where the pre-indexed shape is.
<4> relation
<5> Name of the index where the pre-indexed shape is. Defaults to 'shapes'.
<6> The field specified as path containing the pre-indexed shape. Defaults to 'shape'.


==== Geo Bounding Box Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-geo-bounding-box-query.html[Geo Bounding Box Query]

.. code-block:: java

    QueryBuilder qb = geoBoundingBoxQuery("pin.location") <1>
        .setCorners(40.73, -74.1,                         <2>
                    40.717, -73.99);                      <3>

<1> 字段
<2> 边界框顶部左边点
<3> 边界框底部右边点


==== 地理距离查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-geo-distance-query.html[地理距离查询]

.. code-block:: java

    QueryBuilder qb = geoDistanceQuery("pin.location")  <1>
        .point(40, -70)                                 <2>
        .distance(200, DistanceUnit.KILOMETERS);        <3>

<1> 字段
<2> 中心点
<3> 到中心点的距离


==== 地理多变形查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-geo-polygon-query.html[地理多变形查询]

.. code-block:: java

    List<GeoPoint> points = new ArrayList<>();             <1>
    points.add(new GeoPoint(40, -70));
    points.add(new GeoPoint(30, -80));
    points.add(new GeoPoint(20, -90));

    QueryBuilder qb =
            geoPolygonQuery("pin.location", points);       <2>

<1> 添加文档应落入的多边形的点
<2> 使用字段和点初始化查询


****************************************
Specialized queries
****************************************

This group contains queries which do not fit into the other groups:

<<java-query-dsl-mlt-query,`more_like_this` 查询>>::

This query finds documents which are similar to the specified text, document,
or collection of documents.

<<java-query-dsl-script-query,`script` 查询>>::

This query allows a script to act as a filter.  Also see the
<<java-query-dsl-function-score-query,`function_score` query>>.

<<java-query-percolate-query,`percolate` 查询>>::

This query finds percolator queries based on documents.


==== More Like This Query (mlt)

参见:
 * https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-mlt-query.html[More Like This Query]

.. code-block:: java

    String[] fields = {"name.first", "name.last"};                 <1>
    String[] texts = {"text like this one"};                       <2>
    Item[] items = null;

    QueryBuilder qb = moreLikeThisQuery(fields, texts, items)
        .minTermFreq(1)                                            <3>
        .maxQueryTerms(12);                                        <4>

<1> 字段
<2> 文本
<3> 忽略阈值
<4> 生成的查询中词条的最大数量

==== 脚本查询

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-script-query.html[脚本查询]

.. code-block:: java

    QueryBuilder qb = scriptQuery(
        new Script("doc['num1'].value > 1") <1>
    );

<1> 内联脚本


如果你在每个数据节点上都存储了一个名为 `myscript.painless` 的脚本, 脚本内容如下:

.. code-block:: text

    doc['num1'].value > params.param1

那么你可以像下面这样使用它:

.. code-block:: java

    QueryBuilder qb = scriptQuery(
        new Script(
            ScriptType.FILE,                       <1>
            "painless",                            <2>
            "myscript",                            <3>
            Collections.singletonMap("param1", 5)) <4>
    );

<1> 脚本类型: `ScriptType.FILE`, `ScriptType.INLINE` 或 `ScriptType.INDEXED`
<2> 脚本引擎
<3> 脚本名称
<4> 参数是 `<String, Object>` 类型的 `Map`


==== Percolate Query

参见:
 * https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-percolate-query.html[Percolate Query]


.. code-block:: java

    Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
    TransportClient client = new PreBuiltTransportClient(settings);
    client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

Before the `percolate` query can be used an `percolator` mapping should be added and
a document containing a percolator query should be indexed:

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

This indexes the above term query under the name
*myDesignatedQueryName*.

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

Span queries are low-level positional queries which provide expert control
over the order and proximity of the specified terms. These are typically used
to implement very specific queries on legal documents or patents.

Span queries cannot be mixed with non-span queries (with the exception of the `span_multi` query).

该组中的查询有:

<<java-query-dsl-span-term-query,`span_term` 查询>>::

The equivalent of the <<java-query-dsl-term-query,`term` query>> but for use with
other span queries.

<<java-query-dsl-span-multi-term-query,`span_multi` 查询>>::

Wraps a <<java-query-dsl-term-query,`term`>>, <<java-query-dsl-range-query,`range`>>,
<<java-query-dsl-prefix-query,`prefix`>>, <<java-query-dsl-wildcard-query,`wildcard`>>,
<<java-query-dsl-regexp-query,`regexp`>>, or <<java-query-dsl-fuzzy-query,`fuzzy`>> query.

<<java-query-dsl-span-first-query,`span_first` 查询>>::

Accepts another span query whose matches must appear within the first N
positions of the field.

<<java-query-dsl-span-near-query,`span_near` 查询>>::

Accepts multiple span queries whose matches must be within the specified distance of each other, and possibly in the same order.

<<java-query-dsl-span-or-query,`span_or` 查询>>::

Combines multiple span queries -- returns documents which match any of the
specified queries.

<<java-query-dsl-span-not-query,`span_not` 查询>>::

Wraps another span query, and excludes any documents which match that query.

<<java-query-dsl-span-containing-query,`span_containing` 查询>>::

Accepts a list of span queries, but only returns those spans which also match a second span query.

<<java-query-dsl-span-within-query,`span_within` 查询>>::

The result from a single span query is returned as long is its span falls
within the spans returned by a list of other span queries.


==== Span Term Query

See https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-term-query.html[Span Term Query]

.. code-block:: java

    QueryBuilder qb = spanTermQuery(
        "user",                                     <1>
        "kimchy"                                    <2>
    );

<1> field
<2> value


==== Span Multi Term Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-multi-term-query.html[Span Multi Term Query]

.. code-block:: java

    QueryBuilder qb = spanMultiTermQueryBuilder(
        prefixQuery("user", "ki")                   <1>
    );

<1> 可以是任何继承了 `MultiTermQueryBuilder` 类的生成器。例如: `FuzzyQueryBuilder`, `PrefixQueryBuilder`, `RangeQueryBuilder`, `RegexpQueryBuilder` 或者 `WildcardQueryBuilder`.


==== Span First Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-first-query.html[Span First Query]

.. code-block:: java

    QueryBuilder qb = spanFirstQuery(
        spanTermQuery("user", "kimchy"),            <1>
        3                                           <2>
    );

<1> 查询
<2> 最大结束位置


==== Span Near Query

See https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-near-query.html[Span Near Query]

.. code-block:: java

    QueryBuilder qb = spanNearQuery(
        spanTermQuery("field","value1"),            <1>
        12)                                         <2>
        .addClause(spanTermQuery("field","value2")) <1>
        .addClause(spanTermQuery("field","value3")) <1>
        .inOrder(false);                            <3>

<1> span term queries
<2> slop factor: the maximum number of intervening unmatched positions
<3> whether matches are required to be in-order


==== Span Or Query

See https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-or-query.html[Span Or Query]

.. code-block:: java

    QueryBuilder qb = spanOrQuery(
        spanTermQuery("field","value1"))               <1>
        .addClause(spanTermQuery("field","value2"))    <1>
        .addClause(spanTermQuery("field","value3"));   <1>

<1> span term queries


==== Span Not Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-not-query.html[Span Not Query]

.. code-block:: java

    QueryBuilder qb = spanNotQuery(
        spanTermQuery("field","value1"),   <1>
        spanTermQuery("field","value2"));  <2>

<1> span query whose matches are filtered
<2> span query whose matches must not overlap those returned


==== Span Containing Query

参见 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-containing-query.html[Span Containing Query]

.. code-block:: java

    QueryBuilder qb = spanContainingQuery(
        spanNearQuery(spanTermQuery("field1","bar"), 5) <1>
              .addClause(spanTermQuery("field1","baz"))
              .inOrder(true),
        spanTermQuery("field1","foo"));                 <2>

<1> `big` 部分
<2> `little` 部分


==== Span Within Query

See https://www.elastic.co/guide/en/elasticsearch/reference/5.2/query-dsl-span-within-query.html[Span Within Query]

.. code-block:: java

    QueryBuilder qb = spanWithinQuery(
        spanNearQuery(spanTermQuery("field1", "bar"), 5) <1>
            .addClause(spanTermQuery("field1", "baz"))
            .inOrder(true),
        spanTermQuery("field1", "foo"));                 <2>

<1> `big` part
<2> `little` part
