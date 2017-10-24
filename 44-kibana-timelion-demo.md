#Kibana Timlion
![](/assets/timelion 1.png)
###用途
**1. 假設你想要比較 這周跟上周資料的圖表差異 **
**2. 想要比較百分比的相除的值 **
> 簡單來講 他可以做一些時間序列的加減乘除


#基本語法教學


先假設我們有這些欄位

| Name          | dataType | description  | 備註                                    |
|---------------|----------|--------------|-----------------------------------------|
| @timestamp    | date     | Custom Field | Log 進入時間                            |
| @version      | keyword  | Custom Field | 如果重複進入的資料會往上+1              |
| featureResult | text     | Custom Field | 最後結果                                |
| host          | text     | Custom Field | 哪一台Server                            |
| index_prefix  | text     | Custom Field | Index 共用的設定          |
| input_type    | text     | Custom Field |                                         |
| logger        | text     | Custom Field | logger                                  |
| mark          | text     | Custom Field | 代表是Standard or Standby Server or xxx |
| message       | text     | Custom Field | 原始資料                                |
| messages      | text     | Custom Field | 剩餘資訊                                |
| Method        | text     | Custom Field | 哪一個重要的方法                        |
| module        | text     | Custom Field | 哪一個模組                              |
| offset        | long     | Custom Field | 用於代表時間值 在 TimeLion用到          |
| procesId      | long     | Custom Field | logger的ProcesId                        |
| source        | text     | Custom Field | 原始檔案位置                            |
| status        | text     | Custom Field | logger Status                           |
| tags          | text     | Custom Field | for 單純搜尋用 不能分析                 |
| type          | text     | Custom Field | Document Type                           |

請看下面例子:

這樣會顯示一條線 預設是 根據你的查詢條件來
````
.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ")

````
> .es() 代表是每一條線 或是 長條圖 裡面的參數可以從右上角的docs去看
> index 可以選定一個 table的資料
> q 條件的查詢用法 跟discover一樣 可以去看文件 
> 右上角是選擇這個查詢條件要查詢多久的資料
> 下面的條件是選擇間隔時間
![# ](/assets/TimeLion2.png)

這樣就可以改成一條bar 
````
.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ").bar()

````
>所有的開始都是.es() 後面再. 他會有提示可以用的功能 可以簡單看一下功能
![](/assets/Timelion3.png) 

