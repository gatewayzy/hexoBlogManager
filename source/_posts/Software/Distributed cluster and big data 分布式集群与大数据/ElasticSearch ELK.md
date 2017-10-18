---
title: ElasticSearch
comments: true
date: 2017-01-12 13:58:05
updated: 2017-01-12 13:58:56
categories: Softwares
tags:
- Big data
- Distributed cluster
---

**说明：**ElasticSearch、logstash、kibana的配置使用。
<!-- more -->

---
参考文章：
* [ES官网: Get Started with Elasticsearch, Kibana, X-Pack](https://www.elastic.co/start)
* [ELKstack 中文指南](https://kibana.logstash.es/content/logstash/get-start/full-config.html)

---
## ELKstack与软件安装

* ELKstack 一般包括Elasticsearch、Logstash、Kibana等套装软件。
* Elasticsearch（ES）通过cluster名称自动加入结点，拓展性强，支持数据量亿级以上，自动选择master，构建索引时查询效率仍比较高。
* 下文的ES、es均代指ElasticSearch。

### 安装ES
下载解压即可，之后配置完成即可使用。

### 安装kibana
下载解压，配置好使用。用于可视化管理。

### 安装x-pack
* 用于ES的Security, monitoring, and more for Elasticsearch and Kibana. 安装方法为，运行命令：
    * es/bin/elasticsearch-plugin install x-pack
    * kibana/bin/kibana-plugin install x-pack

### 安装[elasticsearch-head](https://github.com/mobz/elasticsearch-head)
* es-head是集群可视化管理工具，es5.x版本之后不支持以插件安装es-head，按照github说明安装成独立nodejs web程序即可（安装node、npm，再npm install等）。
* 配置：
    * 允许es跨域请求：添加ES配置`http.cors.enabled: true` `http.cors.allow-origin: "*"` 注意使用*不安全。
    * 允许es认证：添加ES配置`http.cors.allow-headers: Authorization`
    * 由于x-pack认证问题（见`https://github.com/mobz/elasticsearch-head/issues/304`），未成功，所以采用关闭x-pack认证，设置es`xpack.security.enabled: false`。当然也可以把该plugin文件夹删除。
* 如果显示集群状态存在red，可以删除所有的索引，重新构建。
* es-head使用方法可以参考[_head插件对elasticsearch 索引文档的增删改查](http://blog.csdn.net/bsh_csn/article/details/53908406)

### 安装logstash
* [下载并安装logstash](https://www.elastic.co/downloads/logstash)，它可以书写logstash配置生成格式化的日志文件，非常适合日志记录与分析。
* 参考[Elasticsearch5.2.1集群搭建，动态加入节点，并添加监控诊断插件](http://blog.csdn.net/gamer_gyt/article/details/59077189)一文配置logstash。运行后，通过es-head查看生成的日志索引。注意下载文件应该检验sha1是否正确。
* 在logstash中配置logstash的端口等，在启动logstash对应的配置文件中，有输入input、过滤filter、输出output等配置，output中配置相应的es。

### 安装bigdesk
* 使用[bigdesk](https://github.com/hlstudio/bigdesk)进行es集群性能监控。可以查看集群中各个节点的存储、查询、jvm等运行情况。
* 下载之后，将site文件夹重命名放到web容器(如tomcat)下发布访问相应的地址即可，如`http://127.0.0.1:8080/bigdesk/`，再链接相应的集群地址。

### 总结来说
* 整一套ES软件可以当作一个MVC模型，logstash是controller层，es是一个model层，kibana是view层。logstash负责日志分析，通过对输入进行格式化处理生成json数据，es负责数据索引，kibana负责可视化。ls和es基于java，kbn基于nodejs。
* 其他的第三方插件：es-head提供集群索引信息监管，bigdesk提供集群性能监控。

---
## Elasticsearch的配置与使用

### 单节点ES的配置

* 启动es：bin/elasticsearch，访问9200端口查看。用户名密码: `Username: elastic Password: changeme`。安装x-pack的话，查看的时候就会需要该默认用户名密码。
* 重启es：只能查看端口，关闭进程，启动服务。ps -ef | grep elastic，kill -9 2382（进程号），然后再启动es。 
* 配置es：修改conf中的jvm.properties，设置xms、xmx大小等。修改elasticsearch.yml，修改集群名、端口、IP等。启动失败可以查看后文的“问题与解决”。
    

### ES集群配置

* 配置es可以参考[Elasticsearch5.2.1集群搭建，动态加入节点，并添加监控诊断插件](http://blog.csdn.net/gamer_gyt/article/details/59077189)，但是建议`discovery.zen.minimum_master_nodes`至少为2，通讯端口、监听端口、集群节点列表等都是需要配置的。
* 配置kibana可以参考[官方kibana集群设置](https://www.elastic.co/guide/en/kibana/current/production.html#load-balancing).用一个单独的es作为kibana结点。
* ES高性能集群常用三种角色的ES结点：
    1. master 主结点，不放数据但占有空闲资源，用于协调管理。设置node.master: true， node.data: false。
    1. data 数据结点，只存放数据，不能选举为master，用于负载。设置node.master:false， node.data: true 。
    1. 搜索器结点，不是master或data结点，主要负责从其他节点搜索、生成结果。设置node.master: false， node.data: false。

#### ES集群配置demo
* 4个ES结点（1个搜索器结点，3个data结点），集群使用2备份。下面是集群配置。注意集群是根据集群名进行区分的。

1. es-9199的elasticsearch.yml，非master，非data，用于搜索器，比如用于给kibana做连接。

```
cluster.name: esc5.0
node.name: 82.65-9199
path.data: /home/zju/software/elastic/data/data0
path.logs: /home/zju/logs/elasticsearch-0
network.host: 10.15.82.65
http.port: 9199
transport.tcp.port: 9299
discovery.zen.ping.unicast.hosts: ["10.15.82.65:9299", "10.15.82.65:9300", "10.15.82.66:9300", "10.15.82.66:9301"]
discovery.zen.minimum_master_nodes: 2

# add for extension
http.cors.enabled: true
http.cors.allow-origin: "*"
#xpack.security.enabled: false
node.master: false
node.data: false
node.ingest: false
# dda
```

2. es-9200的elasticsearch.yml，不设置结点属性，集群自动分配master和data结点。

```
cluster.name: esc5.0
node.name: 82.65-9200
path.data: /home/zju/software/elastic/data/data1, /home/zju/software/elastic/data/data2
path.logs: /home/zju/logs/elasticsearch-1
network.host: 10.15.82.65
http.port: 9200
transport.tcp.port: 9300
discovery.zen.ping.unicast.hosts: ["10.15.82.65:9300", "10.15.82.66:9300", "10.15.82.66:9301"]
discovery.zen.minimum_master_nodes: 2

# add for extension
http.cors.enabled: true
http.cors.allow-origin: "*"
#xpack.security.enabled: false
# dda
```

3. 其余两个es结点类似的配置。

```
cluster.name: esc5.0
node.name: 82.66-9200
path.data: /home/zju/software/elastic/data/data1-1, /home/zju/software/elastic/data/data1-2
path.logs: /home/zju/logs/elasticsearch-1
network.host: 10.15.82.66
http.port: 9200
transport.tcp.port: 9300
discovery.zen.ping.unicast.hosts: ["10.15.82.65:9299", "10.15.82.65:9300", "10.15.82.66:9300", "10.15.82.66:9301"]
discovery.zen.minimum_master_nodes: 2

# add for extension
http.cors.enabled: true
http.cors.allow-origin: "*"
#xpack.security.enabled: false
# dda
```

#### ES集群启动与重启脚本
* ES启动就是启动各个es结点，每个节点使用`./elasticsearch-5.0.0-9201/bin/elasticsearch &`
* ES关闭使用端口进行kill，shutdown脚本如下。

```
ID=`lsof -i:9201 | awk '{print $2}'`
echo $ID
echo "To kill all the processes:"
echo "##############################"
for id in $ID
do
  kill -9 $id
  echo "killed $id"
done
echo "#############################"
```

* 重启脚本就是先运行shutdown脚本，再运行启动脚本。

### ES的Java API使用

文末示例代码给出Java API对ES进行増查改删、查询与高级查询、批量操作的举例。

### SearchType详解

es在查询时，可以指定搜索类型为`QUERY_THEN_FETCH, QUERY_AND_FEATCH, DFS_QUERY_THEN_FEATCH 和 DFS_QUERY_AND_FEATCH（SACN,COUNT都已不建议使用）`。那么这4种搜索类型有什么区别？

ES天生就是为分布式而生，但分布式有分布式的缺点。比如要搜索某个单词，但是数据却分别在5个分片（Shard)上面，这5个分片可能在5台主机上面。因为全文搜索天生就要排序（按照匹配度进行排名）,但数据却在5个分片上，如何得到最后正确的排序呢？ES是这样做的，大概分两步。
* ES客户端会将这个搜索词同时向5个分片发起搜索请求，这叫Scatter, 
* 这5个分片基于本Shard独立完成搜索，然后将符合条件的结果全部返回，这一步叫Gather。 

客户端将返回的结果进行重新排序和排名，最后返回给用户。也就是说，ES的一次搜索，是一次scatter/gather过程（这个跟map-reduce也很类似）.然而这其中有两个问题:
* 第一、数量问题。比如，用户需要搜索”双黄连”，要求返回最符合条件的前10条。但在5个分片中，可能都存储着双黄连相关的数据。所以ES会向这5个分片都发出查询请求，并且要求每个分片都返回符合条件的10条记录。当ES得到返回的结果后，进行整体排序，然后取最符合条件的前10条返给用户。这种情况，ES5个shard最多会收到10*5=50条记录，这样返回给用户的结果数量会多于用户请求的数量。
* 第二、排名问题。上面搜索，每个分片计算分值都是基于自己的分片数据进行计算的。计算分值使用的词频率和其他信息都是基于自己的分片进行的，而ES进行整体排名是基于每个分片计算后的分值进行排序的，这就可能会导致排名不准确的问题。如果我们想更精确的控制排序，应该先将计算排序和排名相关的信息（词频率等）从5个分片收集上来，进行统一计算，然后使用整体的词频率去每个分片进行查询。

这两个问题，估计ES也没有什么较好的解决方法，最终把选择的权利交给用户，方法就是在搜索的时候指定query type。 
1. query and fetch 
向索引的所有分片（shard）都发出查询请求，各分片返回的时候把元素文档（document）和计算后的排名信息一起返回。这种搜索方式是最快的。因为相比下面的几种搜索方式，这种查询方法只需要去shard查询一次。但是各个shard返回的结果的数量之和可能是用户要求的size的n倍。
2. query then fetch（默认的搜索方式） 
如果你搜索时，没有指定搜索方式，就是使用的这种搜索方式。这种搜索方式，大概分两个步骤，第一步，先向所有的shard发出请求，各分片只返回排序和排名相关的信息（注意，不包括文档document)，然后按照各分片返回的分数进行重新排序和排名，取前size个文档。然后进行第二步，去相关的shard取document。这种方式返回的document可能是用户要求的size的n倍
3. DFS query and fetch 
这种方式比第一种方式多了一个初始化散发(initial scatter)步骤，有这一步，据说可以更精确控制搜索打分和排名。这种方式返回的document与用户要求的size是相等的。
4. DFS query then fetch 
比第2种方式多了一个初始化散发(initial scatter)步骤。这种方式返回的document与用户要求的size是相等的。

DSF是什么缩写？初始化散发是一个什么样的过程？

从es的官方网站我们可以指定，初始化散发其实就是在进行真正的查询之前，先把各个分片的词频率和文档频率收集一下，然后进行词搜索的时候，各分片依据全局的词频率和文档频率进行搜索和排名。显然如果使用`DFS_QUERY_THEN_FETCH`这种查询方式，效率是最低的，因为一个搜索，可能要请求3次分片。但，使用DFS方法，搜索精度应该是最高的。 
至于DFS是什么缩写，没有找到相关资料，这个D可能是Distributed，F可能是frequency的缩写，至于S可能是Scatter的缩写，整个单词可能是分布式词频率和文档频率散发的缩写。 
总结一下，从性能考虑`QUERY_AND_FETCH`是最快的，`DFS_QUERY_THEN_FETCH`是最慢的。从搜索的准确度来说，DFS要比非DFS的准确度更高。

---
## Logstash的配置与使用

参考文章：[logstash日志分析的配置和使用](http://yincheng.site/logstash)

### 书写配置文件

* 配置文件主要有input、filter、output，功能强大，支持多种输入输出，filter进行自定义加工。
* 对nginx、Apache等服务器的访问日志支持性非常好。自带IP解析为goip、clientUA解析为userAgent设备信息等。
* 支持grok正则匹配、date时间匹配、mutate数据修改、goip查询归类、userAgent设备解析等。参考[Logstash 最佳实践](http://udn.yyuap.com/doc/logstash-best-practice-cn/filter/grok.html)


### Logstash将MySQL导入到ES

* 参考文章：
    * [ElasticSearch与MySQL数据同步以及修改表结构](https://github.com/leotse90/blogs/blob/master/ElasticSearch%E4%B8%8EMySQL%E6%95%B0%E6%8D%AE%E5%90%8C%E6%AD%A5%E4%BB%A5%E5%8F%8A%E4%BF%AE%E6%94%B9%E8%A1%A8%E7%BB%93%E6%9E%84.md)
    * [MySQL到Elasticsearch的同步之路](http://suclogger.com/MySQL%E5%88%B0Elasticsearch%E7%9A%84%E5%90%8C%E6%AD%A5%E4%B9%8B%E8%B7%AF/)
    * [Logstash Reference [5.4] » Input plugins » jdbc](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-jdbc.html)
* Logstash Reference [5.4]的官网流程主要为：
    * 下载jdbc驱动，书写logstash配置文件，官网有参数说明，启动logstash，查看ES集群。
    * 遇到的问题：jvm堆溢出(修改jvm.properties设置)、`retrying failed action with response code: 429 `(提示信息不知道是不是并发问题，但是数据已经导入成功)
    * 150万的短文本数据很快就导入完成，用时不到1min。主片存储空间2.5G(毕竟Lucene空间换时间)。
* 使用Demo：
    * 配置文件看是否需要修改端口，jvm.properties文件等。
    * 下面配置文件中，注意：schedule不设置就是立即执行，设置就是类似crontab定时，设置`clean_run=false`(默认true)就是`sql_last_value`为0（这一点不确定），`use_column_value`、`tracking_column`用于指定要track的栏目，指定之后就可以用`sql_last_value`存储上次更新后的值，支持track number和timestamp类型，建议用timestamp（考虑一下原有数据更新怎么办？数据软删除怎么办？）。【这几个重要的参数还是需要参考官网：[Logstash Reference [5.4] » Input plugins » jdbc](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-jdbc.html)】

```
# 用logstash同步mysql数据，Logstash-5.4.0为例，schedule是定时配置，
input {
  #jdbc {
    #type => "qa"
    #jdbc_driver_library => "~/software/elastic/logstash-5.4.0-9600/lib/mylib/mysql-connector-java-5.1.35.jar"
    #jdbc_driver_class => "com.mysql.jdbc.Driver"
    #jdbc_connection_string => "jdbc:mysql://10.15.82.58:3306/qa"
    #jdbc_user => "hhh"
    #jdbc_password => "hehe"
    ##schedule => "0 5 * * *"
    ##schedule => "*/1 * * * *"
    #statement => "SELECT id, question, body, answer, source, source_id, timestamp edit_time from qa_all WHERE timestamp >:sql_last_value"
    #use_column_value => true
    #tracking_column => edit_time
    #clean_run => false
  #}

  jdbc {
    type => "logstash-logs"
    jdbc_driver_library => "~/software/elastic/logstash-5.4.0-9600/lib/mylib/mysql-connector-java-5.1.35.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://10.15.82.50:3306/aaa"
    jdbc_user => "hehe"
    jdbc_password => "hehe"
    #schedule => "*/4 * * * *"
    statement => "SELECT visitor_id, user_id, ip, url, param, is_platform, referrer, windows_version, brower_version, screen_size, DATE_FORMAT(in_time, '%Y-%m-%d %T') in_time, DATE_FORMAT(out_time, '%Y-%m-%d %T')out_time, stay_time, click_list, move_list, position, url_id, is_ajax, resource_count, resource_unit, download_count, download_unit , timestamp FROM visitor_info WHERE timestamp >:sql_last_value"
    use_column_value => true
    tracking_column => timestamp
    clean_run => true
  }
}

filter{
  if [type] == "logstash-logs" {
# 尝试使用filter实现datetime的格式转换
#    date {
#      match => [ "timestamp", "yyyy-MM-dd HH:mm:ss" , "yyyy-MM-dd HH:mm:ss.SSS" ]
#      locale => "cn"
#      #target => "@timestamp"
#    }

    geoip {
      source => "ip" # 将ip字段解析为geoip
    }

    mutate{
      convert => ["stay_time", "float"] # 字段类型转换为float
    }
  }
}

output {
  if [type] == "qa" {
    elasticsearch {
        #protocol => http
        index => "qa"
        document_type => "tcm"
        #document_id => "%{uid}"
        hosts => ["http://10.15.82.65:9200"]
    }
  }

  if [type] == "logstash-logs" {
    elasticsearch {
        #protocol => http
        index => "logstash-logs"
        document_type => "tcm"
        #document_id => "%{uid}"
        hosts => ["http://10.15.82.65:9200"]
    }
  }
}
```

### Logstash的启动重启脚本
* 启动Logstash的脚本。

```
# startup.sh
# 启动Logstash脚本，指定对应的配置文件，后台运行
./bin/logstash -f ./config/my_jdbc.conf &
```

* 关闭Logstash的脚本，使用进程查找的方式kill掉。
```
# shutdown.sh
ID=`ps -aux |grep logstash |grep elastic |grep jdk| grep 9600| awk '{print $2}'`
echo $ID
echo "To kill all the processes:"
echo "##############################"
for id in $ID
do
  kill -9 $id
  echo "killed $id"
done
echo "#############################"
```

* 重启Logstash的脚本：运行shutdown脚本，再运行startup脚本。

## Kibana的配置与使用
* 启动kibana： 运行`./bin/kibana`，访问5601端口。用户名密码: `Username: elastic Password: changeme`
* 重启kibana：如果ps -ef|grep kibana，ps -ef|grep 5601 的方法找不到其进程，尝试使用`fuser -n tcp 5601`或者`netstat -anp|grep 5601``，再kill -9 关闭。然后启动即可。

### Kibana的配置
* 配置kibana：修改config的yml文件，设置用户名和密码对应es的设置(x-pack)。设置host对应ES的ip，设置请求的url是es的ip:port。
* Kibana连接ES集群内的结点时，最好将该ES结点按照官网设置成`node.master: false` `node.data: false` `node.ingest: false`搜索器模式(写在es.yml中即可)以专门用于搜索，不作为master或者data结点，然后在Kibana配置中连接该ES结点。

```
# kibana配置，默认使用5601端口，listen的ip，配置name，连接的ES结点最好是搜索器模式。
#server.port: 5601
server.host: "10.15.82.65"
server.name: "10.15.82.65-esc-kibana"
elasticsearch.url: "http://10.15.82.65:9199"
```

### Kibana的使用
* 参考文章
    * [Kibana基本使用](http://blog.csdn.net/ming_311/article/details/50619859)
* Kibana的使用还是比较简单的，由于是webUI的操作，基本就是选择ES数据，对应到图的设置即可。
* 大体流程：就是选择目标index，创建visualize图，保存图，组成dashboard。
* 问题解决：生成Tile map提示`No Compatible Fields... geo_point`  参考： [Visualize Geo location of log using Elasticsearch + Logstash + Kibana](https://autofei.wordpress.com/2017/01/11/visualize-geo-location-of-log-using-elasticsearch-logstash-kibana/)，主要就是index默认匹配的是logstash-开头的。


---
## 问题与解决
* ES启动报错`vm.max_map_count`过小
    * 错误提示`max virtual memory areas vm.max_map_count [65530] likely too low, increase to at least [262144]`
    * 临时解决：`sudo sysctl -w vm.max_map_count=262144`，更新设置sysctl -p，查看设置sysctl -a | grep max_map
    * 永久解决：上面的修改，重启后值会无效，如果想永久改变需要如下操作： `vm.max_map_count=262144`直接写到/etc/sysctl.conf中,然后执行sysctl -p 
* ES启动报错`max file descriptors`过小
    * 错误提示`max file descriptors [4096] for elasticsearch process likely too low, increase to at least [65536]`
    * 解决：sudo vim /etc/security/limits.conf 添加用户zju的参数为`zju - nofile 65536` ，注销用户或重启，查看ulimit -n。
* Logstash导入时出现时间格式匹配failure
    * 如果MySQL中是datatime类型，可能无法转换到timestamp类型，最简单的解决方法是设置tracking_column的一列最好就是timestamp类型，这样也会自动作为ES的doc的@timestamp信息。
* Logstash定时导入出现重复导入
    * 解决方法：正确设置`clean_run、use_column_value、tracking_column`，并且select语句中使用`where id >:sql_last_value`，当然tracking_column的一列最好使用timestamp，可以探测到修改的记录。


---
## ES 5.0 Java API代码示例

### index增查改删

```
package com.sr.util.elasticsearch;

import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.transport.InetSocketTransportAddress;
import org.elasticsearch.common.xcontent.XContentBuilder;
import org.elasticsearch.rest.RestStatus;
import org.elasticsearch.script.Script;
import org.elasticsearch.script.ScriptService;
import org.elasticsearch.transport.client.PreBuiltTransportClient;

import static org.elasticsearch.common.xcontent.XContentFactory.*;

import java.io.IOException;
import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

/**索引API：用于查找、新建、删除一个索引
 * Created by dell on 2017/5/17.
 */
public class IndexCRUD {
    public static void main(String[] args) throws Exception {
        TransportClient client = getClient();
        String index = "test";
        String type = "book";

        System.out.println("测试索引的crud\r\n");
        System.out.println("create =======================");
        createIndex(client, index, type);

        System.out.println("select => get =======================");
        // 查询不到id会异常，那么是不是只能先query，再根据id查索引
        //getIndex(client, index, type, "1");
        getIndex(client, index, null, "2");

        System.out.println(" update index =======================");
        updateIndex(client, index, type);

        System.out.println(" delete =======================");
        //在2.0以前版本，删除操作有两种方式，一种是通过id来进行删除，但是这种方式一般不常用，因为id不容易得到
        //另一种是DeleteByQuery()，但是会强制自动刷新引起内存溢出。
        // 5版本之后移除DeleteByQuery，如果是少量删除就获取id再删除，如果是大量，应考虑建立多个索引，整个删除再重建索引
        deleteIndex(client, index, type);

        // 关闭连接
        client.close();
    }

    /**
     * 可以使用UpdateRequest、prepareUpdate进行索引更新，支持通过script、doc传入数据
     *
     * @param client
     * @param index
     * @param type
     * @throws IOException
     * @throws InterruptedException
     * @throws java.util.concurrent.ExecutionException
     */
    private static void updateIndex(TransportClient client, String index, String type) throws IOException, InterruptedException, java.util.concurrent.ExecutionException {
        // 可以使用UpdateRequest，也可以使用prepareUpdate
        UpdateRequest updateRequest = new UpdateRequest();
        updateRequest.index(index);
        updateRequest.type(type);
        updateRequest.id("2");
        XContentBuilder xContentBuilder = jsonBuilder()
                .startObject()
                .field("gender", "male")//之前没有的字段可以添加进去
                .field("title", "book updateRequest") //之前有的字段会被更新
                .endObject();
        updateRequest.doc(xContentBuilder);
        //updateRequest.script(new Script("ctx._source.gender = \"male\""));// 脚本和doc不能同时使用
        client.update(updateRequest).get();

        // 可以用脚本或者文档，但二者不可同时set
        // 脚本可以使用ScriptService.ScriptType.FILE指定外部的脚本文件
        UpdateResponse updateResponse = client.prepareUpdate(index, type, "AVwVXiFYfbUBmxyYF65g")
                .setScript(new Script("ctx._source.gender = \"male\"", ScriptService.ScriptType.INLINE, null, null))
                .get();
        // 文档适合于简洁的情况，指定内容将和原始doc进行整合
        client.prepareUpdate(index, type, "AVwVUieAkr-jPeVnIcUG")
                .setDoc(jsonBuilder()
                        .startObject()
                        .field("title", "book-prepareUpdate") // 之前有的字段会被更新
                        .field("gender", "male")//之前没有也添加进去
                        .endObject())
                .get();
        System.out.println(updateResponse.status());
    }

    /**
     * 获取es连接
     *
     * @return
     * @throws UnknownHostException
     */
    private static TransportClient getClient() throws UnknownHostException {
        // 设置链接es的集群名，是否发现所有集群ip
        Settings settings = Settings.builder().put("cluster.name", "esc5.0").put("client.transport.sniff", true).build();
        TransportClient client = new PreBuiltTransportClient(settings);
        // 添加链接
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.15.82.65"), 9300));
        return client;
    }

    /**
     * 创建索引，可不指定id，数据可通过map、json存入
     *
     * @param client
     * @param index
     * @param type
     */
    private static void createIndex(TransportClient client, String index, String type) {
        // 数据封装在json中，生成的方式可以用map、自己拼写json、Jackson或es的jsonBuilder()序列化生成json
        Map<String, Object> map = new HashMap();
        map.put("name", "book1");
        map.put("title", "title1");
        map.put("author", "Geo");
        map.put("year", 2011);
        map.put("date", new Date());
        // 添加索引可以不指定id
        client.prepareIndex(index, type)
                .setSource(map)
                .get();
        // setSource全部覆盖,setId产生新的一份
        map.put("name", "book2");
        map.put("title", "title2");
        map.put("author", "2");
        map.put("year", 2012);
        map.put("date", new Date());
        IndexResponse response = client.prepareIndex(index, type, "2")
                .setSource(map)
                .get();
        // 查看处理结果
        // 所在索引名称
        String _index = response.getIndex();
        // 类型
        String _type = response.getType();
        // 是否有docId
        String _id = response.getId();
        // Version (第一次index这个doc会返回1)
        long _version = response.getVersion();
        // status has stored current instance statement.
        RestStatus status = response.status();
        System.out.println("create 结果状态：" + _index + "\t" + _type + "\t" + _id + "\t" + _version + "\t" + status);
    }

    /**
     * 删除索引中的doc
     *
     * @param client
     * @param index
     * @param type
     */
    private static void deleteIndex(TransportClient client, String index, String type) {
        DeleteResponse response = client.prepareDelete(index, type, "AVwVYdbRkr-jPeVnIcUH")
                .get();
        System.out.println(response.status());
    }

    /**
     * 查看索引，type为空选择所有
     *
     * @param client
     * @param index
     * @param type
     * @param id
     */
    private static void getIndex(TransportClient client, String index, String type, String id) {
        // 可以先使用excute是执行，再用actionGet是等待返回结果
       /*
        GetResponse getIndex = client.prepareGet(index, type, id).execute().actionGet();
        System.out.println("索引是否存在：" + getIndex.isExists());
        System.out.println("GetResponse: " + getIndex);
*/
        // 也可以直接使用get，默认使用异步线程，设置为false则是同步
        GetResponse response = client.prepareGet(index, type, id)
                .setOperationThreaded(false)
                .get();
        System.out.println("GetResponse: " + response);
    }

}
```

### doc查询

```
package com.sr.util.elasticsearch;

import com.vividsolutions.jts.geom.Coordinate;
import org.elasticsearch.action.search.SearchRequestBuilder;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.geo.ShapeRelation;
import org.elasticsearch.common.geo.builders.ShapeBuilders;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.text.Text;
import org.elasticsearch.common.transport.InetSocketTransportAddress;
import org.elasticsearch.common.unit.TimeValue;
import org.elasticsearch.index.query.Operator;
import org.elasticsearch.index.query.QueryBuilder;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.index.search.MatchQuery;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.aggregations.AggregationBuilder;
import org.elasticsearch.search.aggregations.AggregationBuilders;
import org.elasticsearch.search.fetch.subphase.highlight.HighlightBuilder;
import org.elasticsearch.search.sort.SortOrder;
import org.elasticsearch.search.suggest.SuggestBuilder;
import org.elasticsearch.search.suggest.SuggestBuilders;
import org.elasticsearch.search.suggest.term.TermSuggestionBuilder;
import org.elasticsearch.transport.client.PreBuiltTransportClient;

import java.io.IOException;
import java.net.InetAddress;
import java.net.UnknownHostException;
import java.util.ArrayList;
import java.util.List;

/**
 * 查询API：用于查询、排序、分页、过滤、高亮、滚动等
 * Created by dell on 2017/5/17.
 */
public class QueryTest {

    public static void main(String[] args) throws Exception {
        TransportClient client = getClient();
        String index = "test";

        // QueryBuilders 有非常多的查询方法
        // 但是默认查询时英文单词是按空格切分，不能使用单词的一部分进行查询，除非使用通配符构造目标词
        System.out.println("==== queryTermAndPrefix ======");
        //queryTermAndPrefix(client, index);
        System.out.println("==== matchAll ======");
        //matchAll(client, index);
        System.out.println("==== matchQuery ======");
        //matchQuery(client, index);
        System.out.println("==== queryString ======");
        //queryString(client, index);
        // 中文查询过程中，就不会遇到上面“一个英文单词进行局部查找”的问题
        // 如下中文索引查询所示，查询默认是queryStringQuery默认是模糊匹配，且可自动分配
        System.out.println("==== queryByString1 ======");
        //queryByString(client, "qa", "question:心肌炎 AND answer: 病毒");
        System.out.println("==== queryByString2 ======");
        //queryByString(client, "qa", "question:心肌炎 OR answer: 病毒");
        System.out.println("==== queryByString3 ======");
        //queryByString(client, "kb_nlpcc2016", "subject:坦克大战 AND predicate: 类型");
        System.out.println("==== queryByString4 ======");
        //queryByString(client, "kb_nlpcc2016", "subject:坦克大战 OR predicate: 类型");
        System.out.println("==== queryByString5 ======");
        //queryByString(client, "kb_nlpcc2016", "subject:坦克大战");

        System.out.println("==== geoShapeQuery ======");
        //geoShapeQuery(client, "logstash-logs");


        System.out.println("==== pageAndSort ======");
        //pageAndSort(client, index);
        System.out.println("==== scanAndScroll ======");
        //scanAndScroll(client, "qa");

        // ES查询主要有两个部分query and filter，两者的主要区别在于:filter是不计算相关性的,同时可以cache。因此,filter速度要快于query
        System.out.println("==== filter ======");
        //filterQuery(client, "test");
        System.out.println("==== facets ======");
        //facets(client, "test");
        System.out.println("==== highlight ======");
        //highlight(client, "test");
        System.out.println("==== suggester ======");
        //suggester(client, "test");

        client.close();
    }

    /**
     * 构造简单查询
     *
     * @param client
     * @param index
     */
    private static void queryTermAndPrefix(TransportClient client, String index) {
        QueryBuilder queryBuilder = QueryBuilders
                .disMaxQuery() //对子查询的结果做union，score沿用子查询score的最大值。这种查询广泛应用于muti-field的查询
                .add(QueryBuilders.termQuery("title", "title")) // termQuery 查询时不分词，必须含有对应的term，不能查到title1
                .add(QueryBuilders.termQuery("title", "title1"))// 可以查到title1的文档，但是查不到title2
                //.add(QueryBuilders.termQuery("title", "book")) // 可查到book updateRequest
                //.add(QueryBuilders.termQuery("title", "updaterequest")) // 可查到book updateRequest，但updateRequest查不到，因为es存在词处理
                .add(QueryBuilders.prefixQuery("title", "boo")) // 可查到book updateRequest
                ;
        System.out.println(queryBuilder.toString());
        SearchResponse response = client.prepareSearch(index)
                .setQuery(queryBuilder).get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    /**
     * 查询索引中的所有文档
     *
     * @param client
     * @param index
     */
    private static void matchAll(TransportClient client, String index) {
        QueryBuilder queryBuilder = QueryBuilders.matchAllQuery().boost(11f); // boost设置查询结果的score得分
        SearchResponse response = client.prepareSearch(index)
                .setQuery(queryBuilder).get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    private static void matchQuery(TransportClient client, String index) {
        QueryBuilder queryBuilder = QueryBuilders.matchQuery("title", "updateRequest") // updateRequest查得到updateRequest，但是update、Request查不到
                .operator(Operator.OR) // 默认查询的通配符是or
                .zeroTermsQuery(MatchQuery.ZeroTermsQuery.ALL) // 按照声明的顺序返回包含此枚举类型的常数的数组。此方法可用于迭代常量（还不知道怎么用）
                ;
        SearchResponse response = client.prepareSearch(index)
                .setQuery(queryBuilder).get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    private static void queryString(TransportClient client, String index) {
        // queryStringQuery可以使用复杂的查询语句
        QueryBuilder queryBuilder = QueryBuilders.queryStringQuery("title:update* OR name:book1")// updateRequest查得到updateRequest，但是update、Request查不到，通配符可以
                ;
        SearchResponse response = client.prepareSearch(index)
                .setQuery(queryBuilder).get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    private static void queryByString(TransportClient client, String index, String queryString) {
        // queryStringQuery可以使用复杂的查询语句
        QueryBuilder queryBuilder = QueryBuilders.queryStringQuery(queryString);
        SearchResponse response = client.prepareSearch(index)
                .setQuery(queryBuilder).get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    private static void geoShapeQuery(TransportClient client, String index) {
        // 使用地理信息查询，参考api：https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-geo-queries.html
        List<Coordinate> points = new ArrayList<>();
        points.add(new Coordinate(0, 0));
        points.add(new Coordinate(0, 10));
        points.add(new Coordinate(10, 10));
        points.add(new Coordinate(10, 0));
        points.add(new Coordinate(0, 0));
        QueryBuilder queryBuilder = null;
        try {
            queryBuilder = QueryBuilders.geoShapeQuery("geoip.location",
                    ShapeBuilders.newMultiPoint(points))
                    .relation(ShapeRelation.WITHIN)
            ;
        } catch (IOException e) {
            e.printStackTrace();
        }
        SearchResponse response = client.prepareSearch(index)
                .setQuery(queryBuilder).get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    /**
     * 使用from和size进行分页。
     * <p>
     * 基本步骤：查找出total count， 设置from、size参数获取分页，ES支持一次查找同时返回total count和目标数据
     * </p>
     * <p>
     * 缺点：深度分页(比如size=10&from=50000，越往后的分页，执行的效率越低，且需要申请的排序内存就很大)时很慢：会对所有total进行排序，并选取目标页。每次执行都会重复这样操作，速度慢还会造成内存溢出
     * </p>
     *
     * @param client
     * @param index
     */
    private static void pageAndSort(TransportClient client, String index) {
        // 对文本进行sort/aggregate(聚合)/or access values，默认是没意义的，若用需要进行设置fielddata=true。
        // 参考api：https://www.elastic.co/guide/en/elasticsearch/reference/current/fielddata.html
        SearchRequestBuilder builder = client.prepareSearch(index);
        SearchResponse response = builder.setQuery(QueryBuilders.matchAllQuery())
                .setFrom(4)
                .setSize(2)
                //.addSort(SortBuilders.fieldSort("_score")) // sort支持字段排序、scriptSort、geoDistanceSort。
                .addSort("_score", SortOrder.ASC)
                .get();
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    /**
     * 使用scan和scroll提高分页效率
     * <p>
     * ES深度分页效率不高，尽量使用scan、scroll进行标记，再返回结果。但是如果是深度排序分页，那还是要用sort的from/size
     * </p>
     * <p>
     * scan、scroll类似于mysql的cursor游标，根据查找、标记、排序策略、返回结果。
     * scroll会进行标记并排序，标记缓存3或5分钟，如果只查找而不需要排序，还是推荐scan。
     * scroll相比sort的from/size，优点在于可匹配后缓存排序的标记，避免每次都匹配、排序。
     * scan只会标记但不排序，标记也会缓存几分钟，适合于分页查找但不需排序的场合。
     * scan在es2以后被deprecated
     * </p>
     * <p>
     * 注意sort的from/size是综合所有shard分片的，但是scan/scroll是基于各个shard分片的
     * </p>
     *
     * @param client
     * @param index
     */
    private static void scanAndScroll(TransportClient client, String index) {

        SearchRequestBuilder builder = client.prepareSearch(index);
        SearchResponse response = builder
                .setQuery(QueryBuilders.queryStringQuery("question:脑炎后帕金森病"))
                //.setSearchType(SearchType.SCAN)  // 默认scroll为scroll，进行排序缓存，不排序用scan且需指明scan，但是es2以后已经deprecated了
                .setScroll(TimeValue.timeValueMinutes(3))
                .setSize(2)  // size还是返回最终的分页size，而不是每个分片的size？！
                .get();
        printHits(response, response.getHits());
        System.out.println("ScrollId: " + response.getScrollId());

        for (int sum = 0; sum < response.getHits().getTotalHits() && sum < 10; ) {
            response = client
                    .prepareSearchScroll(response.getScrollId()) // 继续使用之前的scroll，将不断向下获取数据
                    .setScroll(TimeValue.timeValueMinutes(8))// 必须设置新的scroll缓存时间，才可以使用之前的scroll
                    .get();
            System.out.println("ScrollId: " + response.getScrollId()); // 会发现是同一个scrollId
            sum += response.getHits().hits().length;
            System.out.println("总量: " + response.getHits().getTotalHits() + ", 已经查到: " + sum);
        }
        SearchHits hits = response.getHits();
        printHits(response, hits);
    }

    private static void filterQuery(TransportClient client, String index) {
        QueryBuilder queryBuilder = QueryBuilders.boolQuery()
                .must(QueryBuilders.prefixQuery("name", "book")) // 不使用filter时，total hits: 6
                .filter(QueryBuilders.termQuery("title", "title1")) // filter之后命中：total hits: 5
                ;
        SearchResponse response = client.prepareSearch(index).setQuery(queryBuilder).get();
        printHits(response, response.getHits());
    }

    /**
     * facets es2之后被超类 aggregations 代替
     * 切面计算，支持多种切面：filter facets、term facets、query facts等
     * @param client
     * @param index
     */
    private static void facets(TransportClient client, String index) {
        System.out.println("还不知道下面的用法对不对，切面计算会将filter之前的结果进行计算");
        QueryBuilder queryBuilder = QueryBuilders.boolQuery()
                .must(QueryBuilders.prefixQuery("name", "book")) // 不使用filter时，total hits: 6
                .filter(QueryBuilders.termQuery("title", "title1")) // filter之后命中：total hits: 5
                ;
        AggregationBuilder aggregationBuilder = AggregationBuilders.filter("title",queryBuilder);
        SearchResponse response = client.prepareSearch(index).addAggregation(aggregationBuilder).get();
        printHits(response, response.getHits());
    }

    /**
     * 搜索文本高亮，可以用fragments结果作为返回的高亮文本
     * @param client
     * @param index
     */
    private static void highlight(TransportClient client, String index) {
        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.preTags("<em>");
        highlightBuilder.postTags("</em>");
        highlightBuilder.field("title");

        SearchResponse response = client.prepareSearch(index)
                .setQuery(QueryBuilders.termQuery("title", "book"))
                .highlighter(highlightBuilder)
                .get();
        printHits(response,response.getHits());

        for(SearchHit hit:response.getHits()){
            System.out.println("String方式打印文档搜索内容:");
            System.out.println(hit.getSourceAsString());
            System.out.println("Map方式打印高亮内容");
            System.out.println(hit.getHighlightFields());

            System.out.println("遍历高亮集合，打印高亮片段:");
            Text[] text = hit.getHighlightFields().get("title").getFragments();
            for (Text str : text) {
                System.out.println(str.string());
            }
        }
    }

    /**
     * 查询建议，如搜索引擎建议“您是不是要找”,suggest 的api也在es5中进行了改动。需要将目标字段设置为type: completion
     * @param client
     * @param index
     */
    private static void suggester(TransportClient client, String index) {
        SearchResponse response = client.prepareSearch(index)
                .setQuery(QueryBuilders.matchAllQuery())
                .suggest(new SuggestBuilder()
                        .addSuggestion("fooSuggest", SuggestBuilders.completionSuggestion("title").text("b"))) // 添加搜索建议，并设置suggester别名
                .get();
        printHits(response,response.getHits());
    }


    /**
     * 输出查询结果
     *
     * @param response
     * @param hits
     */
    private static void printHits(SearchResponse response, SearchHits hits) {
        System.out.println("total hits: " + response.getHits().getTotalHits());
        for (SearchHit hit : hits
                ) {
            //System.out.println(hit.getSource().get("predicate")); // 获取数据对应的字段值
            System.out.println(hit.getId() + "\t" + hit.getIndex() + "\t" + hit.getType() + "\t" + hit.getFields() + "\t" + hit.getSourceAsString() + "\t" + hit.getHighlightFields() + "\t" + hit.getScore());
        }
    }

    /**
     * 获取es连接
     *
     * @return
     * @throws UnknownHostException
     */
    public static TransportClient getClient() throws UnknownHostException {
        // 设置链接es的集群名，是否发现所有集群ip
        Settings settings = Settings.builder().put("cluster.name", "esc5.0").put("client.transport.sniff", true).build();
        TransportClient client = new PreBuiltTransportClient(settings);
        // 添加链接
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("10.15.82.65"), 9300));
        return client;
    }
}

```

### bulk批量操作

```
package com.sr.util.elasticsearch;

import org.apache.commons.collections.map.HashedMap;
import org.elasticsearch.action.bulk.BulkRequestBuilder;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.get.MultiGetItemResponse;
import org.elasticsearch.action.get.MultiGetRequestBuilder;
import org.elasticsearch.action.get.MultiGetResponse;
import org.elasticsearch.action.search.MultiSearchRequestBuilder;
import org.elasticsearch.action.search.MultiSearchResponse;
import org.elasticsearch.client.transport.TransportClient;

import java.util.Map;

/**
 * 批量操作：将多个索引、删除、更新等操作打包成一个请求
 * Created by dell on 2017/5/17.
 */
public class BulkTest {

    public static void main(String[] args) throws Exception {
        TransportClient client = QueryTest.getClient();
        String index = "test";
        String type = "book";

        System.out.println("=== bulk index ==========");
        bulkRequest(client, index, type);

        System.out.println("=== multiGet ===========");
        multiGet(client, index, type);

        System.out.println("=== multiSearch ===========");
        // multiSearch
        MultiSearchRequestBuilder builder = client.prepareMultiSearch();
        builder.add(client.prepareSearch(index, type).request());
        builder.add(client.prepareSearch(index, type).request()); // filter
        MultiSearchResponse response= builder.get();

        MultiSearchResponse.Item[] items = response.getResponses();
        for (MultiSearchResponse.Item item: items
             ) {
            System.out.println(item.getResponse().getHits());
        }


    }

    /**
     * 一次性get多个id对应的文档，功能比较单一
     * @param client
     * @param index
     * @param type
     */
    private static void multiGet(TransportClient client, String index, String type) {
        MultiGetRequestBuilder builder = client.prepareMultiGet();
        MultiGetResponse responses = builder.add(index, type, "1", "2","3").get();
        System.out.println(responses.toString());

        MultiGetItemResponse[] respa = responses.getResponses();
        for (MultiGetItemResponse res:respa
             ) {
            System.out.println(res.getId() + res.getFailure() + res.getResponse().isExists());
        }
    }

    /**
     * bulkRequestBuilder可以添加多种、多个操作，一次性提交各种操作，可用于批量导入。批量导出可以选择所有然后写入文件。
     * @param client
     * @param index
     * @param type
     */
    private static void bulkRequest(TransportClient client, String index, String type) {
        Map<String, Object> map = new HashedMap();
        BulkRequestBuilder bulkRequestBuilder = client.prepareBulk();

        map.put("title","bulk-data1");
        bulkRequestBuilder.add(client.prepareIndex(index,type).setSource(map).request());
        map.put("title","bulk-data2");
        bulkRequestBuilder.add(client.prepareIndex(index,type).setSource(map).request());

        BulkResponse responses = bulkRequestBuilder.get(); // 可以添加很多个request然后一次性提交以批量操作
        System.out.println(responses.toString());
    }
}
```

### 管理API

```
package com.sr.util.elasticsearch;

import org.elasticsearch.action.admin.cluster.health.ClusterHealthResponse;
import org.elasticsearch.action.admin.cluster.state.ClusterStateResponse;
import org.elasticsearch.action.admin.cluster.stats.ClusterStatsResponse;
import org.elasticsearch.action.admin.indices.mapping.put.PutMappingResponse;
import org.elasticsearch.client.ClusterAdminClient;
import org.elasticsearch.client.Response;
import org.elasticsearch.client.transport.TransportClient;

import java.net.UnknownHostException;

/**管理Api，用于集群监控和管理
 * Created by dell on 2017/5/17.
 */
public class AdminTest {
    public static void main(String[] args) throws Exception {
        TransportClient client = QueryTest.getClient();

        // 集群健康状态
        System.out.println("==== cluster status ======");
        ClusterAdminClient clusterAdminClient = client.admin().cluster();
        ClusterHealthResponse response = clusterAdminClient.prepareHealth().get();
        System.out.println(response.getStatus());

        // 集群结点
        System.out.println("==== cluster nodes ======");
        ClusterStatsResponse response1 = client.admin().cluster().prepareClusterStats().get();
        System.out.println(response1.getNodes().toString());

        // 设置mapping映射，可以新建或修改多个mapping映射
        // api: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-admin-indices.html
        client.admin().indices().prepareCreate("twitter")
                .addMapping("tweet", "{\n" +
                        "    \"tweet\": {\n" +
                        "      \"properties\": {\n" +
                        "        \"message\": {\n" +
                        "          \"type\": \"string\"\n" +
                        "        }\n" +
                        "      }\n" +
                        "    }\n" +
                        "  }")
                .get();

        PutMappingResponse response2 =
                client.admin().indices().preparePutMapping("twitter")
                        .setType("user")
                        .setSource("{\n" +
                                "  \"properties\": {\n" +
                                "    \"name\": {\n" +
                                "      \"type\": \"string\"\n" +
                                "    }\n" +
                                "  }\n" +
                                "}")
                        .get();
        System.out.println(response2.toString());
        // 还有更多...
    }
}
```

