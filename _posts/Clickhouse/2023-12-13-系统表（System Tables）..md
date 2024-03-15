---
layout: post
title:  "ClickHouse中常见的系统表"
date:   2023-12-13 13:50:00 +0800
categories: clickhouse
---

### [介绍](https://clickhouse.com/docs/en/operations/system-tables#system-tables-introduction)
系统表提供了以下信息:

* 服务器状态、进程和环境。
* 服务器的内部进程。
* 构建ClickHouse二进制文件时使用的选项。

系统表:

* 位于系统数据库中。
* 仅用于读取数据。
* 不能被丢弃或改变，但可以被分离。

大多数系统表将数据存储在RAM中。ClickHouse服务器在开始时创建这样的系统表。

与其他系统表不同，系统日志表metric_log、query_log、query_thread_log、trace_log、part_log、crash_log、text_log和backup_log由MergeTree表引擎提供，默认情况下，它们的数据存储在文件系统中。如果从文件系统中删除一个表，ClickHouse服务器会在下一次写入数据时再次创建一个空表。如果在新版本中更改了系统表模式，那么ClickHouse将重命名当前表并创建一个新表。

系统日志表可以通过创建一个与/etc/clickhouse-server/config下的表同名的配置文件来定制。或者在/etc/clickhouse-server/config.xml中设置相应的元素。

