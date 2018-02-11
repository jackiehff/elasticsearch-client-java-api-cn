########################################
文档 API
########################################

本节描述了以下 CRUD API:

.单文档 API \* <> \* <> \* <> \* <> \* <>

.多文档 API \* <> \* <>

NOTE: 所有 CRUD API 都是单索引 API。\ ``index``
参数接受单个索引名称或者指向单个索引的一个别名 ``alias``\ 。


=== 索引 API

索引 API 允许用户将一个类型化的 JSON 文档索引到一个特定的索引中, 并且使它可以被搜索到。


==== 生成 JSON 文档

生成一个 JSON 文档有以下几种不同的方式:

* 手动(即自己动手)使用本地 `byte[]` 或者作为 `String`

* 使用 `Map`, 它会自动转换成与其等价的 JSON

* 使用 http://wiki.fasterxml.com/JacksonHome[Jackson] 这样的第三方类库来序列化你的 Java Bean

* 使用内置的帮助类 XContentFactory.jsonBuilder()

在 Elasticsearch 内部, 每种类型都被转换成 `byte[]`(因此字符串也会被转换成一个 `byte[]`)。因此, 可以直接使用已经是这种形式的对象。`jsonBuilder` 是高度优化过的 JSON 生成器, 它可以直接构造一个 `byte[]`。


===== 自己动手

这里没有什么真正很难的地方, 但是需要注意的是, 你需要根据 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/mapping-date-format.html[日期格式] 来编码日期。

.. code-block:: java

    String json = "{" +
            "\"user\":\"kimchy\"," +
            "\"postDate\":\"2013-01-30\"," +
            "\"message\":\"trying out Elasticsearch\"" +
        "}";


===== 使用 Map

Map 是一个键值对集合, 它表示一个 JSON 结构:

.. code-block:: java

    Map<String, Object> json = new HashMap<String, Object>();
    json.put("user","kimchy");
    json.put("postDate",new Date());
    json.put("message","trying out Elasticsearch");


===== 序列化你的Bean

可以使用 http://wiki.fasterxml.com/JacksonHome[Jackson] 将你的 Java Bean 序列化成 JSON 格式。你需要在项目中添加 http://search.maven.org/#search%7Cga%7C1%7Cjackson-databind[Jackson Databind] 依赖, 接着你就可以使用 `ObjectMapper` 来序列化你的 Java Bean:

.. code-block:: java

    import com.fasterxml.jackson.databind.*;

    // instance a json mapper
    ObjectMapper mapper = new ObjectMapper(); // create once, reuse

    // generate json
    byte[] json = mapper.writeValueAsBytes(yourbeaninstance);


===== 使用 Elasticsearch 帮助类

Elasticsearch 提供了内置的帮助类来生成 JSON 内容。

.. code-block:: java

    import static org.elasticsearch.common.xcontent.XContentFactory.*;

    XContentBuilder builder = jsonBuilder()
        .startObject()
            .field("user", "kimchy")
            .field("postDate", new Date())
            .field("message", "trying out Elasticsearch")
        .endObject()

请注意, 你还可以使用 `startArray(String)` 和 `endArray()` 方法来添加数组。顺便说一下, `field` 方法可以接受许多对象类型, 你可以直接传递数字, 日期, 甚至是其它 XContentBuilder 对象。

如果你要查看生成的 JSON 内容, 你可以使用 `string()` 方法.

.. code-block:: java

    String json = builder.string();


==== 索引文档

下面的例子将一个 JSON 文档索引到一个名为 twitter 的索引中, 文档对应的类型为 tweet, 文档 ID 为 1:

.. code-block:: java

    import static org.elasticsearch.common.xcontent.XContentFactory.*;

    IndexResponse response = client.prepareIndex("twitter", "tweet", "1")
            .setSource(jsonBuilder()
                        .startObject()
                            .field("user", "kimchy")
                            .field("postDate", new Date())
                            .field("message", "trying out Elasticsearch")
                        .endObject()
                      )
            .get();

请注意, 你也可以将文档作为 JSON 字符串进行索引并且不需要指定文档 ID:

