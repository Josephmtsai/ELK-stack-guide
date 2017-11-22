# Windows Version

## Install of JAVA 
Add Envirement Vars
> please make sure the folder with server and JVM DLL

```
JAVA_HOME= "C:\Program Files\Java\jre1.8.0_131"

Path += "C:\Program Files\Java\jre1.8.0_131\bin"
```


##Install of ElasticSearch
>install guide 
https://www.elastic.co/guide/en/elasticsearch/reference/current/windows.html

![](/assets/es1.png)  

![](/assets/es2.png)

![](/assets/es3.png)

##Install of Logstash

https://www.elastic.co/guide/en/beats/libbeat/current/logstash-installation.html
https://www.elastic.co/guide/en/logstash/current/running-logstash-command-line.html

###Extract folder 

###put the config in CustomConfig folder

>save as default.conf 


```
 input {
 
    beats {
                 port => 5044
         }
 }
 
 filter {
 	
 		grok {
 			match => { "message" => "%{TIMESTAMP_ISO8601:log_timestamp}"}
 		}
 		date {
 			match => [ "log_timestamp", "ISO8601"]
 			target => "@log_timestamp"
 		}
 }
 
 output {
   elasticsearch {
     hosts => ["http://localhost:9200"]
     index => "logstash-%{+xxxx.ww}"
     document_type => "%{[@metadata][type]}"
    }
 }

```

![](/assets/logstash config.png)



register as service

Download nssm

http://nssm.cc/download

**cmd as admin **

nssm install logstash "E:\Logstash\logstash-5.5.0\bin\logstash.bat" "-f :\Logstash\logstash-5.5.0\CustomConfig -r "
![](/assets/logstash service.png)

nssm start logstash


![](/assets/logstashlog.png)


## Install of Filebeat

### Extract Folder

### Create bat file 

>remove old version then install.bat

```
%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -Command "set-executionpolicy unrestricted"
%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -Command "&{stop-service filebeat}"
%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -Command "& 'E:\filebeat-5.4.3-windows-x86_64\uninstall-service-filebeat.ps1'"

%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -Command "&{E:\filebeat-5.4.3-windows-x86_64\install-service-filebeat.ps1}"
%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -Command "Set-Service filebeat -startuptype "manual""
%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -Command "&{start-service filebeat}"

cd E:\filebeat-5.4.3-windows-x86_64
E:

pause
```

###Edit the filebeat.yml

>Make sure all the setting is correct ,see the Chapt 5
![](/assets/logstashyml.png)

## Install of Kibana

Extract folder

go to current folder

run cmd

```
bin\kibana.bat
```


Then start the service 
