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

Note the use of the ``__time_range`` keyword, which is reserved for dealing
with the time boundary filtering mentioned above.

But what happens with filtering when dealing with slices coming from
different tables or databases? If the column name is shared, the filter will
be applied, it's as simple as that.


How to limit the timed refresh on a dashboard?
----------------------------------------------
By default, the dashboard timed refresh feature allows you to automatically re-query every slice
on a dashboard according to a set schedule. Sometimes, however, you won't want all of the slices
to be refreshed - especially if some data is slow moving, or run heavy queries. To exclude specific
slices from the timed refresh process, add the ``timed_refresh_immune_slices`` key to the dashboard
``JSON Metadata`` field:

.. code-block:: json

    {
       "filter_immune_slices": [],
        "expanded_slices": {},
        "filter_immune_slice_fields": {},
        "timed_refresh_immune_slices": [324]
    }

In the example above, if a timed refresh is set for the dashboard, then every slice except 324 will
be automatically re-queried on schedule.

Slice refresh will also be staggered over the specified period. You can turn off this staggering
by setting the ``stagger_refresh`` to ``false`` and modify the stagger period by setting
``stagger_time`` to a value in milliseconds in the ``JSON Metadata`` field:

.. code-block:: json

    {
        "stagger_refresh": false,
        "stagger_time": 2500
    }

Here, the entire dashboard will refresh at once if periodic refresh is on. The stagger time of
2.5 seconds is ignored.

Why does 'flask fab' or superset freezed/hung/not responding when started (my home directory is NFS mounted)?
-------------------------------------------------------------------------------------------------------------
By default, superset creates and uses an sqlite database at ``~/.superset/superset.db``. Sqlite is known to `don't work well if used on NFS`__ due to broken file locking implementation on NFS.

__ https://www.sqlite.org/lockingv3.html

You can override this path using the ``SUPERSET_HOME`` environment variable.

Another work around is to change where superset stores the sqlite database by adding ``SQLALCHEMY_DATABASE_URI = 'sqlite:////new/location/superset.db'`` in superset_config.py (create the file if needed), then adding the directory where superset_config.py lives to PYTHONPATH environment variable (e.g. ``export PYTHONPATH=/opt/logs/sandbox/airbnb/``).

What if the table schema changed?
---------------------------------

Table schemas evolve, and Superset needs to reflect that. It's pretty common
in the life cycle of a dashboard to want to add a new dimension or metric.
To get Superset to discover your new columns, all you have to do is to
go to ``Menu -> Sources -> Tables``, click the ``edit`` icon next to the
table who's schema has changed, and hit ``Save`` from the ``Detail`` tab.
Behind the scene, the new columns will get merged it. Following this,
you may want to
re-edit the table afterwards to configure the ``Column`` tab, check the
appropriate boxes and save again.

How do I go about developing a new visualization type?
------------------------------------------------------
Here's an example as a Github PR with comments that describe what the
different sections of the code do:
https://github.com/airbnb/superset/pull/3013

What database engine can I use as a backend for Superset?
---------------------------------------------------------

To clarify, the *database backend* is an OLTP database used by Superset to store its internal
information like your list of users, slices and dashboard definitions.

Superset is tested using Mysql, Postgresql and Sqlite for its backend. It's recommended you
install Superset on one of these database server for production.

Using a column-store, non-OLTP databases like Vertica, Redshift or Presto as a database backend simply won't work as these databases are not designed for this type of workload. Installation on Oracle, Microsoft SQL Server, or other OLTP databases may work but isn't tested.

Please note that pretty much any databases that have a SqlAlchemy integration should work perfectly fine as a datasource for Superset, just not as the OLTP backend.

How can i configure OAuth authentication and authorization?
-----------------------------------------------------------

You can take a look at this Flask-AppBuilder `configuration example
<https://github.com/dpgaspar/Flask-AppBuilder/blob/master/examples/oauth/config.py>`_.

How can I set a default filter on my dashboard?
-----------------------------------------------

Easy. Simply apply the filter and save the dashboard while the filter
is active.

How do I get Superset to refresh the schema of my table?
--------------------------------------------------------

When adding columns to a table, you can have Superset detect and merge the
new columns in by using the "Refresh Metadata" action in the
``Source -> Tables`` page. Simply check the box next to the tables
you want the schema refreshed, and click ``Actions -> Refresh Metadata``.

Is there a way to force the use specific colors?
------------------------------------------------

It is possible on a per-dashboard basis by providing a mapping of
labels to colors in the ``JSON Metadata`` attribute using the
``label_colors`` key.

.. code-block:: json

    {
        "label_colors": {
            "Girls": "#FF69B4",
            "Boys": "#ADD8E6"
        }
    }

Does Superset work with [insert database engine here]?
------------------------------------------------------

The community over time has curated a list of databases that work well with
Superset in the :ref:`ref_database_deps` section of the docs. Database
engines not listed in this page may work too. We rely on the
community to contribute to this knowledge base.

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