.. code-block:: java

    String json = "{" +
            "\"user\":\"kimchy\"," +
            "\"postDate\":\"2013-01-30\"," +
            "\"message\":\"trying out Elasticsearch\"" +
        "}";

    IndexResponse response = client.prepareIndex("twitter", "tweet")
            .setSource(json)
            .get();

`IndexResponse` 对象返回给你所有信息:

.. code-block:: java

    // Index name
    String _index = response.getIndex();
    // Type name
    String _type = response.getType();
    // Document ID (generated or not)
    String _id = response.getId();
    // Version (if it's the first time you index this document, you will get: 1)
    long _version = response.getVersion();
    // status has stored current instance statement.
    RestStatus status = response.status();

有关索引操作的更多信息, 请查看 REST https://www.elastic.co/guide/en/elasticsearch/reference/5.2/docs-index_.html[索引]文档。


==== 操作线程

当在相同节点上执行实际的 API 操作时, 索引API允许用户设置将要执行操作的线程模型(API 在同一台服务器上分配的分片上执行)。

你可以选择在不同的线程上执行操作, 或者在调用线程上执行(请注意, API 仍然是异步的)。`operationThreaded` 的默认值为 `true`, 意味着操作是在不同的线程上执行。


=== 获取 API

获取 API 允许用户根据文档 ID 获取索引中一个类型化的 JSON 文档。下面的例子从 twitter 索引中获取一个 JSON 文档, 文档对应的类型是 tweet, 文档 ID 为 1:

.. code-block:: java

    GetResponse response = client.prepareGet("twitter", "tweet", "1").get();

更多有关获取操作的信息, 请查看 REST https://www.elastic.co/guide/en/elasticsearch/reference/5.2/docs-get.html[获取]文档。

==== 操作线程

当在相同节点上执行实际的 API 操作时, 获取 API 允许用户设置将要执行操作的线程模型(API 在同一台服务器上分配的分片上执行)。

你可以选择在不同的线程上执行操作, 或者在调用线程上执行它(请注意, API 仍然是异步的)。`operationThreaded` 的默认值为 `true`, 意味着操作是在不同的线程上执行。下面的示例将其设置为 `false`:

.. code-block:: java

    GetResponse response = client.prepareGet("twitter", "tweet", "1")
            .setOperationThreaded(false)
            .get();


=== 删除 API

删除 API 允许用户根据文档 ID 删除特定索引中一个类型化的 JSON 文档。下面的示例从索引 twitter 中删除类型 tweet、文档 ID 为 1 的 JSON 文档:

.. code-block:: java

    DeleteResponse response = client.prepareDelete("twitter", "tweet", "1").get();

更多关于删除操作的信息, 请查看 https://www.elastic.co/guide/en/elasticsearch/reference/5.2/docs-delete.html[删除 API] 文档。

=== 根据查询删除 API

根据查询删除 API 允许用户删除根据查询结果得到文档集合:

.. code-block:: java

    BulkIndexByScrollResponse response =
        DeleteByQueryAction.INSTANCE.newRequestBuilder(client)
            .filter(QueryBuilders.matchQuery("gender", "male")) <1>
            .source("persons")                                  <2>
            .get();                                             <3>

    long deleted = response.getDeleted();                       <4>

<1> 查询
<2> 索引
<3> 执行操作
<4> 删除的文档数量

因为它是一个耗时比较长的操作, 所以如果你想要异步执行, 你可以调用 `execute` 方法来替代 `get` 方法并提供一个像下面这样的监听器:

.. code-block:: java

    DeleteByQueryAction.INSTANCE.newRequestBuilder(client)
        .filter(QueryBuilders.matchQuery("gender", "male"))                  <1>
        .source("persons")                                                   <2>
        .execute(new ActionListener<BulkIndexByScrollResponse>() {           <3>
            @Override
            public void onResponse(BulkIndexByScrollResponse response) {
                long deleted = response.getDeleted();                        <4>
            }
            @Override
            public void onFailure(Exception e) {
                // Handle the exception
            }
        });

<1> 查询
<2> 索引
<3> 监听器
<4> 删除的文档数量


=== 更新 API

