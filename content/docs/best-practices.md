---
title: "Hive Best Practices"
date: 2020-09-16T04:01:38+05:30
draft: false
aliases: [/best-practices.html]
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

Data Producers
--------------

- Always create tables in a database.
- When getting raw data from an initial loading or output of a MapReduce job, create an external table for staging that
  can be used to load an optimized table.
- Don't place data for external tables in the database directory.
- Carefully choose the format for storming.

  - **Text** – slow to read and write, compressed files cannot be split, may lead to huge maps.
  - **RCFile** – columns stored as binary blobs, default blocks of 4 MB, only the needed columns read. 
  - **Avro IDL** – full row read by consumers of data.
  - **ORC** – uses type specific encoders, stores statistics, index for skipping rows, and larger blocks (small reads).

- Limit the number of partition: 1000 partitions is much faster than 10000. Nested partitions are almost always wrong
  (pressure on NameNode and HCatalog).
- Compress your data.
- Heavily comment your table definition and schema elements that allows the use of a data discovery tool.
- Append (rather than insert) new columns.
- Mark tables with skewed columns as skewed for efficient querying.
- Use `SORT BY` while storing data for better compression with ORCFile.
- Lay out related tables the same way, partition, and sort order.
- Partition data along natural query boundaries (e.g., date).
- Minimize data shuffle by collocating the most commonly joined data.
- Use RCFile with compression to save storage and to improve performance

Data Consumers
--------------

- Use partition filter on partitioned tables.
- Understand the [differences between SORT BY and ORDER BY](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+SortBy#LanguageManualSortBy-DifferencebetweenSortByandOrderBy). 
- Use skew join for skewed data.
- Avoid	`CROSS` joins.
- Filter out `NULL` values for inner joins.
- Use default parallel execution of Hive.
- **UDFs** - you need to figure out if something special needs to be done for distributable UDAFs.
- Add frequently used commands in `$HOME/.hiverc`. For example: `SET mapred.job.queue.name=unfunded` 
- Use `EXPLAIN` to analyze a query (before executing it).
- Verify the query plan to ensure a single scan through largest table.
- Check the query plan to ensure partition pruning is happening.
- Use at least one `ON` clause in every `JOIN`.
- Tune `io.sort.mb` to avoid spilling.
- Tune `mapred.max.split.size` and `mapred.min.split.size` to ensure a single mapper wave.
- Tune `mapred.reduce.tasks` to an appropriate value based on the map output.
- Give extra memory for map joins like broadcast joins.
- Tune ORC parameters for better read performance.
