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

This example shows the connection string for our test weather database. 
As noted in the text below the URI, you should refer to the SQLAlchemy 
documentation on 
`creating new connection URIs <https://docs.sqlalchemy.org/en/rel_1_2/core/engines.html#database-urls>`_
for your target database.

Click the **Test Connection** button to confirm things work end to end. 
Once Superset can successfully connect and authenticate, you should see 
a popup like this:

.. image:: images/tutorial/tutorial_05_connection_popup.png
   :scale: 50%

Moreover, you should also see the list of tables Superset can read from 
the schema you’re connected to, at the bottom of the page:

.. image:: images/tutorial/tutorial_06_list_of_tables.png
   :scale: 70%

If the connection looks good, save the configuration by clicking the **Save** 
button at the bottom of the page:

.. image:: images/tutorial/tutorial_07_save_button.png
   :scale: 70%

Adding a new table
------------------

Now that you’ve configured a database, you’ll need to add specific tables 
to Superset that you’d like to query.

Under the **Sources** menu, select the *Tables* option:

.. image:: images/tutorial/tutorial_08_sources_tables.png
   :scale: 70%

On the resulting page, click on the green plus sign, near the top left:

.. image:: images/tutorial/tutorial_09_add_new_table.png
   :scale: 70%

You only need a few pieces of information to add a new table to Superset:

* The name of the table

.. image:: images/tutorial/tutorial_10_table_name.png
   :scale: 70%

* The target database from the **Database** drop-down menu (i.e. the one 
  you just added above)

.. image:: images/tutorial/tutorial_11_choose_db.png
   :scale: 70%

* Optionally, the database schema. If the table exists in the “default” schema 
  (e.g. the *public* schema in PostgreSQL or Redshift), you can leave the schema 
  field blank.

Click on the **Save** button to save the configuration:

.. image:: images/tutorial/tutorial_07_save_button.png
   :scale: 70%

When redirected back to the list of tables, you should see a message indicating 
that your table was created:

.. image:: images/tutorial/tutorial_12_table_creation_success_msg.png
   :scale: 70%

This message also directs you to edit the table configuration. We’ll edit a limited 
portion of the configuration now - just to get you started - and leave the rest for 
a more advanced tutorial.

Click on the edit button next to the table you’ve created:

.. image:: images/tutorial/tutorial_13_edit_table_config.png
   :scale: 70%

On the resulting page, click on the **List Table Column** tab. Here, you’ll define the 
way you can use specific columns of your table when exploring your data. We’ll run 
through these options to describe their purpose:

* If you want users to group metrics by a specific field, mark it as **Groupable**.
* If you need to filter on a specific field, mark it as **Filterable**.
* Is this field something you’d like to get the distinct count of? Check the **Count 
  Distinct** box.
* Is this a metric you want to sum, or get basic summary statistics for? The **Sum**, 
  **Min**, and **Max** columns will help.
* The **is temporal** field should be checked for any date or time fields. We’ll cover 
  how this manifests itself in analyses in a moment.

Here’s how we’ve configured fields for the weather data. Even for measures like the 
weather measurements (precipitation, snowfall, etc.), it’s ideal to group and filter 
by these values:

.. image:: images/tutorial/tutorial_14_field_config.png

As with the configurations above, click the **Save** button to save these settings.

Exploring your data
-------------------

To start exploring your data, simply click on the table name you just created in 
the list of available tables:

.. image:: images/tutorial/tutorial_15_click_table_name.png

By default, you’ll be presented with a Table View:

.. image:: images/tutorial/tutorial_16_datasource_chart_type.png

Let’s walk through a basic query to get the count of all records in our table. 
First, we’ll need to change the **Since** filter to capture the range of our data. 
You can use simple phrases to apply these filters, like "3 years ago":

.. image:: images/tutorial/tutorial_17_choose_time_range.png

The upper limit for time, the **Until** filter, defaults to "now", which may or may 
not be what you want.

Look for the Metrics section under the **GROUP BY** header, and start typing "Count" 
- you’ll see a list of metrics matching what you type:

.. image:: images/tutorial/tutorial_18_choose_metric.png

Select the *COUNT(\*)* metric, then click the green **Query** button near the top 
of the explore:

.. image:: images/tutorial/tutorial_19_click_query.png

You’ll see your results in the table:

.. image:: images/tutorial/tutorial_20_count_star_result.png

Let’s group this by the *weather_description* field to get the count of records by 
the type of weather recorded by adding it to the *Group by* section:

.. image:: images/tutorial/tutorial_21_group_by.png

and run the query:

.. image:: images/tutorial/tutorial_22_group_by_result.png

Let’s find a more useful data point: the top 10 times and places that recorded the 
highest temperature in 2015.

We replace *weather_description* with *latitude*, *longitude* and *measurement_date* in the 
*Group by* section:

.. image:: images/tutorial/tutorial_23_group_by_more_dimensions.png

And replace *COUNT(\*)* with *max__measurement_flag*:

.. image:: images/tutorial/tutorial_24_max_metric.png

The *max__measurement_flag* metric was created when we checked the box under **Max** and 
next to the *measurement_flag* field, indicating that this field was numeric and that 
we wanted to find its maximum value when grouped by specific fields.

In our case, *measurement_flag* is the value of the measurement taken, which clearly 
depends on the type of measurement (the researchers recorded different values for 
precipitation and temperature). Therefore, we must filter our query only on records 
where the *weather_description* is equal to "Maximum temperature", which we do in 
the **Filters** section at the bottom of the explore:

.. image:: images/tutorial/tutorial_25_max_temp_filter.png

Finally, since we only care about the top 10 measurements, we limit our results to 
10 records using the *Row limit* option under the **Options** header:

.. image:: images/tutorial/tutorial_26_row_limit.png

We click **Query** and get the following results:

.. image:: images/tutorial/tutorial_27_top_10_max_temps.png

In this dataset, the maximum temperature is recorded in tenths of a degree Celsius. 
The top value of 1370, measured in the middle of Nevada, is equal to 137 C, or roughly 
278 degrees F. It’s unlikely this value was correctly recorded. We’ve already been able 
to investigate some outliers with Superset, but this just scratches the surface of what 
we can do.

You may want to do a couple more things with this measure:

* The default formatting shows values like 1.37k, which may be difficult for some 
  users to read. It’s likely you may want to see the full, comma-separated value. 
  You can change the formatting of any measure by editing its config (*Edit Table 
  Config > List Sql Metric > Edit Metric > D3Format*)
* Moreover, you may want to see the temperature measurements in plain degrees C, 
  not tenths of a degree. Or you may want to convert the temperature to degrees 
  Fahrenheit. You can change the SQL that gets executed against the database, baking 
  the logic into the measure itself (*Edit Table Config > List Sql Metric > Edit 
  Metric > SQL Expression*)

For now, though, let’s create a better visualization of these data and add it to 
a dashboard.

We change the Chart Type to "Distribution - Bar Chart":

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