你可以创建一个 `UpdateRequest` 对象并将其发送给客户端:

.. code-block:: java

    UpdateRequest updateRequest = new UpdateRequest();
    updateRequest.index("index");
    updateRequest.type("type");
    updateRequest.id("1");
    updateRequest.doc(jsonBuilder()
            .startObject()
                .field("gender", "male")
            .endObject());
    client.update(updateRequest).get();

或者你也可以使用 `prepareUpdate()` 方法:

.. code-block:: java

    client.prepareUpdate("ttl", "doc", "1")
            .setScript(new Script("ctx._source.gender = \"male\"" <1> , ScriptType.INLINE, null, null))
            .get();

    client.prepareUpdate("ttl", "doc", "1")
            .setDoc(jsonBuilder()               <2>
                .startObject()
                    .field("gender", "male")
                .endObject())
            .get();

<1> 你的脚本. 它也可以是一个本地存储的脚本名称。在这种情况下, 你需要使用 `ScriptType.FILE`。
<2> 将要合并到现有文档的一个文档。

请注意, 你不能同时提供 `script` 和 `doc`.

==== 通过脚本更新

更新 API 允许根据用户所提供的脚本来更新文档:

.. code-block:: java

    UpdateRequest updateRequest = new UpdateRequest("ttl", "doc", "1")
            .script(new Script("ctx._source.gender = \"male\""));
    client.update(updateRequest).get();


==== 通过合并文档进行更新

更新 API 还支持传递部分文档, 它将合并到现有的文档中(简单递归合并, 对象的内部合并, 替换核心的键值对和数组)。例如:

.. code-block:: java

    UpdateRequest updateRequest = new UpdateRequest("index", "type", "1")
            .doc(jsonBuilder()
                .startObject()
                    .field("gender", "male")
                .endObject());
    client.update(updateRequest).get();


==== 更新插入

Elasticsearch 还支持更新插入。如果文档不存在, `upsert` 元素将用于索引新鲜文档:

.. code-block:: java

    IndexRequest indexRequest = new IndexRequest("index", "type", "1")
            .source(jsonBuilder()
                .startObject()
                    .field("name", "Joe Smith")
                    .field("gender", "male")
                .endObject());
    UpdateRequest updateRequest = new UpdateRequest("index", "type", "1")
            .doc(jsonBuilder()
                .startObject()
                    .field("gender", "male")
                .endObject())
            .upsert(indexRequest);              <1>
    client.update(updateRequest).get();

<1> 如果文档不存在, `indexRequest` 中的文档将会被添加进来

如果文档 `index/type/1` 已经存在, 那么在该操作之后我们将会得到一个像下面这样的文档:

.. code-block:: json

    {
        "name"  : "Joe Dalton",
        "gender": "male"        <1>
    }
    // NOTCONSOLE

<1> 该字段由更新请求添加

如果它不存在, 那么我们将会得到一个新的文档:

.. code-block:: json

    {
        "name" : "Joe Smith",
        "gender": "male"
    }
    // NOTCONSOLE


== Update By Query API

experimental[The update-by-query API is new and should still be considered experimental.  The API may change in ways that are not backwards compatible]

The simplest usage of `updateByQuery` updates each
document in an index without changing the source. This usage enables
<<picking-up-a-new-property,picking up a new property>> or another online
mapping change.

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);

    updateByQuery.source("source_index").abortOnVersionConflict(false);

    BulkIndexByScrollResponse response = updateByQuery.get();

Calls to the `updateByQuery` API start by getting a snapshot of the index, indexing
any documents found using the `internal` versioning.

NOTE: Version conflicts happen when a document changes between the time of the
snapshot and the time the index request processes.

When the versions match, `updateByQuery` updates the document
and increments the version number.

All update and query failures cause `updateByQuery` to abort. These failures are
available from the `BulkIndexByScrollResponse#getIndexingFailures` method. Any
successful updates remain and are not rolled back. While the first failure
causes the abort, the response contains all of the failures generated by the
failed bulk request.

