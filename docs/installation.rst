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

Cloud-native（云原生）!
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

OS 依赖
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

Python 的 setup tools 和 pip
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

 Load Balancer 背后的配置
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

Database dependencies
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

Caching
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


Deeper SQLAlchemy integration
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


External Password store for SQLAlchemy connections
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


SSL Access to databases
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


Middleware
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

Event Logging
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


Upgrading
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


Celery Tasks
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

* To start a Celery worker to leverage the configuration run: ::

    celery worker --app=superset.tasks.celery_app:app --pool=prefork -O fair -c 4

* To start a job which schedules periodic background jobs, run ::

    celery beat --app=superset.tasks.celery_app:app

To setup a result backend, you need to pass an instance of a derivative
of ``werkzeug.contrib.cache.BaseCache`` to the ``RESULTS_BACKEND``
configuration key in your ``superset_config.py``. It's possible to use
Memcached, Redis, S3 (https://pypi.python.org/pypi/s3werkzeugcache),
memory or the file system (in a single server-type setup or for testing),
or to write your own caching interface. Your ``superset_config.py`` may
look something like:

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

For performance gains, `MessagePack <https://github.com/msgpack/msgpack-python>`_
and `PyArrow <https://arrow.apache.org/docs/python/>`_ are now used for results
serialization. This can be disabled by setting ``RESULTS_BACKEND_USE_MSGPACK = False``
in your configuration, should any issues arise. Please clear your existing results
cache store when upgrading an existing environment.

**Important notes**

* It is important that all the worker nodes and web servers in
  the Superset cluster share a common metadata database.
  This means that SQLite will not work in this context since it has
  limited support for concurrency and
  typically lives on the local file system.

* There should only be one instance of ``celery beat`` running in your
  entire setup. If not, background jobs can get scheduled multiple times
  resulting in weird behaviors like duplicate delivery of reports,
  higher than expected load / traffic etc.

* SQL Lab will only run your queries asynchronously if you enable
  "Asynchronous Query Execution" in your database settings.


Email Reports
-------------
Email reports allow users to schedule email reports for

* chart and dashboard visualization (Attachment or inline)
* chart data (CSV attachment on inline table)

**Setup**

Make sure you enable email reports in your configuration file

.. code-block:: python

    ENABLE_SCHEDULED_EMAIL_REPORTS = True

Now you will find two new items in the navigation bar that allow you to schedule email
reports

* Manage -> Dashboard Emails
* Manage -> Chart Email Schedules

Schedules are defined in crontab format and each schedule
can have a list of recipients (all of them can receive a single mail,
or separate mails). For audit purposes, all outgoing mails can have a
mandatory bcc.

In order get picked up you need to configure a celery worker and a celery beat
(see section above "Celery Tasks"). Your celery configuration also
needs an entry ``email_reports.schedule_hourly`` for ``CELERYBEAT_SCHEDULE``.

To send emails you need to configure SMTP settings in your configuration file. e.g.

.. code-block:: python

    EMAIL_NOTIFICATIONS = True

    SMTP_HOST = "email-smtp.eu-west-1.amazonaws.com"
    SMTP_STARTTLS = True
    SMTP_SSL = False
    SMTP_USER = "smtp_username"
    SMTP_PORT = 25
    SMTP_PASSWORD = os.environ.get("SMTP_PASSWORD")
    SMTP_MAIL_FROM = "insights@komoot.com"


To render dashboards you need to install a local browser on your superset instance

  * `geckodriver <https://github.com/mozilla/geckodriver>`_ and Firefox is preferred
  * `chromedriver <http://chromedriver.chromium.org/>`_ is a good option too

You need to adjust the ``EMAIL_REPORTS_WEBDRIVER`` accordingly in your configuration.

You also need to specify on behalf of which username to render the dashboards. In general
dashboards and charts are not accessible to unauthorized requests, that is why the
worker needs to take over credentials of an existing user to take a snapshot. ::

    EMAIL_REPORTS_USER = 'username_with_permission_to_access_dashboards'


**Important notes**

* Be mindful of the concurrency setting for celery (using ``-c 4``).
  Selenium/webdriver instances can consume a lot of CPU / memory on your servers.

* In some cases, if you notice a lot of leaked ``geckodriver`` processes, try running
  your celery processes with ::

    celery worker --pool=prefork --max-tasks-per-child=128 ...

* It is recommended to run separate workers for ``sql_lab`` and
  ``email_reports`` tasks. Can be done by using ``queue`` field in ``CELERY_ANNOTATIONS``

* Adjust ``WEBDRIVER_BASEURL`` in your config if celery workers can't access superset via its
  default value ``http://0.0.0.0:8080/`` (notice the port number 8080, many other setups use
  port 8088).

