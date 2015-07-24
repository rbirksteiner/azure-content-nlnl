<properties 
	pageTitle="Azure Stream Analytics：替換輸入和輸出的登入認證 |Azure" 
	description="了解如何更新 Azure Stream Analytics 的輸入和輸出認證。" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#替換輸入/輸出認證

##摘要
Azure Stream Analytics 目前不允許在工作執行的時候，取代輸入/輸出中的認證。

雖然 Azure Stream Analytics 不支援從上次的輸出繼續工作，不過我們還是會教您如何儘量縮短程序停止和開始之間的延遲狀況。

##第 1 部分 - 準備一組新的認證：
此部分適用於下列的輸入/輸出：

* Blob 儲存體
* 事件中心
* SQL 資料庫
* 資料表儲存體

針對其他的輸入/輸出，請參考第 2 部分。

###部落格儲存體/資料表儲存體
1.  在 Azure 管理入口網站中，瀏覽至 [儲存體] 擴充：![graphic1][graphic1]
2.  找出工作使用的儲存體，然後進到裡面：![graphic2][graphic2]
3.  按一下 [管理存取金鑰] 命令：![graphic3][graphic3]
4.  在主要存取金鑰和次要存取金鑰之間， **挑選工作使用的金鑰**。
5.  按重新產生：![graphic4][graphic4]
6.  複製剛剛產生的金鑰：![graphic5][graphic5]
7.  繼續第 2 部分。

###事件中樞
1.  在 Azure 管理入口網站中，瀏覽至 [服務匯流排] 擴充：![graphic6][graphic6]
2.  找出工作使用的服務匯流排命名空間，然後進到裡面：![graphic7][graphic7]
3.  如果工作會在「服務匯流排命名空間」上使用共用存取原則，請跳到步驟 6  
4.  移至 [事件中樞] 索引標籤：![graphic8][graphic8]
5.  找出工作使用的事件中樞，然後進到裡面：![graphic9][graphic9]
6.  移至 [設定] 索引標籤：![graphic10][graphic10]
7.  在 [原則名稱] 下拉式清單中，找出工作使用的共用存取原則：![graphic11][graphic11]
8.  在主要金鑰和次要金鑰之間， **挑選工作未使用的金鑰**。  
9.  按重新產生：![graphic12][graphic12]
10. 複製剛剛產生的金鑰：![graphic13][graphic13]
11. 繼續第 2 部分。  

###SQL Database
[AZURE.NOTE]注意：您必須連接到 SQL 資料庫服務。為了示範整個過程，我們會借鑑 Azure 管理入口網站的管理經驗，不過您也可以選擇使用其他類似 SQL Server Management Studio 的用戶端工具。

1.  在 Azure 管理入口網站中，瀏覽至 [SQL 資料庫] 擴充：![graphic14][graphic14]
2.  找出工作使用的 SQL 資料庫，然後在同一行上**按一下伺服器**連結：![graphic15][graphic15]
3.  按一下 [管理] 命令：![graphic16][graphic16]
4.  輸入主要資料庫：![graphic17][graphic17]
5.  輸入使用者名稱和密碼，然後按一下 [登入]：![graphic18][graphic18]
6.  按一下 [新增查閱]：![graphic19][graphic19]
7.  輸入下列查詢之後，即可將 <login_name>換成您的使用者名稱以及將<enterStrongPasswordHere>換成您的新密碼：`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  按一下 [執行]：![graphic20][graphic20]
9.  回到步驟 2，這一次按一下資料庫：![graphic21][graphic21]
10. 按一下 [管理] 命令：![graphic22][graphic22]
11. 輸入使用者名稱和密碼，然後按一下 [登入]：![graphic23][graphic23]
12. 按一下 [新增查閱]：![graphic24][graphic24]
13. 輸入下列查詢，即可將 <user_name> 換成當您登入此資料庫的內容時想用的識別名稱 (例如，使用之前提供給 <login_name> 的值)，並將 <login_name> 換成新的使用者名稱：`CREATE USER <user_name> FROM LOGIN <login_name>`
14. 按一下 [執行]：![graphic25][graphic25]
15. 現在您應該為新使用者提供與原始使用者相同的角色和權限。
16. 繼續第 2 部分。

##第 2 部分：停止 Azure Stream Analytics 工作
1.  在 Azure 管理入口網站中，移至 [Azure Stream Analytics] 擴充：![graphic26][graphic26]
2.  找出您的工作，然後進到裡面： ![graphic27][graphic27]
3.  根據您是否想替換輸入或輸出時的認證，然後移至 [輸入] 或 [輸出] 索引標籤。![graphic28][graphic28]
4.  按一下 [停止] 命令並確認工作已停止：![graphic29][graphic29] 等候工作停止。
5.  找出您要替換認證的輸入或輸出，然後進到裡面：![graphic30][graphic30]
6.  繼續第 3 部分。

##第 3 部分：編輯 Stream Analytics 工作的認證

###Blob 儲存體/資料表儲存體
1.	尋找 [儲存體帳戶金鑰] 欄位，然後貼上剛剛產生的金鑰：![graphic31][graphic31]
2.	按一下 [儲存] 命令，然後確認儲存所做的變更：![graphic32][graphic32]
3.	當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。
4.	繼續第 4 部分。

###事件中樞
1.	尋找 [事件中樞原則金鑰] 欄位，然後貼上剛剛產生的金鑰：![graphic33][graphic33]
2.	按一下 [儲存] 命令，然後確認儲存所做的變更：![graphic34][graphic34]
3.	當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。
4.	繼續第 4 部分。

###Power BI
1.	按一下 [更新授權]：  
* ![graphic35][graphic35]
* 系統會要求您進行以下確認：  
* ![graphic36][graphic36]
2.	按一下 [儲存] 命令，然後確認儲存所做的變更：![graphic37][graphic37]
3.	當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。
4.	繼續第 4 部分。

###SQL 資料庫
1.	尋找 [使用者名稱] 和 [密碼] 欄位，然後貼上剛剛建立的一組認證：![graphic38][graphic38]
2.	按一下 [儲存] 命令，然後確認儲存所做的變更：![graphic39][graphic39]
3.	當您儲存所做的變更時，系統會自動測試連線，以保證萬無一失。  
4.	繼續第 4 部分。

##第 4 部分：從上次停止的時間開始您的工作
1.	離開輸入/輸出：![graphic40][graphic40]
2.	按一下 [開始] 命令：![graphic41][graphic41]
3.	挑選 [上次停止時間]，然後按一下 [確定]： ![graphic42][graphic42]
4.	繼續第 5 部分。  

##第 5 部分：移除舊的認證集
這部分適用於下列的輸入/輸出：* Blob 儲存體 * 事件中樞 * SQL 資料庫 * 資料表儲存體

###Blob 儲存體/資料表儲存體
為工作以前使用的存取金鑰，重複第 1 部分，以更新現在未使用的存取金鑰。

###事件中樞
為工作以前使用的金鑰，重複第 1 部分，以更新現在未使用的金鑰。

###SQL 資料庫
1.	從第 1 部分的步驟 7 回到查詢視窗，然後輸入以下查閱，即可將 <previous_login_name> 換成工作以前使用的使用者名稱：`DROP LOGIN <previous_login_name>`  
2.	按一下 [執行]：![graphic43][graphic43]  

系統應該會要求您進行以下確認：

	Command(s) completed successfully.

## 取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/zh-tw/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
- [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
- [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics 管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 

<!---HONumber=62-->