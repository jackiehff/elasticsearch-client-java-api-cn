########################################
Java管理API
########################################

Elasticsearch 提供了一套完整的 Java API 来处理管理任务.

要想访问他们, 你需要在客户端对象上调用 `admin()` 方法来获取一个 `AdminClient` 对象:

.. code-block:: java

    AdminClient adminClient = client.admin();

.. note:: 在本指南的剩余部分, 我们将使用 `client.admin()`。


****************************************
索引管理
****************************************

要通过 Java API 访问索引, 你需要调用 `AdminClient` 对象的 `indices()` 方法:

.. code-block:: java

    IndicesAdminClient indicesAdminClient = client.admin().indices();

.. note:: 在本指南的剩余部分, 我们将使用 `client.admin().indices()`。


创建索引
========================================

使用 IndicesAdminClient 对象, 你可以创建一个使用所有默认设置并且没有映射的索引:

.. code-block:: java

    client.admin().indices().prepareCreate("twitter").get();

索引设置
----------------------------------------

创建的每个索引都可以有与其相关的特定设置.

.. code-block:: java

    client.admin().indices().prepareCreate("twitter")
            .setSettings(Settings.builder()             <1>
                    .put("index.number_of_shards", 3)
                    .put("index.number_of_replicas", 2)
            )
            .get();                                     <2>

#. 该索引的设置
#. 执行操作并等待响应结果


Put Mapping
========================================

PUT 映射 API 允许你在创建索引时添加一个新的类型:

.. code-block:: java

    client.admin().indices().prepareCreate("twitter")    <1>
        .addMapping("tweet", "{\n" +                     <2>
                "    \"tweet\": {\n" +
                "      \"properties\": {\n" +
                "        \"message\": {\n" +
                "          \"type\": \"text\"\n" +
                "        }\n" +
                "      }\n" +
                "    }\n" +
                "  }")
        .get();

#. 创建一个名为 `twitter` 的索引
#. 它同时添加了一个名为 `tweet` 的映射类型。


PUT 映射 API 还允许用户给现有的索引添加一个新的类型:

.. code-block:: java

    client.admin().indices().preparePutMapping("twitter")    <1>
            .setType("user")                                 <2>
            .setSource("{\n" +                               <3>
                    "  \"properties\": {\n" +
                    "    \"name\": {\n" +
                    "      \"type\": \"text\"\n" +
                    "    }\n" +
                    "  }\n" +
                    "}")
            .get();

    // You can also provide the type in the source document
    client.admin().indices().preparePutMapping("twitter")
            .setType("user")
            .setSource("{\n" +
                    "    \"user\":{\n" +                      <4>
                    "        \"properties\": {\n" +
                    "            \"name\": {\n" +
                    "                \"type\": \"text\"\n" +
                    "            }\n" +
                    "        }\n" +
                    "    }\n" +
                    "}")
            .get();

#. 在现有索引上添加一个映射 `twitter`
#. 添加一个 `user` 映射类型.
#. `user` 有一个预定义的类型
#. 类型也可以在source中提供

你可以使用同样的 API 来更新一个现有的映射:

.. code-block:: java

    client.admin().indices().preparePutMapping("twitter")   <1>
            .setType("user")                               <2>
            .setSource("{\n" +                              <3>
                    "  \"properties\": {\n" +
                    "    \"user_name\": {\n" +
                    "      \"type\": \"text\"\n" +
                    "    }\n" +
                    "  }\n" +
                    "}")
            .get();

#. 在现有索引上添加一个映射 `twitter`
#. 更新 `user` 映射类型.
#. `user` 现在有一个新的字段 `user_name`


刷新
========================================

刷新 API 允许用户显示地刷新一个或多个索引:

.. code-block:: java

    client.admin().indices().prepareRefresh().get(); <1>
    client.admin().indices()
            .prepareRefresh("twitter")               <2>
            .get();
    client.admin().indices()
            .prepareRefresh("twitter", "company")   <3>
            .get();

#. 刷新所有索引
#. 刷新一个索引
#. 刷新多个索引


获取设置
========================================

获取设置 API API 允许用户检索一个或多个索引的设置:

.. code-block:: java

    GetSettingsResponse response = client.admin().indices()
            .prepareGetSettings("company", "employee").get();                           <1>
    for (ObjectObjectCursor<String, Settings> cursor : response.getIndexToSettings()) { <2>
        String index = cursor.key;                                                      <3>
        Settings settings = cursor.value;                                               <4>
        Integer shards = settings.getAsInt("index.number_of_shards", null);             <5>
        Integer replicas = settings.getAsInt("index.number_of_replicas", null);         <6>
    }

