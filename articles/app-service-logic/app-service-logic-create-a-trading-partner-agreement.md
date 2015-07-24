<properties 
   pageTitle="在 Microsoft Azure 應用程式服務中建立交易夥伴協議" 
   description="建立交易夥伴協議" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/22/2015"
   ms.author="hariag"/>


# 建立交易夥伴協議   

交易夥伴是指涉及 B2B (企業對企業) 通訊的實體。當兩個夥伴建立關係時，這稱為*協議*。定義的協議會以這兩個夥伴想要達成的通訊為基礎，且是特定的通訊協定或傳輸。Azure App Service 支援的各種 B2B 通訊協定和傳輸包括：

- AS2 (Applicability Statement 2)
- EDIFACT (聯合國/行政、商業、傳輸的電子資料交換 (UN/EDIFACT))
- X12 (ASC X12)

### 支援 B2B 案例的 BizTalk API 應用程式
下列 API 應用程式會啟用這些使用 Azure 入口網站中豐富且直覺化體驗的功能：


## BizTalk 交易夥伴管理 (TPM)
- 建立和管理夥伴、設定檔與身分識別
- 儲存和管理 EDI 結構描述
- 儲存和管理憑證 (用於 AS2 通訊協定)
- 建立和管理 AS2 協議
- 建立和管理 EDIFACT 協議 (包含傳送端上的批次處理)
- 建立和管理 X12 協議 (包含傳送端上的批次處理)

![][1]


## AS2 連接器
- 執行如相關 TPM API 應用程式執行個體中所定義的 AS2 協議
- 瀏覽 AS2 處理/追蹤資訊以進行疑難排解


## BizTalk EDIFACT
- 執行如相關 TPM API 應用程式執行個體中所定義的 EDIFACT 協議
- 瀏覽 EDIFACT 處理/追蹤資訊以進行疑難排解
- 提供如相關 TPM API 應用程式執行個體中 EDIFACT 協議所定義的批次狀態管理 (「開始」和「停止」)


## BizTalk X12
- 執行如相關 TPM API 應用程式執行個體中所定義的 X12 協議 
- 瀏覽 X12 處理/追蹤資訊以進行疑難排解
- 提供如相關 TPM API 應用程式執行個體中 X12 協議所定義的批次狀態管理 (「開始」和「停止」)

如前所述，AS2、x12 和 EDIFACT API 應用程式都需要 TPM API 應用程式，才能如預期般運作。


## 開始使用
建立交易夥伴協議：

1. 建立 **BizTalk 交易夥伴管理**連接器的執行個體。這需要一個空白的 SQL Database 才能運作。開始之前，請確定具有並可供使用的空白資料庫。
2. 視協議的需要上傳結構描述和憑證。您可透過瀏覽已建立的 TPM 執行個體，並逐步執行 [結構描述] 和/或 [憑證] 部分，來完成上述動作
3. 瀏覽至已建立的 TPM 執行個體，並逐步執行 [**夥伴**] 部分
4. 視需要建立夥伴。並適當地編輯設定檔，以及新增必要的身分識別
5. 現在使用 [**協議**] 部分來建立協議。在協議建立期間，其中一個初始步驟是選擇通訊協定。根據選擇的通訊協定，才能進行其他的協議設定。

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.jpg
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.jpg
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.jpg
 

<!---HONumber=62-->