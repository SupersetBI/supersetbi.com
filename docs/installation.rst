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

安装与配置
============================

入门指南
---------------

Superset 已经弃用支持 Python ``2.*`` 并且只支持 ``~=3.6`` ，以充分利用Python的新特性，
减少支持以前版本的负担。我们在``3.6``上运行我们的测试套件，同时也完全支持 ``3.7``。

云原生
-------------

Superset 被设计成高可用。它是 "cloud-native" 因为它已经被设计成大规模横向可扩展，
分布式环境，并且在容器内工作良好。
尽管您可以在适中的设置下或仅在笔记本电脑上就能轻松测试驱动Superset，
但实际上扩展这个平台几乎没有任何限制。
Superset 也是云原生的，因为它很灵活，允许你选择你的 Web 服务器 (Gunicorn, Nginx, Apache)，
你的元数据数据库引擎 (MySQL, Postgres, MariaDB, ...)，
你的消息队列 (Redis, RabbitMQ, SQS, ...)，
你的结果后端 (S3, Redis, Memcached, ...)，
你的缓存层 (Memcached, Redis, ...)，可以很好地与 NewRelic、StatsD 和 DataDog 等服务协同工作，
并且能够在最流行的数据库技术上运行分析工作负载。

Superset 在有数百个并发用户的大型环境中进行了测试。
Airbnb 的生产环境在 Kubernetes 内部运行，
每天为600多名活跃用户提供超过10万张图表的浏览服务。

Superset web 服务器和 Superset Celery workers（可选）是无状态的，
因此您可以根据需要在多个服务器上运行来进行扩展。

从 Docker 开始
-----------------

.. note ::
    与 docker 相关的文件和文档由项目中的核心提交者积极维护和管理。
    欢迎围绕 Docker 的帮助和贡献！

如果你了解docker，那么你是幸运的，我们为你提供了初始化开发环境的捷径: ::

    git clone https://github.com/apache/incubator-superset/
    cd incubator-superset
    # 您可以在每次需要启动 Superset 时运行此命令:
    docker-compose up

几分钟后，superset 初始化完成，您可以打开浏览器并查看 `http://localhost:8088` 来开始你的旅程。

然后，容器服务器将在修改 Superset python 和 javascript 源代码时重新加载。
不过，不要忘记重新加载页面以考虑新的前端。

参阅 `CONTRIBUTING.md#building <https://github.com/apache/incubator-superset/blob/master/CONTRIBUTING.md#building>`_,提供另一种服务前端的方式。

目前不建议在生产环境中运行 docker-compose。

如果你试图在Mac上构建，它有137个退出，你需要增加你的docker资源。
OSX指令: https://docs.docker.com/docker-for-mac/#advanced (Search for memory)

或者如果你很好奇，想要自底向上安装superset，那就继续吧。

参阅 `docker/README.md <https://github.com/apache/incubator-superset/blob/master/docker/README.md>`_

系统依赖
---------------

Superset将数据库连接信息存储在其元数据数据库中。
为此，我们使用 ``cryptography`` Python 库来加密连接密码。
不幸的是，这个库具有操作系统级依赖项。

您可能想尝试下一步
(“Superset安装和初始化”)，如果遇到错误，请回到这一步。

下面是如何安装它们:

对于 **Debian** 和 **Ubuntu**，以下命令将确保安装所需的依赖项: ::

    sudo apt-get install build-essential libssl-dev libffi-dev python-dev python-pip libsasl2-dev libldap2-dev

**Ubuntu 18.04** 如果你已经安装了 python3.6和 python2.7，这是 **Ubuntu 18.04 LTS** 的默认设置, 你也可以运行这个命令: ::

    sudo apt-get install build-essential libssl-dev libffi-dev python3.6-dev python-pip libsasl2-dev libldap2-dev

否则，``cryptography`` 构建将失败。

对于 **Fedora** 和 **RHEL-derivatives**，以下命令将确保安装所需的依赖项: ::

    sudo yum upgrade python-setuptools
    sudo yum install gcc gcc-c++ libffi-devel python-devel python-pip python-wheel openssl-devel cyrus-sasl-devel openldap-devel

**Mac OS X** 如果可能的话，您应该升级到最新版本的OS X，因为该版本的问题更有可能得到解决。
您 **可能需要** 最新版本的 XCode 来支持您安装的 OS X 版本。
您还应该安装 XCode 命令行工具: ::

    xcode-select --install

不推荐使用 System python。Homebrew 的 python 也附带了 pip: ::

    brew install pkg-config libffi openssl python
    env LDFLAGS="-L$(brew --prefix openssl)/lib" CFLAGS="-I$(brew --prefix openssl)/include" pip install cryptography==2.4.2

**Windows** 目前还不支持，但是如果您想尝试一下，请下载 `get-pip.py <https://bootstrap.pypa.io/get-pip.py>`_。
运行 ``python get-pip.py``，可能需要管理员访问。然后运行以下命令: ::

    C:\> pip install cryptography

    # 你可能还需要创建 C:\Temp
    C:\> md C:\Temp

Python virtualenv
-----------------
建议在 virtualenv 中安装 Superset。Python 3 已经发布了 virtualenv。
但是，如果由于某些原因它没有安装在您的环境中，您可以通过操作系统的包安装它，否则您可以从 pip 安装它: ::

    pip install virtualenv

您可以创建并激活一个 virtualenv: ::

    # virtualenv 是在 Python 3.6+ 中作为 venv 发布的，代替 pyvenv。
    # 参看 https://docs.python.org/3.6/library/venv.html
    python3 -m venv venv
    . venv/bin/activate

在Windows上，激活它的语法略有不同: ::

    venv\Scripts\activate

一旦你激活了你的 virtualenv，你所做的一切都被限制在 virtualenv 里面了。要退出 virtualenv，只需键入 ``deactivate``。

Python 设置工具和 pip
----------------------------
获得最新的 ``pip`` 和 ``setuptools`` 库，把所有的机会都放在您这一边。::

    pip install --upgrade setuptools pip

Superset 安装和初始化
----------------------------------------
遵循以下几个简单的步骤来安装 Superset。::

    # 安装 superset
    pip install apache-superset

    # 初始化数据库
    superset db upgrade

    # 创建一个管理员用户 (在设置密码之前，系统将提示您设置用户名、姓和名)
    $ export FLASK_APP=superset
    superset fab create-admin

    # 加载一些数据来处理
    superset load_examples

    # 创建默认角色和权限
    superset init

    # 要在端口 8088 上启动开发 web 服务器，使用 -p 绑定到另一个端口
    superset run -p 8088 --with-threads --reload --debugger