#. 获取索引 `company` 和 `employee` 的设置
#. 遍历结果集
#. 索引名称
#. 指定索引的设置
#. 索引的分片数量
#. 索引的副本数量


更新索引设置
========================================

通过调用以下代码你可以更改索引设置:

.. code-block:: java

    client.admin().indices().prepareUpdateSettings("twitter")   <1>
            .setSettings(Settings.builder()                     <2>
                    .put("index.number_of_replicas", 0)
            )
            .get();

#. 待更新的索引
#. 设置


****************************************
集群管理
****************************************

要访问集群 Java API, 你需要在 `AdminClient` 对象上调用 `cluster()` 方法:

.. code-block:: java

    ClusterAdminClient clusterAdminClient = client.admin().cluster();

.. note:: 在本指南的剩余部分, 我们将使用 `client.admin().cluster()`.


集群健康
========================================

健康
----------------------------------------

集群健康 API 允许用户获取有关集群健康的一个非常简单的状态并且也可以给你一些有关每个索引的集群状态的技术信息:

.. code-block:: java

    ClusterHealthResponse healths = client.admin().cluster().prepareHealth().get(); <1>
    String clusterName = healths.getClusterName();              <2>
    int numberOfDataNodes = healths.getNumberOfDataNodes();     <3>
    int numberOfNodes = healths.getNumberOfNodes();             <4>

    for (ClusterIndexHealth health : healths.getIndices().values()) { <5>
        String index = health.getIndex();                       <6>
        int numberOfShards = health.getNumberOfShards();        <7>
        int numberOfReplicas = health.getNumberOfReplicas();    <8>
        ClusterHealthStatus status = health.getStatus();        <9>
    }

#. 获取所有索引信息
#. 获取集群名称
#. 获取数据节点总数
#. 获取节点总数
#. 遍历所有索引
#. 索引名称
#. 分片数量
#. 副本数量
#. 索引状态


等待特定状态
----------------------------------------

你可以使用集群健康 API 来等待整个集群或指定的索引达到一个特定的状态:

.. code-block:: java

    client.admin().cluster().prepareHealth()            <1>
            .setWaitForYellowStatus()                   <2>
            .get();
    client.admin().cluster().prepareHealth("company")   <3>
            .setWaitForGreenStatus()                    <4>
            .get();

    client.admin().cluster().prepareHealth("employee")  <5>
            .setWaitForGreenStatus()                    <6>
            .setTimeout(TimeValue.timeValueSeconds(2))  <7>
            .get();

#. 准备一个健康请求对象
#. 等待集群状态变成 yellow
#. 为 `company` 索引准备健康请求对象
#. 等待索引状态变成 green
#. 为 `employee` 索引准备健康请求对象
#. 等待索引状态变成 green
#. 最多等待 2s

如果索引没有达到预期的状态值并且你想在这种情况下失败, 你需要显示地中断结果:

.. code-block:: java

    ClusterHealthResponse response = client.admin().cluster().prepareHealth("company")
            .setWaitForGreenStatus()    <1>
            .get();

    ClusterHealthStatus status = response.getIndices().get("company").getStatus();
    if (!status.equals(ClusterHealthStatus.GREEN)) {
        throw new RuntimeException("Index is in " + status + " state"); <2>
    }

#. 等待索引状态变成 green
#. 如果不是 `GREEN` 抛出异常


存储脚本 API
========================================

存储脚本 API 允许用户和存储在 Elasticsearch 索引中的脚本和模板进行交互。它可以用来创建, 更新, 查询以及删除存储的脚本和模板。

.. code-block:: java

    PutStoredScriptResponse response = client.admin().cluster().preparePutStoredScript()
                    .setId("script1")
                    .setContent(new BytesArray("{\"script\": {\"lang\": \"painless\", \"source\": \"_score * doc['my_numeric_field'].value\"} }"), XContentType.JSON)
                    .get();

    GetStoredScriptResponse response = client().admin().cluster().prepareGetStoredScript()
                    .setId("script1")
                    .get();

    DeleteStoredScriptResponse response = client().admin().cluster().prepareDeleteStoredScript()
                    .setId("script1")
                    .get();

要存储模板, 可以简单地在 scriptLang 上使用 "mustache"。

脚本语言
----------------------------------------

该 API 允许用户设置与之交互的索引脚本的语言. 如果没有设置则将使用默认的脚本语言。