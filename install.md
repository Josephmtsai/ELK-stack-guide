Please Use SSH and Use Root to Login and Install
```
sftp account:password@ip 22

```

**Please know That Logstash and ElasticSearch Should not install in the same server, they need CPU for I/O**

**Please know that this is for no network using**

## Install of Logstash

```
cd /tmp
rpm -ivh logstash-5.0.0.rpm
```

## Install of ElasticSearch

```
cd /tmp
rpm -ivh elasticsearch-5.0.0
```

## Install of Kibana

```
cd /tmp
rpm -ivh kibana-5.0.0-x86_64.rpm
```

> > rpm -qa logstash  代表 查詢目前的版本
> >
> > rpm -e logstash 代表 移除套件

![](2.png)



## Upgrade of ElasticSearch

```
cd /tmp
rpm -Uvh elasticsearch-5.0.0
```

>Please Make sure the Kibana is the Same version of ElasticSearch
>ElasticSearch 如果沒辦法啟動 請先檢查 /var/log/ElasticSearch 裡面的log
>接著檢查設定檔是否有設定需要調整

###五版以後Elastic & Kibana 必須一起升級











