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

使用 Apache Superset 研究数据
===================================

在本教程中，我们将通过研究一个真实的数据集来介绍 Apache Superset 中的关键概念，
该数据集包含一个英国组织的员工在2011年的飞行。每趟航班的信息如下:

-  旅客部门。在本教程中，部门已重命名为“橙色”，“黄色”和“紫色”。
-  机票费用。
-  旅游舱（经济舱，高级经济舱，商务舱和头等舱）。
-  票是单张还是回程。
-  旅行日期。
-  有关始发地和目的地的信息。
-  起点和终点之间的距离，以公里（km）为单位。

启用上传 CSV 功能
-----------------------------------

您可能需要启用将 CSV 上传到数据库的功能。以下部分说明了如何为示例数据库启用此功能。

在顶部菜单中，`Sources --> Databases`。 在列表中找到 :guilabel:`examples` ，然后选择编辑记录按钮。

.. image:: images/usertutorial/edit-record.png

在 :guilabel:`Edit Database` 页面中，选中 :guilabel:`Allow Csv Upload` 复选框。

最后，通过选择页面底部的 :guilabel:`Save` 进行保存。

获取并加载数据
------------------------------

从 `Github <https://raw.githubusercontent.com/apache-superset/examples-data/master/tutorial_flights.csv>`__ 将本教程的数据下载到您的计算机。

在顶部菜单中，选择 :menuselection:`Sources --> Upload a CSV` 。

.. image:: images/usertutorial/upload_a_csv.png

然后，输入 :guilabel:`Table name` 为 `tutorial_flights` ，然后从计算机中选择 :guilabel:`CSV file` 。

.. image:: images/usertutorial/csv_to_database_configuration.png

接下来，在 :guilabel:`Parse Dates` 字段中输入文本 `Travel Date`。

.. image:: images/usertutorial/parse_dates_column.png

将所有其他选项保留为默认设置，然后选择页面底部的 :guilabel:`Save` 。

表格可视化
-------------------

在本部分中，我们将创建我们的第一个可视化文件：一个表格，以显示航班数量和每次旅行的费用。

要创建新图表，请选择 :menuselection:`New --> Chart`。

.. image:: images/usertutorial/add_new_chart.png

在 `Create a new chart` 对话框中，从 :guilabel:`Chose a datasource` 下拉列表中选择 :guilabel:`tutorial_flights` 。

.. image:: images/usertutorial/chose_a_datasource.png

接下来，选择可视化类型为 :guilabel:`Table` 。

.. image:: images/usertutorial/select_table_visualization_type.png

然后，选择 :guilabel:`Create new chart` 以进入图表视图。

默认情况下，Apache Superset 仅显示数据的最后一周：在我们的示例中，我们要查看数据集中的所有数据。 
没问题 - 在 :guilabel:`Time` 部分中，通过选择 :guilabel:`Last week` 然后将选择更改
为 :guilabel:`No filter`，从而在 :guilabel:`Time range` 上删除过滤器，最后按 :guilabel:`OK` 确认选择。

.. image:: images/usertutorial/no_filter_on_time_filter.png

现在，我们要使用 :guilabel:`Group by` 选项在表中指定行。由于在此示例中，
我们想了解不同的旅行舱位，因此在此菜单中选择 :guilabel:`Travel Class` 。

接下来，我们可以使用 :guilabel:`Metrics` 选项指定我们希望在表格中看到的指标。
:guilabel:`Count(*)` 已经存在，它表示表中的行数（在这种情况下，它对应于航班数，因为我们每个航班都有一行）。 
要添加成本，请在 :guilabel:`Metrics` 中选择 :guilabel:`Cost`。:guilabel:`Save` 默认的聚合选项，即对列求和。

.. image:: images/usertutorial/sum_cost_column.png

最后，选择 :guilabel:`Run Query` 以查看表的结果。

.. image:: images/usertutorial/tutorial_table.png

恭喜，您已经在 Apache Superset 中创建了您的第一个可视化！

