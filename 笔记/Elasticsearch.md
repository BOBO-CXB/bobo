# Elasticsearch

### 基本概念

#### 类比关系型数据库

| Elasticsearch                 | 关系型数据库        |
| ----------------------------- | :------------------ |
| 索引（index）                 | 数据库（database）  |
| 类型（type）（ES7中完全删除） | 表（table）         |
| 映射（mapping）               | 表结构（schema）    |
| 文档（document）              | 行（row）           |
| 字段（field）                 | 列（column）        |
| 反向索引                      | 索引                |
| 查询DSL                       | SQL                 |
| GET http://....               | SELECT * FROM table |
| PUT  http://....              | UPDATE table SET    |
| DELETE  http://.....          | DELETE              |

#### 1、index（索引）

​		索引，是ES的一个逻辑存储，对应关系型数据库中的库，ES可以把索引数据存放到服务器中，也可以sharding(分片)后存储到多台服务器上。每个索引有一个或多个分片，每个分片可以有多个副本。

#### 2、type（类型）

​		类型，ES中，一个索引可以存储多个用于不同用途的对象，可以通过类型来区分索引中的不同对象，对应关系型数据库中表的概念。但是在ES6.0开始，类型的概念被废弃，ES7中将它完全删除。

#### 3、document（文档）

​	    文档，存储在ES中的主要实体叫文档，可以理解为关系型数据库中表的一行数据记录。每个文档由多个字段（field）组成。区别于关系型数据库的是，ES是一个非结构化的数据库，每个文档可以有不同的字段，并且有一个唯一标识。

#### 4、mapping（映射）

​		映射是对索引库中的索引字段及其数据类型进行定义，类似于关系型数据库中的表结构。ES默认动态创建索引和索引类型的mapping，这就像是关系型数据中的，无需定义表机构，更不用指定字段的数据类型。当然也可以手动指定mapping类型。

#### 5、shard（分片）

​	如果我们的索引数据量很大，超过硬件存放单个文件的限制，就会影响查询请求的速度，Es引入了分片技术。一个分片本身就是一个完成的搜索引擎，文档存储在分片中，而分片会被分配到集群中的各个节点中，随着集群的扩大和缩小，ES会自动的将分片在节点之间进行迁移，以保证集群能保持一种平衡。分片有以下特点：

- ES的一个索引可以包含多个分片（shard）；
- 每一个分片（shard）都是一个最小的工作单元，承载部分数据；
- 每个shard都是一个lucene实例，有完整的简历索引和处理请求的能力；
- 增减节点时，shard会自动在nodes中负载均衡；
- 一个文档只能完整的存放在一个shard上
- 一个索引中含有shard的数量，默认值为5，在索引创建后这个值是不能被更改的。
- 优点：水平分割和扩展我们存放的内容索引；分发和并行跨碎片操作提高性能/吞吐量；
- 每一个shard关联的副本分片（replica shard）的数量，默认值为1，这个设置在任何时候都可以修改。

#### 6、replica（副本）

​	副本（replica shard）就是shard的冗余备份，它的主要作用：

- 冗余备份，防止数据丢失；
- shard异常时负责容错和负载均衡；



### 常用API

#### 查看索引状态

```shell
curl http://localhost:9200/_cluster/health?pretty
```

#### 查看索引

```shell
#加上 ?v 将显示字段名
curl http://localhost:9200/_cat/indices
curl http://localhost:9200/_cat/indices?v

#查看具体某个索引
curl http://localhost:9200/_cat/indices/{index}?v
#模糊匹配
curl -sXGET http://localhost:9200/_cat/indices/wjl*?v
```

#### 查看索引分片情况

```shell
curl http://localhost:9200/_cat/shards?v 

#查询指定索引的分片情况
curl http://localhost:9200/_cat/shards/{index}?v 

#查看不能分配的分片
curl -sXGET http://localhost:9200/_cat/shards?v | grep UNASSIGNED

#查看所有分片的恢复情况
curl http://localhost:9200/_cat/recovery

#查看某个具体索引的分片恢复情况
curl http://localhost:9200/_cat/recovery/{index}

```

#### 数据查询

```shell
curl -X POST http://localhost:9200/system_logger_2023-8/_search -H "Content-type: application/json" -d '{"query": {"bool": {"must": [{"match_all": {}}],"must_not": [],"should": []}},"from": 0,"size": 10,"sort": [],"aggs": {}}'
```

```json
{
    "query": {
        "bool": {
            "must": [
                {
                    "match_all": {}
                }
            ],
            "must_not": [],
            "should": []
        }
    },
    "from": 0,
    "size": 10,
    "sort": [],
    "aggs": {}
}
```



