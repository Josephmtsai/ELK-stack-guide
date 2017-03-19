#Logstash grok 效能

>此處為記錄logstash 效能的部分
>主要是參考此篇

https://www.elastic.co/blog/do-you-grok-grok

首先我們要知道

1. Grok 是根據regular expression 來的 所以寫法也會影響效能

2. _geoip_lookup_failure 這個代表示沒有match 到任何的正規表示式 (代表) 他會將你所有的MATCH 跑過一輪才會結束 ，如下面的例子 他會執行五次MATCH 才會判斷 這個是非常浪費效能的事情
 

```
grok {
break_on_match => true
match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\] \[(?<PERFORMANCE>[A-Z\-a-z]+)\]\[(?<describe>[A-Z\-a-z ]+)\]\[(?<Method>[A-Z\-a-z . /=?0-9\[\]:()',]+)\]\[%{NUMBER:performanceTime:int}ms\]%{GREEDYDATA:messages}"}
match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\]\[(?<webapi>[A-Z\-a-z]+)\]\[(?<apiPath>[A-Z\-a-z . /=?0-9\[\]:()]+)\]\[(?<httpMethod>[A-Z\-a-z /.\-]+)\]\[%{IP:clientIP}\]%{GREEDYDATA:messages}"}
match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\] \[(?<Feature>(FEATURE))\]\[(?<Method>[A-Z\-a-z . /=?0-9:()]+)\]\[(?<featureResult>[A-Z\-a-z . /=?0-9:()]+)\]%{GREEDYDATA:messages}"}
match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\] %{GREEDYDATA:messages}"}
match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) (?<status>(INFO|DEBUG|WARN|ERROR))"}
}

```

![](/assets/Image 40.png)