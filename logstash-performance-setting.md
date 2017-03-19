#Logstash grok 效能

>此處為記錄logstash 效能的部分
>主要是參考此篇

https://www.elastic.co/blog/do-you-grok-grok

首先我們要知道

1. Grok 是根據regular expression 來的 所以寫法也會影響效能

2. _geoip_lookup_failure 這個代表示沒有match 到任何的正規表示式 (代表) 他會將你所有的MATCH 跑過一輪才會結束 ，如下面的例子 他會執行五次MATCH 才會判斷 這個是非常浪費效能的事情
![](/assets/Image 40.png) 

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

**Beware of the performance impact when grok fails to match**

![](/assets/Screen Shot 2016-09-27 at 14.02.29.png)
>這張圖期實我也看了很久 這張意思是說

> **當你一開始就失敗的話 因為GROK並不會中斷處理 所以就算在一開始失敗 他還是會跑到最後面才會認定失敗 等於浪費非常多時間 ，BLOG 上面是寫說有可能比成功match slow 6倍**


解決方法 BLOG 上面是提到加上^ 跟$ 我們都知道這是regular expression的開始以及結束字元 這可以加速他判斷

![](/assets/Screen Shot 2016-09-27 at 14.06.53.png)

>所以這樣可以增進他馬上判斷錯誤的效能 減少時間耗損