To prevent version conflicts from causing `updateByQuery` to abort, set
`abortOnVersionConflict(false)`. The first example does this because it is
trying to pick up an online mapping change and a version conflict means that
the conflicting document was updated between the start of the `updateByQuery`
and the time when it attempted to update the document. This is fine because
that update will have picked up the online mapping update.

The `UpdateByQueryRequestBuilder` API supports filtering the updated documents,
limiting the total number of documents to update, and updating documents
with a script:

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);

    updateByQuery.source("source_index")
            .filter(termQuery("level", "awesome"))
            .size(1000)
            .script(new Script("ctx._source.awesome = 'absolutely'", ScriptType.INLINE, "painless", emptyMap()));

    BulkIndexByScrollResponse response = updateByQuery.get();

`UpdateByQueryRequestBuilder` also enables direct access to the query used
to select the documents. You can use this access to change the default scroll size or
otherwise modify the request for matching documents.

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);

    updateByQuery.source("source_index")
            .source().setSize(500);

    BulkIndexByScrollResponse response = updateByQuery.get();

You can also combine `size` with sorting to limit the documents updated:

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);

    updateByQuery.source("source_index").size(100)
            .source().addSort("cat", SortOrder.DESC);

    BulkIndexByScrollResponse response = updateByQuery.get();

In addition to changing the `_source` field for the document, you can use a
script to change the action, similar to the Update API:

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);

    updateByQuery.source("source_index")
            .script(new Script(
                "if (ctx._source.awesome == 'absolutely) {"
              + "  ctx.op='noop'
              + "} else if (ctx._source.awesome == 'lame') {"
              + "  ctx.op='delete'"
              + "} else {"
              + "ctx._source.awesome = 'absolutely'}", ScriptType.INLINE, "painless", emptyMap()));

    BulkIndexByScrollResponse response = updateByQuery.get();

As in the <<docs-update,Update API>>, you can set the value of `ctx.op` to change the
operation that executes:

`noop`::

Set `ctx.op = "noop"` if your script doesn't make any
changes. The `updateByQuery` operaton then omits that document from the updates.
This behavior increments the `noop` counter in the
<<docs-update-by-query-response-body, response body>>.

`delete`::

Set `ctx.op = "delete"` if your script decides that the document must be
deleted. The deletion will be reported in the `deleted` counter in the
<<docs-update-by-query-response-body, response body>>.

Setting `ctx.op` to any other value generates an error. Setting any
other field in `ctx` generates an error.

This API doesn't allow you to move the documents it touches, just modify their
source. This is intentional! We've made no provisions for removing the document
from its original location.

You can also perform these operations on multiple indices and types at once, similar to the search API:

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);

    updateByQuery.source("foo", "bar").source().setTypes("a", "b");

    BulkIndexByScrollResponse response = updateByQuery.get();

If you provide a `routing` value then the process copies the routing value to the scroll query,
limiting the process to the shards that match that routing value:

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);
    updateByQuery.source().setRouting("cat");
    BulkIndexByScrollResponse response = updateByQuery.get();

`updateByQuery` can also use the <<ingest>> feature by
specifying a `pipeline` like this:

.. code-block:: java

    UpdateByQueryRequestBuilder updateByQuery = UpdateByQueryAction.INSTANCE.newRequestBuilder(client);
    updateByQuery.setPipeline("hurray");
    BulkIndexByScrollResponse response = updateByQuery.get();

=== Works with the Task API

You can fetch the status of all running update-by-query requests with the
<<tasks,Task API>>:

.. code-block:: java

    ListTasksResponse tasksList = client.admin().cluster().prepareListTasks()
            .setActions(UpdateByQueryAction.NAME).setDetailed(true).get();

    for (TaskInfo info: tasksList.getTasks()) {
        TaskId taskId = info.getTaskId();
        BulkByScrollTask.Status status = (BulkByScrollTask.Status) info.getStatus();
        // do stuff
    }


With the `TaskId` shown above you can look up the task directly:

// provide API Example
.. code-block:: java

    GetTaskResponse get = client.admin().cluster().prepareGetTask(taskId).get();

=== Works with the Cancel Task API

Any Update By Query can be canceled using the <<tasks,Task Cancel API>>:

