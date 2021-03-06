![](/assets/kibana result.png)#IIS Log Sample

date time s-ip cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Referer) sc-status sc-substatus sc-win32-status time-taken
2017-07-09 00:00:05 172.17.23.125 POST /service/healthapi/needrefresh - 80 - 60.251.47.197 Mozilla/5.0+(Windows+NT+6.1;+Win64;+x64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/58.0.3029.110+Safari/537.36 http://xxxx/en-gb/sports/getbanner?id=sbk-right 200 0 0 218



##實做對應到logstash parsing進去elasticsearch

###先在filebeat設定file folder
>透過給予他一個客製欄位 判斷它需要parsing log
![](/assets/fileBeatConfig.png)


### logstash pattern
會得到這樣

>[@metadata][beat] ==你從filebeat設定的index (預設小寫)

```
filter {
    if [@metadata][beat] =~ "" and [fields][iisfield] {

        grok {
			break_on_match => true			
            match => { "message" => "%{TIMESTAMP_ISO8601:logDate} %{NOTSPACE:sitename} %{WORD:httpMethod} %{URIPATH:Method} %{NOTSPACE:queryString} %{NUMBER:port} %{NOTSPACE:requestUserName} %{IPORHOST:clientIP} %{NOTSPACE:sourceUseragent} %{NOTSPACE:refererURL} %{NUMBER:response:int} %{NUMBER:status:int} %{NUMBER:sc-substatus:int} %{NUMBER:responseTime:int}" }
        }
       
        useragent {
            source => "sourceUseragent"
            target => "useragent"
        }
        #customize timestamp
        date {
            timezone => "America/Anguilla"
            match => ["logDate", "ISO8601"]
            target => "@timestamp"
        }
        
        
        if [Method] {
            mutate {
                add_tag => ["%{Method}"]
            }
        }
       
        if "membersite" in [type] {
            mutate {
                add_field => { "module" => "membersite" }
            }
        }
        if "mobilesite" in [type] {
            mutate {
                add_field => { "module" => "mobile" }
            }
        }
        if "app" in [type] {
            mutate {
                add_field => { "module" => "mobileapp" }
            }
        }
        if "mobileintegrationspi" in [type] {
            mutate {
                add_field => { "module" => "mobileappspi" }
            }
        }
        if "fsbtechspi" in [type] {
            mutate {
                add_field => { "module" => "fsbtechspi" }
            }
        }
        if "affiliateportal" in [type] {
            mutate {
                add_field => { "module" => "affiliateportal" }
            }
        }
        if "rtms" in [type] {
            mutate {
                add_field => { "module" => "rtms" }
            }
        }
  
        if "iis" in [type] {
            mutate {
                replace => {
                    "[@metadata][type]" => "iis"
                    "type" => "iis"
                }
            }
        }
 
        if [clientIP] {
            geoip {
                source => "clientIP"
                target => "geoip"
                #database => "/etc/logstash/GeoLite2-City.mmdb"
                add_field => ["[geoip][coordinates]", "%{[geoip][longitude]}"]
                add_field => ["[geoip][coordinates]", "%{[geoip][latitude]}"]
				add_tag => ["iis-geoip"]
            }
        }
		mutate {
            convert => ["[geoip][coordinates]", "float"]
            remove_field => ["logDate"]
            remove_tag => ["beats_input_codec_plain_applied", "beats_input_codec_multiline_applied"]
        }
    }
}
```

###打開service後 去kibana看 就可以看到結果

![](/assets/kibana result.png)


![](/assets/kibana result 2.png)

https://grokdebug.herokuapp.com/


![](/assets/grokDebug.png)

reference:


https://docs.microsoft.com/en-us/iis/configuration/system.applicationHost/sites/siteDefaults/logFile/

https://logz.io/blog/iis-log-analyzer/