##### 常用查询参数：https://blog.csdn.net/qq_24487005/article/details/123993901

##### 一、常用查询关键字

###### 1、match

​	match是模糊匹配查询，根据分词器(如果创建mapping没有指定分词器，Es将会采取默认的分词器:standard,standard分词将会把匹配的词组分成单个的字,而不是短语)将指定的query查询的语句进行分词匹配。

```json
#查询索引中name为Tom的文档：
{
    'query': {
        'match': {
            'name': 'Tom'
        }
    }
}
```

###### 2、match_all

​		match_all是没有任何条件，检索全部数据

```json
#检索全部数据
{
  "query": {
    "match_all": {}
  }
}
```



###### 3、match_phars

​	match_pharse属于短语匹配,要求查询的词句和匹配的短语顺序必须一致,并且是连续的，但可以设置 `slop` 值来指定查询项之间可以分隔多远的距离

```json
#查询索引中work为程序员的文档：
{
    'query': {
        'match_pharse': {
            'work': '程序员'
        }
    }
}
```

###### 4、fuzzy

​	uzzy查询的时候,不会根据分词器匹配,只会进行拆分,比如查询的是"海zei王",在分词器下(也就是match中)是无法匹配到单个词的，因为它不是一个短语,但是在fuzzy中是可以匹配的，并且fuzzy支持模糊和一定的容错查询匹配,因为它做的是匹配词的拆分,并不是短语。

```json
#查询索引中name为海zei王的文档：
{
    'query': {
        'fuzzy': {
            'name': '海zei王'
        }
    }
}
```

###### 5、multi_match

​		多字段匹配，比如在查询test这个值的时候我们不仅需要在A这个字段中查询，同时它也有可能存在B中，按照普通的写法你可能需要写两次查询，但是使用了multi_search只需要指定具体的fields就可以实现多字段查询

```json
#同时在age字段和phone字段搜索包含38这个数字的文档：
{
    'query': {
        'multi_match': {
            'query': '38',
            'fields':['age','phone']
        }
    }
}
```

###### 6、regexp

​		正则表达式匹配，该匹配模式下我们可以按照正则表达式的符号去匹配具体的值,比如name字段，可以包含有.和*去正则匹配具体的值，?表示任一字符，*表示所有字符，还有其他的正则符号都可以使用。

```json
#匹配手机号以1到9之前的开头，并且第二位是3最后一位是0或者1的手机号的文档：
{
    'query': {
        'regexp': {
            'phone': {
                'value':'[1-9]3.*[0-1]'
            }
        }
    }
}
```

###### 7、wildcard

​		通配符匹配，wildcard和regexp类似,不过它们也有不同之处。regexp的实际匹配能力要大于wildcard，在进行简单的匹配时候，比如名字的*或者?的简单普通匹配,建议使用wildcard而不是regexp,wildcard的效率要高于regexp，regexp可以实现更为复杂的场景,但是效率低一些,通俗的说wildcard是regexp的简化版本.

```json
#采用通配符匹配手机的电话号
{
    'query': {
        'wildcard': {
            'phone': '13643091*'
        }
    }
}
```

###### 8、term

​		term的英文含义表示是:精确的意思，在term查询中，表示做的是精确查询，整词匹配，不会对所匹配项进行拆分。直接以整词进行匹配，如果能查询到就命中该文档

```json
#查询的name为iot的文档
{
    'query': {
        'term': {
            'name': {
                'value':'iot'
            }
        }
    }
}
```

###### 9、 terms

​		terms和term的区别就是terms允许匹配多个值，而term只允许匹配一个值,在进行多值匹配的场景中可以使用terms,terms匹配到其中任何一个值就会认为整个文档是匹配的,terms多个值如果多个都匹配会返回所有文档

```json
#查询age为24、66的任一值，查到就返回文档
{
    'query': {
        'terms': {
            'age': ['24','36']
        }
    }
}
```

###### 10、range

​		range表示一个区间范围查找,这个范围可以是日期或者数值，ES的range比较灵活和明确,可以指定两个边界是否包含，通过参数include_lower:true 、include_upper:true来控制

```json
#查询age为20到30区间所有文档,
#include_lower表示是否包含边界最小值(true表示包含,false表示不包含),
#include_upper表示是否包含边界最大值(true表示包含,false表示不包含)
 
{
    'query': {
        'range': {
            'age': {
                'from': 20,
                'to': 30,
                'include_lower':true,
                'include_upper':false
                }
        }
    }
}

#查询log_time对应时间区间内的所有文档,
#gt/gt表示起始时间，gte包含起始时间点,gt不包含起始时间点,
#lt/lte表示结束时间，lte包结束时间点,lt不包含结束时间点
 
{
    'query': {
        'range': {
                'log_time': {
                    'gte': '2022-04-02 00:00:00',
                    'lt': '2022-04-02 23:00:00',
                    'format': 'yyyy-MM-dd HH:mm:ss',
                }
            }
    }
}
 
 
```