要保存可视化，请单击屏幕左上方的 :guilabel:`Save` 。选择 :guilabel:`Save as` 选项，
然后将图表名称输入为 Tutorial Table（您将能够通过 :guilabel:`Charts` 屏再次找到它，可从顶部菜单访问）。
同样，选择 :guilabel:`Add to new dashboard` ，然后输入 `Tutorial Dashboard`。
最后，选择 :guilabel:`Save & go to dashboard`。

.. image:: images/usertutorial/save_tutorial_table.png

看板基础
----------------

接下来，我们将探索看板界面。如果您已按照上一节进行操作，则应该已经打开了看板。
否则，您可以通过选择顶部菜单上的 :guilabel:`Dashboards` ，
然后从看板列表中选择 :guilabel:`Tutorial dashboard` 来导航到看板。

在该看板上，您应该看到在上一节中创建的表。选择 :guilabel:`Edit dashboard` ，然后将鼠标悬停在表格上。
通过选择表格的右下角（光标也会改变），您可以通过拖放来调整其大小。

.. image:: images/usertutorial/resize_tutorial_table_on_dashboard.png

最后，通过选择右上角的 :guilabel:`Save changes` 来保存更改。

透视表
-----------

在本节中，我们将使用更复杂的可视化数据透视表扩展分析。
在本节的最后，您将创建了一个表，该表显示按部门，按旅行舱划分的前六个月的每月航班支出。

和以前一样，通过选择顶部菜单上的 :menuselection:`New --> Chart` 来创建新的可视化。
再次选择 tutorial_flights 作为数据源，然后单击可视化类型以转到可视化菜单。
选择 :guilabel:`Pivot Table` 可视化（可以通过在搜索框中输入文本进行筛选），
然后 :guilabel:`Create a new chart`。

在 :guilabel:`Time` 部分中，将 Time Column 保留为 Travel Date
（由于我们的数据集中只有一个时间列，因此会自动选择该列）。然后，
将 :guilabel:`Time Grain` 选择为 month，因为每天的数据过于精细而无法查看模式。
然后，通过单击 :guilabel:`Time Range` 部分中的 Last week，
将时间范围选择为2011年的前六个月，然后在 :guilabel:`Custom` 中直接输入日期
或使用日历小部件分别选择 :guilabel:`Start / end`, 1\ :sup:`st` January 2011 和
30\ :sup:`th` June 2011（通过选择月份名称然后选择年份，可以更快地移动到较远的日期）。

.. image:: images/usertutorial/select_dates_pivot_table.png

接下来，在 :guilabel:`Query` 部分中，删除默认的 COUNT(*) 并添加 Cost，以保持默认的 SUM aggregate。
请注意，Apache Superset 将通过列表左侧列上的符号指示度量标准的类型
（ABC 表示字符串，＃ 表示数字，a clock face 表示时间等等）。

在 :guilabel:`Group by` 中选择 :guilabel:`Time`：
这将自动使用 Time Column 和我们在 Time 部分中定义的 Time Grain selections。

在 :guilabel:`Columns` 中，首先选择 :guilabel:`Department`，
然后选择 :guilabel:`Travel Class`。全部设置好了 - 让我们 :guilabel:`Run Query` 来查看一些数据！

.. image:: images/usertutorial/tutorial_pivot_table.png

您应该在行中看到 months，在列中看到 Department 和 Travel Class。
在我们的看板中，选择 :guilabel:`Save` ，
命名图表 Tutorial Pivot 并使用
:guilabel:`Add chart to existing dashboard`
选择 :guilabel:`Tutorial Dashboard`,
最后 :guilabel:`Save & go to dashboard`。

折线图
----------

在本节中，我们将创建一个折线图，以了解整个数据集上按月计算的机票平均价格。
和之前一样，选择 :menuselection:`New --> Chart` ,然后
:guilabel:`tutorial_flights` 作为数据源和 :guilabel:`Line Chart` 作为可视化类型。

与以前一样，在 Time 部分中，将 :guilabel:`Time Column` 保留为 Travel Date，
将 :guilabel:`Time Grain` 保留为 month，但是这次在 :guilabel:`Time range` 中
选择 :guilabel:`No filter`，因为我们要查看整个数据集。

