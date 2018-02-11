########################################
聚合
########################################

Elasticsearch 提供了一套完整的 Java API 来使用聚合. 参见 `聚合指南 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/search-aggregations.html>`_ 。

使用聚合构造器工厂( ``AggregationBuilders``)并且将查询时你想计算的每个聚合操作都添加到你的搜索请求中:

.. code-block:: java

    SearchResponse sr = node.client().prepareSearch()
        .setQuery( /* your query */ )
        .addAggregation( /* add an aggregation */ )
        .execute().actionGet();

请注意, 你可以添加多个聚合. 详情参见 `Java搜索API <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/search-search.html>`_ 。

要构建一个聚合请求, 可以使用 ``AggregationBuilders`` 帮助类。只需要在你的类中引入它:

.. code-block:: java

    import org.elasticsearch.search.aggregations.AggregationBuilders;


****************************************
结构化聚合
****************************************

正如 `聚合指南 <https://www.elastic.co/guide/en/elasticsearch/reference/6.2/search-aggregations.html>`_ 中的解释, 你可以在聚合内部定义一个子聚合。

聚合可以是度量聚合或桶聚合.

例如, 这里由一个由以下三种聚合组成的一个3级聚合:

* 词项聚合(bucket)
* 日期直方图聚合(bucket)
* 平均聚合(metric)

.. code-block:: java

    SearchResponse sr = node.client().prepareSearch()
    .addAggregation(
        AggregationBuilders.terms("by_country").field("country")
        .subAggregation(AggregationBuilders.dateHistogram("by_year")
            .field("dateOfBirth")
            .dateHistogramInterval(DateHistogramInterval.YEAR)
            .subAggregation(AggregationBuilders.avg("avg_children").field("children"))
        )
    )
    .execute().actionGet();


****************************************
Metrics聚合
****************************************

最小值聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-min-aggregation.html[最小值聚合]。

创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    MinAggregationBuilder aggregation =
            AggregationBuilders
                    .min("agg")
                    .field("height");

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.min.Min;


.. code-block:: java

    // sr is here your SearchResponse object
    Min agg = sr.getAggregations().get("agg");
    double value = agg.getValue();


