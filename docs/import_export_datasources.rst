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

Exporting the complete supported YAML schema
--------------------------------------------
In order to obtain an exhaustive list of all fields you can import using the YAML import run: ::

    superset export_datasource_schema

Again, you can use the ``-b`` flag to include back references.


Importing Datasources from YAML
-------------------------------
In order to import datasources from a YAML file(s), run: ::

    superset import_datasources -p <path or filename>

If you supply a path all files ending with ``*.yaml`` or ``*.yml`` will be parsed.
You can apply additional flags e.g.: ::

    superset import_datasources -p <path> -r

Will search the supplied path recursively.

The sync flag ``-s`` takes parameters in order to sync the supplied elements with
your file. Be careful this can delete the contents of your meta database. Example:

   superset import_datasources -p <path / filename> -s columns,metrics

This will sync all ``metrics`` and ``columns`` for all datasources found in the
``<path / filename>`` in the Superset meta database. This means columns and metrics
not specified in YAML will be deleted. If you would add ``tables`` to ``columns,metrics``
those would be synchronised as well.


If you don't supply the sync flag (``-s``) importing will only add and update (override) fields.
E.g. you can add a ``verbose_name`` to the column ``ds`` in the table ``random_time_series`` from the example datasets
by saving the following YAML to file and then running the ``import_datasources`` command. ::

    databases:
    - database_name: main
      tables:
      - table_name: random_time_series
        columns:
        - column_name: ds
          verbose_name: datetime