在 :guilabel:`Metrics` 中，删除默认的 :guilabel:`COUNT(*)` 并添加 :guilabel:`Cost`。
这一次，我们希望更改这个列的聚合方式，以显示平均值:
我们可以通过在 :guilabel:`aggregate` 下拉菜单中选择 :guilabel:`AVG` 来实现这一点。

.. image:: images/usertutorial/average_aggregate_for_cost.png

接下来，选择 :guilabel:`Run Query` 来显示图表上的数据。

这个看起来怎么样? 嗯，我们可以看到平均成本在12月上升。
但是，将单程票和回程票合并在一起可能没有意义，而是为每种类型的票分别显示两行。

为此，请在 :guilabel:`Group by` 框中选择 :guilabel:`Ticket Single or Return`，
然后再次选择 :guilabel:`Run Query`。真好！我们可以看到，平均而言，单程机票比往返机票便宜，
而12月的大幅上涨是由往返机票造成的。

我们的图表看起来已经不错，但让我们通过转到左侧窗格上的 :guilabel:`Customize` 标签自定义更多内容。
在此窗格中，尝试更改 :guilabel:`Color Scheme` ，通过在 :guilabel:`Show Range Filter` 下拉列表中
选择 No 来删除范围过滤器，并使用 :guilabel:`X Axis Label` 和 :guilabel:`Y Axis Label` 添加一些标签。

.. image:: images/usertutorial/tutorial_line_chart.png

完成后，:guilabel:`Save` 为 Tutorial Line Chart，使用 :guilabel:`Add chart to
existing dashboard` 将该图表添加到 Tutorial Dashboard 看板上，然后 :guilabel:`Save & go to dashboard`。

标记
------

在本节中，我们将向看板添加一些文本。
如果您已经在那里了，您可以通过在顶部菜单中选择 :guilabel:`Dashboards` 来导航到 dashboard，
然后从 dashboards 列表中选择 :guilabel:`Tutorial dashboard`。
通过选择 :guilabel:`Edit dashboard` 进入编辑模式。

在 Insert 组件窗格中，在看板上拖放一个 :guilabel:`Markdown` 框。
寻找蓝色的线，它指示了方框的定位位置。

.. image:: images/usertutorial/blue_bar_insert_component.png

现在，要编辑文本，选择方框。您可以以 markdown 格式输入文本(有关此格式的详细信息，请参阅 `this Markdown
Cheatsheet <https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet>`__ )。
您可以使用框顶部的菜单在 :guilabel:`Edit` 和 :guilabel:`Preview` 之间切换。

.. image:: images/usertutorial/markdown.png

要退出，请选择看板的任何其他部分。最后，不要忘记使用 :guilabel:`Save changes` 来保存更改。

筛选盒
----------

在本节中，您将学习如何向看板添加过滤器。具体来说，我们将创建一个过滤器，
使我们能够查看那些离开特定国家/地区的航班。

可以像创建其他任何可视化类型一样创建 filter box 可视化类型
通过选择 :menuselection:`New --> Chart` ，
然后选择 :guilabel:`tutorial_flights` 作为数据源，
然后选择 :guilabel:`Filter Box` 作为可视化类型

首先，在 :guilabel:`Time` 部分，通过选择 :guilabel:`No filter`，从 :guilabel:`Time range` 选择中删除过滤器。

接下来，在 :guilabel:`Filters Configurations` 中，首先通过选择加号添加新的过滤器，
然后通过选择铅笔图标来编辑新创建的过滤器。

对于我们的用例，以字母顺序显示国家/地区列表是最有意义的。
首先，在 :guilabel:`Origin Country` 列中输入所有其他选项，然后选择 :guilabel:`Run Query`。
这为我们提供了过滤器的预览。

接下来，通过取消选中 :guilabel:`Date Filter` 复选框来删除日期过滤器。

.. image:: images/usertutorial/filter_on_origin_country.png


