########################################
前言
########################################


本节描述了 Elasticsearch 提供的 Java API。Elasticsearch 的所有操作都是使用 :ref:`client` 对象来执行的。所有操作本质上都是完全异步的 (要么接受一个监听器, 要么返回一个 Future 对象)。

另外, 客户端上的操作可以累积起来并以 `批量 <https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-docs-bulk.html>`_ 的方式执行。

注意，所有的 API 都是通过 Java API 暴露的(实际上, Elasticsearch 内部是使用 Java API 来执行操作的)。

.. warning::
  我们计划在 Elasticsearch 7.0 版本中关闭 TransportClient 并且在 8.0 版本中完全删除它。你应该使用 Java 高级 REST 客户端作为替代, 它可以执行 HTTP 请求, 而不是序列化 Java 请求。`迁移指南 <https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.2/java-rest-high-level-migration.html>`_ 中描述了迁移所需的所有步骤。

  Java 高级 REST 客户端目前已经支持常用的 API, 但是仍然有很多需要添加进来。你可以对 `Java 高级 REST 客户端完整性 <https://github.com/elastic/elasticsearch/issues/27205>`_ 这个问题添加评论来告诉我们你的应用程序需要哪些缺少的API，从而帮助我们确定优先级。

  任何缺少的 API 现在都可以通过使用具有 JSON 请求和响应体的 `低级 Java REST 客户端 <https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-low.html>`_ 来实现。