### 常见的系统表
[zookeeper_log](https://clickhouse.com/docs/en/operations/system-tables/zookeeper_log)
该表包含了向ZooKeeper服务器请求的参数信息和ZooKeeper服务器的响应信息。

[zookeeper_connection](https://clickhouse.com/docs/en/operations/system-tables/zookeeper_connection)
如果没有配置ZooKeeper的系统，该表不存在。该表显示了ZooKeeper的当前连接(包括辅助ZooKeeper)。每行显示一个连接的信息。

[zookeeper](https://clickhouse.com/docs/en/operations/system-tables/zookeeper)
系统除非配置了ClickHouse Keeper或ZooKeeper，否则该表不存在。该表公开配置中定义的Keeper集群中的数据。查询必须具有' path = '条件或使用WHERE子句设置的path IN条件，如下所示。这对应于要获取数据的子节点的路径。

查询`SELECT * FROM system。zookeeper WHERE path = '/clickhouse'`输出`/clickhouse`节点上所有子节点的数据。要输出所有根节点的数据，则写`path = ' / '`。如果`path`中指定的路径不存在，将抛出异常。 
 
查询`SELECT * FROM system。zookeeper WHERE path IN ('/'， '/clickhouse')`输出`/`和`/clickhouse`节点上所有子节点的数据。如果在指定的“路径”集合中有不存在的路径，将引发异常。它可用于执行一批Keeper路径查询。

[users](https://clickhouse.com/docs/en/operations/system-tables/users)
该表中包含在服务器上配置的用户帐户列表。

[user_processes](https://clickhouse.com/docs/en/operations/system-tables/user_processes)
这个系统表可以用来获得内存使用和用户配置的概览。

[trace_log](https://clickhouse.com/docs/en/operations/system-tables/trace_log)
该表中包含由抽样查询分析器收集的堆栈跟踪信息。

[time_zones](https://clickhouse.com/docs/en/operations/system-tables/time_zones)
该表中包含ClickHouse服务器支持的时区列表。这个时区列表可能会根据ClickHouse的版本而有所不同。

[text_log](https://clickhouse.com/docs/en/operations/system-tables/text_log)
该表中包含日志记录条目。可以通过服务器配置项`text_log.level`限制进入该表中的日志数据级别。

[tables](https://clickhouse.com/docs/en/operations/system-tables/tables)
该表中包含服务器中每个表的元数据。

[table_engines](https://clickhouse.com/docs/en/operations/system-tables/table_engines)
该表中包含服务器支持的表引擎的描述及其特性支持信息。

[symbols](https://clickhouse.com/docs/en/operations/system-tables/symbols)
包含用于ClickHouse的二进制文件的信息。它需要自省权限才能访问。此表仅对c++专家和ClickHouse工程师有用。

[settings_profile_elements](https://clickhouse.com/docs/en/operations/system-tables/settings_profile_elements)
该表中描述设置配置文件的如下内容:
* 约束。
* 该设置应用于的角色和用户。
* 父设置配置文件。
  
[settings_profiles](https://clickhouse.com/docs/en/operations/system-tables/settings_profiles)
该表中包含配置文件的属性概要

[stack_trace](https://clickhouse.com/docs/en/operations/system-tables/stack_trace)
该表中包含所有服务器线程的堆栈跟踪。允许开发人员自查服务器状态。

[storage_policies](https://clickhouse.com/docs/en/operations/system-tables/storage_policies)
该表中包含有关服务器配置中定义的存储策略和卷的信息。

[settings](https://clickhouse.com/docs/en/operations/system-tables/settings)
包含有关当前用户会话设置的信息。

[session_log](https://clickhouse.com/docs/en/operations/system-tables/session_log)
包含有关所有成功和失败的登录和注销事件的信息。

[server_settings](https://clickhouse.com/docs/en/operations/system-tables/server_settings)
包含有关服务器的全局设置的信息，这些设置在config.xml中指定。目前，该表只显示config.xml第一层的设置，不支持嵌套配置(例如logger)。

[schema_inference_cache](https://clickhouse.com/docs/en/operations/system-tables/schema_inference_cache)
包含有关所有缓存文件架构的信息。

[scheduler](https://clickhouse.com/docs/en/operations/system-tables/scheduler)
包含位于本地服务器上的调度节点的信息和状态。此表可用于监控。该表为每个调度节点包含一行。

[row_policies](https://clickhouse.com/docs/en/operations/system-tables/row_policies)
包含一个特定表的过滤器，以及应该使用此行策略的角色和/或用户列表。

[roles](https://clickhouse.com/docs/en/operations/system-tables/roles)
包含已配置角色的信息。

[role_grants](https://clickhouse.com/docs/en/operations/system-tables/role-grants)
包含用户和角色的角色授权。要向该表添加表项，请使用GRANT role To user。

[replication_queue](https://clickhouse.com/docs/en/operations/system-tables/replication_queue)
包含去重队列中存储的任务信息，这些任务存储在ClickHouse Keeper或ZooKeeper中，用于ReplicatedMergeTree家族中的表。

[replicated_fetches](https://clickhouse.com/docs/en/operations/system-tables/replicated_fetches)
包含有关当前正在运行的后台获取的信息。

[replicas](https://clickhouse.com/docs/en/operations/system-tables/replicas)
包含驻留在本地服务器上的复制表的信息和状态。此表可用于监控。每个Replicated\*表都包含一行。

[quotas_usage](https://clickhouse.com/docs/en/operations/system-tables/quotas_usage)
所有用户的配额使用情况。

[quotas](https://clickhouse.com/docs/en/operations/system-tables/quotas)
包含配额信息。

[quota_usage](https://clickhouse.com/docs/en/operations/system-tables/quota_usage)
当前用户的配额使用情况:已使用的配额和剩余的配额。

[quota_limits](https://clickhouse.com/docs/en/operations/system-tables/quota_limits)
包含有关所有配额的间隔或最大值的信息。

[query_views_log](https://clickhouse.com/docs/en/operations/system-tables/query_views_log)
包含运行查询时执行的相关视图的信息，例如视图类型或执行时间。

[query_thread_log](https://clickhouse.com/docs/en/operations/system-tables/query_thread_log)
包含有关执行查询的线程的信息，例如，线程名称、线程开始时间、查询处理的持续时间。

[query_log](https://clickhouse.com/docs/en/operations/system-tables/query_log)
包含有关已执行查询的信息，例如开始时间、处理持续时间、错误消息。
请注意！这个表不包含插入查询所需的数据。
您可以在服务器配置的query_log部分更改查询日志的设置。
可以通过设置log_queries = 0来禁用查询日志记录。我们不建议关闭日志记录，因为该表中的信息对于解决问题很重要。
日志数据是批量写入的，在服务器中配置query_log设置项的flush_interval_milliseconds的参数设置数据的输出周期。若要强制刷写，请使用`SYSTEM FLUSH LOGS`查询。

[query_cache](https://clickhouse.com/docs/en/operations/system-tables/query_cache)
显示查询缓存的内容。

[processors_profile_log](https://clickhouse.com/docs/en/operations/system-tables/processors_profile_log)
此表包含处理器级别的分析(可以在EXPLAIN PIPELINE中找到)。

[processes](https://clickhouse.com/docs/en/operations/system-tables/processes)
这个系统表用于实现SHOW PROCESSLIST查询。

[parts_columns](https://clickhouse.com/docs/en/operations/system-tables/parts_columns)
包含有关使用MergeTree引擎的表和列的信息。

[parts](https://clickhouse.com/docs/en/operations/system-tables/parts)
包含有关使用MergeTree引擎的表的信息。

[part_log](https://clickhouse.com/docs/en/operations/system-tables/part_log)
只有指定了Part_log服务器设置，才会创建Part_log表。
此表包含与MergeTree表中的数据部分发生的事件有关的信息，例如添加或合并数据。

[opentelemetry_span_log](https://clickhouse.com/docs/en/operations/system-tables/opentelemetry_span_log)
包含有关已执行查询的跟踪范围的信息。

[one](https://clickhouse.com/docs/en/operations/system-tables/one)
这个表包含一行，一个空的UInt8列包含值0。

如果SELECT查询没有指定FROM子句，则使用该表。

这类似于其他dbms中的双表。

。。。。未完待续