##### 二、组合查询

bool （布尔）过滤器。 这是个 复合过滤器（compound filter） ，它可以接受多个其他过滤器作为参数，并将这些过滤器结合成各式各样的布尔（逻辑）组合。

###### 1、must

​		文档 必须must 匹配这些条件才能被包含进来，与 AND 等价。

```json
#must数组里面可以插入多个关键字查询条件
#以下用例中查询sn为00000000， date_time为2022-04-02 00:00:00 到2022-04-02 23:00:00的文件
{
    'query': {
        'bool': {
                'must': [
                        {'terms': { 'sn': ['00000000']}},
                        {'range': {'date_time': {
                                'gt': '2022-04-02 00:00:00',
                                'lt': '2022-04-02 23:00:00',
                                'format': 'yyyy-MM-dd HH:mm:ss',
                            }}
                        }
                    ]
            }
    }
}
 
```

###### 2、must_not

​		文档 必须不 must_not 匹配这些条件才能被包含进来，与 NOT 等价。

```json
#用例为：查询name不为李白的文件
{
    'query': {
        'bool': {
                'must_not': [
                        {'terms': { 'name': ['李白']}}
                    ]
            }
    }
}
```

###### 3、should

​		至少有一个语句要匹配，与 OR 等价

```json
#用例为：查询name为李白或者age为12 的文件
{
    'query': {
        'bool': {
                'should': [
                        {'terms': { 'name': ['李白']}},
                        {'term': { 'age': '12'}}
                    ]
            }
    }
}
```

###### 4、filter 

​		必须匹配，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。filter不会计算搜索条件相关度分数，也不会根据相关度分数进行排序，相对效率更高一些。

```json
#用例为：查询name为李白的文件
{
    'query': {
        'bool': {
                'filter': [
                        {'terms': { 'name': ['李白']}}
                    ]
            }
    }
}
```

###### 5、must、must_not、should组合用例 

```json
#以下用例中查询
#sn为00000000， date_time为2022-04-02 00:00:00 到2022-04-02 23:00:00
#并且detail不以智能开头或者name为Tom的文档

{
    'query': {
        'bool': {
                'must': [
                        {'terms': { 'sn': ['00000000']}},
                        {'range': {'date_time': {
                                'gt': '2022-04-02 00:00:00',
                                'lt': '2022-04-02 23:00:00',
                                'format': 'yyyy-MM-dd HH:mm:ss',
                            }}
                        }
                    ],
                'must_not': [
                        {'regexp': {'detail':'智能*'}}
                    ],
                'should': [
                        {'match': { 'name': 'Tom'}}
                   ]
            }
    }
}
 
```

##### 三、分页和排序

​		分页检索，可以通过size和from来实现,size返回的条数，默认是10条，from返回的开始。排序,可以根据sort来实现，该字段可以指定索引字段，也可以指定返回结果中的其他字段。

```json
{
    "query":{
        "match_all":{}
    },
    "from":1,
    "size":10，
     "sort": {
           "age":"desc",
           "money":"asc"
       }
}
 
```

##### 返回数据结构

```json
{
    "took": 14,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 10000,
            "relation": "gte"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "system_logger_2023-7",
                "_type": "_doc",
                "_id": "1679663072852451328",
                "_score": 1.0,
                "_source": {
                    "threadId": "506",
                    "exceptionStack": "receive number of bytes :168\n[]",
                    "level": "INFO",
                    "createTime": 1689297857855,
                    "name": "org.hliot.pro.network.monitor.mqtt-server",
                    "context": {
                        "server": "hliot-platform:8844"
                    },
                    "methodName": "bytesRead",
                    "className": "org.hliot.pro.network.monitor.LogNetMonitor",
                    "id": "1679663072852451328",
                    "lineNumber": 39,
                    "message": "receive number of bytes :168",
                    "threadName": "vert.x-eventloop-thread-10"
                }
            }
        ]
    }
}
```

###### 1、took

​		该命令请求花费了多长时间，单位：毫秒。

###### 2、timed_out

​		搜索是否超时。

###### 3、_shards

- total：搜索分片总数。

- successful：搜索成功的分片数量。

- skipped：没有搜索的分片，跳过的分片。

- failed：搜索失败的分片数量。

