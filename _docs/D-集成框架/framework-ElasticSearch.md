---
title: ElasticSearch资源笔记
permalink: /docs/framework-ElasticSearch/
---

-----------------------------------------------------------------
### 关于elasticsearch
开源的搜索引擎，建立在一个全文搜索引擎库 Apache Lucene™ 基础之上
源码 https://github.com/elastic/elasticsearch
使用 Java 编写的，它的内部使用 Lucene 做索引与搜索，但是它的目的是使全文检索变得简单， 通过隐藏 Lucene 的复杂性，取而代之的提供一套简单一致的 RESTful API

-----------------------------------------------------------------
### 必读文档
基础概念简述
https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html#_document
关键系统配置
https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html
最佳实践（建议）
https://www.elastic.co/guide/en/elasticsearch/reference/current/general-recommendations.html
数据备份模型
https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-replication.html#_basic_write_model
官方参考手册
https://www.elastic.co/guide/index.html
elasticsearch参考手册
https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
elasticsearch官方中文权威指南=>重点是API查询接口
https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html
elk中文指南（可作为快速参考）=>重点是elk三者间的搭配
https://www.gitbook.com/book/chenryn/elk-stack-guide-cn
《Mastering Elasticsearch》中文版=>重点是ES架构和原理
https://www.gitbook.com/book/wizardforcel/mastering-elasticsearch/details

-----------------------------------------------------------------
### 安装
为便于自动化部署，使用tar方式安装，并提供脚本工具，将 部署/启动/停止 全自动化。
不建议使用过多插件，但x-pack必选

**安装java**
yum search java | grep -i --color JDK
yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel
在末尾添加/etc/profile
export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.51-1.b16.el7_1.x86_64"
执行 source /etc/profile

**安装elasticsearch**
下载页面
https://www.elastic.co/downloads
tar方式安装官方文档
https://www.elastic.co/guide/en/elasticsearch/reference/5.6/zip-targz.html
elasticsearch中文权威指南（快速阅读，浏览即可）
https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html
测试 http://localhost:9200

**安装kibina**
下载页面
https://www.elastic.co/downloads
tar方式安装官方文档
https://www.elastic.co/guide/en/kibana/5.6/targz.html
kibina设置
https://www.elastic.co/guide/en/kibana/5.6/settings.html
kibana帮助手册
https://www.elastic.co/guide/en/kibana/current/index.html
测试 http://localhost:5601
默认账号密码： elastic/changeme

**安装logstash**
下载页面
https://www.elastic.co/downloads
安装步骤
https://www.elastic.co/downloads/logstash
logstash config高级实例
https://www.elastic.co/guide/en/logstash/current/config-examples.html
logstash帮助文档
https://www.elastic.co/guide/en/logstash/5.6/index.html

**安装x-pack**
安装官方文档
https://www.elastic.co/guide/en/x-pack/5.6/installing-xpack.html
要求x-pack的版本号与kibana的版本号完全一致（5.6.1）
kibana/es/logstash加载插件
```bash
bin/kibana-plugin install file:///home/pdop/elk/x-pack-5.6.1.zip
bin/elasticsearch-plugin install file:///home/pdop/elk/x-pack-5.6.1.zip
bin/logstash-plugin install file:///home/pdop/elk/x-pack-5.6.1.zip
```

**其它工具**
Json数据自动生成工具
https://www.json-generator.com/#
httpie工具（类似curl）
https://github.com/jakubroztocil/httpie
https://www.hi-linux.com/posts/59166.html

-----------------------------------------------------------------
### 官方文档索引
重点是 数据操作API/查询操作API
**数据操作API**
Index操作API
https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html
Document操作API
https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html
Cluster操作API
https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster.html
Mapping操作API
https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html

**查询操作API**
Search操作API
https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html
Aggregations操作API
https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html
Query DSL查询语言手册
https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html
cat工具API
https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html

**配置描述**
常规模块配置
https://www.elastic.co/guide/en/elasticsearch/reference/current/modules.html
Index模块配置
https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html

**配置相关参考**
部署设置必读
https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html
内存设置参考
https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration-memory.html#mlockall
网络设置参考
https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html
避免spit-brain问题
https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#split-brain

