# Performance Setting in Windows Memory High Problem
之前有遇過filebeat memory 越來越高的問題

如圖片 開啟大約12HR後會到1.7GB
**他會越來越高 到會造成系統問題**
![](/assets/filebeatdefault.png)

觀看了文件後

https://www.elastic.co/guide/en/beats/filebeat/5.3/configuration-global-options.html

有幾個設定預設是這樣

**scan_frequency 預設是十秒**

**filebeat.spool_size: 2048**

假設問題是 你的LOG量 十秒內 超過2048 他就會Cache 在你的記憶體內，接下來都無法釋放


所以如果 Log 量 十秒內3000筆 他就會讓記憶體卡住出不去

###解決方法

filebeat.spool_size: 4096 
> 單獨設定這個就有效果 他有提示說設定這個會造成記憶體升高但是實際上沒有差異 總比你讓記憶體到幾GB好


還有

![](/assets/Load Balance Setting.png)

設定load balance 假設你logstash server 有兩台

**結果**
實際上開啟12HR 後還是沒有變多多少
![](/assets/filebeatspoolsize.png)

實際比較後 發現LOG量並沒有跟設定的差太多，但是記憶體的問題已經被解決
![](/assets/Compare result.png)


# Performance Setting in Mutiline
**發現 Mutiline不能混用 只能挑選下面一種修改 不然會有檔案跟內容錯誤的問題**

##Input log with Mutiline

*  Mutipleline in logstash input   

>優點：統一input 可以開mutithread      
>缺點：如果量很大可能會影響logstash cpu 用量 ，input 從filebeat 來還是需要一行一行合併　可能會造成效能問題

      input {
         beats {
            port => 5044
            codec => multiline {
            pattern => "^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}[\.,][0-9]{3,7} "
            negate => true
            what => "previous"
            }
            congestion_threshold  => 40
           }
        }


* Mutipleline in logstash filter 

>優點：分開設定在filter 內   
>缺點：如果量很大可能會影響logstash cpu 用量，不能開mutithread ，變成一定要一行一行進去才能收集　是最不好的方法　　　　

        filter {
            if  [@metadata][beat] =~ "188bet"   {
                  multiline {
                   pattern => "^%{TIMESTAMP_ISO8601}"
                   negate => true
                   what => "previous"
                }
          }
        }
* Mutipleline in filebeat    

>優點:　各自由filebeat　送log 到 logstash , 減少logstash cpu     
>缺點：可能會影響原本server cpu,mem

**下列設定是by各個要撈的檔案　**




        - input_type: log
          paths:
            - D:\AgileBet\Logs\MobileApp\*-ERROR.log
          document_type: apperror	  
          multiline.pattern: '^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2} '
          multiline.negate: true
          multiline.match: after
##Performance Compare note.
Server : 18 (other team have total 20 or more)
###using Mutipleline in logstash input  
>30 scends : 2000~4000  ,lost 90%   
>we have 3 team to deploy the 4 logstash server with 2 es server.

>Server filebeat have 300 mem, seems stock in server side 

###using Mutipleline in filebeat   

>30 scends : 30000

>Server filebeat have 20 mem, s
![](Image 32.png)