最大值聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-max-aggregation.html[最大值聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    MaxAggregationBuilder aggregation =
            AggregationBuilders
                    .max("agg")
                    .field("height");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.max.Max;

.. code-block:: java

    // sr is here your SearchResponse object
    Max agg = sr.getAggregations().get("agg");
    double value = agg.getValue();


求和聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-sum-aggregation.html[求和聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    SumAggregationBuilder aggregation =
            AggregationBuilders
                    .sum("agg")
                    .field("height");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.sum.Sum;

.. code-block:: java

    // sr is here your SearchResponse object
    Sum agg = sr.getAggregations().get("agg");
    double value = agg.getValue();


平均值聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-avg-aggregation.html[平均值聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AvgAggregationBuilder aggregation =
            AggregationBuilders
                    .avg("agg")
                    .field("height");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.avg.Avg;

.. code-block:: java

    // sr is here your SearchResponse object
    Avg agg = sr.getAggregations().get("agg");
    double value = agg.getValue();


统计聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-stats-aggregation.html[统计聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    StatsAggregationBuilder aggregation =
            AggregationBuilders
                    .stats("agg")
                    .field("height");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.stats.Stats;

.. code-block:: java

    // sr is here your SearchResponse object
    Stats agg = sr.getAggregations().get("agg");
    double min = agg.getMin();
    double max = agg.getMax();
    double avg = agg.getAvg();
    double sum = agg.getSum();
    long count = agg.getCount();


扩展统计聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-extendedstats-aggregation.html[扩展统计聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    ExtendedStatsAggregationBuilder aggregation =
            AggregationBuilders
                    .extendedStats("agg")
                    .field("height");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.stats.extended.ExtendedStats;

.. code-block:: java

    // sr is here your SearchResponse object
    ExtendedStats agg = sr.getAggregations().get("agg");
    double min = agg.getMin();
    double max = agg.getMax();
    double avg = agg.getAvg();
    double sum = agg.getSum();
    long count = agg.getCount();
    double stdDeviation = agg.getStdDeviation();
    double sumOfSquares = agg.getSumOfSquares();
    double variance = agg.getVariance();


值计数聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-valuecount-aggregation.html[值计数聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    ValueCountAggregationBuilder aggregation =
            AggregationBuilders
                    .count("agg")
                    .field("height");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.valuecount.ValueCount;

.. code-block:: java

    // sr is here your SearchResponse object
    ValueCount agg = sr.getAggregations().get("agg");
    long value = agg.getValue();


百分比聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-percentile-aggregation.html[百分比聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    PercentilesAggregationBuilder aggregation =
            AggregationBuilders
                    .percentiles("agg")
                    .field("height");

你可以提供自己的百分比替代默认值:

.. code-block:: java

    PercentilesAggregationBuilder aggregation =
            AggregationBuilders
                    .percentiles("agg")
                    .field("height")
                    .percentiles(1.0, 5.0, 10.0, 20.0, 30.0, 75.0, 95.0, 99.0);

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.percentiles.Percentile;
    import org.elasticsearch.search.aggregations.metrics.percentiles.Percentiles;

.. code-block:: java

    // sr is here your SearchResponse object
    Percentiles agg = sr.getAggregations().get("agg");
    // For each entry
    for (Percentile entry : agg) {
        double percent = entry.getPercent();    // Percent
        double value = entry.getValue();        // Value

        logger.info("percent [{}], value [{}]", percent, value);
    }


第一个示例的基本输出如下:

.. code-block:: text

    percent [1.0], value [0.814338896154595]
    percent [5.0], value [0.8761912455821302]
    percent [25.0], value [1.173346540141847]
    percent [50.0], value [1.5432023318692198]
    percent [75.0], value [1.923915462033674]
    percent [95.0], value [2.2273644908535335]
    percent [99.0], value [2.284989339108279]


百分比范围聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-percentile-rank-aggregation.html[百分比范围聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    PercentileRanksAggregationBuilder aggregation =
            AggregationBuilders
                    .percentileRanks("agg")
                    .field("height")
                    .values(1.24, 1.91, 2.22);


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.percentiles.Percentile;
    import org.elasticsearch.search.aggregations.metrics.percentiles.PercentileRanks;

.. code-block:: java

    // sr is here your SearchResponse object
    PercentileRanks agg = sr.getAggregations().get("agg");
    // For each entry
    for (Percentile entry : agg) {
        double percent = entry.getPercent();    // Percent
        double value = entry.getValue();        // Value

        logger.info("percent [{}], value [{}]", percent, value);
    }


该示例会产生以下基本输出:

.. code-block:: text

    percent [29.664353095090945], value [1.24]
    percent [73.9335313461868], value [1.91]
    percent [94.40095147327283], value [2.22]

基数聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-cardinality-aggregation.html[基数聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    CardinalityAggregationBuilder aggregation =
            AggregationBuilders
                    .cardinality("agg")
                    .field("tags");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.cardinality.Cardinality;

.. code-block:: java

    // sr is here your SearchResponse object
    Cardinality agg = sr.getAggregations().get("agg");
    long value = agg.getValue();


地理边界聚合
========================================


下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-geobounds-aggregation.html[地理边界聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    GeoBoundsBuilder aggregation =
            GeoBoundsAggregationBuilder
                    .geoBounds("agg")
                    .field("address.location")
                    .wrapLongitude(true);


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.metrics.geobounds.GeoBounds;

.. code-block:: java

    // sr is here your SearchResponse object
    GeoBounds agg = sr.getAggregations().get("agg");
    GeoPoint bottomRight = agg.bottomRight();
    GeoPoint topLeft = agg.topLeft();
    logger.info("bottomRight {}, topLeft {}", bottomRight, topLeft);

该示例会产生以下基本输出:

.. code-block:: text

    bottomRight [40.70500764381921, 13.952946866893775], topLeft [53.49603022435221, -4.190029308156676]


Top Hits聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-top-hits-aggregation.html[Top Hits聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
        AggregationBuilders
            .terms("agg").field("gender")
            .subAggregation(
                AggregationBuilders.topHits("top")
            );

你可以使用大部分的选项用于标准搜索, 比如 `from`, `size`, `sort`, `highlight`, `explain` 等等。

.. code-block:: java

    AggregationBuilder aggregation =
        AggregationBuilders
            .terms("agg").field("gender")
            .subAggregation(
                AggregationBuilders.topHits("top")
                    .explain(true)
                    .size(1)
                    .from(10)
            );

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.terms.Terms;
    import org.elasticsearch.search.aggregations.metrics.tophits.TopHits;

.. code-block:: java

    // sr is here your SearchResponse object
    Terms agg = sr.getAggregations().get("agg");

    // For each entry
    for (Terms.Bucket entry : agg.getBuckets()) {
        String key = entry.getKey();                    // bucket key
        long docCount = entry.getDocCount();            // Doc count
        logger.info("key [{}], doc_count [{}]", key, docCount);

        // We ask for top_hits for each bucket
        TopHits topHits = entry.getAggregations().get("top");
        for (SearchHit hit : topHits.getHits().getHits()) {
            logger.info(" -> id [{}], _source [{}]", hit.getId(), hit.getSourceAsString());
        }
    }

第一个示例会产生以下基本输出:

.. code-block:: text

    key [male], doc_count [5107]
     -> id [AUnzSZze9k7PKXtq04x2], _source [{"gender":"male",...}]
     -> id [AUnzSZzj9k7PKXtq04x4], _source [{"gender":"male",...}]
     -> id [AUnzSZzl9k7PKXtq04x5], _source [{"gender":"male",...}]
    key [female], doc_count [4893]
     -> id [AUnzSZzM9k7PKXtq04xy], _source [{"gender":"female",...}]
     -> id [AUnzSZzp9k7PKXtq04x8], _source [{"gender":"female",...}]
     -> id [AUnzSZ0W9k7PKXtq04yS], _source [{"gender":"female",...}]


脚本度量聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-metrics-scripted-metric-aggregation.html[脚本度量聚合]。

如果你想要在一个嵌入式的数据节点上运行 Groovy 脚本, 不要忘了将 Groovy 添加到你的类路径中(例如用于单元测试)。
例如, 你可以将下面的依赖添加到 Maven 的 `pom.xml` 文件中:

.. code-block:: xml

    <dependency>
        <groupId>org.codehaus.groovy</groupId>
        <artifactId>groovy-all</artifactId>
        <version>2.3.2</version>
        <classifier>indy</classifier>
    </dependency>


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    ScriptedMetricAggregationBuilder aggregation = AggregationBuilders
        .scriptedMetric("agg")
        .initScript(new Script("params._agg.heights = []"))
        .mapScript(new Script("params._agg.heights.add(doc.gender.value == 'male' ? doc.height.value : -1.0 * doc.height.value)"));

你也可以指定一个 `combine` 脚本, 它将在每个分片上执行:

.. code-block:: java

    ScriptedMetricAggregationBuilder aggregation = AggregationBuilders
        .scriptedMetric("agg")
        .initScript(new Script("params._agg.heights = []"))
        .mapScript(new Script("params._agg.heights.add(doc.gender.value == 'male' ? doc.height.value : -1.0 * doc.height.value)"))
        .combineScript(new Script("double heights_sum = 0.0; for (t in params._agg.heights) { heights_sum += t } return heights_sum"));

你也可以指定一个 `reduce` 脚本, 它将在节点上执行并得到下面这个请求:

.. code-block:: java

    ScriptedMetricAggregationBuilder aggregation = AggregationBuilders
        .scriptedMetric("agg")
        .initScript(new Script("params._agg.heights = []"))
        .mapScript(new Script("params._agg.heights.add(doc.gender.value == 'male' ? doc.height.value : -1.0 * doc.height.value)"))
        .combineScript(new Script("double heights_sum = 0.0; for (t in params._agg.heights) { heights_sum += t } return heights_sum"))
        .reduceScript(new Script("double heights_sum = 0.0; for (a in params._aggs) { heights_sum += a } return heights_sum"));


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.terms.Terms;
    import org.elasticsearch.search.aggregations.metrics.tophits.TopHits;

.. code-block:: java

    // sr is here your SearchResponse object
    ScriptedMetric agg = sr.getAggregations().get("agg");
    Object scriptedResult = agg.aggregation();
    logger.info("scriptedResult [{}]", scriptedResult);

请注意, 返回结果取决于你构建的脚本.对于第一个示例, 基本输出如下:

.. code-block:: text

    scriptedResult object [ArrayList]
    scriptedResult [ {
    "heights" : [ 1.122218480146643, -1.8148918111233887, -1.7626731575142909, ... ]
    }, {
    "heights" : [ -0.8046067304119863, -2.0785486707864553, -1.9183567430207953, ... ]
    }, {
    "heights" : [ 2.092635728868694, 1.5697545960886536, 1.8826954461968808, ... ]
    }, {
    "heights" : [ -2.1863201099468403, 1.6328549117346856, -1.7078288405893842, ... ]
    }, {
    "heights" : [ 1.6043904836424177, -2.0736538674414025, 0.9898266674373053, ... ]
    } ]

第二个示例输出如下:

.. code-block:: text

    scriptedResult object [ArrayList]
    scriptedResult [-41.279615707402876,
                    -60.88007362339038,
                    38.823270659734256,
                    14.840192739445632,
                    11.300902755741326]

最后一个示例输出如下:

.. code-block:: text

    scriptedResult object [Double]
    scriptedResult [2.171917696507009]


****************************************
Bucket聚合
****************************************

全局聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-global-aggregation.html[全局聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilders
        .global("agg")
        .subAggregation(AggregationBuilders.terms("genders").field("gender"));


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.global.Global;

.. code-block:: java

    // sr is here your SearchResponse object
    Global agg = sr.getAggregations().get("agg");
    agg.getDocCount(); // Doc count


过滤器聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-filter-aggregation.html[过滤器聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilders
        .filter("agg", QueryBuilders.termQuery("gender", "male"));


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.filter.Filter;

.. code-block:: java

    // sr is here your SearchResponse object
    Filter agg = sr.getAggregations().get("agg");
    agg.getDocCount(); // Doc count


多过滤器聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-filters-aggregation.html[多过滤器聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
        AggregationBuilders
            .filters("agg",
                new FiltersAggregator.KeyedFilter("men", QueryBuilders.termQuery("gender", "male")),
                new FiltersAggregator.KeyedFilter("women", QueryBuilders.termQuery("gender", "female")));


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.filters.Filters;

.. code-block:: java

    // sr is here your SearchResponse object
    Filters agg = sr.getAggregations().get("agg");

    // For each entry
    for (Filters.Bucket entry : agg.getBuckets()) {
        String key = entry.getKeyAsString();            // bucket key
        long docCount = entry.getDocCount();            // Doc count
        logger.info("key [{}], doc_count [{}]", key, docCount);
    }

该示例会产生以下基本输出:

.. code-block:: text

    key [men], doc_count [4982]
    key [women], doc_count [5018]


Missing Aggregation
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-missing-aggregation.html[Missing Aggregation]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilders.missing("agg").field("gender");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.missing.Missing;

.. code-block:: java

    // sr is here your SearchResponse object
    Missing agg = sr.getAggregations().get("agg");
    agg.getDocCount(); // Doc count


嵌套聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-nested-aggregation.html[嵌套聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilders
        .nested("agg", "resellers");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.nested.Nested;

.. code-block:: java

    // sr is here your SearchResponse object
    Nested agg = sr.getAggregations().get("agg");
    agg.getDocCount(); // Doc count


反向嵌套聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-reverse-nested-aggregation.html[反向嵌套聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
        AggregationBuilders
            .nested("agg", "resellers")
            .subAggregation(
                    AggregationBuilders
                            .terms("name").field("resellers.name")
                            .subAggregation(
                                    AggregationBuilders
                                            .reverseNested("reseller_to_product")
                            )
            );


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.nested.Nested;
    import org.elasticsearch.search.aggregations.bucket.nested.ReverseNested;
    import org.elasticsearch.search.aggregations.bucket.terms.Terms;

.. code-block:: java

    // sr is here your SearchResponse object
    Nested agg = sr.getAggregations().get("agg");
    Terms name = agg.getAggregations().get("name");
    for (Terms.Bucket bucket : name.getBuckets()) {
        ReverseNested resellerToProduct = bucket.getAggregations().get("reseller_to_product");
        resellerToProduct.getDocCount(); // Doc count
    }


Children聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-children-aggregation.html[Children 聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
        AggregationBuilders
            .children("agg", "reseller"); <1>

1. `"agg"` 是聚合的名称, `"reseller"` 是子类型


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.children.Children;

.. code-block:: java

    // sr is here your SearchResponse object
    Children agg = sr.getAggregations().get("agg");
    agg.getDocCount(); // Doc count


词条聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-terms-aggregation.html[词条聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilders
        .terms("genders")
        .field("gender");


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.terms.Terms;

.. code-block:: java

    // sr is here your SearchResponse object
    Terms genders = sr.getAggregations().get("genders");

    // For each entry
    for (Terms.Bucket entry : genders.getBuckets()) {
        entry.getKey();      // Term
        entry.getDocCount(); // Doc count
    }

排序
========================================

按照桶的 `doc_count` 的升序排序:

.. code-block:: java

    AggregationBuilders
        .terms("genders")
        .field("gender")
        .order(Terms.Order.count(true))

按照桶中词条的字母的升序排序:

.. code-block:: java

    AggregationBuilders
        .terms("genders")
        .field("gender")
        .order(Terms.Order.term(true))

按照单值的度量子聚合进行桶排序 (按照聚合名称进行标识):

.. code-block:: java

    AggregationBuilders
        .terms("genders")
        .field("gender")
        .order(Terms.Order.aggregation("avg_height", false))
        .subAggregation(
            AggregationBuilders.avg("avg_height").field("height")
        )


重要词条聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-significantterms-aggregation.html[重要词条聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .significantTerms("significant_countries")
                    .field("address.country");

    // Let say you search for men only
    SearchResponse sr = client.prepareSearch()
            .setQuery(QueryBuilders.termQuery("gender", "male"))
            .addAggregation(aggregation)
            .get();


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.significant.SignificantTerms;

.. code-block:: java

    // sr is here your SearchResponse object
    SignificantTerms agg = sr.getAggregations().get("significant_countries");

    // For each entry
    for (SignificantTerms.Bucket entry : agg.getBuckets()) {
        entry.getKey();      // Term
        entry.getDocCount(); // Doc count
    }


范围聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-range-aggregation.html[范围聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .range("agg")
                    .field("height")
                    .addUnboundedTo(1.0f)               // from -infinity to 1.0 (excluded)
                    .addRange(1.0f, 1.5f)               // from 1.0 to 1.5 (excluded)
                    .addUnboundedFrom(1.5f);            // from 1.5 to +infinity


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.range.Range;

.. code-block:: java

    // sr is here your SearchResponse object
    Range agg = sr.getAggregations().get("agg");

    // For each entry
    for (Range.Bucket entry : agg.getBuckets()) {
        String key = entry.getKeyAsString();             // Range as key
        Number from = (Number) entry.getFrom();          // Bucket from
        Number to = (Number) entry.getTo();              // Bucket to
        long docCount = entry.getDocCount();    // Doc count

        logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, from, to, docCount);
    }

第一个示例的基本输出如下:

.. code-block:: text

    key [*-1.0], from [-Infinity], to [1.0], doc_count [9]
    key [1.0-1.5], from [1.0], to [1.5], doc_count [21]
    key [1.5-*], from [1.5], to [Infinity], doc_count [20]


日期范围聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-daterange-aggregation.html[日期范围聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .dateRange("agg")
                    .field("dateOfBirth")
                    .format("yyyy")
                    .addUnboundedTo("1950")    // from -infinity to 1950 (excluded)
                    .addRange("1950", "1960")  // from 1950 to 1960 (excluded)
                    .addUnboundedFrom("1960"); // from 1960 to +infinity


使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.range.Range;

.. code-block:: java

    // sr is here your SearchResponse object
    Range agg = sr.getAggregations().get("agg");

    // For each entry
    for (Range.Bucket entry : agg.getBuckets()) {
        String key = entry.getKeyAsString();                // Date range as key
        DateTime fromAsDate = (DateTime) entry.getFrom();   // Date bucket from as a Date
        DateTime toAsDate = (DateTime) entry.getTo();       // Date bucket to as a Date
        long docCount = entry.getDocCount();                // Doc count

        logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, fromAsDate, toAsDate, docCount);
    }

该示例会产生以下基本输出:

.. code-block:: text

    key [*-1950], from [null], to [1950-01-01T00:00:00.000Z], doc_count [8]
    key [1950-1960], from [1950-01-01T00:00:00.000Z], to [1960-01-01T00:00:00.000Z], doc_count [5]
    key [1960-*], from [1960-01-01T00:00:00.000Z], to [null], doc_count [37]


IP范围聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-iprange-aggregation.html[Ip 范围聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregatorBuilder<?> aggregation =
            AggregationBuilders
                    .ipRange("agg")
                    .field("ip")
                    .addUnboundedTo("192.168.1.0")             // from -infinity to 192.168.1.0 (excluded)
                    .addRange("192.168.1.0", "192.168.2.0")    // from 192.168.1.0 to 192.168.2.0 (excluded)
                    .addUnboundedFrom("192.168.2.0");          // from 192.168.2.0 to +infinity

请注意, 你还可以将IP掩码用作范围:

.. code-block:: java

    AggregatorBuilder<?> aggregation =
            AggregationBuilders
                    .ipRange("agg")
                    .field("ip")
                    .addMaskRange("192.168.0.0/32")
                    .addMaskRange("192.168.0.0/24")
                    .addMaskRange("192.168.0.0/16");

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.range.Range;

.. code-block:: java

    // sr is here your SearchResponse object
    Range agg = sr.getAggregations().get("agg");

    // For each entry
    for (Range.Bucket entry : agg.getBuckets()) {
        String key = entry.getKeyAsString();            // Ip range as key
        String fromAsString = entry.getFromAsString();  // Ip bucket from as a String
        String toAsString = entry.getToAsString();      // Ip bucket to as a String
        long docCount = entry.getDocCount();            // Doc count

        logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, fromAsString, toAsString, docCount);
    }

第一个示例的基本输出如下:

.. code-block:: text

    key [*-192.168.1.0], from [null], to [192.168.1.0], doc_count [13]
    key [192.168.1.0-192.168.2.0], from [192.168.1.0], to [192.168.2.0], doc_count [14]
    key [192.168.2.0-*], from [192.168.2.0], to [null], doc_count [23]

而对于第二个示例 (使用 Ip 掩码) 的基本输出:

.. code-block:: text

    key [192.168.0.0/32], from [192.168.0.0], to [192.168.0.1], doc_count [0]
    key [192.168.0.0/24], from [192.168.0.0], to [192.168.1.0], doc_count [13]
    key [192.168.0.0/16], from [192.168.0.0], to [192.169.0.0], doc_count [50]


直方图聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-histogram-aggregation.html[直方图聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .histogram("agg")
                    .field("height")
                    .interval(1);

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.histogram.Histogram;

.. code-block:: java

    // sr is here your SearchResponse object
    Histogram agg = sr.getAggregations().get("agg");

    // For each entry
    for (Histogram.Bucket entry : agg.getBuckets()) {
        Number key = (Number) entry.getKey();   // Key
        long docCount = entry.getDocCount();    // Doc count

        logger.info("key [{}], doc_count [{}]", key, docCount);
    }


日期直方图聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-datehistogram-aggregation.html[日期直方图聚合].


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .dateHistogram("agg")
                    .field("dateOfBirth")
                    .dateHistogramInterval(DateHistogramInterval.YEAR);

或者你想要设置10天的一个间隔:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .dateHistogram("agg")
                    .field("dateOfBirth")
                    .dateHistogramInterval(DateHistogramInterval.days(10));

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.histogram.Histogram;

.. code-block:: java

    // sr is here your SearchResponse object
    Histogram agg = sr.getAggregations().get("agg");

    // For each entry
    for (Histogram.Bucket entry : agg.getBuckets()) {
        DateTime key = (DateTime) entry.getKey();    // Key
        String keyAsString = entry.getKeyAsString(); // Key as String
        long docCount = entry.getDocCount();         // Doc count

        logger.info("key [{}], date [{}], doc_count [{}]", keyAsString, key.getYear(), docCount);
    }

该示例会产生第一个示例的基本输出:

.. code-block:: text

    key [1942-01-01T00:00:00.000Z], date [1942], doc_count [1]
    key [1945-01-01T00:00:00.000Z], date [1945], doc_count [1]
    key [1946-01-01T00:00:00.000Z], date [1946], doc_count [1]
    ...
    key [2005-01-01T00:00:00.000Z], date [2005], doc_count [1]
    key [2007-01-01T00:00:00.000Z], date [2007], doc_count [2]
    key [2008-01-01T00:00:00.000Z], date [2008], doc_count [3]


地理距离聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-geodistance-aggregation.html[地理距离聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .geoDistance("agg", new GeoPoint(48.84237171118314,2.33320027692004))
                    .field("address.location")
                    .unit(DistanceUnit.KILOMETERS)
                    .addUnboundedTo(3.0)
                    .addRange(3.0, 10.0)
                    .addRange(10.0, 500.0);

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.range.Range;

.. code-block:: java

    // sr is here your SearchResponse object
    Range agg = sr.getAggregations().get("agg");

    // For each entry
    for (Range.Bucket entry : agg.getBuckets()) {
        String key = entry.getKeyAsString();    // key as String
        Number from = (Number) entry.getFrom(); // bucket from value
        Number to = (Number) entry.getTo();     // bucket to value
        long docCount = entry.getDocCount();    // Doc count

        logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, from, to, docCount);
    }

该示例会产生以下基本输出:

.. code-block:: text

    key [*-3.0], from [0.0], to [3.0], doc_count [161]
    key [3.0-10.0], from [3.0], to [10.0], doc_count [460]
    key [10.0-500.0], from [10.0], to [500.0], doc_count [4925]


地理哈希网格聚合
========================================

下面展示了如何使用 Java API 进行 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/search-aggregations-bucket-geohashgrid-aggregation.html[地理哈希网格聚合]。


创建聚合请求
----------------------------------------

下面是一个创建聚合请求的代码示例:

.. code-block:: java

    AggregationBuilder aggregation =
            AggregationBuilders
                    .geohashGrid("agg")
                    .field("address.location")
                    .precision(4);

使用聚合响应
----------------------------------------

引入聚合定义类:

.. code-block:: java

    import org.elasticsearch.search.aggregations.bucket.geogrid.GeoHashGrid;

.. code-block:: java

    // sr is here your SearchResponse object
    GeoHashGrid agg = sr.getAggregations().get("agg");

    // For each entry
    for (GeoHashGrid.Bucket entry : agg.getBuckets()) {
        String keyAsString = entry.getKeyAsString(); // key as String
        GeoPoint key = (GeoPoint) entry.getKey();    // key as geo point
        long docCount = entry.getDocCount();         // Doc count

        logger.info("key [{}], point {}, doc_count [{}]", keyAsString, key, docCount);
    }

该示例会产生以下基本输出:

.. code-block:: text

    key [gbqu], point [47.197265625, -1.58203125], doc_count [1282]
    key [gbvn], point [50.361328125, -4.04296875], doc_count [1248]
    key [u1j0], point [50.712890625, 7.20703125], doc_count [1156]
    key [u0j2], point [45.087890625, 7.55859375], doc_count [1138]
    ...
