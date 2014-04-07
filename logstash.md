#Logstash
Ship logs from any source, parse them, get the right timestamp, index them, and search them.

 - 管理events和logs。用于收集，分析和存储log，为searching做准备。
 - 完全的开源免费
 - 和[ElasticSearch](http://www.elasticsearch.org/)关系暧昧, The inputs are your log files. The output will be elasticsearch.


#a single, standalone server

- download
    > wget -N https://download.elasticsearch.org/logstash/logstash/logstash-1.3.3-flatjar.jar -O logstash.jar
- run
    > java -jar logstash.jar agent -f logstash.conf -- web
    > java -jar logstash.jar agent -f test.conf -- web
- web view
    > 127.0.0.1:9300


# Storing logs with **Elasticsearch**
不需要专门的下载一个elasticsearch，可以使用嵌入模式。


```

 output {
  stdout {codec => rubydebug }
  elasticsearch {
    embedded => true
  }
}


```




# LogStash Config Language

```ruby
    # This is a comment. You should use comments to describe
    # parts of your configuration.
    input {
      ...
    }

    filter {
      ...
    }

    output {
      ...
    }
```