安装之后，您应该能够将浏览器指向正确的 hostname:port `http://localhost:8088 <http://localhost:8088>`_,
使用创建管理员帐户时输入的凭据登录，并导航到 `Menu -> Admin -> Refresh Metadata` 。
此操作应该为 Superset 带来所有需要注意的数据源，并且它们应该出现在 `Menu -> Datasources`，
从那里你可以开始玩你的数据！

一个合适的 WSGI HTTP 服务器
-------------------------

虽然可以将 Superset 设置为在 Nginx 或 Apache 上运行，但很多都使用 Gunicorn，
最好是在 **async mode** 下，这甚至可以实现令人印象深刻的并发性，而且相当容易安装和配置。
请参考您首选技术的文档，以在您的环境中正常工作的方式设置此 Flask WSGI 应用程序。
以下是一个在生产中运行良好的 **async** 设置: ::

 　gunicorn \
        -w 10 \
        -k gevent \
        --timeout 120 \
        -b  0.0.0.0:6666 \
        --limit-request-line 0 \
        --limit-request-field_size 0 \
        --statsd-host localhost:8125 \
        "superset.app:create_app()"

有关更多信息，请参阅 `Gunicorn documentation <https://docs.gunicorn.org/en/stable/design.html>`_。

请注意，开发环境 web 服务器（`superset run` or `flask run`）不准备用在生产环境。

如果不使用 gunicorn，你或许想要禁用 flask-compress 的使用，可以通过在你的 `superset_config.py`
中设置 `ENABLE_FLASK_COMPRESS = False`。

Flask-AppBuilder 权限
----------------------------

默认情况下，每次初始化 Flask-AppBuilder (FAB) 应用程序时，
权限和视图都会自动添加到后端并与 ‘Admin’ 角色关联。但是，问题是，当您运行多个并发 worker 时，
这会在定义权限和视图时创建大量争用和竞争条件。

为了缓解这个问题，可以通过设置 `FAB_UPDATE_PERMS = False`（默认为True）来禁用权限的自动更新。

在生产环境中，初始化可以采用以下形式：

  superset init
  gunicorn -w 10 ... superset:app

负载均衡后的配置
------------------------------------

如果您在 load balancer 或 reverse proxy（如 AWS 上的 NGINX 或 ELB）后面运行 Superset，
则可能需要使用 healthcheck 端点，以便您的 load balancer 知道您的 Superset 实例是否正在运行。
这是在``/health``提供的，如果 webserver 正在运行，它将返回一个包含 "OK" 的200响应。

如果 load balancer 正在插入 X-Forwarded-For/X-Forwarded-Proto 头，
则应在 superset 配置文件中设置 `ENABLE_PROXY_FIX = True` 以提取和使用头。

如果 reverse proxy 用于提供 ssl 加密，则可能需要 `X-Forwarded-Proto` 的显式定义。
对于 Apache webserver，可以如下设置: ::

    RequestHeader set X-Forwarded-Proto "https"

配置
-------------

要配置应用程序，您需要创建一个文件（module）``superset_config.py``，并确保它位于 PYTHONPATH 中。
以下是可以在该配置模块中 copy / paste 的一些参数: ::

    #---------------------------------------------------------
    # Superset specific config
    #---------------------------------------------------------
    ROW_LIMIT = 5000

    SUPERSET_WEBSERVER_PORT = 8088
    #---------------------------------------------------------

    #---------------------------------------------------------
    # Flask App Builder configuration
    #---------------------------------------------------------
    # Your App secret key
    SECRET_KEY = '\2\1thisismyscretkey\1\2\e\y\y\h'

    # The SQLAlchemy connection string to your database backend
    # This connection defines the path to the database that stores your
    # superset metadata (slices, connections, tables, dashboards, ...).
    # Note that the connection information to connect to the datasources
    # you want to explore are managed directly in the web UI
    SQLALCHEMY_DATABASE_URI = 'sqlite:////path/to/superset.db'

    # Flask-WTF flag for CSRF
    WTF_CSRF_ENABLED = True
    # Add endpoints that need to be exempt from CSRF protection
    WTF_CSRF_EXEMPT_LIST = []
    # A CSRF token that expires in 1 year
    WTF_CSRF_TIME_LIMIT = 60 * 60 * 24 * 365

    # Set this API key to enable Mapbox visualizations
    MAPBOX_API_KEY = ''

https://github.com/apache/incubator-superset/blob/master/superset/config.py 
中定义的所有参数和默认值都可以在本地``superset_config.py``中更改。
管理员将希望通读该文件，以了解可以在本地配置的内容以及现有的默认值。

由于 ``superset_config.py`` 充当一个 Flask 配置模块，它可以用来改变 Flask 本身的设置，
也可以用来改变 Flask 的扩展，比如``flask-wtf``, ``flask-cache``,
``flask-migrate`` 和 ``flask-appbuilder``。Flask App Builder, 
Superset 使用的 web 框架提供了很多配置设置。有关如何配置它的更多信息，
请参阅`Flask App Builder Documentation
<https://flask-appbuilder.readthedocs.org/en/latest/config.html>`_。

一定要换的配置:

* *SQLALCHEMY_DATABASE_URI*, 默认情况下，它存储在 *~/.superset/superset.db*
* *SECRET_KEY*, 一个长而随机的字符串

如果您需要免除CSRF的端点，e.g. 您正在运行一个自定义的 auth postback 端点，
您可以将它们添加到 *WTF_CSRF_EXEMPT_LIST*

     WTF_CSRF_EXEMPT_LIST = ['']


.. _ref_database_deps:

数据库依赖
---------------------

除了 Sqlite (Python标准库的一部分)之外，Superset 没有绑定到数据库的连接。
您将需要安装作为元数据数据库使用的数据库所需的包，以及通过 Superset 连接到
要访问的数据库所需的包。

以下是一些推荐的软件包。

