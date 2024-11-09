---
title: 15.ElasticSearch与搜索
category: Django框架
tags:
  - framework
  - django
  - elastic search
  - search
date: 2023-1-15 00:00:00
---



## ElasticSearch

[Elasticsearch](https://www.elastic.co/cn/elasticsearch/)是一个基于Lucene的搜索服务器。由Java开发而来，它提供了一个分布式多用户能力的全文搜索引擎，基于Restfull Web接口。

### 安装

这里使用docker容器技术来部署ElasticSearch服务器。

```bash
docker run -d --name es01 -p 9200:9200 -p 9300:9300 -e discovery.type=single-node -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:8.7.0
```

> 默认9200端口，开发9300端口。
>
> `ES_JAVA_OPTS`：限制内存。

### 配置

[elasticsearch-analysis-ik 8.7.0](https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v8.7.0)是一个服务于ElasticSearch的插件，用于将一段文字划分为一个个的关键字，即将句子拆分为词语或字，而拆分中文需要使用中文分词器IK。

1. 上传

```bash
docker cp ./elasticsearch-analysis-ik-8.7.0.zip es01:/usr/share/elasticsearch/plugins/
```

2. 安装IK及配置跨域

```bash
# 进入容器
docker exec -it es01 bash

# 进入plugins目录
cd plugins

# 解压到ik目录下
unzip elasticsearch-analysis-ik-8.7.0.zip -d ik

# 删除压缩包，不然会导致重启失败。
rm -rf elasticsearch-analysis-ik-8.7.0.zip

# 进入config目录
cd ../config

# 编辑elasticsearch.yml
vi elasticsearch.yml
```

添加如下内容：

```yml
http.cors.enabled: true
http.cors.allow-origin: "*"
```

> 该配置是允许跨域。
>
> 同时关闭xpack安全特性（8以上版本的默认配置才需要修改）`xpack.security.enabled: false`。

3. 退出容器并重启

```bash
# 退出容器
exit

# 重启容器
docker restart es01
```

4. 测试

跨域测试：

```bash
curl localhost:9200
```

> 出现以下内容及代表成功
> ```json
> {
>   "name" : "1565e199b701",
>   "cluster_name" : "docker-cluster",
>   "cluster_uuid" : "aYV_LTkKTiCI6oSPNabuTw",
>   "version" : {
>     "number" : "8.7.0",
>     "build_flavor" : "default",
>     "build_type" : "docker",
>     "build_hash" : "09520b59b6bc1057340b55750186466ea715e30e",
>     "build_date" : "2023-03-27T16:31:09.816451435Z",
>     "build_snapshot" : false,
>     "lucene_version" : "9.5.0",
>     "minimum_wire_compatibility_version" : "7.17.0",
>     "minimum_index_compatibility_version" : "7.0.0"
>   },
>   "tagline" : "You Know, for Search"
> }
> ```

IK分词器测试：

请先安装Kibana，并使用Dev Tools控制台。

```json
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "夜里的寒蛰在我手心发着冷，他挣扎得很凶。我将手舞动了一下，让那只昏迷了的昆虫在空中支持了片刻后坠落到地上。在这一片漆黑中，我听见了它翅膀摔打的声响。"
}
```

> 成功分词及代表安装成功
>
> `analyzer`：分词器，以下是IK预设的分词器。
>
> 1. `ik_smart`：智能分词（最精确分词）。
> 2. `ik_max_word`：最细粒度分词。
> 3. `ik_synonym`：同义词合并。
> 4. `ik_pinyin`：汉字转拼音。
> 5. `ik_dict_pinyin`：汉转拼，且可以自定义拼音。

### 结构

1. indices（索引，类似表）
2. types（类型，7.0以上版本已弃用，统一为`_doc`类型）
3. document（文档，数据）

它遵循Restfull风格接口。

| method | url  | description |
| :------: | :----: | :-----------: |
| POST | 索引/类型/ | 创建文档（随机ID） |
| PUT | 索引/类型/文档ID/ | 修改/创建文档（指定ID） |
| DELETE | 索引/类型/文档ID/ | 删除文档 |
| GET | 索引/类型/文档ID/ | 查询文档 |

### 索引操作

1. 创建索引

```json
PUT /index_name/
{
  "mappings": {
    "properties": {
      "field_name": {
        "type": "text"
      },
      ...
    }
  }
}
```

2. 删除索引

```json
DELETE /index_name/
```

3. 查询索引

```json
// 获取所有信息
GET /index_name/
```

4. 修改索引

```json
// 新增字段
PUT /index_name/_mapping/
{
  "properties":{
    "new_field":{
      "type":"text"
    }
  }
}

// 修改设置
PUT /index_name/_settings/
{
  "number_of_replicas": 2
}
```

### 文档操作

1. 创建文档

```json
// 随机ID创建
POST /index_name/_doc/
{
  "field_name": value
}
```

2. 删除文档

```json
DELETE /index_name/_doc/id/
```

3. 查询文档

```json
GET /index_name/_doc/id/
```

4. 修改文档

```json
// 指定ID修改或创建所有字段
PUT /index_name/_doc/1/
{
  "field_name": value,
  ...
}
  
// 指定ID修改指定字段
POST /index_name/_update/id/
{
  "doc":{
    "field_name": value
  }
}
```

5. 条件查询

```json
// 模糊查询
// match会使用分词器进行匹配
// term会使用精确匹配
GET /index_name/_search?q=field:value

GET /index_name/_search
{
  "query": {
    "match": {
      "field": value
    }
  }
}

// match_all查询所有数据
GET /index_name/_search
{
  "query": {
    "match_all": {}
  }
}

// 高亮查询
GET /index_name/_search
{
  "query": {
    "match": {
      "field": value
    }
  },
  "highlight": {
    "pre_tags": "<span style='color:#e1251b'>",
    "post_tags": "</span>", 
    "fields": {
      "field":{}
    }
  }
}

// 字段过滤
GET /index_name/_search
{
  "query": {
    "match": {
      "field": value
    }
  },
  "_source": ["field", ...]
}
              
// 排序查询, desc降序，asc升序
GET /index_name/_search
{
  "query": {
    "match": {
      "field": value
    }
  },
  "sort": [
    {
      "field": {
        "order": "desc"
      }
    }
  ]
}

// 分页查询
// from：offset起始值
// size：数据量
// from = (page - 1) * size
GET /index_name/_search
{
  "query": {
    "match": {
      "field": value
    }
  },
  "from": 0,
  "size": 10
}

// 逻辑查询
// must：and, 与, &&
// should：or, 或, ||
// must_not：not, 非, !
GET /index_name/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "field": value
          }
        },{
          "match": {
            "field": value
          }
        }
      ]
    }
  }
}

// 比较查询
// 大于：gt
// 小于：lt
// 等于：eq
// 大于等于：gte
// 小于等于：lte
GET /index_name/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "field": {
              "gte": num1,
              "lte": num2
            }
          }
        }
      ]
    }
  }
}
```

### 其他操作

1. 获取健康值

```json
GET /_cat/health/
```



## 其他相关工具

Kibana和Head是服务于ElasticSearch开源的基于浏览器的可视化平台插件。

### Kibana

1. 安装

```bash
docker run --name kibana -e ELASTICSEARCH_HOSTS=http://172.17.0.2:9200 -p 5601:5601 -d kibana:8.7.0
```

> 版本需要对应。
>
> 此处的ip可以通过`docker inspect es`查看。

2. 访问

```http
http://127.0.0.1:5601/
```

### Head

> 官方已不再维护，建议使用Kibana自带的Dev Tools替代Head插件。

1. 安装

```bash
docker run -d --name head -p 9100:9100 mobz/elasticsearch-head
```

2. 配置

```bash
# 进入容器
docker exec -it es_admin /bin//bash
# 进入_site目录
cd /usr/src/app/_site
# 编辑文件
vim vendor.js
```

> 修改如下内容：
>
> ```js
> # 6886行
> application/json;charset=UTF-8
> # 7573行
> application/json;charset=UTF-8
> ```

最后使用`docker restart es_admin`重启即可。

3. 访问

```http
http://127.0.0.1:9100/
```



## Python集成

详情参见[Python Elasticsearch Client](https://elasticsearch-py.readthedocs.io/en/master/)。

1. 安装

```bash
pip3 install elasticsearch
```

> 注意使用的版本。

2. 配置

```python config/es_config.py
HOST = "127.0.0.1"
PORT = 9200
SCHEME = "http"
```

3. 创建客户端

```python
from elasticsearch import Elasticsearch

es = Elasticsearch([{"host": HOST, "port": PORT, "scheme": SCHEME}])
```

4. 数据操作

```python
# 创建/更新
es.index(index='',document='',id='')
# 创建
es.create(index='',id='',document='')
# 修改
es.update(index='',id='',body={})
# 删除
es.delete(index='',id='')
# 查询
es.search(index='',body={})
```

> 详情API参见[Elasticsearch 8.0.0 文档](https://elasticsearch-py.readthedocs.io/en/master/api.html)。
>
> 需要在视图中实现数据的初始化和同步。