​		搜索分片信息，_shards 部分告诉我们在查询中参与分片的总数，以及这些分片成功了多少个失败了多少个。正常情况下我们不希望分片失败，但是分片失败是可能发生的。如果我们遭遇到一种灾难级别的故障，在这个故障中丢失了相同分片的原始数据和副本，那么对这个分片将没有可用副本来对搜索请求作出响应。假若这样，Elasticsearch 将报告这个分片是失败的，但是会继续返回剩余分片的结果。

###### 4、hits

​		搜索结果集，返回结果中最重要的部分是 hits ，它包含 total 字段来表示匹配到的文档总数，并且一个 hits 数组包含所查询结果的前十个文档。

- total：返回多少条数据。

  - value：命中总数

  - relation：取值eq(等于)/gte(大于等于)，表示hits.total.value与实际的搜索命中数量的关系

  - ```json
    #表示实际的搜索命中数量超过10000
    "total": {
                "value": 10000,
                "relation": "gte"
            },
    ```

- max_score：返回结果中，最大的匹配度分值。

- hits：默认查询前十条数据，根据分值降序排序。

  - _index：索引库名称。
  - _type：类型名称。
  - id：该条数据的id。
  - score：关键字与该条数据的匹配度分值。
  - _source：索引库中类型，返回结果字段，不指定的话，默认全部显示出来。

#### 删除索引

```shell
curl -XDELETE 'http://localhost:9200/access_logger_2023-9'
```

#### 查看ES在线的节点

```shell
curl localhost:9200/_cat/nodes?v
```

#### ES集群性能优化及维护

https://blog.csdn.net/guokexiaohao/article/details/103105234

### 管理工具

elasticsearch-head 安装

```shell
docker pull docker mobz/elasticsearch-head:5

docker run -p 9100:9100 mobz/elasticsearch-head:5
```

### 数据导出

##### 可以使用Logstash将索引内的文档导入csv，步骤如下：

- 下载安装Logstash [下载地址](https://www.elastic.co/downloads/logstash)
- 编写导出数据的配置文件

```conf
input {
 elasticsearch {
    hosts => "10.193.98.3:19801"
    index => "device_log_1659129831723401216_2023-10"
    query => '{
    "query": {
        "bool": {
            "must": [
                {
                    "match_all": {}
                }
            ],
            "must_not": [],
            "should": []
        }
    },
    "sort": [],
    "aggs": {}
}'
  }
}
 
output {
  csv {
    # This is the fields that you would like to output in CSV format.
    # The field needs to be one of the fields shown in the output when you run your
    # Elasticsearch query
 
    fields => ["createTime", "type", "content" , "timestamp"]
  
    # This is where we store output. We can use several files to store our output
    # by using a timestamp to determine the filename where to store output.    
    path => "E:\deviceData\csv-export.csv"
  }
}
```

- 执行命令

```shell
logstash -f output.conf
```



### 常见问题

##### ES 默认的单节点 shards 数量是 1000 个分片（cluster.max_shards_per_node 为 1000），超出分片数量会导致ES无法写入，可以调整ES的最大分片数量

```shell
#通过API 进行修改
#transient 临时
#persistent 永久
 
PUT /_cluster/settings
    {
      "persistent": {
        "cluster": {
          "max_shards_per_node":10000
        }
      }
    }
```



##### 解决elasticsearch-head连接跨域问题

```shell
#进入elasticsearch 容器中
docker exec -it b5fd350cc62d bash

#查看配置文件路径
cat config/elasticsearch.yml
pwd

#/usr/share/elasticsearch/config/elasticsearch.yml

# 拷贝容器中elasticsearch.yml 文件到当前位置下
docker cp b5fd350cc62d:/usr/share/elasticsearch/config/elasticsearch.yml . 
# 编辑elasticsearch文件
vi elasticsearch.yml 
# 加入以下配置
http.cors.enabled: true
http.cors.allow-origin: "*"
http.cors.allow-headers: "*"
# 将编辑好的文件传输到容器中
docker cp elasticsearch.yml b5fd350cc62d:/usr/share/elasticsearch/config/elasticsearch.yml

#重启容器
docker restart b5fd350cc62d
```



##### 解决elasticsearch-head访问报错406问题

```shell
#修改容器内_site目录下的vendor.js文件 /usr/src/app/_site/vendor.js


#6886行
contentType: "application/x-www-form-urlencoded
改成
contentType: “application/json;charset=UTF-8”
 
#7573行
var inspectData = s.contentType === “application/x-www-form-urlencoded” &&
改成
var inspectData = s.contentType === “application/json;charset=UTF-8” &&

```



