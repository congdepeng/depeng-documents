#Logstash
Ship logs from any source, parse them, get the right timestamp, index them, and search them.

 - 管理events和logs。用于收集，分析和存储log，为searching做准备。
 - 完全的开源免费
 - 和[ElasticSearch](http://www.elasticsearch.org/)关系暧昧, The inputs are your log files. The output will be elasticsearch.


#a single, standalone server



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