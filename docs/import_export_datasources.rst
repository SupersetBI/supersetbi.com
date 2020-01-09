..  Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

..    http://www.apache.org/licenses/LICENSE-2.0

..  Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.

导入和导出数据源
===================================

superset cli 允许您从 YAML 导入和导出数据源。
数据源包括数据库和 druid 集群。数据将按照如下层次结构组织: ::

    .
    ├──databases
    |  ├──database_1
    |  |  ├──table_1
    |  |  |  ├──columns
    |  |  |  |  ├──column_1
    |  |  |  |  ├──column_2
    |  |  |  |  └──... (more columns)
    |  |  |  └──metrics
    |  |  |     ├──metric_1
    |  |  |     ├──metric_2
    |  |  |     └──... (more metrics)
    |  |  └── ... (more tables)
    |  └── ... (more databases)
    └──druid_clusters
       ├──cluster_1
       |  ├──datasource_1
       |  |  ├──columns
       |  |  |  ├──column_1
       |  |  |  ├──column_2
       |  |  |  └──... (more columns)
       |  |  └──metrics
       |  |     ├──metric_1
       |  |     ├──metric_2
       |  |     └──... (more metrics)
       |  └── ... (more datasources)
       └── ... (more clusters)


导出数据源到 YAML
-----------------------------
您可以通过运行将当前的数据源打印到标准输出: ::

    superset export_datasources


将您的数据源保存到一个文件运行: ::

    superset export_datasources -f <filename>


默认情况下，缺省值 (null) 将被省略。使用 ``-d`` 标志来包含它们。
如果想要包含回引用(例如，一个列包含它所属的表 id )，请使用 ``-b`` 标志。

或者，您可以使用 UI 导出数据源:

1. 打开 **Sources** -> **Databases** 导出与单个或多个数据库关联的所有表。 
   (**Tables** 针对一个或多个表，**Druid Clusters** 针对集群，**Druid Datasources** 针对数据源)
#. 选择要导出的项目
#. 单击 **Actions** -> **Export to YAML**
#. 如果你想导入一个你通过 UI 导出的项目，你需要将它嵌套在它的父元素中，
   例如，一个 `database` 需要嵌套在 `databases` 下，一个 `table` 需要嵌套在一个 `database` 元素中。

导出完整的受支持的 YAML schema
--------------------------------------------
为了获得所有字段的详尽列表，您可以使用 YAML import 导入 运行: ::

    superset export_datasource_schema

同样，您可以使用 ``-b`` 标志来包含反向引用。

从 YAML 导入数据源
-------------------------------
为了从YAML文件导入数据源, 运行: ::

    superset import_datasources -p <path or filename>

如果您提供一个路径，所有文件以 ``*.yaml`` or ``*.yml`` 结尾将被解析。你可以应用额外的标志，例如: ::

    superset import_datasources -p <path> -r

将递归搜索提供的路径。

sync 标志 ``-s`` 接受参数，以便将提供的元素与文件同步。小心，这会删除元数据库的内容。例子:

   superset import_datasources -p <path / filename> -s columns,metrics


这将同步 Superset 元数据库中 ``<path / filename>`` 中找到的所有数据源的所有 ``metrics`` 和 ``columns``。
这意味着 YAML 中未指定的列和指标将被删除。如果您要将 ``tables`` 添加到 ``columns,metrics``，
那么这些指标也将被同步。

如果您不提供同步标志( ``-s`` )，导入将只添加和更新(覆盖)字段。例如，
您可以从示例数据集中将以下 YAML 保存到文件中，然后运行 ``import_datasources`` 命令，
从而将 ``verbose_name`` 添加到表 ``random_time_series`` 中的 ``ds`` 列。::

    databases:
    - database_name: main
      tables:
      - table_name: random_time_series
        columns:
        - column_name: ds
          verbose_name: datetime

