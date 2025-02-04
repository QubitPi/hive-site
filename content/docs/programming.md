---
title: "Programming Hive"
date: 2020-09-14T04:01:38+05:30
draft: false
aliases: [/programming.html]
---

<!---
  Copyright Jiaqi Liu
 
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at
 
      http://www.apache.org/licenses/LICENSE-2.0
 
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->

Overview
--------

This chapter is compiled from the Hive documentation from Hortonworks, Cloudera, and the book
[Programming Hive](http://www.amazon.com/Programming-Hive-Edward-Capriolo-ebook/dp/B009D76316/ref=sr_1_1?ie=UTF8&qid=1402703076&sr=8-1&keywords=programming+hive)

Hive CLI
--------

> ⚠️ **Deprecation in favor of Beeline CLI**. HiveServer2 (introduced in Hive 0.11) has its own CLI called
> [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93NewCommandLineShell),
> which is a JDBC client based on SQLLine. Due to new development being focused on HiveServer2, Hive CLI will soon be
> deprecated in favor of Beeline.

### Start Beeline

```console
beeline -u <database URL> -n <username> -p <password>
```

### Print Configs Overridden by User or Hive.
 
```console
set;
```
    
### Prints Hadoop and Hive Configs

```console
set -v;
```

### Set Configurations

```console
set <key>=<value>;
```
 
### Add [Resources](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli#LanguageManualCli-HiveResources) to the Hadoop Distributed Cache

```console
add FILE[S] <filepath> <filepath>*;
add JAR[S] <filepath> <filepath>*;
add ARCHIVE[S] <filepath> <filepath>*;
```

### List Databases

```sql
SHOW DATABASES;
```

### List Tables	 

```sql
SHOW TABLES;
```
    
### Truncate Table

#### Through spark-shell

```sql
sql("TRUNCATE TABLE <db>.<table>");
```
    
### Create a Table (Example)

```sql
CREATE TABLE IF NOT EXISTS celebrity (
    object_key String,
    name String
)
COMMENT 'Celebrity Info'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;
```

### Describe a Table

```sql
DESCRIBE <tablename>;
```

For more information:

```sql
DESCRIBE EXTENDED <tablename>;
```

### List Functions	 

```sql
`hive> show functions;`
```

### Describe a Function

```sql
`hive> describe function <functionname>;`
```

HiveQL Basics
-------------

As the section indicates, we're just going to look at some more commonly used HiveQL statements. See the
[Hive DDL Language Manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL) for the comprehensive
HiveQL documentation.

### Databases

#### Creating

##### Syntax

```sql
CREATE (DATABASE|SCHEMA) [IF NOT EXISTS] database_name
    [COMMENT database_comment]
    [LOCATION hdfs_path]
    [WITH DBPROPERTIES (property_name=property_value, ...)];
```

##### Example

```sql
CREATE DATABASE my_db IF NOT EXISTS my_db LOCATION '/tmp/my_databases/';
```

#### Dropping

##### Syntax

```sql
DROP (DATABASE|SCHEMA) [IF EXISTS] database_name [RESTRICT|CASCADE];
```

##### Example

```sql
DROP my_db IF EXISTS my_db;
```

#### Altering

Using the `ALTER DATABASE` command, you can set key-value pairs in the `DBPROPERTIES` associated with a database. No
other metadata about the database can be changed, including its name and directory location.

##### Syntax

```sql
ALTER DATABASE database_name SET DBPROPERTIES (property_name=property_value, ...);
```

##### Example

```sql
ALTER DATABASE stock_portfolio SET DBPROPERTIES ('advisor' = 'Jason Jones');
```

### Tables

#### Data Types

The following table lists the supported data types for columns:

| Data Types | Example                                                                                                                                                                     | Description                                                                                                                                                                                                                            |
|------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TINYINT    | `20`                                                                                                                                                                        | One-byte signed integer                                                                                                                                                                                                                |
| SMALLINT   | `327`                                                                                                                                                                       | Two-byte signed integer                                                                                                                                                                                                                |
| INT        | `214748`                                                                                                                                                                    | Four-byte signed integer                                                                                                                                                                                                               |
| BIGINT     | `9223372036`                                                                                                                                                                | Eight-byte signed integer                                                                                                                                                                                                              |
| FLOAT      | 3.14                                                                                                                                                                        | Single precision floating point                                                                                                                                                                                                        |
| STRING     | Strings are great                                                                                                                                                           | A sequence of characters. The character set can be specified. Single or double quotes can be used                                                                                                                                      |
| BOOLEAN    | `TRUE`                                                                                                                                                                      | The boolean value of either `true` or `false`                                                                                                                                                                                          |
| TIMESTAMP  | 1327882394 (Unix epoch seconds), 1327882394.123456789 (Unix epoch seconds plus nanoseconds), and '2012-02-03 12:34:56.123456789' (JDBC-compliant java.sql.Timestamp format) | Integer, float, or string                                                                                                                                                                                                              |
| BINARY     | --                                                                                                                                                                          | An array of bytes                                                                                                                                                                                                                      |
| ARRAY      | ['pineapples', 'mangos']                                                                                                                                                    | A collection of key-value tuples, where the fields are accessed using array notation (e.g., ['key'])                                                                                                                                   |
| MAP        | ('colors', '7', 'patterns', '10')                                                                                                                                           | A collection of key/value pairs or tuples defined as `Map<string, integer>`                                                                                                                                                            |
| STRUCT     | {'fname':'Tom', 'nickname':'Haymaker', 'lname':'Jiggins'}                                                                                                                   | Analogous to a C struct or an 'object'. Fields can be accessed using the 'dot' notation. For example, if a column name is of type `STRUCT {first STRING; last STRING}`, then the first name field can be referenced using `name.first` |

#### Viewing/Describing

To see all of the tables in a database::

```sql
SHOW TABLES;
```

You can also view the schema of a particular table::

```sql
DESCRIBE yourtable;
```

#### Creating

To create a table, you define a schema with the `CREATE` that specifies rows delimited by commas and the file type to
store the data at the given location.

##### Syntax

```sql
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
  [(col_name data_type [COMMENT col_comment], ...)]
  [COMMENT table_comment]
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
  [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
  [SKEWED BY (col_name, col_name, ...) ON ([(col_value, col_value, ...), ...|col_value, col_value, ...]) [STORED AS DIRECTORIES] (Note: Only available starting with Hive 0.10.0)]
  [
   [ROW FORMAT row_format] [STORED AS file_format]
   | STORED BY 'storage.handler.class.name' [WITH SERDEPROPERTIES (...)]  (Note: Only available starting with Hive 0.6.0)
  ]
  [LOCATION hdfs_path]
  [TBLPROPERTIES (property_name=property_value, ...)]  (Note: Only available starting with Hive 0.6.0)
  [AS select_statement]  
```

##### Example

In this statement, you are defining the schema for the table `search` and storing the data as an ORC File.

```sql
CREATE TABLE search (
    bcookie string, 
    time_stamp int, 
    yuid string, 
    ip string, 
    pg_spaceid string, 
...)
STORED AS orc;
```

When you drop a typical table, the raw data is lost because the directory corresponding to the table in warehouse is
deleted. Thus, to retain the raw data for others to run queries, you should create `"EXTERNAL"` tables.

To create an external table, explicitly use the keyword `EXTERNAL` and point to the location of data while creating
the tables. This will ensure that the data is not moved into a location inside the warehouse directory.

```sql
CREATE EXTERNAL TABLE search (
    bcookie string, 
    time_stamp int, 
    yuid string, 
    ip string, 
    pg_spaceid string, 
...)
STORED AS orc
location '/some-path';
```

### Creating/Adding Partitions

In addition to creating columns for a table, you can create partitions, which allow you to efficiently find rows based
on certain criteria.

##### Syntax

Creating a table with partitions::

```sql
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
    [(col_name data_type [COMMENT col_comment], ...)]
   [COMMENT table_comment]
   [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
```

Adding a partition to an existing table::

```sql
ALTER TABLE table_name ADD [IF NOT EXISTS] PARTITION partition_spec [LOCATION 'location1'] partition_spec [LOCATION 'location2'] ...
```

##### Example

For example, you could partition the table based the criteria of the locale and datestamp to fetch BCookies from the  "US" after the date "2014-01-01". 

```sql
CREATE EXTERNAL TABLE search (
    bcookie string, 
    time_stamp int, 
    yuid string, 
    ip string, 
    pg_spaceid string, 
...)
PARTITIONED BY (
    locale string, 
    datestamp string)
STORED AS orc
location '...';
```

You can also add partitions to existing tables with the `ALTER` key word

```sql
ALTER TABLE search ADD PARTITION ( locale=‘US', datestamp=‘20130201') 
LOCATION '...';
```

### Reading Data

Reading data uses the `SELECT` key word and can include filters and functions. Because a `SELECT` statement can be
complex, we're going to just look at some examples and not give the syntax.

#### Selecting All Values

```sql
`SELECT * FROM	your_table;`
```

#### Selecting Columns

```sql
`SELECT column_a, column_b FROM your_table;`
```

#### Filtering Data

##### `WHERE`

The results from the query below are filtered based on the amount and region::

```sql
SELECT * FROM sales WHERE amount > 10 AND region = "US"
```

##### `HAVING`

```sql
SELECT col1 FROM t1 GROUP BY col1 HAVING SUM(col2) > 10
```

##### `LIMIT`

Limit indicates the number of rows to be returned. The rows returned are chosen at random. The following query returns 5
rows from t1 at random.

```sql
SELECT * FROM t1 LIMIT 5
```

#### Grouping/Ordering/Sorting

##### `GROUP BY`

```sql
SELECT
    a,
    sum(b)
FROM
    t1
GROUP BY
    a;
```

##### `ORDER BY`

When using `ORDER BY`, data is passed through one reducer to create an ordered result set from all of the data. This
may require an exceptionally long time to execute for larger data sets.

```sql
SELECT s.symbol, s.price_open, s.price_close
FROM stocks s
ORDER BY s.symbol ASC, s.price_open DESC;
```

##### `SORT BY`

`SORT BY` orders data within each reducer, thereby performing a local ordering, where each reducer's output will be
sorted. The performance is better, but you get a local ordering instead of an ordering of all the data from each
reducer.

```sql
SELECT s.symbol, s.price_open, s.price_close
FROM stocks s
SORT BY s.symbol ASC, s.price_open DESC;
```

### Writing Data

#### Load Files Into Tables

Load operations are copy/move operations from HDFS or local filesystem that move datafiles into locations corresponding
to Hive tables. File format must agree with the table format.

##### Syntax

```sql
LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename 
[PARTITION (partcol1=val1, partcol2=val2 ...)];
```

##### Example

```sql
LOAD DATA LOCAL INPATH './data/nyse.txt' OVERWRITE INTO TABLE stocks;
```

#### Insert Data From a Query Into Tables

Query results can be inserted into tables of file system directories by using the insert clause.

```sql
INSERT OVERWRITE TABLE tablename1 [PARTITION (partcol1=val1, partcol2=val2 ...) [IF NOT EXISTS]] select_statement1 FROM from_statement;
INSERT INTO TABLE tablename1 [PARTITION (partcol1=val1, partcol2=val2 ...)] select_statement1 FROM from_statement;
```

Hive also supports multiple inserts in the same statement or dynamic partition inserts.

```sql
set hive.exec.dynamic.partition = true | false;
set hive.exec.dynamic.partition.mode = strict | nonstrict; 
```

#### Alter Table Add Partitions

You can use ALTER TABLE ADD PARTITION to add partitions to a table. The location must be a directory inside of which
data files reside.

```sql
ALTER TABLE table_name ADD PARTITION (partCol = 'value1') location 'loc1';
```

If new partitions are directly added to HDFS, Hive will not be aware of these. Thus, you will need to add the metadata
about the partitions from HDFS into the Hive metastore with the following:

```sql
MSCK REPAIR TABLE table_name;
```

Integrating Hive with Oozie
---------------------------

In Oozie, a *hive action* runs a Hive job. The workflow job will wait until the Hive job completes before continuing to
the next action. To run the Hive job, you have to configure the hive action with the job-tracker, name-node and Hive
script elements as well as the necessary parameters and configuration

```xml
<workflow-app name="sample-wf" xmlns="uri:oozie:workflow:0.1">
    ...
    <action name="myfirsthivejob">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-traker>foo:8021</job-tracker>
            <name-node>bar:8020</name-node>
            <prepare>
                <delete path="${jobOutput}"/>
            </prepare>
            <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
            </configuration>
            <script>myscript.q</script> 
            <param>InputDir=/home/tucu/input-data</param>
            <param>OutputDir=${jobOutput}</param>
        </hive>
        <ok to="myotherjob"/>
        <error to="errorcleanup"/>
    </action>
    ...
</workflow-app>
```

Integrating Hive With HBase
---------------------------

Hive defines the `HBaseStorageHandler` class to enable integration with [HBase](http://hbase.apache.org/). This
class has hooks for input/output formats and for metadata operations like CREATE, DELETE, etc.

Hive tables have columns and column types. In Hbase tables, this is the equivalent to
[column families and column qualifiers](http://hbase.apache.org/book/columnfamily.html). Hive, however, does not need to
include all the columns in an HBase table.

The following schema definition stores the data with the `HBaseStorageHandler`, maps the HBase columns with the key
word `SERDEPROPERTIES`, and points to the HBase table with the key word `TBLEPROPERTIES`.

```sql
CREATE TABLE short_urls(
   short_url string,
   url string,
   hit_count int
)
STORED BY
'org.apache.hadoop.hive.hbase.HBaseStorageHandler'

WITH SERDEPROPERTIES
("hbase.columns.mapping" = ":key, u:url, s:hits")

TBLPROPERTIES
("hbase.table.name" = "short_urls");
```
