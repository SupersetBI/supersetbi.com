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

常见问题解答
===

我可以一次 query/join 多个表吗?
---------------------------------------------

不直接提供。 Superset SQLAlchemy 数据源只能是单个表或视图。

在处理表时，解决方案是物化一个包含分析所需的所有字段的表，很可能是通过某个预定的批处理过程实现的。

视图是一个简单的逻辑层，它将任意 SQL 查询抽象为一个虚拟表。
这允许您连接和联合多个表，并使用任意 SQL 表达式应用一些转换。
这里的限制是您的数据库性能，因为 Superset 将有效地在您的查询(视图)之上运行查询。
一个好的做法可能是限制自己只将主大表连接到一个或多个小表，
并避免在可能的情况下使用 ``GROUP BY``，因为 Superset 将执行自己的 ``GROUP BY`` ，
并且执行两次可能会降低性能。

无论您使用的是表还是视图，重要的因素是您的数据库是否足够快，
能够以交互方式为它提供服务，从而在 Superset 中提供良好的用户体验。


我的数据源能有多大?
------------------------------

它可以是巨大的!如前所述，主要的标准是您的数据库是否能够执行查询并在用户可接受的时间范围内返回结果。
许多分布式数据库可以执行以交互方式扫描 tb 级数据的查询。


如何创建自己的可视化?
-------------------------------------

我们计划让向框架添加新的可视化变得更容易，同时，我们标记了一些 PR 作为 ``example``，
给人们提供如何贡献新的可视化的例子。

https://github.com/airbnb/superset/issues?q=label%3Aexample+is%3Aclosed

我可以上传和可视化 csv 数据吗？
------------------------------------

可以，使用 ``Sources`` 菜单项下的 ``Upload a CSV`` 按钮。
这将打开一个允许您指定所需信息的表单。从 CSV 创建表之后，
就可以像其他任何表一样加载 ``Sources -> Tables`` 页面。


为什么我的查询超时了?
------------------------------

导致查询超时的原因有很多。

- 对于运行来自 Sql Lab 的长查询，默认情况下 Superset 允许它在被 celery 杀死之前运行长达6个小时。如果希望增加运行查询的时间，可以在配置中指定超时。例如:

  ``SQLLAB_ASYNC_TIME_LIMIT_SEC = 60 * 60 * 6``

- Superset 运行在 gunicorn web 服务器上，可能 web 请求会超时。如果希望增加默认值(50)，可以在启动 web 服务器时使用 ``-t`` 标志指定超时时间，该标志以秒为单位表示。
  
  ``superset runserver -t 300``

- 如果您在加载 dashboard 或 explore slice 时看到超时(504 Gateway Time-out)，那么您可能在网关或代理服务器(如 Nginx )之后。如果没有收到来自 Superset 服务器的及时响应(它正在处理长时间的查询)，这些 web 服务器将直接向客户机发送504状态码。Superset 有一个客户端超时限制来解决这个问题。如果在 client-side 超时(默认60秒)内没有返回查询，Superset 将显示警告消息，以避免网关超时消息。如果你有一个较长的网关超时限制，你可以改变 ``superset_config.py`` 中的超时设置:
  
  ``SUPERSET_WEBSERVER_TIMEOUT = 60``


为什么地图在 mapbox 可视化中不可见?
-------------------------------------------------------


你需要在 mapbox.com 注册，得到一个 API key 并且在 ``superset_config.py`` 中配置 ``MAPBOX_API_KEY``。


如何向看板添加动态过滤器?
------------------------------------------

非常容易：使用 ``Filter Box`` 组件，构建一个 slice，并且添加它到你的看板。

``Filter Box`` 组件允许您定义一个查询来填充可用于过滤的下拉列表。
为了构建不同值的列表，我们运行一个查询，并根据您提供的指标对结果进行降序排序。

这个组件还有一个 ``Date Filter`` 复选框，它可以为您的看板启用时间筛选功能。
选中复选框并刷新之后，您将看到一个 ``from`` 和一个 ``to`` 下拉列表。