+------------------+---------------------------------------+-------------------------------------------------+
| database         | pypi package                          | SQLAlchemy URI prefix                           |
+==================+=======================================+=================================================+
| Amazon Athena    | ``pip install "PyAthenaJDBC>1.0.9"``  | ``awsathena+jdbc://``                           |
+------------------+---------------------------------------+-------------------------------------------------+
| Amazon Athena    | ``pip install "PyAthena>1.2.0"``      | ``awsathena+rest://``                           |
+------------------+---------------------------------------+-------------------------------------------------+
| Amazon Redshift  | ``pip install sqlalchemy-redshift``   | ``redshift+psycopg2://``                        |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Drill     | ``pip install sqlalchemy-drill``      | For the REST API:``                             |
|                  |                                       | ``drill+sadrill://``                            |
|                  |                                       | For JDBC                                        |
|                  |                                       | ``drill+jdbc://``                               |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Druid     | ``pip install pydruid``               | ``druid://``                                    |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Hive      | ``pip install pyhive``                | ``hive://``                                     |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Impala    | ``pip install impyla``                | ``impala://``                                   |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Kylin     | ``pip install kylinpy``               | ``kylin://``                                    |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Pinot     | ``pip install pinotdb``               | ``pinot+http://CONTROLLER:5436/``               |
|                  |                                       | ``query?server=http://CONTROLLER:5983/``        |
+------------------+---------------------------------------+-------------------------------------------------+
| Apache Spark SQL | ``pip install pyhive``                | ``jdbc+hive://``                                |
+------------------+---------------------------------------+-------------------------------------------------+
| BigQuery         | ``pip install pybigquery``            | ``bigquery://``                                 |
+------------------+---------------------------------------+-------------------------------------------------+
| ClickHouse       | ``pip install sqlalchemy-clickhouse`` |                                                 |
+------------------+---------------------------------------+-------------------------------------------------+
| Elasticsearch    | ``pip install elasticsearch-dbapi``   | ``elasticsearch+http://``                       |
+------------------+---------------------------------------+-------------------------------------------------+
| Exasol           | ``pip install sqlalchemy-exasol``     | ``exa+pyodbc://``                               |
+------------------+---------------------------------------+-------------------------------------------------+
| Google Sheets    | ``pip install gsheetsdb``             | ``gsheets://``                                  |
+------------------+---------------------------------------+-------------------------------------------------+
| IBM Db2          | ``pip install ibm_db_sa``             | ``db2+ibm_db://``                               |
+------------------+---------------------------------------+-------------------------------------------------+
| MySQL            | ``pip install mysqlclient``           | ``mysql://``                                    |
+------------------+---------------------------------------+-------------------------------------------------+
| Oracle           | ``pip install cx_Oracle``             | ``oracle://``                                   |
+------------------+---------------------------------------+-------------------------------------------------+
| PostgreSQL       | ``pip install psycopg2``              | ``postgresql+psycopg2://``                      |
+------------------+---------------------------------------+-------------------------------------------------+
| Presto           | ``pip install pyhive``                | ``presto://``                                   |
+------------------+---------------------------------------+-------------------------------------------------+
| Snowflake        | ``pip install snowflake-sqlalchemy``  | ``snowflake://``                                |
+------------------+---------------------------------------+-------------------------------------------------+
| SQLite           |                                       | ``sqlite://``                                   |
+------------------+---------------------------------------+-------------------------------------------------+
| SQL Server       | ``pip install pymssql``               | ``mssql://``                                    |
+------------------+---------------------------------------+-------------------------------------------------+
| Teradata         | ``pip install sqlalchemy-teradata``   | ``teradata://``                                 |
+------------------+---------------------------------------+-------------------------------------------------+
| Vertica          | ``pip install                         |  ``vertica+vertica_python://``                  |
|                  | sqlalchemy-vertica-python``           |                                                 |
+------------------+---------------------------------------+-------------------------------------------------+
| Hana             | ``pip install hdbcli sqlalchemy-hana``|  ``hana://``                                    |
|                  | or ``pip install superset[hana]``     |                                                 |
+------------------+---------------------------------------+-------------------------------------------------+


注意，还支持许多其他数据库，主要的标准是是否存在函数式 SqlAlchemy dialect 和 Python 驱动程序。
在 google 上搜索关键字 ``sqlalchemy`` 和描述要连接的数据库的关键字，应该可以找到正确的位置。


Hana
------------

Hana 的连接字符串如下 ::

    hana://{username}:{password}@{host}:{port}


(AWS) Athena
------------

Athena 的连接字符串如下 ::

    awsathena+jdbc://{aws_access_key_id}:{aws_secret_access_key}@athena.{region_name}.amazonaws.com/{schema_name}?s3_staging_dir={s3_staging_dir}&...

至少需要 escape/encode s3_staging_dir, i.e., ::

    s3://... -> s3%3A//...

您还可以像这样使用 `PyAthena` 库（不需要java）::

    awsathena+rest://{aws_access_key_id}:{aws_secret_access_key}@athena.{region_name}.amazonaws.com/{schema_name}?s3_staging_dir={s3_staging_dir}&...

参看 `PyAthena <https://github.com/laughingman7743/PyAthena#sqlalchemy>`_.

(Google) BigQuery
-----------------

BigQuery 的连接字符串如下 ::

    bigquery://{project_id}

此外，您还需要通过服务帐户配置身份验证。通过谷歌云平台控制面板创建服务帐户，
为其提供对适当的 BigQuery 数据集的访问，并下载服务帐户的 JSON 配置文件。
在 Superset 中，使用以下格式将 JSON blob 添加到
数据库配置页中的 “Secure Extra” 字段 ::

    {
        "credentials_info": <contents of credentials JSON file>
    }

结果文件应该具有这种结构 ::

    {
        "credentials_info": {
            "type": "service_account",
            "project_id": "...",
            "private_key_id": "...",
            "private_key": "...",
            "client_email": "...",
            "client_id": "...",
            "auth_uri": "...",
            "token_uri": "...",
            "auth_provider_x509_cert_url": "...",
            "client_x509_cert_url": "...",
        }
    }

然后，您应该能够连接到您的 BigQuery 数据集。

能够上传数据, e.g. 样本数据，python库 `pandas_gbq` 是必需的。

Elasticsearch
-------------

Elasticsearch 的连接字符串如下 ::

    elasticsearch+http://{user}:{password}@{host}:9200/

使用 HTTPS ::

    elasticsearch+https://{user}:{password}@{host}:9200/


Elasticsearch 默认限制为 10000 行，因此可以在集群中增加这个限制，
或者在 config 中增加 set Superset 的行限制 ::


    ROW_LIMIT = 10000

例如，您可以在SQLLab上查询多个索引 ::

    select timestamp, agent from "logstash-*"

但是，要对多个索引使用可视化，需要在集群上创建别名索引 ::

    POST /_aliases
    {
        "actions" : [
            { "add" : { "index" : "logstash-**", "alias" : "logstash_all" } }
        ]
    }

然后用 ``alias`` 名称 ``logstasg_all`` 注册你的表

Snowflake
---------

Snowflake 的连接字符串如下 ::

    snowflake://{user}:{password}@{account}.{region}/{database}?role={role}&warehouse={warehouse}

连接字符串中不需要 schema，因为它是按 table/query 定义的。
如果为用户定义了默认值，可以省略角色和仓库, i.e.

    snowflake://{user}:{password}@{account}.{region}/{database}

