########################################
前言
########################################


本节描述了 Elasticsearch 提供的 Java API。Elasticsearch 的所有操作都是使用 <<client,Client>> 对象来执行的。所有操作本质上都是完全异步的 (要么接受一个监听器, 要么返回一个 Future 对象)。

另外, 客户端上的操作可以累积起来并以 <<java-docs-bulk,批量>> 的方式执行。

注意，所有的 API 都是通过 Java API 暴露的(Elasticsearch 内部实际上是使用 Java API 来执行操作的)。

.. warning::
  我们计划在 Elasticsearch 7.0 版本中关闭 TransportClient 并且在 8.0 版本中完全删除它。你应该使用 Java 高级 REST 客户端作为替代, 它可以执行 HTTP 请求, 而不是序列化 Java 请求。`迁移指南 <https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.2/java-rest-high-level-migration.html>`_ 中描述了迁移所需的所有步骤。

  Java 高级 REST 客户端目前已经支持常用的 API, 但是仍然有很多需要添加进来。You can help us prioritise by telling us which missing APIs you need for your application by adding a comment to this issue: Java high-level REST client completeness.

  Any missing APIs can always be implemented today by using the low level Java REST Client with JSON request and response bodies.