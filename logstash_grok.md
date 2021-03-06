#Logstash Pattern 簡單教學


簡單對存入進去es的資料做一些講解    
* index => DB
* type => Table
* document => 剩下的檔案
![](1-oP20tJyA_eDOycJPyDYo2A.png)

基礎 由 三塊組合而成
* input    
* filter    
* output     

參考圖為最下方網址
![](/assets/Sameple.png)
基本的程式碼會長這樣由剛剛講的三個部分組成

![](/assets/Image 36.png)

基本上logstash 支援將各個檔案合併的方法，但是需要透過編號的方法 讓他區分開

logstash 在啟動的時候會在去將這些檔案合併，所以請依照數字排序 不要讓config出問題

有問題可以從log 看

>為什麼需要這樣呢? 因為logstash 可以針對不同的index 執行不同的parsing,因為這塊非常消耗資源所以在寫的時候要非常小心


![](/assets/Image 10.png)


#input 

>mutiline 如果沒有放在這個區域他沒辦法啟動 多執行緒去判別
>port => 進入的port
> mutiline 判斷ISO 8601 或是 ISO 8601後面時間區碼是7碼 將他多行合併成一行

      input {

         beats {
              port => 5044
              codec => multiline {
              pattern => "^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}[\.,][0-9]{3,7} "
              negate => true
              what => "previous"
              }
          }
      }


#filter
測試自己的pattern 可以先從下面兩個網址去查詢


https://grokdebug.herokuapp.com/   
http://grokconstructor.appspot.com/do/match

>[@metadata][beat] 這個是從你的filebeat 設定檔案的index 來的 他可以取不同的名子 **切記要小寫**    
>grok 代表他會match什麼樣的條件 會依照match的順序    
>break_on_match => true 代表只要符合他就會跳掉    
>%{TIMESTAMP_ISO8601:logDate}  TIMESTAMP_ISO8601 代表他內建的條件 : 後面是你想要的欄位名稱    
>%{POSINT:processId:int} 最後可以指定他的型態    
>(?&lt;logger>[A-Z\-a-z .]+) 如果想自定pattern 格式 可以透過 [內的regular expression]去撰寫    
>mutate => 可以將你的欄位移除 或是轉換型態   ex: remove_field ,remove_tag
>GREEDYDATA => 剩餘資料全部變成此欄位
>

* tag:可以用來搜尋 : 可以透過 動態的方法 加入tag

      mutate {
        add_tag => [ "%{httpMethod}" ,"%{apiPath}" ]
      }	

* geoip 可以將ip 轉換成地圖所需的格式 經緯度
* **database => "/etc/logstash/GeoLiteCity.dat** 此在五版已經改為內建 請勿在加入
* 撰寫的時候請先判斷欄位 是否存在 在寫


* 如果Server 有多台logstash  可以透過加tag 區分來源server
      filter {
          mutate {
              add_tag => [ "dev-logstash-01" ]
          }
      }


完整範例如下:

      filter {
          if  [@metadata][beat] =~ ""   {

              grok {		
                  break_on_match => true
                  match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\] \[(?<PERFORMANCE>[A-Z\-a-z]+)\]\[(?<describe>[A-Z\-a-z  ]+)\]\[(?<Method>[A-Z\-a-z . /=?0-9\[\]:()',]+)\]\[%{NUMBER:performanceTime:int}ms\]%{GREEDYDATA:messages}"}
                  match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\]\[(?<webapi>[A-Z\-a-z]+)\]\[(?<apiPath>[A-Z\-a-z . /=?0-9\[\]:()]+)\]\[(?<httpMethod>[A-Z\-a-z /.\-]+)\]\[%{IP:clientIP}\]%{GREEDYDATA:messages}"}
                  match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\] \[(?<Feature>(FEATURE))\]\[(?<Method>[A-Z\-a-z . /=?0-9:()]+)\]\[(?<featureResult>[A-Z\-a-z . /=?0-9:()]+)\]%{GREEDYDATA:messages}"}
                  match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) \[(?<status>(INFO|DEBUG|WARNING|ERROR))\] \[(?<logger>[A-Z\-a-z .]+)\] %{GREEDYDATA:messages}"}
                  match => {"message" => "%{TIMESTAMP_ISO8601:logDate} (\[%{POSINT:processId:int}\]) (?<status>(INFO|DEBUG|WARN|ERROR))"}
              }
              # customize timestamp
              date {
                  timezone => "America/Toronto"
                  match => ["logDate", "ISO8601"]
                  target => "@timestamp"
              }	
              mutate {
                      convert => [ "[geoip][coordinates]", "float"]
                      remove_tag => [ "beats_input_codec_plain_applied", "beats_input_codec_multiline_applied", "_grokparsefailure" ]
                      remove_field => [ "PERFORMANCE","logDate","Feature" ,"webapi" ]
              }



              if [httpMethod]{
                  mutate {
                      add_tag => [ "%{httpMethod}" ,"%{apiPath}" ]
                  }	

              }
              if [Method]{
                  mutate {
                      add_tag => [ "%{Method}"  ]
                  }	
              }
              if [featureResult]{
                  mutate {
                      add_tag => [ "%{featureResult}"  ]
                  }	
              }

              if [clientIP] {
                  geoip {
                        source => "clientIP"
                        target => "geoip"
                        # database => "/etc/logstash/GeoLiteCity.dat"
                        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
                        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
                  }
              }

          }
      }

#Output
>host =>如果是cluster 可以打所有SERVER 他會自動sync
>index => ww 是BY week
>host => 可以傳入單一data node or 全部的data node 他會自動作sync up


        output {
          elasticsearch {
            hosts => ["http://xxx.xx.xxx.xx:9200"]
            index => "%{[@metadata][beat]}-%{+xxxx.ww}"
            document_type => "%{[@metadata][type]}"
           }
        }


#Reference:

Geo IP查詢:http://stackoverflow.com/questions/33673510/logstash-kibana-geoip-not-working    

Parsing JSON:http://stackoverflow.com/questions/33937936/how-to-parse-json-in-logstash-grok-from-a-text-file-line    

替換@timestam:http://stackoverflow.com/questions/20401663/logstash-replace-timestamp-with-syslog-date    

https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html
基礎介紹:https://qbox.io/blog/parsing-logs-using-logstash?utm_source=qbox.io&utm_medium=article&utm_campaign=logstash-5-0-monitoring-api-node-plugin-stats-hot-threads
效能介紹https://www.elastic.co/blog/do-you-grok-grok


