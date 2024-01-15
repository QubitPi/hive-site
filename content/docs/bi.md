---
title: "Business Intelligence (BI) / Analytics"
date: 2020-09-17T04:01:38+05:30
draft: false
aliases: [/bi.html]
---

<!---
  Licensed to the Apache Software Foundation (ASF) under one
  or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.
-->

Overview
--------

You can use business intelligence (BI) and data analytics tools such as [Tableau](http://www.tableausoftware.com/) and
[MicroStrategy](https://www.microstrategy.com/us/) with data on Hadoop. These tools access grid data with Hive through
the
[Hortonworks Hive ODBC driver](http://hortonworks.com/wp-content/uploads/2013/04/Hortonworks-Hive-ODBC-Driver-User-Guide.pdf).

For those who are not familiar with Tableau and MicroStrategy, please read the next section that discusses the
differences and strengths of each so you can use the tool that best meets your needs. If you know which tool is right
for you, continue to :ref:`Visualizing Data and Ad Hoc Reporting With Tableau <getting_started-tableau>` or
:ref:`Standard Reporting With MicroStrategy <gs_bi-ms>`.

### HDFS Stack for BI

The diagram below shows how client software such as MicroStrategy and Tableau use the ODBC driver to communicate with
HiveServer2, which in turn, forwards queries to Hive that ultimately are executed as MapReduce functions on Hadoop.

![BI on the Grid](../../images/bi_grid.png)

MicroStrategy Versus Tableau
----------------------------

The general rule is to use MicroStrategy for standardized reporting or Tableau for visualizing data.

### MicroStrategy

#### Strengths

- Great for reporting that has stabilized (not changing much over time).
- Drill-down/through is great for facilitating an interactive discussion with business leaders.
- Report-sharing helps keep the data on the back-end, reducing the emailing of large Excel files.
- Allows for programmatic usage through a Web service API.
- Supports standard reporting.
- Provides access through a Web interface.
- Developer suite for generating reports.

#### Weaknesses

- Requires greater technical knowledge, and the process of creating a new view is clumsy and time-consuming.
- Provides no capability of combining HDFS data with data outside the HDFS.
- Learning to use MicroStrategy is more difficult.

### Tableau 

#### Strengths

- Lets you quickly make sense of millions of rows of data through visualization. 
- Does an excellent job of recognizing facts and dimensions in de-normalized data files (say CSV or Excel) as well as
  connecting to larger databases.
- The learning curve is low, especially if you are comfortable with Excel Pivot Tables or something similar.
- Allows for ad hoc reporting.

#### Weaknesses

- For broader enterprise needs, such as data security and segmentation, heavy duty report customization, or data
  transformation, Tableau is a less comprehensive tool than MicroStrategy.
- The license for using Tableau is expensive.
- You can only use Tableau through the desktop application.