默认情况下，该过滤将应用于在共享过滤器所基于的列名称的数据源之上构建的所有 slice。
还要求在表编辑器的 column 选项卡中将该列检查为 "filterable"。

但是，如果您不希望在看板上对某些小部件进行筛选，该怎么办呢?
您可以通过编辑看板来做到这一点，在表单中，编辑 ``JSON Metadata`` 字段，
更具体地说是 ``filter_immune_slices`` 键，它接收一个 sliceIds 数组，
这个数组不应该受到任何看板级过滤的影响。


.. code-block:: json

    {
        "filter_immune_slices": [324, 65, 92],
        "expanded_slices": {},
        "filter_immune_slice_fields": {
            "177": ["country_name", "__time_range"],
            "32": ["__time_range"]
        },
        "timed_refresh_immune_slices": [324]
    }

在上面的 json blob 中，slice 324、65 和 92 不会受到任何仪表板级过滤的影响。

现在注意 ``filter_immune_slice_fields`` 键。这种方法允许您更具体地定义一个特定的 slice_id，
应该忽略哪些筛选器字段。

注意使用了关键字 ``__time_range`` ，该关键字用于处理上面提到的时间边界过滤。

但是当处理来自不同表或数据库的 slice 时，过滤会发生什么呢?如果列名是共享的，那么将应用筛选器，就这么简单。


如何限制看板上的定时刷新？
----------------------------------------------
默认情况下，仪表板定时刷新功能使您可以根据设置的时间表自动重新查询仪表板上的每个切片。
但是，有时您不希望刷新所有切片，尤其是当某些数据移动缓慢或运行繁重的查询时。
要从定时刷新过程中排除特定的 slice，
请将 ``timed_refresh_immune_slices`` 键添加到仪表板 ``JSON Metadata`` 字段：

.. code-block:: json

    {
       "filter_immune_slices": [],
        "expanded_slices": {},
        "filter_immune_slice_fields": {},
        "timed_refresh_immune_slices": [324]
    }

在上面的示例中，如果为仪表板设置了定时刷新，则除 324 以外的每个片段都将按计划自动重新查询。

Slice 刷新也将在指定时间段内错开。您可以通过将 ``stagger_refresh`` 设置为 ``false`` 来关闭此交错，
并通过在 ``JSON Metadata`` 字段中将 ``stagger_time`` 设置为以毫秒为单位的值来修改交错周期：

.. code-block:: json

    {
        "stagger_refresh": false,
        "stagger_time": 2500
    }

如果启用定期刷新，则整个看板将立即刷新。2.5秒的交错时间将被忽略。

为什么启动时 'flask fab' 或 superset 冻结/挂起/不响应（我的主目录已安装NFS）？
-------------------------------------------------------------------------------------------------------------

默认情况下，superset 在 ``~/.superset/superset.db`` 中创建并使用一个 sqlite 数据库。
已知由于在 NFS 上的文件锁定实现损坏，`don't work well if used on NFS`__ 。

__ https://www.sqlite.org/lockingv3.html

可以使用 ``SUPERSET_HOME`` 环境变量覆盖此路径。

另一个解决方法是，通过在 superset_config.py 中添加 ``SQLALCHEMY_DATABASE_URI = 'sqlite:////new/location/superset.db'``（如果需要，创建文件），然后将 superset_config.py 所在的目录添加到 PYTHONPATH 环境变量中（例如，``export PYTHONPATH=/opt/logs/sandbox/airbnb/`` ）。

如果 table schema 改变了怎么办?
---------------------------------

Table schemas 在发展，Superset 需要反映这一点。在看板的生命周期中，添加新维度或指标是非常常见的。
要获得 Superset 来发现新的列，您所要做的就是转到 ``Menu -> Sources -> Tables`` ，
单击 schema has changed 的表旁边的 ``edit`` 图标，然后从 ``Detail`` 选项卡中单击 ``Save``。
在背后，新列将被合并。之后，您可能需要稍后重新编辑表格以配置 ``Column`` 选项卡，选中相应的框并再次保存。 

如何开发新的可视化类型？
------------------------------------------------------
这是一个 Github PR 的示例，带有注释，描述了代码的不同部分的功能:
https://github.com/airbnb/superset/pull/3013

