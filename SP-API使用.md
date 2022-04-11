# SP-API

## Report API

### getReports 方法

#### 作用

返回符合過濾的報告

#### 使用限制

0.0222/s

#### 參數

+ **reportTypes**：可選。指定報表的類型，指定報表類型時最少1個，最多10個。
+ **processingStatuses**：可選。指定報表的狀態。值為 ProcessingStatusEnum 枚舉類型，定義以下狀態：
  + `CANCELLED`：報表已經被取消。可以通過兩種方式取消報告。1. 明確的取消請求，在報表建立之前。2. 如果沒有返回數據，會自動取消。
  + `DONE`：報表已完成
  + `FATAL`：報表因錯誤而終止
  + `IN_PROGRESS`：報表在執行中
  + `IN_QUEUE`：報表等待執行。
+ **marketplaceIds**：可選。指定市場ID列表。指定時最小1個最多10個。
+ **pageSize**：可選，指定返回每次呼叫報表筆數。默認值 10。指定時最小1最大100。
+ **createdSince**：可選。指定日期開始**之後**創建的報表。使用ISO-8601字串格式。**報表最多只會保留90天**。
+ **createdUntil**：可選。指定日期**之前**創建的報表。使用ISO-8601字串格式。
+ **nextToken**：獲取下一頁結果的令牌。當查詢結果超過pageSize指定筆數，就會返回 nextToken，**如果要獲取下一頁的結果，請將 nextToken 當作唯一的過濾條件。** nextToken與其他過濾條件一起使用會導致請求錯誤。

### **createReport**

#### 作用

創建報表

#### 使用限制

0.0167/s

#### 參數

+ **body**：必須的。**CreateReportSpecification** 類型，該類型提供以下屬性：
  + **reportOptions**：可選的。創建報告的附加訊息，這因報告的類型有所不同。
  + **reportType**：必須的。報告的類型
  + **dataStartTime**：可選的。指定開始日期決定報表的數據，默認值是現在，其值必須現在或以前的日期。並非所有報表都支持該條件。
  + **dataEndTime**：可選的。指定結束日期決定表表的數據，默認值是現在。其值必須現在或以前的日期。並非所有報表都支持該條件。
  + **marketplaceIds**：必須的。指定市場ID列表，最小1個最多10個

### getReport

#### 作用

查詢指定ID的報表詳細訊息

#### 使用限制

2/s

#### 參數

+ reportId：必須的。報表的ID，報表ID只有和賣家ID結合時才會是唯一。

### **cancelReport**

#### 作用

取消生產指定的報表，只有報表狀態在 **IN_QUEUE** 才可以被取消。

#### 使用限制

0.0222/s

#### 參數

+ reportId：必須的。報表的ID，報表ID只有和賣家ID結合時才會是唯一。

### **getReportSchedules**

#### 作用

返回符合指定條件的報表排程詳細訊息

#### 使用限制

0.2222/s

#### 參數

+ reportType：必須的。指定報表類型列表。最少1個至多10個

### **createReportSchedule**

#### 作用

創建報表排程，如果在同樣市場ID且相同類型的報表排程已經存在，舊的排程將被取消，使用新的替換。

#### 使用限制

0.0222/s

#### 參數

+ body：必須的。**CreateReportScheduleSpecification** 類型，該類定義了以下屬性，提供建立報表排程的資訊：
  + **reportType**：必須的。報表的類型。
  + **marketplaceIds**：必須的。市場的ID列表。
  + **reportOptions**：可選的。傳遞給報表的附加訊息。會根據報表類型有所不同
  + **period**：必須的。指定創建報表的頻率。[Period](https://github.com/amzn/selling-partner-api-docs/blob/main/references/reports-api/reports_2020-09-04.md#period)枚舉類型。
  + **nextReportCreationTime**：可選的。下一次創建報表的時間。<font color="ff0000">待確認</font>

### **getReportSchedule**

#### 作用

返回指定的報表排程詳細訊息。

#### 使用限制

0.0222/s

#### 參數

+ **reportScheduleId**：必須的。報表排程ID。報表排程ID與賣家ID結合才是唯一標示符。

### **cancelReportSchedule**

#### 作用

取消指定的報表排程

#### 使用限制

0.0222/s

#### 參數

+ **reportScheduleId**：必須的。報表排程ID。報表排程ID與賣家ID結合才是唯一標示符。

### **getReportDocument**

返回察看報表文檔所需的訊息。訊息包括預簽名URL和解密文檔內容必須的訊息。

#### 使用限制

0.0167/s

#### 參數

+ **reportDocumentId**：必須的。報表文檔ID。

#### 返回值類型

結果封裝為 [ ReportDocument](https://github.com/amzn/selling-partner-api-docs/blob/main/references/reports-api/reports_2020-09-04.md#reportdocument) 物件。提供以下屬性使用：

+ **reportDocumentId**：必定有值，報表文檔的ID。報表文檔ID和賣家ID結合才是唯一標示。
+ **url**：必定有直，報表文檔預簽名的URL，URL會在五分鐘之後過期。
+ **encryptionDetails**：必定有值，解密報表文檔內容所需的加密詳細訊息。[ReportDocumentEncryptionDetails](https://github.com/amzn/selling-partner-api-docs/blob/main/references/reports-api/reports_2020-09-04.md#reportdocumentencryptiondetails)類型，該類型提供以下屬性：
  + **standard**：使用哪種加密標準。StardardEnum 枚舉類型，目前只有一種 AES。
  + **initializationVector**：解密文檔內容使用的密碼塊鍊
  + **key**：解密文檔使用的加密密鑰。
+ **compressionAlgorithm**：可能為null。壓縮算法。如果存在值，則代表報表已經使用該壓縮算法處理過。CompressionAlgorithmEnum 枚舉類型，目前只有一種 **GZIP**

### 錯誤

 ERROR 封裝錯誤訊息，內部提供以下屬性描述錯誤：

+ **code**：標示錯誤類型的錯誤代碼
+ **message**：錯誤描述訊息
+ **details**：錯誤的詳細訊息

