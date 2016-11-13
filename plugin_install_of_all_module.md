# 2.4 Plugin Install Of All Module

## X-pack 
> 此套件試用一個月 基本功能有 觀測Index 效能、搜尋時間  
> **他需要安裝在Kibana ,elasticsearch**上面

安裝如下:

請先把檔案上傳到Server上 並且下指令去安裝   
`/usr/share/elasticsearch/bin/elasticsearch-plugin install file:///tmp/x-pack-5.0.0.zip`    

`/usr/share/kibana/bin/kibana-plugin install file:///tmp/x-pack-5.0.0.zip`
xpack.security.enabled: false

![](4.png)