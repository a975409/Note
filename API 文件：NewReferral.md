## 1. API 呼叫網址

POST https://evt.hyjr.com.tw/data.aspx/NewReferralApi

## 2. Request 欄位說明

| 欄位名稱               | 資料型態   | 說明                                        |
| ------------------ | ------ | ----------------------------------------- |
| turnDate           | string | 轉診日期，格式如 YYYY-MM-DD                       |
| turnHospital       | int    | 轉入醫院代碼                                    |
| fromHospital       | int    | 轉出醫院代碼                                    |
| name               | string | 病患姓名                                      |
| cId                | string | 身分證字號                                     |
| type               | int    | 轉診類型                                      |
| birthday           | string | 出生日期，格式如 YYYY-MM-DD                       |
| sex                | int    | 性別（0=女，1=男）                               |
| CGC_E              | string | Glasgow Coma Scale - Eye Opening 反應       |
| CGC_V              | string | Glasgow Coma Scale - Verbal Response 語言反應 |
| CGC_M              | string | Glasgow Coma Scale - Motor Response 肢體反應  |
| stressH            | int    | 血壓收縮壓                                     |
| stressL            | int    | 血壓舒張壓                                     |
| onsetDate          | string | 發病日期，格式如 YYYY-MM-DD                       |
| onsetDate_un       | int    | 發病日期不確定標記（0=確定，1=不確定）                     |
| lastDate           | string | 最後正常日期，格式如 YYYY-MM-DD                     |
| lastDate_un        | int    | 最後正常日期不確定標記（0=確定，1=不確定）                   |
| NIHSS              | int    | NIH Stroke Scale 評分                       |
| before_mRS         | int    | 發病前 mRS 評分（修訂Rankin量表）                    |
| is_rtPA            | int    | 是否使用 rtPA（0=否，1=是）                        |
| rtPA_date          | string | rtPA 使用日期，格式如 YYYY-MM-DD                  |
| poisonType         | int    | 中毒類型代碼                                    |
| is_Decontamination | int    | 是否進行解毒處理（0=否，1=是）                         |
| fa1                | string | 額外欄位 fa1                                  |
| fa2                | string | 額外欄位 fa2                                  |
| fa3                | string | 額外欄位 fa3                                  |
| fa4                | string | 額外欄位 fa4                                  |
| fa5                | string | 額外欄位 fa5                                  |
| fb1                | string | 額外欄位 fb1                                  |
| fb2                | string | 額外欄位 fb2                                  |
| fb3                | string | 額外欄位 fb3                                  |
| fb4                | string | 額外欄位 fb4                                  |
| fb5                | string | 額外欄位 fb5                                  |
| teach              | int    | 教學評分                                      |
| face               | int    | 臉部評分                                      |
| arm                | int    | 手臂評分                                      |
| speech             | int    | 語言評分                                      |
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
