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

创建您的第一个看板
=============================

本教程的目标读者是希望在 Superset 中创建图表和看板的人。
我们将向您展示如何将 Superset 连接到一个新数据库并在该数据库中配置一个表以进行分析。
您还将研究公开的数据，并将可视化添加到看板中，以获得端到端用户体验的感觉。

连接到新数据库
----------------------------

我们假设您已经配置了一个数据库，并且可以从运行 Superset 的实例连接到它。
如果您只是测试 Superset 并希望研究示例数据，您可以将一些`示例 PostgreSQL 数据集<https://wiki.postgresql.org/wiki/Sample_Databases>`_加载到一个新的数据库中，
或者配置我们在这里使用的`示例天气数据 <https://github.com/dylburger/noaa-ghcn-weather-data>`_。

在 **Sources** 菜单下，选择 *Databases* 选项:

.. image:: images/tutorial/tutorial_01_sources_database.png
   :scale: 70%

在结果页面上，单击右上角的绿色加号:

.. image:: images/tutorial/tutorial_02_add_database.png
   :scale: 70%

您可以在这个页面上配置许多高级选项，但是对于本演练，您只需要做**两件事**:

1. 命名您的数据库连接:

.. image:: images/tutorial/tutorial_03_database_name.png
   :scale: 70%

2. 提供 SQLAlchemy 连接 URI 并测试连接:

.. image:: images/tutorial/tutorial_04_sqlalchemy_connection_string.png
   :scale: 70%

这个例子显示了测试天气数据库的连接字符串。正如 URI 下面的文本中所指出的，
您应该参考 SQLAlchemy 文档，了解如何为目标数据库`创建新的连接 URI<https://docs.sqlalchemy.org/en/rel_1_2/core/engines.html#database-urls>`_。

单击 **Test Connection** 按钮，确认端到端工作的内容。一旦 Superset 可以成功连接和认证，
你应该看到这样的弹出窗口:

.. image:: images/tutorial/tutorial_05_connection_popup.png
   :scale: 50%

此外，你还应该看到 Superset 可以从你连接的 schema 中读取的表列表，在页面的底部:

.. image:: images/tutorial/tutorial_06_list_of_tables.png
   :scale: 70%

如果连接看起来正常，点击页面底部的 **Save** 按钮保存配置:

.. image:: images/tutorial/tutorial_07_save_button.png
   :scale: 70%

添加新表
------------------

既然已经配置了数据库，就需要向 Superset 添加你想要查询的特定的表。

在 **Sources** 菜单下，选择 *Tables* 选项:

.. image:: images/tutorial/tutorial_08_sources_tables.png
   :scale: 70%

在结果页面上，单击左上角附近的绿色加号:

.. image:: images/tutorial/tutorial_09_add_new_table.png
   :scale: 70%

你只需要几条信息来添加一个新的表到 Superset:

* 表的名称

.. image:: images/tutorial/tutorial_10_table_name.png
   :scale: 70%

* **Database** 下拉菜单中的目标数据库(即您刚刚添加的那个)

.. image:: images/tutorial/tutorial_11_choose_db.png
   :scale: 70%

* 数据库模式也是可选的。如果表存在于 “default” schema 中(例如 PostgreSQL 或 Redshift 中的 *public* schema)，您可以将 schema 字段留空。

点击 **Save** 按钮保存配置:

.. image:: images/tutorial/tutorial_07_save_button.png
   :scale: 70%

当重定向回表列表时，您应该会看到一条消息，指示您的表已经创建:

.. image:: images/tutorial/tutorial_12_table_creation_success_msg.png
   :scale: 70%

此消息还指示您编辑表配置。现在，我们将编辑配置中有限的一部分 — 只是为了让您入门 — 剩下的部分留给更高级的教程。

点击你创建的表格旁边的编辑按钮:

.. image:: images/tutorial/tutorial_13_edit_table_config.png
   :scale: 70%

在结果页面上，单击 **List Table Column** 选项卡。在这里，您将定义在研究数据时使用表的特定列的方式。
我们将通过这些选项来描述它们的用途:

* 如果您希望用户根据特定字段对指标进行分组，请将其标记为 **Groupable**。
* 如果需要对特定字段进行筛选，请将其标记为 **Filterable**。
* 这个字段是你想要明确计数的字段吗?选中 **Count Distinct** 框。
* 您是想要求和的度量，还是得到基本的汇总统计信息? **Sum** 、 **Min** 和 **Max** 列将有所帮助。
* **is temporal** 字段应该检查任何日期或时间字段。我们一会儿会讲到它是如何在分析中表现出来的。

下面是我们如何为天气数据配置字段。即使是天气测量(降水、降雪等)，也应该根据以下值进行分组和筛选:

.. image:: images/tutorial/tutorial_14_field_config.png

与上面的配置一样，单击 **Save** 按钮来保存这些设置。

探索你的数据
-------------------

要开始研究数据，只需单击在可用表列表中创建的表名:

.. image:: images/tutorial/tutorial_15_click_table_name.png

默认情况下，你会看到一个表格视图:

.. image:: images/tutorial/tutorial_16_datasource_chart_type.png

让我们遍历一个基本查询来获得表中所有记录的计数。首先，我们需要更改 **Since** 过滤器来捕获数据的范围。
你可以使用简单的短语来应用这些过滤器，比如“3年前”:

.. image:: images/tutorial/tutorial_17_choose_time_range.png

时间的上限，**Until** 过滤器，默认为 "now"，这可能是你想要的，也可能不是。

