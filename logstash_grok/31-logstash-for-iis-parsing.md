#IIS Log Sample

date time s-ip cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Referer) sc-status sc-substatus sc-win32-status time-taken
2017-07-09 00:00:05 172.17.23.125 POST /service/healthapi/needrefresh - 80 - 60.251.47.197 Mozilla/5.0+(Windows+NT+6.1;+Win64;+x64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/58.0.3029.110+Safari/537.36 http://xxxx/en-gb/sports/getbanner?id=sbk-right 200 0 0 218



##實做對應到logstash parsing進去elasticsearch

會得到這樣

>


###先在filebeat設定file folder
>透過給予他一個客製欄位 判斷它需要parsing log
![](/assets/fileBeatConfig.png)


### logstash pattern


https://grokdebug.herokuapp.com/


![](/assets/grokDebug.png)

reference:


https://docs.microsoft.com/en-us/iis/configuration/system.applicationHost/sites/siteDefaults/logFile/

https://logz.io/blog/iis-log-analyzer/