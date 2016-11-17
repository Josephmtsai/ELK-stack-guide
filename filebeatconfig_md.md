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

      hosts: ["172.16.:5044","172.16.:5044"]

      worker: 4

      loadbalance: true

      index: "logstash-188bet"

    logging.to_files: true
    logging.files:
       path: L:\Beats\filebeat
       name: filebeat

如果一開始不確定INPUT是什麼 可以透過設定 FILE 去觀察是否正確

      output.file:
        # Boolean flag to enable or disable the output module.
        enabled: true

        # Path to the directory where to save the generated files. The option is
        # mandatory.
        path: "你的路徑"

        # Name of the generated files. The default is `filebeat` and it generates
        # files: `filebeat`, `filebeat.1`, `filebeat.2`, etc.
        filename: filebeat

        # Maximum size in kilobytes of each file. When this size is reached, and on
        # every filebeat restart, the files are rotated. The default value is 10240
        # kB.
        #rotate_every_kb: 10000

        # Maximum number of files under path. When this number of files is reached,
        # the oldest file is deleted and the rest are shifted from last to first. The
        # default is 7 files.
        number_of_files: 7