最后，选择 :guilabel:`Save`，将图表命名为 Tutorial Filter，
将图表添加到我们现有的 Tutorial Dashboard 中，然后单击 :guilabel:`Save & go to dashboard`。
进入看板后，尝试使用过滤器仅显示从英国起飞的那些航班 - 您将看到过滤器已应用于看板上的所有其他可视化对象。

发布你的看板
-------------------------

如果您已按照上一节中概述的所有步骤进行操作，则应该拥有一个如下所示的看板。
如果愿意，可以通过选择 :guilabel:`Edit dashboard` 并拖放来重新排列看板的元素。

如果您想使看板可供其他用户使用，只需在左上方的看板标题旁边选择 :guilabel:`Draft`，
即可将看板更改为 :guilabel:`Published` 状态。您也可以通过选择星号来收藏此看板。

.. image:: images/usertutorial/publish_dashboard.png

进一步扩展您的看板
-----------------------------

在以下各节中，我们将介绍更高级的 Apache Superset 主题。

注解
-----------

注释使您可以向图表添加其他上下文。在本节中，我们将为上一节中的教程折线图添加注释。
具体来说，我们将添加一些日期，以回应英国民航局因冰岛格林斯沃特火山喷发而取消某些航班的日期
（2011年5月23日至25日）。

首先，通过导航到 :menuselection:`Manage --> Annotation Layers` 来添加注释层。
通过选择绿色加号来添加新记录，以添加新注释层。 输入名称火山爆发并且保存。
我们可以使用这一层来引用许多不同的注释。

接下来，通过导航到 :menuselection:`Manage --> Annotations` 添加一个注释，然后通过选择绿色加号创建一个新的注释。
然后，选择 :guilabel:`Volcanic Eruptions` 层，
添加简短描述 Grímsvötn 和喷发日期（2011年5月23日至25日），然后最后保存。

.. image:: images/usertutorial/edit_annotation.png

然后，通过转到 :guilabel:`Charts` ，然后从列表中选择 :guilabel:`Tutorial Line Chart`，导航到折线图。
接下来，转到 :guilabel:`Annotations and Layers` 部分，
然后选择 :guilabel:`Add Annotation Layer` 。在此对话框中：

- 将该层命名为 `Volcanic Eruptions`
- 将 :guilabel:`Annotation Layer Type` 更改为 :guilabel:`Event`
- 将 :guilabel:`Annotation Source` 设置为 :guilabel:`Superset annotation`
- 将 :guilabel:`Annotation Layer` 指定为 :guilabel:`Volcanic Eruptions`

.. image:: images/usertutorial/annotation_settings.png

选择 :guilabel:`Apply` 以查看图表上显示的注释。

.. image:: images/usertutorial/annotation.png

如果需要，可以通过更改 :guilabel:`Display configuration` 部分中的设置来更改批注的外观。
否则，选择确定，最后选择 :guilabel:`Save` 以保存图表。如果保留默认选择以覆盖图表，
则注释将保存到图表中，并自动出现在 Tutorial Dashboard 中。

高级分析
------------------

在本节中，我们将探讨 Apache Superset 的高级分析功能，它允许您对数据应用额外的转换。
这三种类型的转换是:

移动平均数
  选择滚动窗口 [#f1]_ ，然后对其进行计算（均值，总和或标准差）。
  第四个选项 cumsum 计算序列 [#f2]_ 的累加和。

时间比较
  及时移动数据，并可选地应用计算来比较移动的数据和实际数据(例如，计算两者之间的绝对差异)。

Python 函数
  使用多种方法之一对数据进行重新采样 [#f3]_ 。

设置基础图表
~~~~~~~~~~~~~~~~~~~~~~~~~

在本部分中，我们将建立一个基础图表，然后将其应用于其他 Advanced Analytics 功能。
首先使用相同的 :guilabel:`tutorial_flights` 数据源和 :guilabel:`Line Chart` 可视化类型创建一个新图表。
在时间部分中，将 :guilabel:`Time Range` 设置为 1\ :sup:`st` October 2011 和 31\ :sup:`st` October 2011。

接下来，在查询部分，将 :guilabel:`Metrics` 更改为 :guilabel:`Cost` 总和。
选择 :guilabel:`Run Query` 以显示图表。你应该在2011年10月看到每个月每天的总成本。

.. image:: images/usertutorial/advanced_analytics_base.png

最后，将可视化保存为 Tutorial Advanced Analytics Base，将其添加到 Tutorial Dashboard。

滚动平均值
~~~~~~~~~~~~

数据差异很大，因此很难识别任何趋势。我们可以采用的一种方法是显示时间序列的滚动平均值。
为此，请在 :guilabel:`Advanced Analytics` 的 :guilabel:`Moving Average` 小节中
的 :guilabel:`Rolling` 框中选择均值，然后在 Periods 和 Min Periods 中输入7。
周期是滚动周期的长度，表示为 :guilabel:`Time Grain` 的倍数。
在我们的示例中，:guilabel:`Time Grain` 为天，因此滚动周期为7天，
因此，在2011年10月7日，显示的值将与2011年10月的前7天相对应。
最后，通过将 :guilabel:`Min Periods` 指定为7，我们可以确保我们的均值始终以7天计算，
因此我们避免了任何爬坡期。

通过选择 :guilabel:`Run Query` 来显示图表之后，您将看到数据的变量更少，
并且由于排除了爬坡期，该系列将在稍后开始。

.. image:: images/usertutorial/rolling_mean.png

将图表保存为 Tutorial Rolling Mean，并将其添加到 Tutorial Dashboard。

时间比较
~~~~~~~~~~~~~~~

在本节中，我们将比较时间序列中的值与一周前的值。
首先打开 Tutorial Advanced Analytics 基本图表，转到顶部菜单中的 :guilabel:`Charts`，
然后在列表中选择可视化名称（或者，在 Tutorial Dashboard 中找到该图表，
然后从该可视化菜单中选择 Explore chart）。

接下来，在 :guilabel:`Advanced Analytics` 的 :guilabel:`Time Comparison` 子部分中，
通过键入 "minus 1 week" 来输入 :guilabel:`Time Shift`（请注意，此框接受使用自然语言的输入）。
:guilabel:`Run Query` 以查看新图表，该图表有一个具有相同值的附加序列，并且将时间倒退了一周。

.. image:: images/usertutorial/time_comparison_two_series.png

然后，将 :guilabel:`Calculation type` 更改为 :guilabel:`Absolute difference` 并选择 :guilabel:`Run
Query`。现在，我们只能再次看到一个系列，这一次显示了我们之前看到的两个系列之间的差异。

.. image:: images/usertutorial/time_comparison_absolute_difference.png

Save the chart as Tutorial Time Comparison and add it to the Tutorial
Dashboard.

Resampling the data
~~~~~~~~~~~~~~~~~~~

In this section, we'll resample the data so that rather than having
daily data we have weekly data. As in the previous section, reopen the
Tutorial Advanced Analytics Base chart.

Next, in the :guilabel:`Python Functions` subsection of
:guilabel:`Advanced Analytics`, enter 7D, corresponding to seven days,
in the :guilabel:`Rule` and median as the :guilabel:`Method` and show
the chart by selecting :guilabel:`Run Query`.

.. image:: images/usertutorial/resample.png

Note that now we have a single data point every 7 days. In our case, the
value showed corresponds to the median value within the seven daily data
points. For more information on the meaning of the various options in
this section, refer to the `Pandas
documentation <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.resample.html>`__.

Lastly, save your chart as Tutorial Resample and add it to the Tutorial
Dashboard. Go to the tutorial dashboard to see the four charts side by
side and compare the different outputs.

.. rubric:: Footnotes

.. [#f1] See the Pandas `rolling method documentation <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.rolling.html>`_ for more information.
.. [#f2] See the Pandas `cumsum method documentation <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.cumsum.html>`_ for more information.
.. [#f3] See the Pandas `resample method documentation <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.resample.html>`_ for more information.

