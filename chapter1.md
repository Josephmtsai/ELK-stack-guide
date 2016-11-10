# File Prepare
 

## Download file


* https://www.elastic.co/downloads/elasticsearch
* https://www.elastic.co/downloads/kibana
* https://www.elastic.co/downloads/logstash



## Using sftp to upload file 
Connect from server
![](Image 4.png)
upload file to tmp folder /tmp

```lcd d:\ELK``` (switch to local disk folder)
```mkdir /tmp``` (create a remote folder to temp use) 
```cd /tmp``` (switch to remote linux folder, if exist) 
```put elasticsearch-5.0.0.rpm```