我可以使用什么数据库引擎作为 Superset 的后端?
---------------------------------------------------------

澄清一下，*database backend* 是一个 OLTP 数据库，Superset 使用它来存储内部信息，比如用户列表、slice 和看板定义。

Superset 的后端使用 Mysql、Postgresql 和 Sqlite 进行测试。建议在其中一个数据库服务器上安装 Superset 以用于生产。

使用列存储、非 OLTP 数据库(如 Vertica、Redshift 或 Presto )作为数据库后端是行不通的，因为这些数据库不适合这种类型的工作负载。安装在 Oracle、Microsoft SQL Server 或其他 OLTP 数据库上可以工作，但没有经过测试。

请注意，几乎所有具有 SqlAlchemy 集成的数据库都可以作为 Superset 的数据源完美地工作，只是不能作为 OLTP 后端。

如何配置 OAuth 身份验证和授权？
-----------------------------------------------------------

您可以看一下这个 Flask-AppBuilder `configuration example
<https://github.com/dpgaspar/Flask-AppBuilder/blob/master/examples/oauth/config.py>`_.

如何在看板上设置默认过滤器？
-----------------------------------------------

简单。只需应用过滤器并在过滤器处于活动状态时保存看板。

如何让 Superset 刷新表的 schema？
--------------------------------------------------------

在向表中添加列时，可以使用 ``Source -> Tables`` 页面中的 "Refresh Metadata" 操作让 Superset 检测并合并新列。
只需选中表格旁边的框您想要刷新的 schema，然后单击 ``Actions -> Refresh Metadata``。

有没有办法强制使用特定的颜色?
------------------------------------------------

通过使用 ``label_colors`` key 在 ``JSON Metadata`` 属性中提供标签到颜色的映射，可以在每个仪表板上实现。

.. code-block:: json

    {
        "label_colors": {
            "Girls": "#FF69B4",
            "Boys": "#ADD8E6"
        }
    }

Superset 是否与 [insert database engine here] 一起工作?
------------------------------------------------------

随着时间的推移，社区已经在文档的 :ref:`ref_database_deps` 中策划了一个可以很好地使用 Superset 的数据库列表。
没有在此页列出的数据库引擎也可以工作。我们依靠社区为这个知识库做出贡献。

.. _SQLAlchemy dialect: https://docs.sqlalchemy.org/en/latest/dialects/
.. _DBAPI driver: https://www.python.org/dev/peps/pep-0249/

For a database engine to be supported in Superset through the
SQLAlchemy connector, it requires having a Python compliant
`SQLAlchemy dialect`_ as well as a
`DBAPI driver`_ defined.
Database that have limited SQL support may
work as well. For instance it's possible to connect
to Druid through the SQLAlchemy connector even though Druid does not support
joins and subqueries. Another key element for a database to be supported is through
the Superset `Database Engine Specification
<https://github.com/apache/incubator-superset/blob/master/superset/db_engine_specs.py>`_
interface. This interface allows for defining database-specific configurations
and logic
that go beyond the SQLAlchemy and DBAPI scope. This includes features like:


* date-related SQL function that allow Superset to fetch different
  time granularities when running time-series queries
* whether the engine supports subqueries. If false, Superset may run 2-phase
  queries to compensate for the limitation
* methods around processing logs and inferring the percentage of completion
  of a query
* technicalities as to how to handle cursors and connections if the driver
  is not standard DBAPI
* more, read the code for more details

Beyond the SQLAlchemy connector, it's also possible, though much more
involved, to extend Superset and write
your own connector. The only example of this at the moment is the Druid
connector, which is getting superseded by Druid's growing SQL support and
the recent availability of a DBAPI and SQLAlchemy driver. If the database
you are considering integrating has any kind of of SQL support, it's probably
preferable to go the SQLAlchemy route. Note that for a native connector to
be possible the database needs to have support for running OLAP-type queries
and should be able to things that are typical in basic SQL:

- aggregate data
- apply filters (==, !=, >, <, >=, <=, IN, ...)
- apply HAVING-type filters
- be schema-aware, expose columns and types

