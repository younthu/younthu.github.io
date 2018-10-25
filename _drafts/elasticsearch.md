---
layout: post
title: elasticsearch
excerpt: elasticsearch是非常优秀的全文索引引擎，基于微服务架构，使用简单，稳定可靠。本文记录一些零碎的填坑方案。
---
[elasticsearch-rails](https://github.com/elastic/elasticsearch-rails), elasticsearch rails gem.

# AccessDenied

升级elasticsearch(1.5 => 6.0)之后会抛accessDenied的问题：

~~~
Elasticsearch::Transport::Transport::Errors::InternalServerError: [500] {"error":{"root_cause":[{"type":"access_control_exception","reason":"access denied (\"java.io.FilePermission\" \"/Users/andrewy/sourcecode/github/dg/config/elasticsearch/analysis/synonym/synonyms_en.txt\" \"read\")"}],"type":"access_control_exception","reason":"access denied (\"java.io.FilePermission\" \"/Users/andrewy/sourcecode/github/dg/config/elasticsearch/analysis/synonym/synonyms_en.txt\" \"read\")"},"status":500}
~~~

## 原因
ElasticeSearch 2.0之前应该没有这个问题，2.0之后加入了java security manager的支持，然后就会抛这个问题.[StackOverflow](https://stackoverflow.com/questions/35401917/reading-a-file-in-an-elasticsearch-plugin?answertab=votes#tab-top)有讨论这个问题, 并且详细给出了几种解决方案。


## 解决办法
解决方法有多种，具体看上面的链接。

最直接的办法是把`hunspell`目录拷贝到`/usr/local/etc/elasticsearch/hunspell`目录下, hunspell 目录下包含`analysis` 和 `en_GB`.

这里贴出通过修改当前用户java配置的方法, 创建`~/.java.policy`文件或者修改`vim /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/java.policy`并填入下面内容然后重启elasticsearch `service elasticsearch restart`:

~~~
  grant {
      //permission java.io.FilePermission "/Users/userhome/config/elasticsearch/en_GB/", "read,write";
      permission java.security.AllPermission;

  };
~~~

或者你也可以把上面内容放入elasticsearch全局配置文件中,Mac下面是: `/usr/local/opt/elasticsearch/bin/elasticsearch`


brew 安装elasticsearch后会自动启动, 然后elasticsearch的实例是会自动组成集群的，这会导致你改了policy文件以后启动elasticsearch不能直接生效，你得把所有elasticsearch实例关闭先。

注意参数`bin/elasticsearch -Dsecurity.manager.enabled=false`在[elasticsearch 2.3](https://www.elastic.co/guide/en/elasticsearch/reference/2.2/modules-scripting-security.html#_disable_the_java_security_manager)之后就不支持了。

# Invalid synonym
不清楚为何升级版本以后同义词字典就不能用了。抛了下面的错误:
~~~
Elasticsearch::Transport::Transport::Errors::BadRequest: [400] {"error":{"root_cause":[{"type":"illegal_argument_exception","reason":"failed to build synonyms"}],"type":"illegal_argument_exception","reason":"failed to build synonyms","caused_by":{"type":"parse_exception","reason":"Invalid synonym rule at line 2","caused_by":{"type":"illegal_argument_exception","reason":"term: People's Republic of China analyzed to a token with posinc != 1"}}},"status":400}
~~~

解析出错的那行字典内容是:
~~~
China, People's Republic of China, mainland China, Communist China, Red China, PRC, Cathay
~~~

没有去找解决办法，先绕了过去:删除无法解析的行.


# Notes
http://localhost:9200/index
/usr/local/var/log/elasticsearch.log #日志地址

curl -XGET 'http://localhost:9200/index/_analyze?analyzer=ik&pretty' -d '我是中国人' # 测试分词功能, 不工作 ,抛下面的错误

不支持x-www-form-urlencoded
~~~
{
  "error" : "Content-Type header [application/x-www-form-urlencoded] is not supported",
  "status" : 406
}
~~~

# 磁盘不足错误
无法做索引,抛错信息如下. 原因，磁盘不足，创建索引失败。解决办法，清理磁盘，然后重新做索引.
~~~
Elasticsearch::Transport::Transport::Errors::Forbidden ([403] {"error":{"root_cause":[{"type":"cluster_block_exception","reason":"blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];"}],"type":"cluster_block_exception","reason":"blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];"},"status":403}):
[e7a32230-0a05-4cf8-afa3-90886eb6d92b]
[e7a32230-0a05-4cf8-afa3-90886eb6d92b] app/jobs/single_locale_indexer_job.rb:13:in `perform'
[e7a32230-0a05-4cf8-afa3-90886eb6d92b] app/models/concerns/es/single_locale_callbacks.rb:8:in `block (3 levels) in <module:SingleLocaleCallbacks>'
[e7a32230-0a05-4cf8-afa3-90886eb6d92b] app/controllers/application_controller.rb:164:in `user_for_paper_trail'
~~~

# parse mapping失败
parse mapping失败.抛错如下.

~~~
Elasticsearch::Transport::Transport::Errors::BadRequest: [400] {"error":{"root_cause":[{"type":"mapper_parsing_exception","reason":"Failed to parse mapping [person]: Could not convert [id_string.index] to boolean"}],"type":"mapper_parsing_exception","reason":"Failed to parse mapping [person]: Could not convert [id_string.index] to boolean","caused_by":{"type":"illegal_argument_exception","reason":"Could not convert [id_string.index] to boolean","caused_by":{"type":"illegal_argument_exception","reason":"Failed to parse value [not_analyzed] as only [true] or [false] are allowed."}}},"status":400}
~~~


原因，es5.0之后index参数改了，只接受`true`或者`false`,
[ElasticSearch文档](https://www.elastic.co/guide/en/elasticsearch/reference/5.0/mapping-index.html)有解释, [Elastic github](https://github.com/elastic/elasticsearch/issues/21134#issuecomment-256563618)有讨论.

解决方法:

把`:analyzed`和`:not_analyzed`改成`true`, `:no`改成`false`


# 参考
[Elasticsearch简介](http://lxwei.github.io/posts/Elasticsearch-%E7%AE%80%E4%BB%8B.html)
[安装中文分词器](http://www.tianshouzhi.com/api/tutorials/elasticsearch/143)
