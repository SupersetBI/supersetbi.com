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


How do I create my own visualization?
-------------------------------------

We are planning on making it easier to add new visualizations to the
framework, in the meantime, we've tagged a few pull requests as
``example`` to give people examples of how to contribute new
visualizations.

https://github.com/airbnb/superset/issues?q=label%3Aexample+is%3Aclosed


Can I upload and visualize csv data?
------------------------------------

Yes, using the ``Upload a CSV`` button under the ``Sources`` menu item.
This brings up a form that allows you specify required information.
After creating the table from CSV, it can then be loaded like any
other on the ``Sources -> Tables`` page.


Why are my queries timing out?
------------------------------

There are many reasons may cause long query timing out.


- For running long query from Sql Lab, by default Superset allows it run as long as 6 hours before it being killed by celery. If you want to increase the time for running query, you can specify the timeout in configuration. For example:

  ``SQLLAB_ASYNC_TIME_LIMIT_SEC = 60 * 60 * 6``


- Superset is running on gunicorn web server, which may time out web requests. If you want to increase the default (50), you can specify the timeout when starting the web server with the ``-t`` flag, which is expressed in seconds.

  ``superset runserver -t 300``

- If you are seeing timeouts (504 Gateway Time-out) when loading dashboard or explore slice, you are probably behind gateway or proxy server (such as Nginx). If it did not receive a timely response from Superset server (which is processing long queries), these web servers will send 504 status code to clients directly. Superset has a client-side timeout limit to address this issue. If query didn't come back within clint-side timeout (60 seconds by default), Superset will display warning message to avoid gateway timeout message. If you have a longer gateway timeout limit, you can change the timeout settings in ``superset_config.py``:

  ``SUPERSET_WEBSERVER_TIMEOUT = 60``


Why is the map not visible in the mapbox visualization?
-------------------------------------------------------

You need to register to mapbox.com, get an API key and configure it as
``MAPBOX_API_KEY`` in ``superset_config.py``.


How to add dynamic filters to a dashboard?
------------------------------------------

It's easy: use the ``Filter Box`` widget, build a slice, and add it to your
dashboard.

The ``Filter Box`` widget allows you to define a query to populate dropdowns
that can be used for filtering. To build the list of distinct values, we
run a query, and sort the result by the metric you provide, sorting
descending.

The widget also has a checkbox ``Date Filter``, which enables time filtering
capabilities to your dashboard. After checking the box and refreshing, you'll
see a ``from`` and a ``to`` dropdown show up.

By default, the filtering will be applied to all the slices that are built
on top of a datasource that shares the column name that the filter is based
on. It's also a requirement for that column to be checked as "filterable"
in the column tab of the table editor.

But what about if you don't want certain widgets to get filtered on your
dashboard? You can do that by editing your dashboard, and in the form,
edit the ``JSON Metadata`` field, more specifically the
``filter_immune_slices`` key, that receives an array of sliceIds that should
never be affected by any dashboard level filtering.


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

In the json blob above, slices 324, 65 and 92 won't be affected by any
dashboard level filtering.

Now note the ``filter_immune_slice_fields`` key. This one allows you to
be more specific and define for a specific slice_id, which filter fields
should be disregarded.

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