查找 **GROUP BY** header 下的指标部分，并开始键入 “Count”
- 你会看到一个匹配你输入内容的指标列表:

.. image:: images/tutorial/tutorial_18_choose_metric.png

选择 *COUNT(\*)* 指标, 然后点击探索顶部附近的绿色 **Query** 按钮::

.. image:: images/tutorial/tutorial_19_click_query.png

您将在表格中看到结果:

.. image:: images/tutorial/tutorial_20_count_star_result.png

让我们通过 *weather_description* 字段对其进行分组，以通过将其添加到 *Group by* 部分
获得记录的天气类型的记录计数:

.. image:: images/tutorial/tutorial_21_group_by.png

并运行查询:

.. image:: images/tutorial/tutorial_22_group_by_result.png

让我们来找一个更有用的数据点: 2015年气温最高的10次和地点。

我们将 *weather_description* 替换为 *latitude* ，*longitude* 和 *measurement_date* 
在 *Group by* 中:

.. image:: images/tutorial/tutorial_23_group_by_more_dimensions.png

用 *max__measurement_flag* 替换 *COUNT(\*)*:

.. image:: images/tutorial/tutorial_24_max_metric.png

*max__measurement_flag* 指标是在我们在 **Max** 和 *measurement_flag* 字段旁边选中方框时创建的，
这表明该字段是数值型的，我们希望根据特定字段分组时找到它的最大值。


在我们的例子中，*measurement_flag* 是测量的值，它显然取决于测量的类型(研究人员记录了降水和温度的不同值)。
因此，我们必须只对 *weather_description* 等于“最高温度”的记录进行筛选，我们在 explore 底部的
**Filters** 部分进行了如下操作:

.. image:: images/tutorial/tutorial_25_max_temp_filter.png

最后，由于我们只关心前10个度量值，所以我们使用 **Options** 标题下的 *Row limit* 选项将结果限制为10条记录:

.. image:: images/tutorial/tutorial_26_row_limit.png

点击 **Query**，得到如下结果:

.. image:: images/tutorial/tutorial_27_top_10_max_temps.png

在这个数据集中，最高温度以十分之一摄氏度记录。1370的最高值，测量在 Nevada 中部，等于137 C，或大约 278 华氏度，
这个值不太可能被正确记录。我们已经能够用超集来研究一些异常值，但这只是我们所能做的事情的皮毛。

您可能想要用这个度量做更多的事情:

* 默认的格式显示类似 1.37k 的值，这可能会让一些用户难以阅读。您可能希望看到完整的、逗号分隔的值。
  您可以通过编辑任何度量的配置来更改其格式(*Edit Table Config > List Sql Metric > Edit Metric > D3Format*)

* 此外，您可能希望看到的温度测量单位是摄氏度，而不是十分之一度。或者你可能想把温度转换成华氏温度。
  您可以更改针对数据库执行的SQL，将逻辑放入度量本身(Edit Table Config > List Sql Metric > Edit Metric > SQL Expression)

现在，让我们创建这些数据的更好的可视化，并将其添加到看板中。

我们将图表类型改为 "Distribution - Bar Chart":

.. image:: images/tutorial/tutorial_28_bar_chart.png

Our filter on Maximum temperature measurements was retained, but the query and 
formatting options are dependent on the chart type, so you’ll have to set the 
values again:

.. image:: images/tutorial/tutorial_29_bar_chart_series_metrics.png

You should note the extensive formatting options for this chart: the ability to 
set axis labels, margins, ticks, etc. To make the data presentable to a broad 
audience, you’ll want to apply many of these to slices that end up in dashboards. 
For now, though, we run our query and get the following chart:

.. image:: images/tutorial/tutorial_30_bar_chart_results.png
   :scale: 70%

Creating a slice and dashboard
------------------------------

This view might be interesting to researchers, so let’s save it. In Superset, 
a saved query is called a **Slice**. 

To create a slice, click the **Save as** button near the top-left of the 
explore:

.. image:: images/tutorial/tutorial_19_click_query.png

A popup should appear, asking you to name the slice, and optionally add it to a 
dashboard. Since we haven’t yet created any dashboards, we can create one and 
immediately add our slice to it. Let’s do it:

.. image:: images/tutorial/tutorial_31_save_slice_to_dashboard.png
   :scale: 70%

Click Save, which will direct you back to your original query. We see that 
our slice and dashboard were successfully created:

.. image:: images/tutorial/tutorial_32_save_slice_confirmation.png
   :scale: 70%

Let’s check out our new dashboard. We click on the **Dashboards** menu:

.. image:: images/tutorial/tutorial_33_dashboard.png

and find the dashboard we just created:

.. image:: images/tutorial/tutorial_34_weather_dashboard.png

Things seemed to have worked - our slice is here!

.. image:: images/tutorial/tutorial_35_slice_on_dashboard.png
   :scale: 70%

But it’s a bit smaller than we might like. Luckily, you can adjust the size 
of slices in a dashboard by clicking, holding and dragging the bottom-right 
corner to your desired dimensions:

.. image:: images/tutorial/tutorial_36_adjust_dimensions.gif
   :scale: 120%

After adjusting the size, you’ll be asked to click on the icon near the 
top-right of the dashboard to save the new configuration.

Congrats! You’ve successfully linked, analyzed, and visualized data in Superset. 
There are a wealth of other table configuration and visualization options, so 
please start exploring and creating slices and dashboards of your own.
