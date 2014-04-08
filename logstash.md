
- Log level
    * [Group by log level](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Group%20by%20log%20level):group by log level
    * [Error logs](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Error%20logs):filter out all errors
- Access logs
    * [Group by response time](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Group%20by%20response%20time): group by response time
    * [Slow logs](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Slow%20logs):Slow logs(default 5000ms, you can change it)
    * [Group by search API call](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Group%20by%20search%20API%20call):Group by search API call
    * [Filter by log id](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Filter%20by%20log%20id):filter relevant logs by log id(You should change default log id)
- Logs of Modules
    * [Insert](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Insert%20logs):logs of insert service
    * [Index](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Index%20logs):logs of index service
    * [Search](http://172.31.50.193:19292/index.html#/dashboard/elasticsearch/Search%20logs):logs of search service






#Logstash
Ship logs from any source, parse them, get the right timestamp, index them, and search them.

 - 管理events和logs。用于收集，分析和存储log，为searching做准备。
 - 完全的开源免费
 - 和[ElasticSearch](http://www.elasticsearch.org/)关系暧昧, The inputs are your log files. The output will be elasticsearch.


#a single, standalone server

- download
    > wget -N https://download.elasticsearch.org/logstash/logstash/logstash-1.3.3-flatjar.jar -O logstash.jar
- run
    > sudo java -jar logstash.jar agent -f logstash.conf -- web
    > sudo java -jar logstash.jar agent -f test.conf -- web
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


# rsyslog config

- install rsyslog
- config file
    * /etc/rsyslog.conf
- setup
    * client: forward the log to logstash server
        ```java
            if $syslogfacility-text == 'USER' then {
                /var/log/user.log
                *.* @@10.0.0.30:5544
            }
        ```
    * logstash