# 2. Config

**請注意 所有的config 設定 請空一格**

ex:`setting: aaaa`

避免linux判斷錯誤

如果發現service停不下來

請先用
top -u 你的程式
`top -u logstash`   
接著 下強制砍掉的指令刪除他   
`kill -9 processId`
