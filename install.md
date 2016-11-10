Please Use SSH and Use Root to Login and Install


**Please know That Logstash and ElasticSearch Should not install in the same server, they need CPU for I/O**


## Install of Logstash
    cd /tmp
    rpm -ivh logstash-5.0.0.rpm
## Install of ElasticSearch
    cd /tmp
    rpm -ivh elasticsearch-5.0.0
## Install of Kibana
    cd /tmp
    rpm -ivh kibana-5.0.0-x86_64.rpm
    
>> rpm -qa logstash  代表 查詢目前的版本
>> rpm -e logstash 代表 移除套件    
    
![](2.png)    