

# Vagrant config

    - weardex-vagrant/tree/master/modules/logstash
        * files
            * upstart (VM 开机启动程序设置)
                - elasticsearch.conf ： exec bin/elasticsearch &
                - logstash.conf ：exec bin/logstash agent -f logstash.conf web &
                - (找到原因，logstash 1.4 需要将agent和web分开运行！！！，如 # test )
        * manifests
            - init.pp

** log没打印出来？ upstart log？


# VM config files :
    - /etc/init/logstash.conf : exec bin/logstash agent -f logstash.conf web &
    - /etc/init/elasticsearch.conf : exec bin/elasticsearch &


# VM scripts
    - sudo start elasticsearch
    - sudo start logstash


# validate
    - ES : curl 'http://localhost:9200/_search?pretty'



# test
    - 1. stop logstash : sudo stop logstash
    - 2. manually run :
        - bin/logstash agent -f logstash.conf
        - bin/logstash web



# rsyslog
http://logstash.net/docs/1.4.0/tutorials/getting-started-with-logstash





input {
  stdin {}
  syslog {
    type => syslog
    port => 5544
  }
}


output {
  elasticsearch { host => localhost }
  stdout { codec => rubydebug }
}