确保用户有权访问和使用所有必需的 databases/schemas/tables/views/warehouses，
因为 Snowflake SQLAlchemy 引擎在创建引擎期间不测试用户权限。

参看 `Snowflake SQLAlchemy <https://github.com/snowflakedb/snowflake-sqlalchemy>`_.

Teradata
---------

Teradata 的连接字符串如下 ::

    teradata://{user}:{password}@{host}

*注意*：需要安装 Teradata ODBC 驱动程序并配置环境变量，以便正确使用 sqlalchemy dialect。此处提供 Teradata ODBC 驱动程序：https://downloads.teradata.com/download/connectivity/odbc-driver/linux

必需的环境变量: ::

    export ODBCINI=/.../teradata/client/ODBC_64/odbc.ini
    export ODBCINST=/.../teradata/client/ODBC_64/odbcinst.ini

参看 `Teradata SQLAlchemy <https://github.com/Teradata/sqlalchemy-teradata>`_.

Apache Drill
------------
在编写本文时，pypi 上没有 SQLAlchemy Dialect，并且必须在此处下载:
`SQLAlchemy Drill <https://github.com/JohnOmernik/sqlalchemy-drill>`_

或者，您可以从命令行完全安装它，如下所示: ::

    git clone https://github.com/JohnOmernik/sqlalchemy-drill
    cd sqlalchemy-drill
    python3 setup.py install

完成之后，您可以通过两种方式连接到 Drill，一种是通过 REST 接口，另一种是通过 JDBC。
如果您通过 JDBC 进行连接，则必须安装 Drill JDBC 驱动程序。

Drill 的基本连接字符串如下 ::

    drill+sadrill://{username}:{password}@{host}:{port}/{storage_plugin}?use_ssl=True

如果您使用 JDBC 来连接 Drill，连接字符串如下: ::

    drill+jdbc://{username}:{password}@{host}:{port}/{storage_plugin}

有关如何在 Superset 中使用 Apache Drill 的完整教程，请参阅本教程:
`Visualize Anything with Superset and Drill <http://thedataist.com/visualize-anything-with-superset-and-drill/>`_

缓存
-------

Superset使用 `Flask-Cache <https://pythonhosted.org/Flask-Cache/>`_ 进行缓存。
配置缓存后端就像在 ``superset_config.py`` 中提供一个符合 Flask-Cache 规范的 ``CACHE_CONFIG`` 常量一样简单。


Flask-Cache 支持多个缓存后端 (Redis, Memcached,
SimpleCache (in-memory), 或者本地文件系统)。如果您打算使用 Memcached，
请使用 `pylibmc` 客户端库，因为 `python-memcached` 不能正确存储二进制数据。
如果您使用的是 Redis，请安装 `redis <https://pypi.python.org/pypi/redis>`_ Python包: ::

    pip install redis

对于设置超时，这是在 Superset 元数据中完成的，并将 "timeout searchpath" 从 slice 配置
上升到你数据源的配置，再上升到你数据库的配置，最终返回到 ``CACHE_CONFIG`` 中定义的全局默认值。

.. code-block:: python

    CACHE_CONFIG = {
        'CACHE_TYPE': 'redis',
        'CACHE_DEFAULT_TIMEOUT': 60 * 60 * 24, # 1 day default (in secs)
        'CACHE_KEY_PREFIX': 'superset_results',
        'CACHE_REDIS_URL': 'redis://localhost:6379/0',
    }


也可以在配置中传递自定义缓存初始化函数来处理额外的缓存用例。
该函数必须返回一个与 `Flask-Cache <https://pythonhosted.org/Flask-Cache/>`_ API 兼容的对象。


.. code-block:: python

    from custom_caching import CustomCache

    def init_cache(app):
        """Takes an app instance and returns a custom cache backend"""
        config = {
            'CACHE_DEFAULT_TIMEOUT': 60 * 60 * 24, # 1 day default (in secs)
            'CACHE_KEY_PREFIX': 'superset_results',
        }
        return CustomCache(app, config)

    CACHE_CONFIG = init_cache

Superset 有一个 Celery 任务，它将根据不同的策略定期预热缓存。
要使用它，请将以下内容添加到 `config.py` 中的 `CELERYBEAT_SCHEDULE` 部分：

.. code-block:: python

    CELERYBEAT_SCHEDULE = {
        'cache-warmup-hourly': {
            'task': 'cache-warmup',
            'schedule': crontab(minute=0, hour='*'),  # hourly
            'kwargs': {
                'strategy_name': 'top_n_dashboards',
                'top_n': 5,
                'since': '7 days ago',
            },
        },
    }

这将每小时将所有图表缓存在前5个最受欢迎的仪表板中。
对于其他策略，请查看 `superset/tasks/cache.py` 文件。


更深层次的 SQLAlchemy 集成
-----------------------------


可以使用 SQLAlchemy 公开的参数调整数据库连接信息。
在 ``Database`` edit 视图中，您将发现一个 ``extra`` 的字段作为 ``JSON`` blob。


.. image:: images/tutorial/add_db.png
   :scale: 30 %


这个JSON字符串包含额外的配置元素。``engine_params`` 对象被解压到 `sqlalchemy.create_engine <https://docs.sqlalchemy.org/en/latest/core/engines.html#sqlalchemy.create_engine>`_ 调用中，
而 ``metadata_params`` 被解压到 `sqlalchemy.MetaData <https://docs.sqlalchemy.org/en/rel_1_2/core/metadata.html#sqlalchemy.schema.MetaData>`_ 调用中。
有关更多信息，请参阅 SQLAlchemy 文档。


.. 注意:: 如果您在 SQLLab 和 PostgreSQL 上使用 CTAS
    请查看 :ref:`ref_ctas_engine_config` 对于特定的 ``engine_params``。


Schemas (Postgres & Redshift)
-----------------------------


Postgres 和 Redshift 以及其他数据库使用 **schema** 的概念作为 **database** 顶部的逻辑实体。
对于要连接到特定 schema 的 Superset，可以在 table 表单中设置 **schema** 参数。


用于 SQLAlchemy 连接的外部密码存储
--------------------------------------------------
可以为数据库密码使用外部存储。如果您正在运行一个自定义秘密分布式框架，
并且不希望将秘密存储在 Superset 的元数据库中，那么这是非常有用的。


示例：
编写一个函数，该函数接受 ``sqla.engine.url`` 类型的单个参数，并返回给定连接字符串的密码。
然后在配置文件中设置 ``SQLALCHEMY_CUSTOM_PASSWORD_STORE`` 以指向该函数。 ::

    def example_lookup_password(url):
        secret = <<get password from external framework>>
        return 'secret'

    SQLALCHEMY_CUSTOM_PASSWORD_STORE = example_lookup_password

