# 5. FileBeat Config

**Version for 5.0.1**
>多行的範例可以只把某一個LOG檔案加入下面設定    
 
    multiline.pattern: '^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2} '
    multiline.negate: true
    multiline.match: after  
>下面設定是針對多執行緒打開    

    filebeat.publish_async: true
> 也可以設定Logstash的worker數量

    worker: 4

完整範例:

     filebeat.prospectors:

    - input_type: log
      paths:
        - D:\AgileBet\Logs\MobileApp\*-All.log
      document_type: appall	
    - input_type: log
      paths:
        - D:\AgileBet\Logs\MobileApp\*-Performance.log
      document_type: appperformance	
    - input_type: log
      paths:
        - D:\AgileBet\Logs\MobileApp\*-WEBAPI.log
      document_type: appwebapi	  
    - input_type: log
      paths:
        - D:\AgileBet\Logs\MobileApp\*-ERROR.log
      document_type: apperror	 
      multiline.pattern: '^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2} '

      multiline.negate: true


      multiline.match: after  
    - input_type: log
      paths:
        - D:\AgileBet\Logs\MobileApp\*-Server.log
      document_type: appserver	  
    - input_type: log
      paths:
        - D:\AgileBet\Logs\MobileApp\*-Feature.log
      document_type: appfeature	  

    filebeat.publish_async: true


    filebeat.registry_file: "C:/programdata/registry"


    output.logstash:

      enabled: true

      hosts: ["172.16.49.172:5044","172.16.49.116:5044"]

      worker: 4

      loadbalance: true

      index: "logstash-188bet"

    logging.to_files: true
    logging.files:



