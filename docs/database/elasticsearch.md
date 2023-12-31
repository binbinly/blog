# Elasticsearch

> [文章来源](https://www.elastic.org.cn/categories/docs)

## 基础

### 下载

- [官方下载](https://www.elastic.co/cn/downloads/past-releases#elasticsearch)
- [github](https://github.com/elastic/elasticsearch)

### 安装

- [安装文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html)

> Kibana汉化，在 kibana 的配置文件中，i18n.locale配置项默认为en，修改为如下配置即可

```bash
i18n.locale: "zh-CN"
```

### 目录结构

- `bin` 可执行脚本文件，包括启动elasticsearch服务、插件管理、函数命令等。
- `config` 配置文件目录，如elasticsearch配置、角色配置、jvm配置等。
- `lib` elasticsearch所依赖的java库。
- `data` 默认的数据存放目录，包含节点、分片、索引、文档的所有数据，生产环境要求必须修改。
- `logs` 默认的日志文件存储路径，生产环境务必修改。
- `modules` 包含所有的Elasticsearch模块，如Cluster、Discovery、Indices等。
- `plugins` 已经安装的插件的目录。
- jdk/jdk.app 7.x 以后特有，自带的 java 环境，8.x版本自带 jdk 17

### 基础配置

```bash
# 集群名称，节点根据集群名称确定是否是同一个集群。默认名称为 elasticsearch，但应将其更改为描述集群用途的适当名称。不要在不同的环境中重用相同的集群名称。否则，节点可能会加入错误的集群
cluster.name：elasticsearch
# 节点名称，集群内唯一，默认为主机名。，但可以在配置文件中显式配置
node.name：node-1
# 节点对外提供服务的地址以及集群内通信的ip地址，例如127.0.0.1和 [::1]。
network.host： 127.0.0.1
# 对外提供服务的端口号，默认 9200
http.port：9200
# 节点通信端口号，默认 9300
transport.port：9300
```

> 开发模式：开发模式是默认配置（未配置集群发现设置），如果用户只是出于学习目的，而引导检查会把很多用户挡在门外，所以ES提供了一个设置项`discovery.type=single-node`。此项配置为指定节点为单节点发现以绕过引导检查。
>
> 生产模式：当用户修改了有关集群的相关配置会触发生产模式，在生产模式下，服务启动会触发ES的引导检查或者叫启动检查（bootstrap checks），所谓引导检查就是在服务启动之前对一些重要的配置项进行检查，检查其配置值是否是合理的。引导检查包括对JVM大小、内存锁、虚拟内存、最大线程数、集群发现相关配置等相关的检查，如果某一项或者几项的配置不合理，ES会拒绝启动服务，并且在开发模式下的某些警告信息会升级成错误信息输出。引导检查十分严格，之所以宁可拒绝服务也要阻止用户启动服务是为了防止用户在对ES的基本使用不了解的前提下启动服务而导致的后期性能问题无法解决或者解决起来很麻烦。因为一旦服务以某种不合理的配置启动，时间久了之后可能会产生较大的性能问题，但此时集群已经变得难以维护和扩展，ES为了避免这种情况而做出了引导检查的设置，本来在开发模式下为警告的启动日志会升级为报错（Error）。这种设定虽然增加了用户的使用门槛，但是避免了日后产生更大的问题。

### 检查服务状态

> 在7.x的版本是通过如下地址访问ES服务：http://localhost:9200/
>
> Elastic 8 默认开启了 SSL，将默认配置项由true改为false即可

```bash
xpack.security.enabled: false
```

> 推荐 https://localhost:9200/

### 构建基于Security的本地集群

```bash
# 在第一个节点启动的时候，控制台会输出 token 
bin/elasticsearch --enrollment-token {token} 
```

### 浏览器插件

- [Elasticsearch Head](https://chromewebstore.google.com/detail/multi-elasticsearch-head/cpmmilfkofbeimbmgiclohpodggeheim)
- [Elasticsearch Tools](https://chromewebstore.google.com/detail/elasticsearch-tools/aombbfhbleaidjmbahldfbajjmgkgojl)
- [Elasticvue](https://chromewebstore.google.com/detail/elasticvue/hkedbapjpblbodpgbajblpnlpenaebaa)

## 简介

Elasticsearch（后称为 ES ）是一个天生支持分布式的搜索、聚合分析和存储引擎。

> ES 最擅长从海量数据中检索少量相关数据，但不擅长单次查询大量数据（大单页）

### 特点

- 开源、免费：基于Apache License 2.0开源协议，并且完全免费。
- 基于Java语言：Elasticsearch基于Java语言开发，运行在Jvm环境中。
- **基于Lucene框架：**基于开源的Apache Lucene框架开发。
- 原生分布式：不依赖与Zookeeper，自带分布式解决方案。
- 高性能：支持海量数据的全文检索。支持PB级数据秒内响应！对于ES来说，上亿级别数据只不过是- 起点！性能几乎没有上限，足以满足各种对性能要求极高的场景。
- 可伸缩：弹性搜索，可根据不同规模服务对性能需要的不同而动态扩展或收缩性能。
- 易扩展：支持非常方便的横向扩展集群。
- 开箱即用：对于小公司，解压后零配置启动服务即可立即使用，门槛极低
- 跨编程语言：支持Java、Golang、Python、C#、PHP等多种变成语言，几乎所有语言开发者都可以使用Elasticsearch。

> 性能高，PB（1PB = 1024TB = 1024²GB）级数据秒内响应。
>
> 不支持事务

### 节点：Node

> 一个节点就是一个Elasticsearch的实例，可以理解为一个 ES 的进程。

```bash
GET _cat/nodes?v
```

### 角色：Roles

常见角色

- 主节点（active master）：一般指活跃的主节点，一个集群中只能有一个，主要作用是对集群的管理。
- 候选节点（master-eligible）：当主节点发生故障时，参与选举，也就是主节点的替代节点。
- 数据节点（data node）：数据节点保存包含已编入索引的文档的分片。数据节点处理数据相关操作，如 CRUD、搜索和聚合。这些操作是 I/O 密集型、内存密集型和 CPU 密集型的。监控这些资源并在它们过载时添加更多数据节点非常重要。
- 预处理节点（ingest node）：预处理节点有点类似于logstash的消息管道，所以也叫ingest pipeline，常用于一些数据写入之前的预处理操作。

```bash
# 可以配置多个角色
node.roles: [ 角色1, 角色2, xxx ]
```

### 索引：Index

- alias：即 索引别名，后续有单独讲解，基础篇不赘述，戳：ES中索引别名（alias）的到底有什么用
- settings：索引设置，常见设置如分片和副本的数量等。
- mapping：即映射，定义了索引中包含哪些字段，以及字段的类型、长度、分词器等。

### 文档：Document

- _index：索引名称
- _id：文档 id。
- _version：版本号
- _seq_no：索引级别的版本号，索引中所有文档共享一个 _seq_no
- _primary_term：_primary_term是一个整数，每当Primary Shard发生重新分配时，比如节点重启，Primary选举或重新分配等，_primary_term会递增1。主要作用是用来恢复数据时处理当多个文档的_seq_no 一样时的冲突，避免 Primary Shard 上的数据写入被覆盖。

## 集群

> ES 是自动发现的，即零配置，开箱即用，无需任何网络配置，Elasticsearch 将绑定到可用的环回地址并扫描本地端口9300到9305连接同一服务器上运行的其他节点，自动形成集群。此行为无需进行任何配置即可提供自动集群服务。

### 核心配置

- `network.host`：即提供服务的ip地址，一般配置为本节点所在服务器的内网地址，此配置会导致节点由开发模式转为生产模式，从而触发引导检查。
- `network.publish_host`：即提供服务的ip地址，一般配置为本节点所在服务器的公网地址
- `http.port`：服务端口号，默认 9200，通常范围为 9200~9299
- `transport.port`：节点通信端口，默认 9300，通常范围为 9300~9399
- `discovery.seed_hosts`：此设置提供集群中其他候选节点的列表，并且可能处于活动状态且可联系以播种发现过程。每个地址可以是 IP 地址，也可以是通过 DNS 解析为一个或多个 IP 地址的主机名。
- `cluster.initial_master_nodes`：指定集群初次选举中用到的候选节点，称为集群引导，只在第一次形成集群时需要，如过配置了 network.host，则此配置项必须配置。重新启动节点或将新节点添加到现有集群时不要使用此设置。

### 健康检查

```bash
GET _cat/health
# or
GET _cluster/health
```

### 常见 Cat Apis

- _cat/indices?health=yellow&v=true：查看当前集群中的所有索引
- _cat/health?v=true：查看健康状态
- _cat/nodeattrs：查看节点属性
- _cat/nodes?v：查看集群中的节点
- _cat/shards：查看集群中所有分片的分配情况

### Cluster APIs

- _cluster/allocation/explain：可用于诊断分片未分配原因
- _cluster/health/ ：检查集群状态

### 索引未分配的原因

- `ALLOCATION_FAILED`: 由于分片分配失败而未分配
- `CLUSTER_RECOVERED`: 由于完整群集恢复而未分配.
- `DANGLING_INDEX_IMPORTED`: 由于导入悬空索引而未分配.
- `EXISTING_INDEX_RESTORED`: 由于还原到闭合索引而未分配.
- `INDEX_CREATED`: 由于API创建索引而未分配.
- `INDEX_REOPENED`: 由于打开闭合索引而未分配.
- `NEW_INDEX_RESTORED`: 由于还原到新索引而未分配.
- `NODE_LEFT`: 由于承载它的节点离开集群而取消分配.
- `REALLOCATED_REPLICA`: 确定更好的副本位置并取消现有副本分配.
- `REINITIALIZED`: 当碎片从“开始”移回“初始化”时.
- `REPLICA_ADDED`: 由于显式添加了复制副本而未分配.
- `REROUTE_CANCELLED`: 由于显式取消重新路由命令而取消分配.

## 分片

> 分片可以理解为 索引的碎片。并且所有碎片都是可以无限复制的

### 种类

- 主分片（primary shard）:
- 副本分片（replica shard）:

### 分片的基本策略

- 一个索引包含一个或多个分片，在7.0之前默认五个主分片，每个主分片一个副本；在7.0之后默认一个主分片。副本可以在索引创建之后修改数量，但是主分片的数量一旦确定不可修改，只能创建索引
- 每个分片都是一个Lucene实例，有完整的创建索引和处理请求的能力
- ES会自动再nodes上做分片均衡 shard reblance
- 一个doc不可能同时存在于多个主分片中，但是当每个主分片的副本数量不为一时，可以同时存在于多个副本中。
- 主分片和其副本分片不能同时存在于同一个节点上。
- 完全相同的副本不能同时存在于同一个节点上。

### 分片的作用和意义

- 高可用性：提高分布式服务的高可用性。
- 提高性能：提供系统服务的吞吐量和并发响应的能力
- 易扩展：当集群的性能不满足业务要求时，可以方便快速的扩容集群，而无需停止服务。

## 索引和文档的基本操作

### 索引设置

```bash
PUT <index_name>

# 创建索引的时候指定 settings
PUT <index_name>
{
  "settings": {}
}

PUT test_setting
{
  "settings": {
    "number_of_shards": 1,  // 主分片数量为 1
    "number_of_replicas": 1, // 每个主分片的副本数。默认为 1，允许配置为 0
    "index.refresh_interval"："1s", // 执行刷新操作的频率，默认为1s. 可以设置 -1 为禁用刷新
    "max_result_window": 10000 // from + size搜索此索引 的最大值。默认为 10000. 搜索请求占用堆内存和时间 from + size，这限制了内存
  }
}
```

### 修改 settings

```bash
PUT /<index_name>/_settings

PUT test_setting/_settings
{
  "number_of_shards": 0
}
```

> 只能在创建索引时或在关闭状态的索引上设置。

> 重要的静态配置 `index.number_of_shards`：索引的主分片的个数，默认为 1，此设置只能在创建索引时设置，每个索引的分片的数量上限为 1024，这是一个安全限制，以防止意外创建索引，这些索引可能因资源分配而破坏集群的稳定性。export ES_JAVA_OPTS=“-Des.index.max_number_of_shards=128” 可以通过在属于集群的每个节点上指定系统属性来修改限制

### 索引操作

> 以小写英文字母命名索引，不要使用驼峰或者帕斯卡命名法则，如过出现多个单词的索引名称，以全小写 + 下划线分隔的方式：如test_index。

```bash
# 创建索引
PUT <index_name>
# 删除索引
DELETE <index_name>
# 索引是否存在
HEAD <index_name>
```

> 索引创建成功后，索引名称，主分片数量，字段类型，不可以更改

### 文档的 CRUD

```bash
# create
PUT /<target>/_doc/<_id>
PUT /<target>/_create/<_id>
POST /<target>/_create/<_id>

# 如果在 PUT 数据的时候当前数据已经存在，则数据会被覆盖，如果在 PUT 的时候指定操作类型 op_type=create，此时如果数据已存在则会返回失败，因为已经强制指定了操作类型为 create，ES就不会再去执行 update 操作
PUT goods/_doc/1?op_type=index
{
  "name":"傻妞手机",
  "content":"华人牌2060款手机傻妞"
}
```

```bash
# 查询指定 id 的文档
# 当添加了_source=false之后，在结果中仅返回了元数据字段
GET <index>/_doc/<_id>?_source=false
# 判断指定 id 的文档是否存在
HEAD  <index>/_doc/<_id>
```

```bash
DELETE /<index>/_doc/<_id>

DELETE goods/_doc/1
```

```bash
POST /<index>/_update/<_id>
{
  "doc": {
    "<field_name>": "<field_value>"
  }
}
```

### Multi get (mget) API

```bash
GET /_mget
{
  "docs": [
    {
      "_index": "<index_name>",
      "_id": "<_id>",
      "_source": false
    },
    {
      "_index": "<index_name>",
      "_id": "<_id>",
      "_source": true
    }
  ]
}
// or 
GET <index_name>/_mget
{
  "docs": [
    {
      "_id": "<_id>"
    },
    {
      "_id": "<_id>"
    }
  ]
}
// or 
GET /<index_name>/_mget
{
  "ids" : ["<_id_1>", "<_id_2>"]
}
```

### Bulk API

```bash
POST /_bulk
POST /<index>/_bulk
{"action": {"mata data"}}
{"data"}
```

```bash
# create
POST /_bulk
{"index":{"_index":"goods","_id":"1"}}
{"name":"phone"}
```

```bash
# update
POST goods/_bulk
{ "update": { "_index": "goods",  "_id": "1"} }
{ "doc" : {"name" : "huawei"} }
```

```bash
# delete
POST /_bulk
{ "delete": { "_index": "goods",  "_id": "1" }}
```

## 映射：Mappings

> ES 中的 mapping 有点类似与关系数据库中表结构的概念

### 查看索引

```bash
GET /<index_name>/_mappings

GET /<index_name>/_mappings/field/<field_name>
```

### 创建索引

```bash
PUT /<index_name>
{
  "mappings": {
    "properties": {
      "field_a": {
        "<parameter_name>": "<parameter_value>"
      },
      ...
    }
  },
  "settings":{},
  "alias":"t" //索引别名
}
```

### 修改索引

```bash
PUT <index_name>/_mapping
{
  "properties": {
    "<field_name>": {
      "type": "text",	// 必须和原字段类型相同，切不许显式声明
      "analyzer":"ik_max_word",	// 必须和元原词器类型相同，切必须显式声明
      "fielddata": false
    }
  }
}
```

> **注意**：并非所有字段参数都可以修改。字段类型不可修改，字段分词器不可修改

### 数据类型

- 基本数据类型
  - Numbers：数字类型，包含很多具体的基本数据类型
  - binary：编码为 Base64 字符串的二进制值。
  - boolean：即布尔类型，接受 true 和 false。
  - alias：字段别名。
  - Keywords：包含 `keyword`、constant_keyword 和 wildcard。
  - Dates：日期类型，包括 `data` 和 data_nanos，两种类型

- 对象关系类型（复杂类型）
  - object：非基本数据类型之外，默认的 json 对象为 object 类型。
  - flattened：单映射对象类型，其值为 json 对象。
  - `nested`：嵌套类型。
  - join：父子级关系类型。
- 结构化类型
  - Range：范围类型，比如 long_range，double_range，data_range 等
  - ip：ipv4 或 ipv6 地址
  - version：版本号
  - murmur3：计算和存储值的散列
- 聚合数据类型
  - aggregate_metric_double：
  - histogram：
- 文本搜索字段
  - `text` ：文本数据类型，用于全文检索。
  - annotated-text：
  - `completion`
  - search_as_you_type：
  - token_count：
- 文档排名类型
  - dense_vector：记录浮点值的密集向量。
  - rank_feature：记录数字特征以提高查询时的命中率。
  - rank_features：记录数字特征以提高查询时的命中率。
- `空间数据类型`
  - geo_point：纬度和经度点。
  - geo_shape：复杂的形状，例如多边形。
  - point：任意笛卡尔点。
  - shape：任意笛卡尔几何。
- 其他类型
  - percolator：用Query DSL 编写的索引查询。

### 映射参数

- `analyzer` 指定分析器，只有 text 类型字段支持。
- coerce 是否允许强制类型转换
- copy_to 该参数允许将多个字段的值复制到组字段中，然后可以将其作为单个字段进行查询
- `doc_values` 为了提升排序和聚合效率，默认true，如果确定不需要对字段进行排序或聚合，也不需要通过脚本访问字段值，则可以禁用doc值以节省磁盘空间（不支持 text 和 annotated_text）
- `dynamic` 控制是否可以动态添加新字段，支持以下四个选项：true：（默认）允许动态映射false：忽略新字段。这些字段不会被索引或搜索，但仍会出现在_source返回的命中字段中。这些字段不会添加到映射中，必须显式添加新字段。runtime：新字段作为运行时字段添加到索引中，这些字段没有索引，是_source在查询时加载的。strict：如果检测到新字段，则会抛出异常并拒绝文档。必须将新字段显式添加到映射中。
- eager_global_ordinals 用于聚合的字段上，优化聚合性能。
- enabled 是否创建倒排索引，可以对字段操作，也可以对索引操作，如果不创建索引，让然可以检索并在_source元数据中展示，谨慎使用，该状态无法修改。
- `fielddata` 查询时内存数据结构，在首次用当前字段聚合、排序或者在脚本中使用时，需要字段为fielddata数据结构，并且创建倒排索引保存到堆中
- `fields` 给 field 创建多字段，用于不同目的（全文检索或者聚合分析排序）
- format 用于格式化代码

```json
"date":{
    "type":"date",
    "format":"yyyy-MM-dd"
}
```

- `ignore_above` 超过长度将被忽略
- ignore_malformed 忽略类型错误
- index_options 控制将哪些信息添加到反向索引中以进行搜索和突出显示。仅用于text 字段
- index_phrases 提升exact_value查询速度，但是要消耗更多磁盘空间
- index_prefixes 前缀搜索：min_chars：前缀最小长度，>0，默认2（包含）max_chars：前缀最大长度，<20，默认5（包含）
- `index` 是否对创建对当前字段创建倒排索引，默认 true，如果不创建索引，该字段不会通过索引被搜索到,但是仍然会在 source 元数据中展示true 新检测到的字段将添加到映射中。（默认）false 新检测到的字段将被忽略。这些字段将不会被索引，因此将无法搜索，但仍会出现在_source返回的匹配项中。这些字段不会添加到映射中，必须显式添加新字段。strict 如果检测到新字段，则会引发异常并拒绝文档。必须将新字段显式添加到映射中
- meta 附加到元字段
- normalizer 文档归一化器
- `norms` 是否禁用评分（在filter和聚合字段上应该禁用）。
- `null_value` 为 null 值设置默认值
- position_increment_gap 用于数组中相邻搜索中的搜索间隙，slop 默认 100 见：代码块 1
- properties 除了mapping还可用于object的属性设置
- `search_analyzer` 设置单独的查询时分析器
- similarity 为字段设置相关度算法，支持：BM25boolean注意：classic（TF-IDF）在 ES 8.x 中已不再支持！
- subobjects ES 8 新增，subobjects 设置为 false 的字段的值，其子字段的值不被扩展为对象。
- store 设置字段是否仅查询
- term_vector 运维参数，在运维篇会详细讲解。

## 分词器：Text Analysis

### 分词发生时期

分词器的处理过程发生在 Index Time 和 Search Time 两个时期。

- Index Time：文档写入并创建倒排索引时期，其分词逻辑取决于映射参数analyzer。
- Search Time：搜索发生时期，其分词仅对搜索词产生作用。

### 分词器的组成

- 切词器（Tokenizer）：用于定义切词（分词）逻辑
- 词项过滤器（Token Filter）：用于对分词之后的单个词项的处理逻辑
- 字符过滤器（Character Filter）：用于处理单个字符
注意：

> 分词器不会对源数据造成任何影响，分词仅仅是对倒排索引或者搜索词的行为。

### 文档归一化处理：Normalization

- 大小写统一
- 时态转换
- 停用词：如一些语气词、介词等在大多数场景下均无搜索意义

```bash
GET _analyze
{
  "text": ["What are you doing!"],
  "analyzer": "english"
}
```

```bash
# 不同词项过滤器的基本使用
GET _analyze
{
  "filter" : ["lowercase"],
  "text" : "WWW ELASTIC ORG CN"
}

GET _analyze
{
  "tokenizer" : "standard",
  "filter" : ["uppercase"],
  "text" : ["www.elastic.org.cn","www elastic org cn"]
}
```

```bash
# 默认停用词使用
GET _analyze
{
  "tokenizer": "standard", 
  "filter": ["stop"],
  "text": ["What are you doing"]
}

### 自定义 filter
DELETE test_token_filter_stop
PUT test_token_filter_stop
{
  "settings": {
    "analysis": {
      "filter": {
        "my_filter": {
          "type": "stop",
          "stopwords": [
            "www"
          ],
          "ignore_case": true
        }
      }
    }
  }
}
GET test_token_filter_stop/_analyze
{
  "tokenizer": "standard", 
  "filter": ["my_filter"], 
  "text": ["What www WWW are you doing"]
}
```

#### 同义词定义规则

- a, b, c => d：这种方式，a、b、c 会被 d 代替。
- a, b, c, d：这种方式下，a、b、c、d 是等价的。

```bash
# 自定义规则，直接在synonym内部声明规则
PUT test_token_filter_synonym
{
  "settings": {
    "analysis": {
      "filter": {
        "my_synonym": {
          "type": "synonym",
          "synonyms": [ "good, nice => excellent" ] //good, nice, excellent
        }
      }
    }
  }
}
GET test_token_filter_synonym/_analyze
{
  "tokenizer": "standard", 
  "filter": ["my_synonym"], 
  "text": ["good"]
}

# 在文件中定义规则，文件相对顶级目录为 ES 的 Config 文件夹。
DELETE test_token_filter_synonym
PUT test_token_filter_synonym
{
  "settings": {
    "analysis": {
      "filter": {
        "my_synonym": {
          "type": "synonym",
          "synonyms_path": "analysis/synonym.txt"
        }
      }
    }
  }
}
GET test_token_filter_synonym/_analyze
{
  "tokenizer": "standard", 
  "text": ["a"], // a b c d s; q w e r ss
  "filter": ["my_synonym"]
}
```

#### 字符过滤器：Character Filter

- type：使用的字符过滤器类型名称，可配置以下值
  - html_strip
  - mapping
  - pattern_replace

```bash
PUT <index_name>
{
  "settings": {
    "analysis": {
      "char_filter": {
        "my_char_filter": {
          "type": "<char_filter_type>",
          "escaped_tags": [	// 需要保留的 html 标签, 当前仅保留 a 标签
            "a"
          ]
        }
      }
    }
  }
}
```

#### 字符映射过滤器：Mapping Character Filter

```bash
PUT test_html_strip_filter
{
  "settings": {
    "analysis": {
      "char_filter": {
        "my_char_filter": {
          "type": "mapping",	// mapping 代表使用字符映射过滤器
          "mappings": [				// 数组中规定的字符会被等价替换为 => 指定的字符
            "滚 => *",
            "垃 => *",
            "圾 => *"
          ]
        }
      }
    }
  }
}
GET test_html_strip_filter/_analyze
{
  //"tokenizer": "standard", 
  "char_filter": ["my_char_filter"],
  "text": "你就是个垃圾！滚"
}
```

#### 正则替换过滤器：Pattern Replace Character Filter

```bash
PUT text_pattern_replace_filter
{
  "settings": {
    "analysis": {
      "char_filter": {
        "my_char_filter": {
          "type": "pattern_replace",	// pattern_replace 代表使用正则替换过滤器			
          "pattern": """(\d{3})\d{4}(\d{4})""",	// 正则表达式
          "replacement": "$1****$2"
        }
      }
    }
  }
}
GET text_pattern_replace_filter/_analyze
{
  "char_filter": ["my_char_filter"],
  "text": "您的手机号是18868686688"
}
```

### 内置分词器

- **Standard** ：默认分词器，中文支持的不理想，会逐字拆分。参数值为：standard
- Pattern：以正则匹配分隔符，把文本拆分成若干词项。参数值为：pattern
- Simple：除了英文单词和字母，其他统统过滤掉，参数值为：simple
- **Whitespace** ：以空白符分隔，不会改变大小写，参数值为：whitespace
- **Keyword** ：可以理解为不做任何操作的分词器，会保留原有文本的所有属性，参数值为：keyword
- Stop：分词规则和 Simple Analyzer 相同，但是增加了对停用词的支持。参数值为：stop
- Language Analyzer：支持全球三十多种主流语言。
- Fingerprint：一种特殊领域分词器，不常用

### 自定义分词器：Custom Analyzer

如果 ES 内置分词器无法满足需要，可以通过对切词器、词项过滤器、字符过滤器三个组件的自由组合来自定义分词器。在使用分词器时候需要注意必须满足以下要求：

- Tokenizer：必须包含一个并且只能指定一个切词器，即必须指定分词器的切词规则。
- Token Filter：可以不指定词项过滤器，也可以指定多个词项过滤器
- Char Filter：可以不指定字符过滤器，也可以指定多个字符过滤器

```bash
# 语法
PUT <index_name>
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer": {	 // 自定义分词器名称
          "type": "<value>", 		 // 接受 ES 内置分词器，也可以配置为 custom 从而自定义分词器
          ...
        }
      }
    }
  }
}
```

```bash
# 案例
PUT test_analyzer
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "my_tokenizer": {
          "type": "pattern",
          "pattern": "[ ,.!?]"
        }
      },
      "char_filter": {
        "html_strip_char_filter": {
          "type": "html_strip",
          "escaped_tags": [
            "a"
          ]
        },
        "my_char_filter": {
          "type": "mapping",
          "mappings": [
            "滚 => *",
            "垃 => *",
            "圾 => *"
          ]
        }
      },
      "filter": {
        "my_filter": {
          "type": "stop",
          "stopwords": [
            "www"
          ],
          "ignore_case": true
        }
      },
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "char_filter": [
            "my_char_filter",
            "html_strip_char_filter"
          ],
          "filter": [
            "my_filter",
            "uppercase"
          ],
          "tokenizer": "my_tokenizer"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title":{
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  }
}

GET test_analyzer/_analyze
{
  "analyzer": "my_analyzer",
  "text": ["asd垃圾a滚sd,<a>www</a>.elastic!org?<p>cnelasticsearch</p><b><span>"]
}
PUT test_analyzer/_doc/1
{
  "title":"asd垃圾a滚sd,<a>www</a>.elastic!org?<p>cnelasticsearch</p><b><span>"
}
```

### analyzer 和 search_analyzer

- `analyzer`：为字段指定的分词器，仅对文本字段生效，针对的是源数据字段，也就是source data
- `search_analyzer`：搜索时分词器，即作用于搜索词的分词器，作用对象为，用户传入的搜索词。
- 当 `search_analyzer` 未指定时，其缺省值为 analyzer，若 analyzer 未指定，search_analyzer 和 analyzer 的值都为standard

```bash
PUT test_analyzer
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "my_tokenizer": {
          "type": "pattern",
          "pattern": "[ ,.!?]"
        },
        "my_search_tokenizer": {
          "type": "pattern",
          "pattern": "[<>(){}]"
        }
      },
      "char_filter": {
        "html_strip_char_filter": {
          "type": "html_strip",
          "escaped_tags": [
            "a"
          ]
        },
        "my_char_filter": {
          "type": "mapping",
          "mappings": [
            "滚 => *",
            "垃 => *",
            "圾 => *"
          ]
        }
      },
      "filter": {
        "my_filter": {
          "type": "stop",
          "stopwords": [
            "www"
          ],
          "ignore_case": true
        }
      },
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "char_filter": [
            "my_char_filter",
            "html_strip_char_filter"
          ],
          "filter": [
            "my_filter"
          ],
          "tokenizer": "my_tokenizer"
        },
        "my_search_analyzer": {
          "type": "custom",
          "char_filter": [
            "my_char_filter",
            "html_strip_char_filter"
          ],
          "filter": [
            "my_filter"
          ],
          "tokenizer": "my_search_tokenizer"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title":{
        "type": "text",
        "analyzer": "my_analyzer",
        "search_analyzer": "my_search_analyzer"
      }
    }
  }
}

GET test_analyzer/_analyze
{
  "analyzer": "my_analyzer",
  "text": ["asd垃圾a滚sd,<a>www</a>.elastic!org?<p>cnelasticsearch</p><b><span>"]
}
GET test_analyzer/_analyze
{
  "analyzer": "my_search_analyzer",
  "text": ["ASD垃圾a滚sd<<a>www</a>>elastic)org(<p>cnELASTICSEARCH</p><b><span>"]
}

PUT test_analyzer/_doc/1
{
  "title":"asd垃圾a滚sd,<a>www</a>.elastic!org?<p>cnelasticsearch</p><b><span>"
}
GET test_analyzer/_search
{
  "query": {
    "match": {
      "title": "ASD垃圾a滚sd<<a>www</a>>)(<p>cnELASTICSEARCH</p><b><span>"
    }
  }
}
```

### 文档归一化器：Normalizers

normalizer 与 analyzer 的作用类似，都是对字段进行处理，但是不同之处在于normalizer不会对字段进行分词，也就是说 normalizer 没有 tokenizer

所以 normalizer 是作用于 keyword 类型的字段的，相当于我们需要给 keyword 类型字段做一个额外的处理时，比如转换为小写时就可以用到 normalizer

> `normalizer` 只能用于 `keyword` 类型， `normalizer` 设置的字段不能被分词

```bash
PUT test_normalizer
{
  "settings": {
    "analysis": {
      "normalizer": {
        "my_normalizer": {
          "filter": [
            "lowercase"
          ]
        }
      },
      "analyzer": {
        "my_analyzer": {
          "filter": [
            "uppercase"
          ],
          "tokenizer": "standard"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "keyword",
        "normalizer": "my_normalizer"
      },
      "content": {
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  }
}

PUT test_normalizer/_doc/1
{
  "title":"ELASTIC Org cn",
  "content":"ELASTIC Org cn"
}

GET test_normalizer/_search
{
  "query": {
    "match": {
      "title": "ELASTIC"
    }
  }
}
GET test_normalizer/_search
{
  "query": {
    "match": {
      "content": "ELASTIC"
    }
  }
}
```

### 中文分词器 IK

> [下载](https://github.com/medcl/elasticsearch-analysis-ik)

#### 安装

- 创建插件文件夹：cd {es-root-path}/plugins/ && mkdir ik
- 将插件解压缩到文件夹：{es-root-path}/plugins/ik
- 重新启动 ES 服务

#### 词库文件描述

IKAnalyzer.cfg.xml：IK分词配置文件

主词库：main.dic

英文停用词：stopword.dic，不会建立在倒排索引中

特殊词库：

quantifier.dic：特殊词库：计量单位等
suffix.dic：特殊词库：行政单位
surname.dic：特殊词库：百家姓
preposition：特殊词库：语气词
自定义词库：网络词汇、流行词、特定领域词库等。

#### 分词器

- ik_max_word：会将文本做最细粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国，中华人民，中华，华人，人民共和国，人民，人，民，共和国，共和，和，国国，国歌”，会穷尽各种可能的组合，适合 Term Query；
- ik_smart：会做最粗粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国，国歌”，适合 Phrase 查询。

#### 词库配置

```properties
<properties>
  <comment>IK Analyzer 扩展配置</comment>
  <!--用户可以在这里配置自己的扩展字典 -->
  <entry key="ext_dict">custom/es_extend.dic;custom/es_buzzword.dic</entry>
  <!--用户可以在这里配置自己的扩展停止词字典-->
  <entry key="ext_stopwords"></entry>
  <!--用户可以在这里配置远程扩展字典 -->
  <entry key="remote_ext_dict">http://localhost/api/hotWord</entry>
  <!--用户可以在这里配置远程扩展停止词字典-->
  <entry key="remote_ext_stopwords">http://localhost/api/stopword</entry>
</properties>
```

## 搜索：Query DSL

```json
// 不查看源数据，仅查看元字段
{
  "_source": false,
  "query": {} 
}
```

```json
// 只看以obj.开头的字段
{
  "_source": "obj.*",
  "query": {} 
}
// 查看以obj.或者obj2.开头的字段，
{
  "_source": [ "obj1.*", "obj2.*" ],
  "query": {} 
}
//查看以obj.或者obj2.开头，并且不以.desc为后缀的字段
{
  "_source": {
    "includes": ["obj1.*", "obj2.*"],
    "excludes": ["*.desc"]
  },
  "query": {} 
}
```

### 分页

- from：从低几个文档开始返回，需要为非负数，默认为 ：0
- size：定义要返回的命中数，默认值为：10

```bash
GET <index>/_search
{
  "from": 0,
  "size": 20
}
```

> from + size 必须小于等于 10000
>
> max_result_window：可以解除 from + size 必须小于 10000 的限制，但是如果不清楚其原理，而盲目修改阈值，可能会造成严重后果
>
> track_total_hits：允许 hits.total 返回实际数值，但是会牺牲性能

### 排序

```bash
# sort_field：可以是 _source field，也可以是 meta data field
GET <index>/_search
{
  "sort": [
    {
      "<sort_field>": {
        "order": "desc" // or asc
      }
    }
  ]
}
```

### Url Query

```bash
GET /goods/_search 
# 带参数查询
GET /goods/_search?q=name:xiaomi
# 分页
GET /goods/_search?from=0&size=2&sort=price:asc 
# 精准匹配 exact value
GET /goods/_search?q=date:2040-07-27
# 如果不指定参数名称，即 _all 搜索 相当于在索引的所有字段中检索
GET /goods/_search?q=2040-07-27
```

### 全文检索：Full Text Query

#### match

```bash
GET <index>/_search
{
  "query": {
    "match": {
      "<field_name>": "<field_value>"
    }
  }
}
```

```json
// match_all：匹配所有结果的子句
{
  "query": {
    "match_all": {}
  }
}
// 短语查询，它用于匹配包含指定短语的文档 而不会匹配单个词条
{
  "query": {
    "match_phrase": {
      "<field_name>": "<field_value>"
    }
  }
}
```

#### 精准查询：Term-Level Query

> term query 不会对搜索词分词 而且会保留搜索词原有的所有属性，如大小写、标点符号等

```json
{
  "query": {
    "term": {
      "<keyword_field_name>": "<field_value>"
    }
  }
}
{
  "query": {
    "terms": {
      "<field_name>": [ "<value1>", "<value2>" ]
    }
  }
}
```

#### Ids 查找

```json
{
  "query": {
    "ids" : {
      "values" : ["1", "4", "100"]
    }
  }
}
```

#### 范围查找：Range Query

> 可选参数 `gt`(>) `gte`(>=) `lt`(<) `lte`(<=>)

```json
{
  "query": {
    "range": {
      "age": {
        "gte": 10,
        "lte": 20,
        "boost": 2.0
      }
    }
  }
}
```

#### 布尔查询：Boolean Query

```json
{
  "query": {
    "bool": {
      "filter": [], 	// 必须符合每条件,不计算相关度分数,会被缓存 and
      "must": [],		// 计算相关度得分,多个条件必须同时满足 and
      "must_not": [], // 必须不满足每个条件,不计算相关度评分 not
      "should":  [],	// 可以满足其中若干条件或全部不满足 or
    }
  }
}
```

```json
GET goods_en/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "brand": "Xiaomi"
          }
        },
        {
          "range": {
            "price": {
              "gt": 5000
            }
          }
        }
      ]
    }
  }
}
```

#### minimum_should_match 参数

> minimum_should_match 参数用来指定 should 返回的文档必须匹配的条件的数量或百分比，如果 bool 查询包含至少一个 should 子句，而没有 must 或 filter 子句，则默认值为 1。
>
> 但是如果 bool 查询中同级子句中出现了 must 或者 filter 子句，则 minimum_should_match 的默认值将变为 0。

#### 子查询嵌套

```json
// 条件1：name 中不包含 iphone
// 条件2: 
//       name中包含"phone" 且 价格价格 大于 20000
// 或者  type 等于"phone"  且 （Brand = HuaWei 或 Apple）
{
  "_source": false,
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "name": "iphone"
          }
        }
      ],
      "should": [
        {
          "bool": {
            "must": [
              {
                "match": {
                  "name": "phone"
                }
              },
              {
                "range": {
                  "price": {
                    "gte": 20000
                  }
                }
              }
            ]
          }
        },
        {
          "bool": {
            "must": [
              {
                "term": {
                  "type": "phone"
                }
              },
              {
                "terms": {
                  "brand.keyword": [
                    "HuaWei",
                    "Apple"
                  ]
                }
              }
            ]
          }
        }
      ]
    }
  }
}
```

## 聚合搜索（Aggregations）

### 语法

- 查询条件：指定需要聚合的文档，可以使用标准的 Elasticsearch 查询语法，如 term、match、range 等等。
- 聚合函数：指定要执行的聚合操作，如 sum、avg、min、max、terms、date_histogram 等等。每个聚合命令都会生成一个聚合结果。
- 聚合嵌套：聚合命令可以嵌套，以便更细粒度地分析数据。

```bash
GET <index_name>/_search
{
  "aggs": {
    "<aggs_name>": { // 聚合名称需要自己定义
      "<agg_type>": {
        "field": "<field_name>"
      }
    }
  }
}
```

### 三种类型的聚合

#### 桶聚合：Bucket Aggregations

```json
{
  "aggs": {
    "agg_tag_value_count": {
      "terms": {
        "field": "tags" // 注意如果是文本，请使用 keyword 类型
      }
    }
  }
}
```

```json
{
  "aggs": {
    "agg_price_interval": {
      "range": {
        "field": "price",
        "ranges": [
          {
            "to": 1000
          },
          {
            "from": 1000,
            "to": 5000
          },
          {
            "from": 5000
          }
        ]
      }
    }
  }
}
```

#### 多个字段同时聚合 Multi Terms

```json
{
  "aggs": {
    "agg_name": {
      "multi_terms": {
        "terms": [
          {
            "field": "field_1"
          },
          {
            "field": "field_2"
          }
        ]
      }
    }
  }
}
```

#### 指标聚合：Metrics Aggregations

用于统计某个指标，如最大值、最小值、平均值，可以结合桶聚合一起使用，如按照商品类型分桶，统计每个桶的平均价格。

- 指标函数
  - 平均值：Avg
  - 最大值：Max
  - 最小值：Min
  - 求和：Sum
  - 详细信息：Stats
  - 数量：Value count

```json
{
  "aggs": {
    "max_price": {
      "max": {
        "field": "price"
      }
    },
    "min_price": {
      "min": {
        "field": "price"
      }
    },
    "avg_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```

#### 管道聚合：Pipeline Aggregations

管道聚合用于对聚合的结果进行二次聚合，如要统计绑定数量最多的标签 bucket，就是要先按照标签进行分桶，再在分桶的结果上计算最大值。

```json
{
  "aggs": {
    "my_aggs": {
      "<function>": {
        ...
      },
      "aggs": {
        "my_price_bucket": {
          ...
        }
      }
    },
    "my_min_bucket":{
      "<pip_function>": {
        "buckets_path": "my_aggs>price_bucket" //路径相对于管道聚合的位置，不是绝对路径，路径不能返回“向上”的路径
      }
    }
  }
}
```

### 聚合数据类型

#### doc values

**doc values** 是正排索引的基本数据结构之一，其存在是为了提升排序和聚合效率，默认true，如果确定不需要对字段进行排序或聚合，也不需要通过脚本访问字段值，则可以禁用 doc values 值以节省磁盘空间。

> 从广义来说，doc values 本质上是一个序列化的 列式存储 。列式存储 适用于聚合、排序、脚本等操作，所有的数字、地理坐标、日期、IP 和不分词（ not_analyzed ）字符类型都会默认开启，不支持 **text**和 **annotated_text**类型

```bash
PUT test_doc_values
{
  "mappings": {
    "properties": {
      "text_field": {
        "type": "text",  // 只能用于全文检索
        "fields": {
          "doc_value_filed1": {
            "type": "keyword",
            "doc_values": true  // 可用于检索和聚合
          },
          "doc_value_filed2": {
            "type": "keyword",
            "doc_values": false // 可检索不可聚合排序
          }
        }
      },
      "doc_value_filed1": {
        "type": "long",
        "doc_values": true // 可用于检索和聚合
      },
      "doc_value_filed3": {
        "type": "long",
        "doc_values": false // 可用于检索和聚合
      }
    }  
  }
}
```

#### fielddata

查询时内存数据结构，在首次用当前字段聚合、排序或者在脚本中使用时，需要字段为 fielddata 数据结构，并且创建倒排索引保存到堆中。与 doc value 不同，当没有doc value 的字段需要聚合时，需要打开 fielddata，然后临时在内存中建立正排索引，fielddata 的构建和管理发生在 JVM Heap 中。Fielddata 默认是不启用的，因为 text 字段比较长，一般只做关键字分词和搜索，很少拿它来进行全文匹配和聚合还有排序。

```bash
PUT /<index>/_mapping
{
  "properties": {
    "tags": {
      "type": "text",
      "fielddata": true  // true：开启fielddata;	false：关闭 fielddata
    }
  }
}
```

### 嵌套聚合

```json
{
  "aggs": {
    "<agg_name>": {
      "<agg_type>": {
        "field": "<field_name>"
      },
      "aggs": {
        "<agg_name_child>": {
          "<agg_type>": {
            "field": "<field_name>"
          }
        }
      }
    }
  }
}
```

```json
//统计不同品牌商品中的每个类型的商品数量
{
  "size": 0,
  "aggs": {
    "term_brand": {
      "terms": {
        "field": "brand.keyword"
      },
      "aggs": {
        "term_type": {
          "terms": {
            "field": "type.keyword"
          }
        }
      }
    }
  }
}
```

```json
//统计不同等级的商品的价格信息（平均价格、最高价、最低价等）
{
  "size": 0, 
  "aggs": {
    "term_lv": {
      "terms": {
        "field": "lv.keyword"
      },
      "aggs": {
        "agg_price": {
          "stats": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

### 分页和排序

- size：对聚合结果查询的数据量，默认值为 10，如 size: 20，则表示取前 20 条数据。
- order_type：对结果特定属性值排序，有两个可选项
  - _count：按照文档数量排序
  - _key：对聚合结果的 key 值排序
  - _term：7.x 版本中已废弃，但尚可使用，ES 8.x 之后版本已不再支持。
- order_value：支持正序和倒序
  - asc：正序排列
  - desc：倒序排列

```bash
GET <index_name>/_search
{
  "size": 0, // 表示在查询结果中不现实源数据
  "aggs": {
    "term_lv": {
      "terms": {
        "field": "<doc_value_field>",
        "size": 10, // 对聚合结果取前 10 条数据
        "order": {
          "<order_type>": "<order_value>"
        }
      }
    }
  }
}
```

#### 多字段排序

类似于 order by a,b,即先按照 a 字段排序，值相同时按照字段 b 排序。

```json
//按照品牌名称聚合
//要求先按照品牌所拥有的商品数量排序
//商品数量相同则按照商品名称排序
{
  "aggs": {
    "first_sort": {
      "terms": {
        "field": "brand.keyword",
        "order": [
          {
            "_count": "desc"
          },
          {
            "_key": "asc"
          }
        ]
      }
    }
  }
}
```

#### 多层聚合嵌套排序

```json
// 按照品牌字段排序，在对同一个品牌下不同的标签排序，都按照数量倒序排列。
{
  "size": 0,
  "aggs": {
    "agg_term_brand": {
      "terms": {
        "field": "brand.keyword",
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "agg_term_tags": {
          "terms": {
            "field": "tags.keyword",
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}
```

#### 按照内层聚合排序

```json
// 按照平均价格对手机品牌由低到高排序？
{
  "size": 0,
  "aggs": {
    "agg_term_tag": {
      "terms": {
        "field": "brand.keyword",
        "order": {
          "agg_stats_price.avg": "asc"
        }
      },
      "aggs": {
        "agg_stats_price": {
          "stats": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

```json
// 统计不同标签下商品数量
// 首先按照标签下商品数量倒序排列
// 当商品数量相同的时候，按照标签下商品的平均价格由高到低排序
{
  "aggs": {
    "terms_tags": {
      "terms": {
        "field": "tags.keyword",
        "order": [
          {
            "_count": "desc" // 首先按照标签桶内文档数量倒序排列
          },
          {
            "stats_price.avg": "desc" // 按照平均价格排序
          }
        ]
      },
      "aggs": {
        "stats_price": {
          "stats": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

### 过滤器

- Filter 用于局部聚合查询条件过滤，可在指定聚合函数内嵌套使用

```json
{
  "aggs": {
    "phone_agg": {
      "filter": {},
      "aggs": {}
    }
  }
}
```

```json
// 分别统计所有商品的平均价格和统计商品类型为 phone 的商品的平均价格
{
  "size": 0,
  "aggs": {
    "avg_price": { "avg": { "field": "price" } },
    "phone_agg": {
      "filter": { "term": { "type.keyword": "phone" } },
      "aggs": {
        "avg_price": { "avg": { "field": "price" } }
      }
    }
  }
} 
```

```json
// 当我们需要按照商品类型分桶，但是希望统计耳机、手机、电视三个分桶的数据总和。
{
  "size": 0,
  "aggs": {
    "agg_stats": {
      "filter": {
        "terms": {
          "type.keyword": [
            "耳机",
            "手机",
            "电视"
          ]
        }
      },
      "aggs": {
        "stats": {
          "stats": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

- Filters

```json
{
  "size": 0,
  "aggs": {
    "types": {
      "filters": {
        "filters": {
          "phone": {
            "term": {
              "type.keyword": "phone"
            }
          },
          "erji": {
            "term": {
              "type.keyword": "erji"
            }
          }
        }
      }
    }
  }
}
// or 
{
  "size": 0,
  "query": {
    "terms": {
      "type": [
        "phone",
        "erji"
      ]
    }
  },
  "aggs": {
    "phone_agg": {
      "terms": {
        "field": "type.keyword"
      }
    }
  }
}
```

- 全局聚合过滤

```bash
POST goods/_search?filter_path=aggregations
{
  "size": 0,
  "query": {
    "term": {
      "type.keyword": "phone"
    }
  },
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```

- Global

```json
// avg_price 的计算结果是基于 query 的查询结果的，而 all_avg_price 的聚合是基于 all data 的
{
  "size": 10,
  "query": {
    ...
  },
  "aggs": {
    "avg_price": {
      ...
    },
    "all_avg_price": {
      "global": {},
      "aggs": {
        ...
      }
    }
  }
}
```

```json
{
  "size": 0,
  "query": {
    "range": {
      "price": {
        "gte": 20000
      }
    }
  },
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    },
    "all_avg_price": {
      "global": {},
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    },
    "muti_avg_price": {
      "filter": {
        "range": {
          "price": {  
            "lte": 20000
          }
        }
      }, 
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

- Post Filter 后置过滤

```json
{
  "aggs": {
    ...
  },
  "post_filter": {
    ...
  }
}
```

> 以上查询中 post filter 对聚合结果没有影响。可以理解为聚合的后置过滤器

```json
// 按照标签聚合，并展示IOS标签下的商品信息
{
  "aggs": {
    "tags_bucket": {
      "terms": {
        "field": "tags.keyword"
      }
    }
  },
  "post_filter": {
    "term": {
      "tags.keyword": "IOS"
    }
  }
}
```

### 对聚合结果查询：Top Hits

Top hits（顶部命中）是一个聚合功能，用于在查询结果中返回每个桶（bucket）中的顶部 N 个文档。这对于需要在聚合结果中查看每个桶中的最相关或最高评分文档的情况非常有用。

```json
// 按照商品类型分类聚合，对每个分桶展示相关的前 5 条文档
{
  "size": 0,
  "aggs": {
    "tags_bucket": {
      "terms": {
        "field": "type.keyword"
      },
      "aggs": {
        "top_agg": {
          "top_hits": {
            "size": 10,
            "sort": [
              {
                "price": {
                  "order": "desc"
                }
              }
            ],
            "from": 0
          }
        }
      }
    }
  }
}
```

### 对聚合结果排序：Bucket Sort

Bucket Sort 是一种聚合操作，用于对桶（bucket）进行排序。它可以根据指定的字段对聚合结果中的桶进行排序，以便按照特定的顺序呈现数据。

```json
{
  "aggs": {
    "aggregation_name": {
      "terms": {
        "field": "字段名"
      },
      "aggs": {
        "sort_field": {
          "bucket_sort": {
            "sort": [
              {
                "字段名": {
                  "order": "排序顺序"
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

> 其中，“aggregation_name” 是聚合操作的名称，“字段名” 是要基于其进行排序的字段。您可以选择指定多个排序字段以及每个字段的排序顺序，如 “asc”（升序）或 “desc”（降序）。

```json
// 假设我们有一个名为 “sales” 的索引，其中包含了销售数据，包括产品名称和销售金额。我们想要按照销售金额对产品进行排序，并获取销售金额最高的前 5 个产品。
{
  "size": 0,
  "aggs": {
    "top_products": {
      "terms": {
        "field": "product_name",
        "size": 5
      },
      "aggs": {
        "sort_sales_amount": {
          "bucket_sort": {
            "sort": [
              {
                "sales_amount": {
                  "order": "desc"
                }
              }
            ],
            "size": 5
          }
        }
      }
    }
  }
}
```

> 在这个例子中，我们首先使用 “terms” 聚合按照 “product_name” 字段进行分桶，并设置 “size” 为 5，以获取前 5 个产品。然后，在每个桶内部，使用 “bucket_sort” 对桶进行排序，根据 “sales_amount” 字段的值进行降序排序。最后，我们设置 “size” 为 5，以获取每个桶内销售金额最高的前 5 个产品。

### 常见的查询函数

#### histogram 用于区间统计，如不同价格商品区间的销售情况

```bash
GET product/_search?size=0
{
  "aggs": {
    "<histogram_name>": {
      "histogram": {
        "field": "price", #字段名称
        "interval": 1000, #区间间隔
        "keyed": true, #返回数据的结构化类型
        "min_doc_count": <num>, #返回桶的最小文档数阈值，即文档数小于num的桶不会被输出
        "missing": 1999 #空值的替换值，即如果文档对应字段的值为空，则默认输出1999（参数值）
      }
    }
  }
}
```

#### date-histogram 基于日期的直方图，比如统计一年每个月的销售额

```bash
GET product/_search?size=0
{
  "aggs": {
    "my_date_histogram": {
      "date_histogram": {
        "field": "createtime", #字段需为date类型
        "<interval_type>": "month", #时间间隔的参数可选项
        "format": "yyyy-MM",  #日期的格式化输出
        "extended_bounds": { #输出空桶
          "min": "2020-01",
          "max": "2020-12"
        }
      }
    }
  }
}
```

- interval_type：时间间隔的参数可选项
  - fixed_interval：ms（毫秒）、s（秒）、 m（分钟）、h（小时）、d（天），注意单位需要带上具体的数值，如2d为两天。需要当心当单位过小，会导致输出桶过多而导致服务崩溃。
  - calendar_interval：
- 时间
  - year
  - quarter
  - month
  - week
  - day
  - hour
  - minute
- 在 8.x 版本中 interval 已弃用，如果你使用的是 7.x 的版本，你将看到下面一段话： 而如果你使用的是 8.x 的版本，那么系统将返回错误。
- missing：指定在日期字段缺失时，如何处理该时间段。
- min_doc_count：指定在时间段内至少需要满足的文档数量才会包含在结果中。默认值为0，表示即使时间段内没有文档，也会返回结果。
- time_zone：指定聚合操作中使用的时区。默认情况下，Elasticsearch使用服务器的时区设置。如："time_zone": "Asia/Shanghai",
- offset：指定聚合结果中时间段的偏移量。可以通过提供时间单位和数量来指定偏移量。例如，"+1h"表示时间段向后偏移1小时。
- keyed：指定是否将结果按时间段的键值对形式返回。如果设置为true，每个时间段的结果将以键值对的形式返回，默认为false。
- extended_bounds：指定自定义的时间范围。可以通过提供"min"和"max"参数来限制聚合操作的时间范围。

#### percentile

percentiles：用于评估当前数值分布情况，比如 99 percentile 是 1000 ， 是指 99%的数值都在 1000 以内。常见的一个场景就是我们制定 SLA 的时候常说 99% 的请求延迟都在100ms 以内，这个时候你就可以用 99 percentile 来查一下，看一下 99 percenttile 的值如果在 100ms 以内，就代表 SLA 达标了。

```bash
GET <index_name>/_search?size=0
{
  "aggs": {
    "<percentiles_name>": {
      "percentiles": {
        "field": "price",
        "percents": [
  				percent1，				#区间的数值，如5、10、30、50、99 即代表5%、10%、30%、50%、99%的数值分布
  				percent2，
  				...
        ]
      }
    }
  }
}
```

> percentile_ranks： percentile rank 其实就是 percentiles 的反向查询，比如我想看一下 1000、3000 在当前数值中处于哪一个范围内，你查一下它的 rank，发现是95，99，那么说明有 95% 的数值都在 1000 以内，99% 的数值都在 3000 以内。

```json
GET <index_name>/_search?size=0
{
  "aggs": {
    "<percentiles_name>": {
      "percentile_ranks": {
        "field": "<field_value>",
        "values": [
          rank1,
          rank2,
          ...
        ]
      }
    }
  }
}
```

### 邻接矩阵：Adjacency matrix

- 社交网络分析：在社交网络中，Adjacency Matrix聚合可以用于分析用户之间的关注关系、朋友关系、共同兴趣等，以揭示社交网络的结构和特征。
- 推荐系统：Adjacency Matrix聚合可以用于构建用户之间的关联图，并根据用户之间的共同兴趣、相似性等推荐相关的内容或用户。
- 知识图谱分析：在知识图谱中，Adjacency Matrix聚合可以用于分析实体之间的关系，例如实体之间的相似性、层级关系、关联关系等。
- 文本分析：Adjacency Matrix聚合可以用于分析文本之间的相似性和关联性，从而帮助我们理解文本之间的关系，例如文档之间的共同主题、相关性等。

> 总的来说，Adjacency Matrix聚合提供了一种强大的方式来分析和可视化数据中的关系和连接。它可以帮助我们发现隐藏的模式、洞察实体之间的关联，进而支持我们做出更好的决策和提供更好的用户体验。

```bash
DELETE emails
PUT /emails/_bulk?refresh
{ "index" : { "_id" : 1 } }
{ "accounts" : ["a", "f"]}
{ "index" : { "_id" : 2 } }
{ "accounts" : ["a", "b"]}
{ "index" : { "_id" : 3 } }
{ "accounts" : ["c", "b"]}

GET emails/_search
GET emails/_search?size=0
{
  "aggs" : {
    "interactions" : {
      "adjacency_matrix" : {
        "filters" : {
          "A" : { "terms" : { "accounts" : ["a", "d"] }},
          "B" : { "terms" : { "accounts" : ["b", "e"] }},
          "C" : { "terms" : { "accounts" : ["c", "f"] }}
        }
      }
    }
  }
}
```

- 案例

```bash
# 假设有电影索引，按照不同的属性对其分类。
PUT 电影/_bulk?refresh
{ "index" : { "_id" : 1 } } 
{  "name":"爱情片","accounts" : ["妈妈的朋友", "霸王别姬"]}
{ "index" : { "_id" : 2 } } 
{ "name":"动作片","accounts" : ["妈妈的朋友", "道士上山"]}
{ "index" : { "_id" : 3 } } 
{ "name":"恐怖片","accounts" : ["午夜凶铃", "道士上山"]}
```

```bash
# 统计每个人的喜好和不同人之间的共同爱好
GET 电影/_search
{
  "size": 0,
  "aggs" : {
    "interactions" : {
      "adjacency_matrix" : {
        "filters" : {
          "张飞" : { "terms" : { "accounts.keyword" : ["妈妈的朋友", "苹果"] }},
          "刘备" : { "terms" : { "accounts.keyword" : ["道士上山", "阿甘正传"] }},
          "关羽" : { "terms" : { "accounts.keyword" : ["午夜凶铃", "霸王别姬"] }}
        }
      },
      "aggs": {
        "top": {
          "top_hits": {
            "size": 10
          }
        }
      }
    }
  }
}
```