一种常见的模式是使用环境变量来提供 secrets。
``SQLALCHEMY_CUSTOM_PASSWORD_STORE`` 也可以用于这个目的。 ::

    def example_password_as_env_var(url):
        # assuming the uri looks like
        # mysql://localhost?superset_user:{SUPERSET_PASSWORD}
        return url.password.format(os.environ)

    SQLALCHEMY_CUSTOM_PASSWORD_STORE = example_password_as_env_var


对数据库的 SSL 访问
-----------------------
这个例子使用了一个需要 SSL 的 MySQL 数据库。
配置可能与其他后端不同。这是放在 ``extra`` 参数中的 ::

    {
        "metadata_params": {},
        "engine_params": {
              "connect_args":{
                  "sslmode":"require",
                  "sslrootcert": "/path/to/my/pem"
            }
         }
    }


Druid
-----

* 在 UI 中，点击 + 号在 `Sources -> Druid Clusters` 菜单中输入集群的相关信息。

* 一旦输入 Druid 集群连接信息后，点击 `Sources -> Refresh Druid Metadata` 菜单项以便填充

* 导航到你的数据源

注意，您可以运行 ``superset refresh_druid`` 命令从您的 Druid 集群刷新元数据


Presto
------

默认情况下，Superset 假定在查询数据源时使用的是 Presto 的最新版本。
如果您使用的是旧版本的 presto，则可以在 ``extra`` 参数中对其进行配置::

    {
        "version": "0.123"
    }


Exasol
---------

Exasol 的连接字符串如下  ::

    exa+pyodbc://{user}:{password}@{host}

*注意*: 为了使 sqlalchemy dialect 正常工作，需要安装 Exasol ODBC 驱动程序。Exasol ODBC 驱动程序可在这里: https://www.exasol.com/portal/display/DOWNLOAD/Exasol+Download+Section

示例配置 (odbcinst.ini 可以留空) ::

    $ cat $/.../path/to/odbc.ini
    [EXAODBC]
    DRIVER = /.../path/to/driver/EXASOL_driver.so
    EXAHOST = host:8563
    EXASCHEMA = main

参阅 `SQLAlchemy for Exasol <https://github.com/blue-yonder/sqlalchemy_exasol>`_.

CORS
----

必须安装额外的 CORS 依赖项:

    superset[cors]


可以指定 `superset_config.py` 中的以下键来配置 CORS:


* ``ENABLE_CORS``: 必须设置为 True 才能启用 CORS
* ``CORS_OPTIONS``: 传递给 Flask-CORS 的选项 (`documentation <https://flask-cors.corydolphin.com/en/latest/api.html#extension>`)


Domain Sharding
---------------

Chrome 允许每个域一次最多打开6个连接。当 dashboard 中有6个以上的 slice 时，
很多时候获取请求会排队等待下一个可用的 socket。
`PR 5039 <https://github.com/apache/incubator-superset/pull/5039>`_ 将域分片添加到 Superset 中，
并且该功能将仅通过配置来启用(默认情况下 Superset 不允许跨域请求)。

* ``SUPERSET_WEBSERVER_DOMAINS``: domain sharding 功能允许的主机名列表。 默认 `None`


中间件
----------

Superset 允许您添加自己的中间件。要添加自己的中间件，请更新 `superset_config.py` 中的
 ``ADDITIONAL_MIDDLEWARE`` key。``ADDITIONAL_MIDDLEWARE`` 应该是您附加的中间件类的列表。

例如，要从像 nginx 这样的代理服务器后面使用 AUTH_REMOTE_USER，您必须添加一个简单的中间件类，
将 HTTP_X_PROXY_REMOTE_USER (或任何来自代理的自定义头文件)的值添加到 Gunicorn 的 REMOTE_USER 环境变量: ::

    class RemoteUserMiddleware(object):
        def __init__(self, app):
            self.app = app
        def __call__(self, environ, start_response):
            user = environ.pop('HTTP_X_PROXY_REMOTE_USER', None)
            environ['REMOTE_USER'] = user
            return self.app(environ, start_response)

    ADDITIONAL_MIDDLEWARE = [RemoteUserMiddleware, ]

*Adapted from http://flask.pocoo.org/snippets/69/*

事件日志
-------------

默认情况下，Superset 会在其数据库上记录特殊的操作事件。
这些日志可以在导航到 "Security" -> "Action Log" 的 UI 上访问。
您可以通过实现自己的事件日志类自由地自定义这些日志。

一个简单的 JSON 到 Stdout 类的例子::

    class JSONStdOutEventLogger(AbstractEventLogger):

        def log(self, user_id, action, *args, **kwargs):
            records = kwargs.get('records', list())
            dashboard_id = kwargs.get('dashboard_id')
            slice_id = kwargs.get('slice_id')
            duration_ms = kwargs.get('duration_ms')
            referrer = kwargs.get('referrer')

            for record in records:
                log = dict(
                    action=action,
                    json=record,
                    dashboard_id=dashboard_id,
                    slice_id=slice_id,
                    duration_ms=duration_ms,
                    referrer=referrer,
                    user_id=user_id
                )
                print(json.dumps(log))


然后在 Superset 的配置中传递一个您想要使用的 logger 类型的实例。


    EVENT_LOGGER = JSONStdOutEventLogger()


升级
---------

升级应该和运行一样简单::

    pip install apache-superset --upgrade
    superset db upgrade
    superset init

我们建议在升级 Superset 时遵循标准的最佳实践，例如在升级之前进行数据库备份，
在升级生产之前升级 staging 环境，以及在平台上活动用户较少的情况下升级生产。

.. 注意 ::
   某些升级可能包含向后不兼容的更改，或需要安排停机时间，
   此时，贡献者会在存储库的 ``UPDATING.md`` 中附加注释。
   建议在运行升级之前查看此文件。


Celery 任务
------------

在大型分析数据库中，通常运行执行数分钟或数小时的查询。
为了支持在典型的 web 请求超时(30-60秒)之外执行的长时间运行的查询，
需要为 Superset 配置一个异步后端，它包括:

* 一个或多个 Superset worker(作为 Celery worker 的实现)，
  可以使用 ``celery worker`` 命令启动，
  运行 ``celery worker --help`` 查看相关选项。
* celery broker(消息队列)，我们建议使用 Redis 或 RabbitMQ
* results backend，它定义工作人员将在何处持久化查询结果


配置 Celery 需要在您的 ``superset_config.py`` 中定义一个 ``CELERY_CONFIG``。
worker 进程和 web 服务器进程应该具有相同的配置。

