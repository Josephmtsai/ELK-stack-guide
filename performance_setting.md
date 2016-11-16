# Performance Setting


##Input log with Mutiline

*  Mutipleline in logstash input   

>優點：統一input 可以開mutithread      　　　　　　
>缺點：如果量很大可能會影響logstash cpu 用量 

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
* Mutipleline in fielbeat

        - input_type: log
          paths:
            - D:\AgileBet\Logs\MobileApp\*-ERROR.log
          document_type: apperror	  
          multiline.pattern: '^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2} '
          multiline.negate: true
          multiline.match: after