## 1. API 呼叫網址

POST https://evt.hyjr.com.tw/data.aspx/NewReferralApi

## 2. Request 欄位說明

| 欄位名稱               | 資料型態   | 說明                                                                                                 |
| ------------------ | ------ | -------------------------------------------------------------------------------------------------- |
| turnDate           | string | 轉診日期，格式：YYYY/MM/DD HH:mm:ss<br>'/' 替換為' - ' 也可                                                     |
| turnHospital       | int    | 轉⼊醫院代碼 <br><br>4= ⻑庚醫院                                                                             |
| fromHospital       | int    | 轉出醫院代碼 <br><br>1= 屏東榮總<br>2= 屏東基督教醫院<br>3= 阮綜合醫院                                                   |
| name               | string | 病患姓名                                                                                               |
| cId                | string | 身分證字號                                                                                              |
| type               | int    | 轉診類型，1=腦中風、2=毒物                                                                                    |
| birthday           | string | 出生日期，格式： YYYY-MM-DD 或 YYYY/MM/DD                                                                   |
| sex                | int    | 性別（0=女，1=男）                                                                                        |
| CGC_E              | string | 意識CGS-E：1~4                                                                                        |
| CGC_V              | string | 意識CGS-V：1~5、E、T、A                                                                                  |
| CGC_M              | string | 意識CGS-M：1~6                                                                                        |
| stressH            | int    | 血壓High                                                                                             |
| stressL            | int    | 血壓Low                                                                                              |
| onsetDate          | string | 發病時間，格式：YYYY/MM/DD HH:mm:ss<br>'/' 替換為' - ' 也可                                                     |
| onsetDate_un       | int    | 1=不確定發病時間                                                                                          |
| lastDate           | string | 最後正常時間，格式：YYYY/MM/DD HH:mm:ss<br>'/' 替換為' - ' 也可                                                   |
| lastDate_un        | int    | 1=不確定最後正常時間                                                                                        |
| NIHSS              | int    | NIHSS分數：1~42                                                                                       |
| before_mRS         | int    | 未發病前mRS分數：1~6                                                                                      |
| is_rtPA            | int    | 是否施打r-tPA，0=是、1=否                                                                                  |
| rtPA_date          | string | 施打r-tPA時間，格式：YYYY/MM/DD HH:mm:ss<br>'/' 替換為' - ' 也可                                                |
| poisonType         | int    | 毒物型態，1=藥物、2=生物、3=環境                                                                                |
| is_Decontamination | int    | 是否除汙（0=否，1=是）                                                                                      |
| fa1                | string | 影像學圖片1，檔案需轉為base64                                                                                 |
| fa2                | string | 影像學圖片2，檔案需轉為base64                                                                                 |
| fa3                | string | 影像學圖片3，檔案需轉為base64                                                                                 |
| fa4                | string | 影像學圖片4，檔案需轉為base64                                                                                 |
| fa5                | string | 影像學圖片5，檔案需轉為base64                                                                                 |
| fb1                | string | 檢驗檔案1，檔案需轉為base64                                                                                  |
| fb2                | string | 檢驗檔案2，檔案需轉為base64                                                                                  |
| fb3                | string | 檢驗檔案3，檔案需轉為base64                                                                                  |
| fb4                | string | 檢驗檔案4，檔案需轉為base64                                                                                  |
| fb5                | string | 檢驗檔案5，檔案需轉為base64                                                                                  |
| teach              | int    | 衛教，如有符合以下多個選項，則將選項的值加總後再傳值<br>1=可決定家屬陪同轉院<br>2=已提供介入EVT資訊<br>4=血壓、靜脈點滴控制<br><br>例如：1+2+4=7，teach=7 |
| face               | int    | 微笑測試，0=無、1=臉部不對稱、2=口角歪斜                                                                            |
| arm                | int    | 舉手測試，0=無、1=一邊手臂垂落不對稱                                                                               |
| speech             | int    | 語言測試，0=無、1=口吃、2=說話不清、3=無法說話                                                                        |

<br><br><br><br><br><br><br>
 Request 範例：
```json
{
    "turnDate": "2026-04-13 10:28",
    "turnHospital": "1",
    "fromHospital": 1006,
    "type": 2,
    "name": "王大陸",
    "cId": "A159615601",
    "birthday": "2026-04-13",
    "sex": "0",
    "CGC_E": "1",
    "CGC_V": "1",
    "CGC_M": "1",
    "stressH": 0,
    "stressL": 0,
    "onsetDate": "",
    "onsetDate_un": 0,
    "lastDate": "",
    "lastDate_un": 0,
    "NIHSS": "0",
    "before_mRS": "0",
    "is_rtPA": "0",
    "rtPA_date": "",
    "poisonType": "1",
    "is_Decontamination": 1,
    "fa1": "",
    "fa2": "",
    "fa3": "",
    "fa4": "",
    "fa5": "",
    "fb1": "",
    "fb2": "",
    "fb3": "",
    "fb4": "",
    "fb5": "",
    "teach": 5,
    "face": "0",
    "arm": "0",
    "speech": "0"
}
```
<br><br><br><br>
## 3. Response 結果 (200 OK)

新增資料成功：
```json
{
    "d": "success"
}
```

授權失敗，原因是用戶端ip尚未加入至白名單
```json
{
    "d": "unauthorized"
}
```