.. code-block:: java

    // Cancel all update-by-query requests
    client.admin().cluster().prepareCancelTasks().setActions(UpdateByQueryAction.NAME).get().getTasks()
    // Cancel a specific update-by-query request
    client.admin().cluster().prepareCancelTasks().setTaskId(taskId).get().getTasks()

Use the `list tasks` API to find the value of `taskId`.

Cancelling a request is typically a very fast process but can take up to a few seconds.
The task status API continues to list the task until the cancellation is complete.

=== Rethrottling

Use the `_rethrottle` API to change the value of `requests_per_second` on a running update:

.. code-block:: java

    RethrottleAction.INSTANCE.newRequestBuilder(client).setTaskId(taskId).setRequestsPerSecond(2.0f).get();

Use the `list tasks` API to find the value of `taskId`.

As with the `updateByQuery` API, the value of `requests_per_second`
can be any positive float value to set the level of the throttle, or `Float.POSITIVE_INFINITY` to disable throttling.
A value of `requests_per_second` that speeds up the process takes
effect immediately. `requests_per_second` values that slow the query take effect
after completing the current batch in order to prevent scroll timeouts.


=== Multi Get API

Multi Get API 允许用户根据文档的 `index`, `type` 以及 `id` 来获取文档列表:

.. code-block:: java

    MultiGetResponse multiGetItemResponses = client.prepareMultiGet()
        .add("twitter", "tweet", "1")           <1>
        .add("twitter", "tweet", "2", "3", "4") <2>
        .add("another", "type", "foo")          <3>
        .get();

    for (MultiGetItemResponse itemResponse : multiGetItemResponses) { <4>
        GetResponse response = itemResponse.getResponse();
        if (response.isExists()) {                      <5>
            String json = response.getSourceAsString(); <6>
        }
    }

<1> 根据单个文档 ID 进行查询
<2> 对同样的索引/类型根据文档 ID 列表进行查询
<3> 还可以从另一个索引上查询
<4> 遍历结果集
<5> 检查文档是否存在
<6> 访问 `_source` 字段

更多有关 Multi Get 操作的信息, 请查看 REST https://www.elastic.co/guide/en/elasticsearch/reference/5.2/docs-multi-get.html[multi get] 文档.


=== Bulk API

Bulk API 允许用户在单个请求中索引和删除多个文档。下面是一个使用示例:

.. code-block:: java

    import static org.elasticsearch.common.xcontent.XContentFactory.*;

    BulkRequestBuilder bulkRequest = client.prepareBulk();

    // either use client#prepare, or use Requests# to directly build index/delete requests
    bulkRequest.add(client.prepareIndex("twitter", "tweet", "1")
            .setSource(jsonBuilder()
                        .startObject()
                            .field("user", "kimchy")
                            .field("postDate", new Date())
                            .field("message", "trying out Elasticsearch")
                        .endObject()
                      )
            );

    bulkRequest.add(client.prepareIndex("twitter", "tweet", "2")
            .setSource(jsonBuilder()
                        .startObject()
                            .field("user", "kimchy")
                            .field("postDate", new Date())
                            .field("message", "another post")
                        .endObject()
                      )
            );

    BulkResponse bulkResponse = bulkRequest.get();
    if (bulkResponse.hasFailures()) {
        // process failures by iterating through each bulk response item
    }

=== 使用批处理器

`BulkProcessor` 类提供了一个简单的接口, 它可以根据请求数量或在指定的时间段后自动地刷新批量操作。

要使用它的话, 首先要创建一个 `BulkProcessor` 实例:

