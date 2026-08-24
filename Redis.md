### 開啟 redis-cli
1. 如果已透過`Docker`架設`redis`，只需要透過 Docker 指令「穿透」進去執行即可。
打開 Windows 的 **PowerShell** 或 **CMD**，輸入以下指令：
```shell
docker exec -it <你的容器名稱或ID> redis-cli
```
![[Pasted image 20260808125852.png]]


### 訂閱模式(subscribe(訂閱)  / publish(發布))
訂閱(接收方)：
```
subscribe geekhour
```

發布(發送方)：
```
publish geekhour python
```

### 資料型態
1. 哈希（Hashes）：用來存放單一`Object(物件)`
設定`哈希（Hashes）`(也就是物件)的指令：
```redis
hset person name laoyang
```
`person`：物件名稱
`name`：物件下的屬性名稱
`laoyang`：屬性值

結果如下：
![[Pasted image 20260808124459.png]]

取得`哈希（Hashes）`(也就是物件)內容
```redis
hgetall person
```

結果如下：
![[Pasted image 20260808124850.png]]

2. 消息队列（Streams）
建立一個`Stream`
```
xadd geekhour * course redis
xadd geekhour * course git
xadd geekhour * course docker
```

`*`代表自動產出消息id，送出命令後會回傳消息id
`course`和`redis`則分別是欄位名稱和值
`geekhour`：`Stream`的`key`

執行結果如下：
![[Pasted image 20260808130815.png]]

建立`Stream`後要如何取出：
```
xread count 2 block 1000 streams geekhour 0
```

`geekhour`：`Stream`的`key`
`count 2`：代表取出2筆`Stream`
`block 1000`：條件範圍內如果沒有`Stream`，則會延遲1000毫秒
最後面的`0`，代表從第幾筆`Stream`開始讀取

如果要取得從現在開始之後的`Stream`，則最後面就改為`$`：
```
xread count 2 block 1000 streams geekhour $
```

另外也可以建立一個`Stream`群組：
```
xgroup create geekhour groupe1 0
```

`groupe1`是消費群組名稱，`0`則是群組id

取得群組資訊：
```
xinfo groups geekhour

執行結果如下
1) 1) "name"
   2) "groupe1"
   3) "consumers"
   4) "0"
   5) "pending"
   6) "0"
   7) "last-delivered-id"
   8) "0-0"
   9) "entries-read"
   10) "null"
   11) "lag"
   12) "4"
```

新增3名群組成員：
```
> xgroup createconsumer geekhour groupe1 consumer1
(integer) 1

> xgroup createconsumer geekhour groupe1 consumer2
(integer) 1

> xgroup createconsumer geekhour groupe1 consumer3
(integer) 1
```

`groupe1`：群組名稱
`consumer1、consumer2、consumer3`：群組成員名稱

新增3名群組成員，再次取得群組資訊：
```
xinfo groups geekhour

執行結果如下
1) 1) "name"
   2) "groupe1"
   3) "consumers"
   4) "3"
   5) "pending"
   6) "0"
   7) "last-delivered-id"
   8) "0-0"
   9) "entries-read"
   10) "null"
   11) "lag"
   12) "4"
```

指定群組成員取得`Stream`內容：
```
xreadgroup group groupe1 consumer1 count 2 block 3000 streams geekhour >
 
執行結果如下：
1) 1) "geekhour"
   2) 1) 1) "1-0"
         2) 1) "course"
            2) "git"
      2) 1) "2-0"
         2) 1) "course"
            2) "docker"
```

`geekhour`：`Stream`的`key`
`groupe1`：群組名稱
`consumer1`：群組成員名稱
`count 2`：代表取出2筆`Stream`
`block 3000`：條件範圍內如果沒有`Stream`，則會延遲3000毫秒
最後面的`>`一定要加，否則指令無法執行

3. 地理空间（Geospacial）=>實際上就是`Sorted Set(有序集合)`

新增單一地點(經緯度)：
```
geoadd city 116.405285 39.904989 beijing
```

`city`：key值
`beijing`：地點名稱

新增多個地點(經緯度)：
```
geoadd city 121.472644 31.231706 shanghai 114.085947 22.547 shenzhen 23.134505 113.2500743 guangzhou 120.153576 30.287459 hangzhou
```

取得指定地點的經緯度：
```
geopos city beijing

輸出結果
1) 1) "116.40528291463852"
   2) "39.9049884229125"
```

兩個地點的直線距離：
```
geosearch city frommember shanghai byradius 300 km
1) "hangzhou"
2) "shanghai"
```

以**上海（shanghai）**為中心，搜尋半徑 **1000 公里**範圍內的所有城市。
```
geosearch city frommember shanghai byradius 1000 km
1) "hangzhou"
2) "shanghai"
```

`byradius`：代表圓形範圍區域

4. [HyperLogLog](https://en.wikipedia.org/wiki/HyperLogLog) (HLL) 是用統計方式解決 [Count-distinct problem](https://en.wikipedia.org/wiki/Count-distinct_problem) 的資料結構以及演算法，不要求完全正確，而是大概的數量。


![[RedisCheatSheet-ByGeekHour.pdf]]