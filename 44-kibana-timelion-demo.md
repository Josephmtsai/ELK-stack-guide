

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


##單一條線的圖表

請看下面例子:

**我們想看到現在登入失敗的線條**

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



##兩條線 與過去同期七天的資料比較

就如我們一開始講的.es() 代表一條線 , 可以分隔第二條線 所以我們下面的例子

**我們想達到判斷過去同期七天的登入失敗以及現在登入的線條比較**
**.es(),.es()** 代表了兩條線 我們可以分別看他的說明
````
.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ")
.label("currenct week login failed counting"),

.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ", offset="-7d")
.label("last week  login failed counting").title("login failed compare")
````
>.label 可以標注你的線說明

>offset 代表是你想要相同的條件內查詢是過去幾天的資料

結果就會像這樣 可以看到Login失敗過去七天相同時間的比較
![](/assets/TimeLion4.png)


## 資料加總相除的比例

接著我們舉個比較困難的例子

**我們想看到登入失敗 /全部登入的資料所佔有的百分比**

**.divide() 裡面可以再放一個 查詢的.es()**
**.multiply 可以放入數字**
````
.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ")
.divide(.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND ( featureResult:FAIL  or featureResult:SUCCESS)"))
.multiply(100)
.label("Current Week Login Failed Perfentage").yaxis(label="percentage")
````
>所以查詢條件的分子 第一個也就是我們放入了 登入失敗的數量 接著第二個我們放入了相除的資料

>也就是登入成功以及失敗的加總查詢 接著把他相除

得到的結果會像這樣

![](/assets/Timelion5.png)


## 資料加總相除的比例 與同期七天的比較

接著舉個更難的例子

````
.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ")
.divide(.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND ( featureResult:FAIL  or featureResult:SUCCESS)"))
.multiply(100).label("Current Week Login Failed Perfentage"),

.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND featureResult:FAIL ", offset="-7d")
.divide(.es(index="xxx-application*",q="type:feature AND Method:LOGIN AND ( featureResult:FAIL  or featureResult:SUCCESS)", offset="-7d"))
.multiply(100).label("Last Week Login Failed Perfentage")
.title("log  failed percentage").yaxis(label="percentage")
````

>結合我們前面幾個例子就可以達到比較過去同期資料的登入失敗的百分比的資料


![](/assets/Timelion6.png)

#將資料放到Dahboard

我們可以把剛剛上面設定好的條件，存放到visual 內 就可以在Dashboard上面呈現資料

所以我們可以透過這個TAB 對一些資料來做處理或是判斷 

![](/assets/TimeLion7.png)

  
![](/assets/Timelion8.png)