.. code-block:: java

    import org.elasticsearch.action.bulk.BackoffPolicy;
    import org.elasticsearch.action.bulk.BulkProcessor;
    import org.elasticsearch.common.unit.ByteSizeUnit;
    import org.elasticsearch.common.unit.ByteSizeValue;
    import org.elasticsearch.common.unit.TimeValue;

    BulkProcessor bulkProcessor = BulkProcessor.builder(
            client,  <1>
            new BulkProcessor.Listener() {
                @Override
                public void beforeBulk(long executionId,
                                       BulkRequest request) { ... } <2>

                @Override
                public void afterBulk(long executionId,
                                      BulkRequest request,
                                      BulkResponse response) { ... } <3>

                @Override
                public void afterBulk(long executionId,
                                      BulkRequest request,
                                      Throwable failure) { ... } <4>
            })
            .setBulkActions(10000) <5>
            .setBulkSize(new ByteSizeValue(5, ByteSizeUnit.MB)) <6>
            .setFlushInterval(TimeValue.timeValueSeconds(5)) <7>
            .setConcurrentRequests(1) <8>
            .setBackoffPolicy(
                BackoffPolicy.exponentialBackoff(TimeValue.timeValueMillis(100), 3)) <9>
            .build();

<1> 添加 Elasticsearch 客户端
<2> 该方法在批量操作执行前调用。例如, 你可以使用 `request.numberOfActions()` 方法查看 numberOfActions
<3> 该方法在批量操作执行后调用。例如, 你可以使用 `response.hasFailures()` 方法检查是否有失败的请求
<4> 该方法在批量失败并抛出 `Throwable` 时调用
<5> 每 10 000 个请求执行一次批量操作
<6> 每 5mb 刷新一次批量操作
<7> 不管请求数量多少, 每 5s 刷新一次批量操作
<8> 设置并发请求数量。值为 0 的话意味着一次只允许执行一个请求。值为1的话意味着在累积新的批量请求时值允许执行1个并发请求。
<9> 设置自定义退避策略, 该策略最初将等待100毫秒, 按指数增加并且最多重试三次。当一个或多个批量项目请求因为 `EsRejectedExecutionException` 异常而失败, 这通常意味着没有足够的计算资源来处理这个请求, 一般会尝试重试。要禁用退避, 可以传递 `BackoffPolicy.noBackoff()`。

默认情况下, `BulkProcessor` 会做以下事情:

* 设置 bulkActions 的值为 `1000`
* 设置 bulkSize 的值为 `5mb`
* 不设置 flushInterval
* 设置 concurrentRequests 的值为 1, 意味着异步执行刷新操作。
* 设置 backoffPolicy 的值为重试 8 次以及 50ms 启动延迟的一个指数退避, 总的等待时间大概是 5.1s。

==== 添加请求

接着你可以简单地将请求添加到 `BulkProcessor`:

.. code-block:: java

    bulkProcessor.add(new IndexRequest("twitter", "tweet", "1").source(/* your doc here */));
    bulkProcessor.add(new DeleteRequest("twitter", "tweet", "2"));

==== 关闭批处理器

当所有的文档都被加载到 `BulkProcessor` 后, 可以使用 `awaitClose` 或 `close` 方法来关闭它:

.. code-block:: java

    bulkProcessor.awaitClose(10, TimeUnit.MINUTES);

或

.. code-block:: java

    bulkProcessor.close();

如果他们是通过设置 `flushInterval` 预先安排的, 那么两种方法都会清除所有剩余的文档并禁用所有其它事先安排的刷新。如果启用了并发请求, 那么 `awaitClose` 方法将在指定的超时时间内等待所有的批量请求执行完成后返回 `true`, 如果在指定的等待时间之后所有批量请求还未完成则返回 `false`。`close` 方法会立即退出而不会等待任何还未完成的批量请求。

==== 在测试中使用批处理器

如果你正在使用 Elasticsearch 运行测试并且使用 `BulkProcessor` 来填充你的数据集, 那么你最好将并发请求的数量设置为 `0`, 这样的话批量刷新操作将会以同步的方式执行:

.. code-block:: java

    BulkProcessor bulkProcessor = BulkProcessor.builder(client, new BulkProcessor.Listener() { /* Listener methods */ })
            .setBulkActions(10000)
            .setConcurrentRequests(0)
            .build();

    // Add your requests
    bulkProcessor.add(/* Your requests */);

    // Flush any remaining requests
    bulkProcessor.flush();

    // Or close the bulkProcessor if you don't need it anymore
    bulkProcessor.close();

    // Refresh your indices
    client.admin().indices().prepareRefresh().get();

    // Now you can start searching!
    client.prepareSearch().get();
