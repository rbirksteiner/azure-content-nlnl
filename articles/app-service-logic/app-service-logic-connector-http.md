<properties 
   pageTitle="Http 接聽程式和連接器" 
   description="在邏輯應用程式中使用 HTTP 接聽程式和 HTTP 動作" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


#在邏輯應用程式中使用 HTTP 接聽程式和 HTTP 動作#

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。不過，在某些情況下，您可能需要使用直接 HTTP 連線，即：

1.	若要開發支援 Web 或行動使用者互動前端的邏輯應用程式。
2.	若要取得並處理沒有現成連接器之 Web 服務的資料。
3.	若要執行已公開為 web 服務，但無法當做 API 應用程式使用的動作。


在這些情況下，組件庫提供兩個選項

1. HTTP 接聽程式：這個連接器可做為觸發程序，並接聽設定的端點上的 HTTP 要求。設定的端點上收到呼叫時，它會觸發流程的新執行個體，並將要求中所收到的資料傳遞到處理的流程中。它也可以設定為在啟動流程時自動回應內送要求，或可讓您根據流程執行建構回應並傳送回應給呼叫者。
2.	HTTP 動作：這可讓您將 Web 要求設定到網際網路上任何可用的端點、取得回應，並提供給流程中的動作取用。

##建立邏輯應用程式的 HTTP 接聽程式
若要使用 HTTP 接聽程式，您必須先建立 HTTP 接聽程式 API 應用程式的執行個體。如下所示，完成此作業：

1.	使用 Azure 入口網站右下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋「HTTP 接聽程式」。
3.	設定 HTTP 接聽程式，如下所示：

	![][1]

4.	進行封裝設定時，您會看到下列有關接聽程式是否應該自動回應或要求您傳送明確回應的選項。將此選項設為 False，才能傳送您自己的回應。

	![][2]

5.	按一下 [確定] 以建立。
6.	建立 API 應用程式執行個體之後，請開啟設定以設定安全性。HTTP 接聽程式目前支援基本驗證。開啟 HTTP 接聽程式時，您可以使用 [安全性] 選項進行設定。

	![][3] <br> <br> <b><u>已知問題</u></b><br> *安全性設定會顯示 [無] 作為預設值，但是它還未定義。您必須將設定變更為 [基本]，然後在儲存它之前變回 [無]，以確定正確地設定 HTTP 接聽程式。*

7. 最後，您需要將 API 應用程式的安全性設定設為 [公用 (匿名)]，以允許外部用戶端存取端點。可在 HTTP 接聽程式 API 應用程式的 [所有設定] > [應用程式設定]下取得此設定。

	![][10]

一旦完成，您立即可在相同資源群組中建立邏輯應用程式，來使用 HTTP 接聽程式。

##在邏輯應用程式中使用 HTTP 接聽程式
建立 API 應用程式之後，您現在可以使用 HTTP 接聽程式做為邏輯應用程式的觸發程序。若要這麼做，您需要：

4.	建立新的邏輯應用程式，並選擇具有 HTTP 接聽程式的相同資源群組。
5.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。HTTP 接聽程式就會出現在右側組件庫中的 [最近使用的] 區段。選取該項目。
6.	您現在可以設定 HTTP 方法以及需要接聽程式在其上觸發流程的相對 URL。<br>

	![][4] ![][5]

7.	若要取得完整的 URI，請按兩下 [HTTP 接聽程式] 以檢視其組態設定，並複製 API 應用程式的「主機」URL。


	![][6]
8.	您現在可以在流程的其他動作中使用 HTTP 要求中所接收的資料，如下所示：<br>![][7]![][8]
9.	最後，若要傳送回應，請新增另一個 HTTP 接聽程式，並選取 [傳送 HTTP 回應] 動作。將要求識別碼設定為從 HTTP 接聽程式取得的 RequestID，並填入您想要傳回的回應本文和 HTTP 狀態 ![][9]

##使用 HTTP 動作
邏輯應用程式原生支援 HTTP 動作，而且無需建立 API 應用程式即可使用。您可以在應用程式邏輯的任何位置插入 HTTP 動作，並選擇呼叫的 URI、標頭和主體。HTTP 動作支援多個用戶端安全性選項。若要使用這些選項，請閱讀[這裡](http://aka.ms/logicapphttpauth)的文章。

HTTP 動作的輸出會是標頭和主體，可進一步在流程的下游中使用，且與取用其他動作和連接器的輸出方式類似。

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png






 

<!---HONumber=62-->