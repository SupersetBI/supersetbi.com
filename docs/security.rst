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

安全
========
Superset 中的安全性由 Flask AppBuilder (FAB) 处理。FAB 是“基于 Flask 构建的简单，快速的应用程序开发框架。” 
FAB 提供身份验证，用户管理，权限和角色。请阅读其 `Security documentation
<https://flask-appbuilder.readthedocs.io/en/latest/security.html>`_。

提供角色
--------------
Superset 附带了一组角色，这些角色由 Superset 本身处理。您可以假设这些角色将随着 Superset 的发展而保持最新状态。
即使 ``Admin`` 用户可以这样做，也不建议您通过删除或添加权限来更改这些角色，
因为在运行下一个 ``superset init`` 命令时，这些角色将重新同步为其原始值。

由于不建议您更改此处描述的角色，因此正确地假设您的安全策略应基于这些基本角色和您创建的角色来组成用户访问权限。
例如，您可以创建一个角色 ``Financial Analyst`` ，该角色将由对一组数据源（表）和/或 数据库的一组权限组成。
然后，将为用户授予 ``Gamma`` ，``Financial Analyst`` ，以及 ``sql_lab`` 。

Admin
"""""
管理员拥有所有可能的权限，包括授予或撤销其他用户的权限以及更改其他人的 slices 和 dashboards。

Alpha
"""""
Alpha 用户有权访问所有数据源，但不能授予或撤消其他用户的访问权限。它们还仅限于更改其拥有的对象。
Alpha 用户可以添加和更改数据源。

Gamma
"""""
Gamma 用户的访问权限受到限制。他们只能使用来自其他角色可以访问的数据源中的数据。
他们仅有权查看由他们有权访问的数据源制成的 slices 和 dashboards 。
当前，Gamma 用户无法更改或添加数据源。尽管他们可以创建 slices 和 dashboards ，
但我们假设他们主要是内容消费者。

还要注意，当 Gamma 用户查看 dashboards 和 slices 列表视图时，他们只会看到他们有权访问的对象。

sql_lab
"""""""
``sql_lab`` 角色授予对 SQL Lab 的访问权限。请注意，虽然默认情况下 ``Admin`` 用户可以访问所有数据库，
但 ``Alpha`` 和 ``Gamma`` 用户都需要基于每个数据库进行访问。

Public
""""""
可以允许注销的用户访问某些 Superset 功能。

通过在 ``superset_config.py`` 中设置 ``PUBLIC_ROLE_LIKE_GAMMA = True``，
可以为公共角色授予与 GAMMA 角色相同的权限集。如果要允许匿名用户查看看板，这将很有用。
仍然需要对特定数据集进行显式授予，这意味着您需要编辑 ``Public`` 角色并将 Public 数据源手动添加到角色。


按数据源访问管理 Gamma
-------------------------------------
以下是向用户提供仅访问特定数据集的方法。首先，请确保具有有限访问权限的用户具有[仅]分配给他们的 Gamma 角色。
其次，创建一个新角色（``Menu -> Security -> List Roles``），然后单击 ``+`` 号。

.. image:: images/create_role.png
   :scale: 50 %


这个新窗口使您可以给这个新角色命名，将其归于用户，然后在 ``Permissions`` 下拉列表中选择表。
要选择要与此角色关联的数据源，只需单击下拉列表，然后使用预输入来搜索表名。

然后，您可以与 Gamma 用户确认他们是否看到与与其角色相关的表相关联的对象（dashboards 和 slices）。


定制
-----------

FAB 公开的权限非常精细，可以进行很大程度的自定义。FAB 自动为创建的每个模型
（can_add，can_delete，can_show，can_edit等）以及每个视图创建许多权限。
最重要的是，Superset 可以公开更详细的权限，例如 ``all_datasource_access``。

我们不建议更改3个基本角色，因为有一组 Superset 所基于的假设。
您可以创建自己的角色，并将它们与现有的角色结合起来。

权限
"""""""""""

角色由一组权限组成，并且 Superset 具有许多类别的权限。以下是权限的不同类别：

- **Model & action**: 模型是 ``Dashboard`` ，``Slice`` 或 ``User`` 之类的实体。每个模型都有一组固定的权限，
  例如 ``can_edit`` ， ``can_show`` ， ``can_delete`` ， ``can_list`` ， ``can_add`` 等。
  通过将 ``can_delete on Dashboard`` 添加到角色，并将该角色授予用户，该用户将能够删除看板。
- **Views**: 视图是单个网页，例如 ``explore`` 视图或 ``SQL Lab`` 视图。授予用户后，他/她将在其菜单项中看到该视图，
  并能够加载该页面。
- **Data source**: 对于每个数据源，都会创建一个权限。如果用户未授予 ``all_datasource_access`` 权限，
  则该用户将只能查看 Slices 或浏览授予他们的数据源。
- **Database**: 授予对数据库的访问权限后，用户便可以访问该数据库中的所有数据源，
  并且只要已授予该用户特定于 SQL Lab 的权限，该用户就可以在SQL Lab中查询该数据库。


限制对一部分数据源的访问
""""""""""""""""""""""""""""""""""""""""""""""

最好的方法可能是给用户 ``Gamma`` 加上一个或多个其他角色，这些角色将增加对特定数据源的访问。
我们建议您为每个访问配置文件创建单独的角色。假设您的财务部门的人员可能有权访问一组数据库和数据源，
并且这些权限可以合并为一个角色。然后，需要将具有此配置文件的用户归因于 ``Gamma``，
作为他们可以访问的模型和视图的基础，以及将 ``Finance`` 角色作为对数据对象权限的集合。


一个用户可以担任多个角色，因此可以向财务主管授予 ``Gamma`` ，``Finance`` 角色，
也可以授予另一个财务主管 ``Executive`` 角色，这些角色收集了一组数据源，这些数据源仅对执行人员可用。
当查看其看板列表时，该用户将仅根据其角色和权限查看其有权访问的看板列表。