SQL Lab
-------
SQL Lab is a powerful SQL IDE that works with all SQLAlchemy compatible
databases. By default, queries are executed in the scope of a web
request so they may eventually timeout as queries exceed the maximum duration of a web
request in your environment, whether it'd be a reverse proxy or the Superset
server itself. In such cases, it is preferred to use ``celery`` to run the queries
in the background. Please follow the examples/notes mentioned above to get your
celery setup working.

Also note that SQL Lab supports Jinja templating in queries and that it's
possible to overload
the default Jinja context in your environment by defining the
``JINJA_CONTEXT_ADDONS`` in your superset configuration. Objects referenced
in this dictionary are made available for users to use in their SQL.

.. code-block:: python

    JINJA_CONTEXT_ADDONS = {
        'my_crazy_macro': lambda x: x*2,
    }

SQL Lab also includes a live query validation feature with pluggable backends.
You can configure which validation implementation is used with which database
engine by adding a block like the following to your config.py:

.. code-block:: python

     FEATURE_FLAGS = {
         'SQL_VALIDATORS_BY_ENGINE': {
             'presto': 'PrestoDBSQLValidator',
         }
     }

The available validators and names can be found in `sql_validators/`.

**Scheduling queries**

You can optionally allow your users to schedule queries directly in SQL Lab.
This is done by addding extra metadata to saved queries, which are then picked
up by an external scheduled (like [Apache Airflow](https://airflow.apache.org/)).

To allow scheduled queries, add the following to your `config.py`:

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

This feature flag is based on [react-jsonschema-form](https://github.com/mozilla-services/react-jsonschema-form),
and will add a button called "Schedule Query" to SQL Lab. When the button is
clicked, a modal will show up where the user can add the metadata required for
scheduling the query.

This information can then be retrieved from the endpoint `/savedqueryviewapi/api/read`
and used to schedule the queries that have `scheduled_queries` in their JSON
metadata. For schedulers other than Airflow, additional fields can be easily
added to the configuration file above.

Celery Flower
-------------
Flower is a web based tool for monitoring the Celery cluster which you can
install from pip: ::

    pip install flower

and run via: ::

    celery flower --app=superset.tasks.celery_app:app

Building from source
---------------------

More advanced users may want to build Superset from sources. That
would be the case if you fork the project to add features specific to
your environment. See `CONTRIBUTING.md#setup-local-environment-for-development <https://github.com/apache/incubator-superset/blob/master/CONTRIBUTING.md#setup-local-environment-for-development>`_.

Blueprints
----------

`Blueprints are Flask's reusable apps <https://flask.palletsprojects.com/en/1.0.x/tutorial/views/>`_.
Superset allows you to specify an array of Blueprints
in your ``superset_config`` module. Here's
an example of how this can work with a simple Blueprint. By doing
so, you can expect Superset to serve a page that says "OK"
at the ``/simple_page`` url. This can allow you to run other things such
as custom data visualization applications alongside Superset, on the
same server.

.. code-block:: python

    from flask import Blueprint
    simple_page = Blueprint('simple_page', __name__,
                                    template_folder='templates')
    @simple_page.route('/', defaults={'page': 'index'})
    @simple_page.route('/<page>')
    def show(page):
        return "Ok"

    BLUEPRINTS = [simple_page]

StatsD logging
--------------

Superset is instrumented to log events to StatsD if desired. Most endpoints hit
are logged as well as key events like query start and end in SQL Lab.

To setup StatsD logging, it's a matter of configuring the logger in your
``superset_config.py``.

.. code-block:: python

    from superset.stats_logger import StatsdStatsLogger
    STATS_LOGGER = StatsdStatsLogger(host='localhost', port=8125, prefix='superset')

Note that it's also possible to implement you own logger by deriving
``superset.stats_logger.BaseStatsLogger``.


Install Superset with helm in Kubernetes
----------------------------------------

You can install Superset into Kubernetes with Helm <https://helm.sh/>. The chart is
located in ``install/helm``.

To install Superset into your Kubernetes:

.. code-block:: bash

    helm upgrade --install superset ./install/helm/superset

Note that the above command will install Superset into ``default`` namespace of your Kubernetes cluster.

Custom OAuth2 configuration
---------------------------

Beyond FAB supported providers (github, twitter, linkedin, google, azure), its easy to connect Superset with other OAuth2 Authorization Server implementations that support "code" authorization.

The first step: Configure authorization in Superset ``superset_config.py``.

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

Second step: Create a `CustomSsoSecurityManager` that extends `SupersetSecurityManager` and overrides `oauth_user_info`:

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

This file must be located at the same directory than ``superset_config.py`` with the name ``custom_sso_security_manager.py``.

Then we can add this two lines to ``superset_config.py``:

.. code-block:: python

  from custom_sso_security_manager import CustomSsoSecurityManager
  CUSTOM_SECURITY_MANAGER = CustomSsoSecurityManager

Feature Flags
-------------

Because of a wide variety of users, Superset has some features that are not enabled by default. For example, some users have stronger security restrictions, while some others may not. So Superset allow users to enable or disable some features by config. For feature owners, you can add optional functionalities in Superset, but will be only affected by a subset of users.

You can enable or disable features with flag from ``superset_config.py``:

.. code-block:: python

     DEFAULT_FEATURE_FLAGS = {
         'CLIENT_CACHE': False,
         'ENABLE_EXPLORE_JSON_CSRF_PROTECTION': False,
         'PRESTO_EXPAND_DATA': False,
     }

Here is a list of flags and descriptions:

* ENABLE_EXPLORE_JSON_CSRF_PROTECTION

  * For some security concerns, you may need to enforce CSRF protection on all query request to explore_json endpoint. In Superset, we use `flask-csrf <https://sjl.bitbucket.io/flask-csrf/>`_ add csrf protection for all POST requests, but this protection doesn't apply to GET method.

  * When ENABLE_EXPLORE_JSON_CSRF_PROTECTION is set to true, your users cannot make GET request to explore_json. The default value for this feature False (current behavior), explore_json accepts both GET and POST request. See `PR 7935 <https://github.com/apache/incubator-superset/pull/7935>`_ for more details.

* PRESTO_EXPAND_DATA

  * When this feature is enabled, nested types in Presto will be expanded into extra columns and/or arrays. This is experimental, and doesn't work with all nested types.


SIP-15
------

`SIP-15 <https://github.com/apache/incubator-superset/issues/6360>`_ aims to ensure that time intervals are handled in a consistent and transparent manner for both the Druid and SQLAlchemy connectors.

Prior to SIP-15 SQLAlchemy used inclusive endpoints however these may behave like exclusive for string columns (due to lexicographical ordering) if no formatting was defined and the column formatting did not conform to an ISO 8601 date-time (refer to the SIP for details).

To remedy this rather than having to define the date/time format for every non-IS0 8601 date-time column, once can define a default column mapping on a per database level via the ``extra`` parameter ::

    {
        "python_date_format_by_column_name": {
            "ds": "%Y-%m-%d"
        }
    }

**New deployments**

All new Superset deployments should enable SIP-15 via,

.. code-block:: python

    SIP_15_ENABLED = True

**Existing deployments**

Given that it is not apparent whether the chart creator was aware of the time range inconsistencies (and adjusted the endpoints accordingly) changing the behavior of all charts is overly aggressive. Instead SIP-15 proivides a soft transistion allowing producers (chart owners) to see the impact of the proposed change and adjust their charts accordingly.

Prior to enabling SIP-15 existing deployments should communicate to their users the impact of the change and define a grace period end date (exclusive of course) after which all charts will conform to the [start, end) interval, i.e.,

.. code-block:: python

    from dateime import date

    SIP_15_ENABLED = True
    SIP_15_GRACE_PERIOD_END = date(<YYYY>, <MM>, <DD>)

To aid with transparency the current endpoint behavior is explicitly called out in the chart time range (post SIP-15 this will be [start, end) for all connectors and databases). One can override the defaults on a per database level via the ``extra``
parameter ::

    {
        "time_range_endpoints": ["inclusive", "inclusive"]
    }


Note in a future release the interim SIP-15 logic will be removed (including the ``time_grain_endpoints`` form-data field) via a code change and Alembic migration.