-----------------------------------------------------------------
### Basic Concepts
**Cluster**
A cluster is a collection of one or more nodes (servers) that together holds your entire data and provides federated indexing and search capabilities across all nodes.
A cluster is identified by a unique name which by default is "elasticsearch". This name is important because a node can only be part of a cluster if the node is set up to join the cluster by its name.

**Node**
A node is a single server that is part of your cluster, stores your data, and participates in the cluster’s indexing and search capabilities.
a node is identified by a name which by default is a random Universally Unique IDentifier (UUID) that is assigned to the node at startup.
if there are no other Elasticsearch nodes currently running on your network, starting a single node will by default form a new single-node cluster named elasticsearch.

**Index**
An index is a collection of documents that have somewhat similar characteristics.
An index is identified by a name (that must be all lowercase) and this name is used to refer to the index when performing indexing, search, update, and delete operations against the documents in it.

**Type**
A type is a logical category/partition of your index whose semantics is completely up to you

**Document**
A document is a basic unit of information that can be indexed
expressed in JSON (JavaScript Object Notation)

**Shareds**
An index may exceed the hardware limits of a single node
To solve this problem, Elasticsearch provides the ability to subdivide your index into multiple pieces called shards.
To define the number of shards that you want.
Each shard is in itself a fully-functional and independent "index" that can be hosted on any node in the cluster.
two primary reasons:
1. horizontally split/scale your content volume
2. distribute and parallelize operations across shards (potentially on multiple nodes) thus increasing performance/throughput

**Replicas**
a failover mechanism in case a shard/node somehow goes offline or disappears for whatever reason.
two primary reasons:
1. provides high availability in case a shard/node fails. a replica shard is never allocated on the same node as the original/primary shard that it was copied from.
2. scale out your search volume/throughput since searches can be executed on all replicas in parallel.

Note:
The number of shards and replicas can be defined per index at the time the index is created. After the index is created, you may change the number of replicas dynamically anytime but you cannot change the number of shards after-the-fact.
By default, each index in Elasticsearch is allocated 5 primary shards and 1 replica which means that if you have at least two nodes in your cluster, your index will have 5 primary shards and another 5 replica shards (1 complete replica) for a total of 10 shards per index.

关于ElasticSearch的基础概念
https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html#_document

-----------------------------------------------------------------
### 数据备份模型（Data Replication Model）
The process of keeping the shard copies in sync and serving reads from them is what we call the data replication model.
原理参考：https://www.microsoft.com/en-us/research/publication/pacifica-replication-in-log-based-distributed-storage-systems/
数据备份模型 https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-replication.html#_basic_write_model
专家（LidongZhou）的个人网页：https://www.microsoft.com/en-us/research/people/lidongz/

**ElasticSearch数据访问**
可选工具：
1.通过curl工具，类似语法
curl -u elastic:changeme -X GET 127.0.0.1:9002/_cat/indices?v
2.通过Kibana的Console
GET /_cat/indices?v
3.通过httpie

**有两种查询API**
1.REST request URI:https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-uri-request.html
2.REST request body:https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-request-body.html
                                https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl.html

-----------------------------------------------------------------
### cat API
用于避免json格式数据查看困难的问题
常用格式：
查看所有cat命令 GET /_cat
查看帮助 GET /_cat/indices?hep
以verbose（冗长模式）显示 GET /_cat/indices?v
指定列标题 GET /_cat/node?h=ip,port,name 或者 GET /_cat/node?h=i,po,n
使用json格式输出 GET /_cat/nodes?format=json    format有 json,text,smile yaml cbor
结果排序 GET /_cat/templets?v&s=name:desc,order

-----------------------------------------------------------------
### Bootstrap检查
用于方式用户配置错误（将用户配置和安全配置对比）
在development模式下，Bootstrap将以warning的形式在日志中给出；
在production模式下，Bootstrap将禁止启动；

**development->production模式切换**
http和transport绑定到localhost，并且discovery type设置为single-node模式时，认为是development模式，将跳过bootstrap检查
可以通过设置es.enforce.bootstrap.checks设置必须强制检查，防止single-node模式跳过bootstrap
设置此参数：https://www.elastic.co/guide/en/elasticsearch/reference/current/setting-system-settings.html#jvm-options
http模块设置：https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-http.html
transport模块设置：https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-transport.html


