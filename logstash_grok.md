#Logstash Pattern 簡單教學

請參考   
https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html

基礎 由 三塊組合而成
* input    
* filter    
* output    

![](Image 10.png)
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

