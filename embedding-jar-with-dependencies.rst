########################################
将依赖嵌入jar包
########################################

如果你想创建一个包含你应用程序代码和所有依赖的 jar 包, 请不要使用 `maven-assembly-plugin` 插件, 因为它无法处理 Lucene jar 所要求的 `META-INF/services` 结构。

相反, 你可以使用 `maven-shade-plugin` 插件并使用如下配置:

.. code-block:: xml

    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.4.1</version>
        <executions>
            <execution>
                <phase>package</phase>
                <goals><goal>shade</goal></goals>
                <configuration>
                    <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
                    </transformers>
                </configuration>
            </execution>
        </executions>
    </plugin>

注意: 如果你想在运行 `java -jar yourjar.jar` 命令时自动调用一个 `main` 类, 你只需要将其添加到 `transformers` 元素中:

.. code-block:: xml

    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
        <mainClass>org.elasticsearch.demo.Generate</mainClass>
    </transformer>
