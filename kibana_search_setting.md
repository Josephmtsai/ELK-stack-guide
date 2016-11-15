# Kibana Search Setting

Please go to Discover Tab first

## Search Guide

一開始你需要存一個簡易的Search 你可以透過左側的 欄位 去加上你想要顯示的欄位 

> Type: 在elasticsearch = DB 
> 展開Type field ,裡面有最近500的值 **點+ 或是-** 代表加上此filter + 代表 符合此條件 - 代表不要包含此條件   

![](Image 22.png)      
移動上去filter   會顯示一些條件，可以再度調整，點最右邊的條件可以修改你要的條件
![](Image 24.png)   
可以修改你所需要的條件  

![](Image 23.png)

可以參考此URL:
https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html


下面為範例:
>must:代表一定都要符合
>must_not: 代表一定不要復合
>should 代表只要符合最低的目標就可以

    "bool" : {
          "must" : {
            "term" : { "user" : "kimchy" }
          },
          "must_not" : {
            "range" : {
              "age" : { "from" : 10, "to" : 20 }
            }
          },
          "should" : [
            { "term" : { "tag" : "wow" } },
            { "term" : { "tag" : "elasticsearch" } }
          ],
          "minimum_should_match" : 1
        }    
        
        
此為完整範例:

      {
        "bool": {
          "must": [
            {
              "match": {
                "type": {
                  "query": "feature"
                }
              }
            }
          ],
          "must_not": [
            {
              "match": {
                "featureResult": {
                  "query": "InProgress"
                }
              }
            }
          ]
        }
      }     


##Save Search
當你存好後可以在此處看到你的Save 設定
![](Image 19.png)  



![](Image 25.png)
**點下編輯後可以看到你存的設定 以及要顯示的欄位**   
![](Image 26.png)

# Export Setting 
>**你可以全部匯出 或是 先把Search > Visualize >Dashboard 依照順序匯出 備份**


![](Image 27.png)