.. code-block:: python

    class CeleryConfig(object):
        BROKER_URL = 'redis://localhost:6379/0'
        CELERY_IMPORTS = (
            'superset.sql_lab',
            'superset.tasks',
        )
        CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
        CELERYD_LOG_LEVEL = 'DEBUG'
        CELERYD_PREFETCH_MULTIPLIER = 10
        CELERY_ACKS_LATE = True
        CELERY_ANNOTATIONS = {
            'sql_lab.get_sql_results': {
                'rate_limit': '100/s',
            },
            'email_reports.send': {
                'rate_limit': '1/s',
                'time_limit': 120,
                'soft_time_limit': 150,
                'ignore_result': True,
            },
        }
        CELERYBEAT_SCHEDULE = {
            'email_reports.schedule_hourly': {
                'task': 'email_reports.schedule_hourly',
                'schedule': crontab(minute=1, hour='*'),
            },
        }

    CELERY_CONFIG = CeleryConfig

* 利用配置去启动一个 Celery worker 运行: ::

    celery worker --app=superset.tasks.celery_app:app --pool=prefork -O fair -c 4


* 启动一个安排定期后台作业的作业，请运行 ::

    celery beat --app=superset.tasks.celery_app:app

要设置 result backend，您需要将 ``werkzeug.contrib.cache.BaseCache`` 的派生实例
传递到 ``superset_config.py`` 中的 ``RESULTS_BACKEND`` 配置键。
可以使用 Memcached、Redis、S3 (https://pypi.python.org/pypi/s3werkzeugcache)、
内存或文件系统（在单个服务器类型设置或测试中），
也可以编写自己的缓存接口。您的 ``superset_config.py`` 可能看起来像：

.. code-block:: python

    # On S3
    from s3cache.s3cache import S3Cache
    S3_CACHE_BUCKET = 'foobar-superset'
    S3_CACHE_KEY_PREFIX = 'sql_lab_result'
    RESULTS_BACKEND = S3Cache(S3_CACHE_BUCKET, S3_CACHE_KEY_PREFIX)

    # On Redis
    from werkzeug.contrib.cache import RedisCache
    RESULTS_BACKEND = RedisCache(
        host='localhost', port=6379, key_prefix='superset_results')

为了提高性能，`MessagePack <https://github.com/msgpack/msgpack-python>`_ 和
 `PyArrow <https://arrow.apache.org/docs/python/>`_ 现在用于结果序列化。
 如果出现任何问题，可以通过在配置中设置 ``RESULTS_BACKEND_USE_MSGPACK = False`` 来禁用此选项。
 升级现有环境时，请清除现有结果缓存存储。

**注意事项**

* 重要的是，Superset 集群中的所有工作节点和 web 服务器共享一个公共元数据数据库。
  这意味着 SQLite 将无法在此上下文中工作，因为它对并发性的支持有限，
  并且通常位于本地文件系统上。

* 在你的整个设置中应该只有一个 ``celery beat`` 运行的实例。
  如果不是，后台作业可能会被多次调度，从而导致一些奇怪的行为，
  如重复提交报告、比预期的负载/流量高等等。

* 如果在数据库设置中启用 "Asynchronous Query Execution" ，SQL Lab 将仅异步运行查询。


Email 报告
-------------
Email reports 允许用户安排电子邮件报告

* 图表和仪表板可视化（附件或内联）
* 图表数据（内联表上的 CSV 附件）

**Setup**

请确保在配置文件中启用电子邮件报告

.. code-block:: python

    ENABLE_SCHEDULED_EMAIL_REPORTS = True


现在，您将在导航栏中发现两个允许您安排电子邮件报告的新项

* Manage -> Dashboard Emails
* Manage -> Chart Email Schedules

计划以 crontab 格式定义，每个计划可以有一个收件人列表
（所有收件人都可以接收一封邮件或单独的邮件）。
出于审核目的，所有外发邮件都可以具有必填密件抄送。

为了能够上手，你需要配置一个 celery worker 和一个 celery beat(参见 "Celery Tasks" 一节)。
您的 celery 配置还需要一个条目 ``email_reports.schedule_hourly`` 针对 ``CELERYBEAT_SCHEDULE``。

要发送电子邮件，您需要在您的配置文件中配置 SMTP 设置。如：

.. code-block:: python

    EMAIL_NOTIFICATIONS = True

    SMTP_HOST = "email-smtp.eu-west-1.amazonaws.com"
    SMTP_STARTTLS = True
    SMTP_SSL = False
    SMTP_USER = "smtp_username"
    SMTP_PORT = 25
    SMTP_PASSWORD = os.environ.get("SMTP_PASSWORD")
    SMTP_MAIL_FROM = "insights@komoot.com"


要呈现仪表板，您需要在 superset 实例上安装一个本地浏览器

  * `geckodriver <https://github.com/mozilla/geckodriver>`_ Firefox 是首选
  * `chromedriver <http://chromedriver.chromium.org/>`_ 也是不错的选项

您需要在您的配置中相应地调整 ``EMAIL_REPORTS_WEBDRIVER``。

您还需要指定代表哪个 username 呈现仪表板。
一般来说，仪表板和图表是不能被未授权的请求访问的，
这就是为什么 worker 需要接管现有用户的凭证来获取快照。::

    EMAIL_REPORTS_USER = 'username_with_permission_to_access_dashboards'


**重要事项**

* 请注意 celery 的并发设置(使用 ``-c 4``)。
  Selenium/webdriver 实例会消耗服务器上的大量 CPU/内存。

* 在某些情况下，如果您注意到大量泄漏的 ``geckodriver`` 进程，请尝试运行您的芹菜进程 ::

    celery worker --pool=prefork --max-tasks-per-child=128 ...

* 建议为 ``sql_lab`` 和 ``email_reports`` 任务运行单独的 worker。
  可以通过在 ``CELERY_ANNOTATIONS`` 中使用 ``queue`` 字段来实现

* 在你的配置中调整 ``WEBDRIVER_BASEURL``，如果 celery workers 不能
  通过它的默认值 ``http://0.0.0.0:8080/`` 访问 superset (请注意端口号8080，许多其他设置使用端口8088)。

SQL Lab
-------
SQL Lab 是一个强大的 SQL IDE，可以与所有 SQLAlchemy 兼容的数据库一起工作。
默认情况下，查询是在 web 请求的范围内执行的，因此当查询超过环境中 web 请求的最大持续时间时，
查询可能最终超时，无论是 reverse proxy 还是 Superset 服务器本身。
在这种情况下，最好使用 ``celery`` 在后台运行查询。
请按照上面提到的例子/说明来设置你的 celery。

还要注意，SQL Lab 支持查询中的 Jinja 模板，并且可以通过在 superset 配置中定义 
``JINJA_CONTEXT_ADDONS`` 来重载环境中的默认 Jinja 上下文。
这个 dictionary 中引用的对象可供用户在其 SQL 中使用。

.. code-block:: python

    JINJA_CONTEXT_ADDONS = {
        'my_crazy_macro': lambda x: x*2,
    }

SQL Lab 还包括一个带有可插入后端的实时查询验证功能。您可以通过在 config.py 中添加如下所示
的块来配置与哪个数据库引擎一起使用的验证实现:

.. code-block:: python

     FEATURE_FLAGS = {
         'SQL_VALIDATORS_BY_ENGINE': {
             'presto': 'PrestoDBSQLValidator',
         }
     }

可用的验证器和名称可以在 `sql_validators/` 中找到。

**Scheduling queries**

您可以选择允许用户在 SQL Lab 中直接调度查询。
这是通过向保存的查询添加额外的元数据来实现的，然后由外部调度来获取这些元数据
(像 [Apache Airflow](https://airflow.apache.org/))。

要允许调度查询，请将以下内容添加到 `config.py`:

.. code-block:: python

    FEATURE_FLAGS = {
        # Configuration for scheduling queries from SQL Lab. This information is
        # collected when the user clicks "Schedule query", and saved into the `extra`
        # field of saved queries.
        # See: https://github.com/mozilla-services/react-jsonschema-form
        'SCHEDULED_QUERIES': {
            'JSONSCHEMA': {
                'title': 'Schedule',
                'description': (
                    'In order to schedule a query, you need to specify when it '
                    'should start running, when it should stop running, and how '
                    'often it should run. You can also optionally specify '
                    'dependencies that should be met before the query is '
                    'executed. Please read the documentation for best practices '
                    'and more information on how to specify dependencies.'
                ),
                'type': 'object',
                'properties': {
                    'output_table': {
                        'type': 'string',
                        'title': 'Output table name',
                    },
                    'start_date': {
                        'type': 'string',
                        'title': 'Start date',
                        # date-time is parsed using the chrono library, see
                        # https://www.npmjs.com/package/chrono-node#usage
                        'format': 'date-time',
                        'default': 'tomorrow at 9am',
                    },
                    'end_date': {
                        'type': 'string',
                        'title': 'End date',
                        # date-time is parsed using the chrono library, see
                        # https://www.npmjs.com/package/chrono-node#usage
                        'format': 'date-time',
                        'default': '9am in 30 days',
                    },
                    'schedule_interval': {
                        'type': 'string',
                        'title': 'Schedule interval',
                    },
                    'dependencies': {
                        'type': 'array',
                        'title': 'Dependencies',
                        'items': {
                            'type': 'string',
                        },
                    },
                },
            },
            'UISCHEMA': {
                'schedule_interval': {
                    'ui:placeholder': '@daily, @weekly, etc.',
                },
                'dependencies': {
                    'ui:help': (
                        'Check the documentation for the correct format when '
                        'defining dependencies.'
                    ),
                },
            },
            'VALIDATION': [
                # ensure that start_date <= end_date
                {
                    'name': 'less_equal',
                    'arguments': ['start_date', 'end_date'],
                    'message': 'End date cannot be before start date',
                    # this is where the error message is shown
                    'container': 'end_date',
                },
            ],
            # link to the scheduler; this example links to an Airflow pipeline
            # that uses the query id and the output table as its name
            'linkback': (
                'https://airflow.example.com/admin/airflow/tree?'
                'dag_id=query_${id}_${extra_json.schedule_info.output_table}'
            ),
        },
    }


此特性标志基于 `react-jsonschema-form <https://github.com/mozilla-services/react-jsonschema-form>`_，
并将向 SQL Lab 添加一个名为 "Schedule Query" 的按钮。
单击按钮时，将显示一个模式，用户可以在其中添加调度查询所需的元数据。

然后可以从 `/savedqueryviewapi/api/read` 检索此信息，
并用于调度在 JSON 元数据中包含 `scheduled_queries` 的查询。
对于除 Airflow 之外的调度器，可以很容易地将其他字段添加到上面的配置文件中。

Celery Flower
-------------
Flower 是一个基于 web 的工具，用于监控 Celery 集群，你可以安装从pip: ::

    pip install flower

and run via: ::

    celery flower --app=superset.tasks.celery_app:app

从源码构建
---------------------

更高级的用户可能希望从源代码构建 Superset。如果您通过派生项目向您的环境中添加特定的特性，就会出现这种情况。
参阅 `CONTRIBUTING.md#setup-local-environment-for-development <https://github.com/apache/incubator-superset/blob/master/CONTRIBUTING.md#setup-local-environment-for-development>`_。

Blueprints
----------

`Blueprints 是 Flask 的可复用的应用 <https://flask.palletsprojects.com/en/1.0.x/tutorial/views/>`_.
Superset 允许您在 ``superset_config`` 模块中指定 Blueprints 数组。
下面是一个使用简单蓝图的例子。通过这样做，您可以期望 Superset 提供一个
显示 "OK" 的页面在 ``/simple_page`` url 上。
这可以让您在同一台服务器上运行自定义数据可视化应用程序和 Superset。

.. code-block:: python

    from flask import Blueprint
    simple_page = Blueprint('simple_page', __name__,
                                    template_folder='templates')
    @simple_page.route('/', defaults={'page': 'index'})
    @simple_page.route('/<page>')
    def show(page):
        return "Ok"

    BLUEPRINTS = [simple_page]

StatsD 日志
--------------

如果需要，Superset 可用于将事件记录到 StatsD。
大多数 endpoints hit 在 SQL Lab 中记录查询开始和结束等关键事件。

要设置 StatsD logging，只需在 ``superset_config.py`` 中配置日志记录器。

.. code-block:: python

    from superset.stats_logger import StatsdStatsLogger
    STATS_LOGGER = StatsdStatsLogger(host='localhost', port=8125, prefix='superset')

请注意，也可以通过派生来实现自己的日志记录器
``superset.stats_logger.BaseStatsLogger``.


Kubernetes 中使用 helm 安装 Superset
----------------------------------------

你可以用 Helm <https://helm.sh/> 将 Superset 安装到 Kubernetes 中。chart 位于 ``install/helm``。

在你的 Kubernetes 中安装 Superset:

.. code-block:: bash

    helm upgrade --install superset ./install/helm/superset

注意，上面的命令将把 Superset 安装到 Kubernetes 集群的 ``default`` 名称空间中。

Custom OAuth2 configuration
---------------------------

除了 FAB 支持的提供者(github, twitter, linkedin, google, azure)之外，
它还可以轻松地将 Superset 与其他支持 "code" 授权的 OAuth2 授权服务器实现连接起来。

第一步: 在 Superset ``superset_config.py`` 中配置授权。

.. code-block:: python

    AUTH_TYPE = AUTH_OAUTH
    OAUTH_PROVIDERS = [
        {   'name':'egaSSO',
            'token_key':'access_token', # Name of the token in the response of access_token_url
            'icon':'fa-address-card',   # Icon for the provider
            'remote_app': {
                'consumer_key':'myClientId',  # Client Id (Identify Superset application)
                'consumer_secret':'MySecret', # Secret for this Client Id (Identify Superset application)
                'request_token_params':{
                    'scope': 'read'               # Scope for the Authorization
                },
                'access_token_method':'POST',    # HTTP Method to call access_token_url
                'access_token_params':{        # Additional parameters for calls to access_token_url
                    'client_id':'myClientId'
                },
                'access_token_headers':{    # Additional headers for calls to access_token_url
                    'Authorization': 'Basic Base64EncodedClientIdAndSecret'
                },
                'base_url':'https://myAuthorizationServer/oauth2AuthorizationServer/',
                'access_token_url':'https://myAuthorizationServer/oauth2AuthorizationServer/token',
                'authorize_url':'https://myAuthorizationServer/oauth2AuthorizationServer/authorize'
            }
        }
    ]

    # Will allow user self registration, allowing to create Flask users from Authorized User
    AUTH_USER_REGISTRATION = True

    # The default user self registration role
    AUTH_USER_REGISTRATION_ROLE = "Public"

第二步: 创建一个 `CustomSsoSecurityManager`，扩展 `SupersetSecurityManager` 并覆盖 `oauth_user_info`:

.. code-block:: python

    from superset.security import SupersetSecurityManager

    class CustomSsoSecurityManager(SupersetSecurityManager):

        def oauth_user_info(self, provider, response=None):
            logging.debug("Oauth2 provider: {0}.".format(provider))
            if provider == 'egaSSO':
                # As example, this line request a GET to base_url + '/' + userDetails with Bearer  Authentication,
        # and expects that authorization server checks the token, and response with user details
                me = self.appbuilder.sm.oauth_remotes[provider].get('userDetails').data
                logging.debug("user_data: {0}".format(me))
                return { 'name' : me['name'], 'email' : me['email'], 'id' : me['user_name'], 'username' : me['user_name'], 'first_name':'', 'last_name':''}
        ...


该文件必须位于与 ``superset_config.py`` 相同的目录下，名称为 ``custom_sso_security_manager.py``。

然后我们可以将这两行添加到 ``superset_config.py`` 中:

.. code-block:: python

  from custom_sso_security_manager import CustomSsoSecurityManager
  CUSTOM_SECURITY_MANAGER = CustomSsoSecurityManager

Feature Flags
-------------

由于用户种类繁多，Superset 有一些默认不启用的功能。例如，一些用户有更强的安全限制，而另一些用户可能没有。
因此，Superset 允许用户通过配置来启用或禁用某些功能。对于特性所有者，您可以在 Superset 中添加可选的功能，
但是只会受到一部分用户的影响。

您可以使用 ``superset_config.py`` 中的标记启用或禁用特性:

.. code-block:: python

     DEFAULT_FEATURE_FLAGS = {
         'CLIENT_CACHE': False,
         'ENABLE_EXPLORE_JSON_CSRF_PROTECTION': False,
         'PRESTO_EXPAND_DATA': False,
     }

以下是标志和描述的列表:

* ENABLE_EXPLORE_JSON_CSRF_PROTECTION

  * 出于某些安全考虑，您可能需要对所有查询到 explore_json 端点的请求强制实施 CSRF 保护。在 Superset 中，我们使用 `flask-csrf <https://sjl.bitbucket.io/flask-csrf/>`_ 为所有 POST 请求添加 csrf 保护，但是这个保护不适用于 GET 方法。
  
  * 当 ENABLE_EXPLORE_JSON_CSRF_PROTECTION 设置为 true 时，您的用户不能对 explore_json 发出 GET 请求。这个特性的默认值为 False (当前行为)，explore_json 同时接受 GET 和 POST 请求。详情请参阅 `PR 7935 <https://github.com/apache/incubator-superset/pull/7935>`_。

* PRESTO_EXPAND_DATA

  * 启用此功能后，Presto 中的嵌套类型将扩展为额外的列 and/or 数组。这是实验性的，并不适用于所有嵌套类型。

SIP-15
------

`SIP-15 <https://github.com/apache/incubator-superset/issues/6360>`_ 的目的是确保以一致和透明的方式处理 Druid 和 SQLAlchemy 连接器的时间间隔。

在 SIP-15 SQLAlchemy 使用包含端点之前，如果没有定义格式，并且列格式不符合 ISO 8601 日期-时间(请参阅 SIP 了解详细信息)，则这些端点可能表现为仅用于字符串列(由于字典排序)。

要解决这个问题，而不是必须为每个 non-IS0 8601 date-time 列定义 date/time 格式，once可以通过 ``extra`` 参数在每个数据库级别上定义默认的列映射 ::

    {
        "python_date_format_by_column_name": {
            "ds": "%Y-%m-%d"
        }
    }

**New deployments**

所有新的 Superset 部署都应该启用SIP-15, 通过

.. code-block:: python

    SIP_15_ENABLED = True

**Existing deployments**

由于不清楚图表创建者是否意识到了时间范围的不一致性(并相应地调整了端点)，因此更改所有图表的行为是过于激进的。
相反，SIP-15 提供了一个软传输，允许生产者(图表所有者)看到提议的变化的影响，并相应地调整他们的图表。

在启用 SIP-15 之前，现有的部署应该向其用户传达更改的影响，并定义一个宽限期结束日期(当然不包括)，之后所有图表将遵循 [start, end) 间隔，即，

.. code-block:: python

    from dateime import date

    SIP_15_ENABLED = True
    SIP_15_GRACE_PERIOD_END = date(<YYYY>, <MM>, <DD>)

为了提高透明度，在图表时间范围内显式地调用当前端点行为( SIP-15 之后，对于所有连接器和数据库，这将是 [start, end] )。
可以通过 ``extra`` 参数覆盖每个数据库级别上的默认值 ::

    {
        "time_range_endpoints": ["inclusive", "inclusive"]
    }


注意，在将来的版本中，通过代码更改和 Alembic 迁移，临时的 SIP-15 逻辑将被删除(包括 ``time_grain_endpoints`` form-data 字段)。
