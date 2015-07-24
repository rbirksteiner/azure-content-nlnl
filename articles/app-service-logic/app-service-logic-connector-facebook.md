<properties 
   pageTitle="Facebook 連接器 API 應用程式" 
   description="如何使用 Facebook 連接器" 
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
   ms.author="adgoda"/>


# 在邏輯應用程式中使用 Facebook 連接器 #

邏輯應用程式可以根據各種資料來源和提供項目連接器觸發，以在流程中取得和處理資料。

Facebook 連接器可讓您從 Facebook 帳戶擷取「使用者時間軸上的新貼文」、「網頁上的新貼文」、「發佈貼文」、「發佈相片」等。

- Facebook 連接器觸發程序將會擷取「使用者時間軸上的新貼文」或「網頁上的新貼文」。擷取到新推文時，它會觸發流程的新執行個體，並將要求中所收到的資料傳遞到處理的流程中。 
- Facebook 連接器動作可讓您「發佈貼文」、「發佈相片 」等等。這些動作會取得回應，並可供流程中的動作取用。

## 建立邏輯應用程式的 Facebook 連接器 ##
若要使用 Facebook 連接器，您必須先建立 Facebook 連接器 API 應用程式的執行個體。如下所示，完成此作業：

1.	使用 Azure 入口網站左下方的 [+新增] 選項開啟 Azure Marketplace。
2.	瀏覽至 [Web 與行動] > [API 應用程式]，並搜尋 [Facebook 連接器]。
3.	設定 Facebook 連接器，如下所示：
 
	![][1]
4.	按一下 [確定] 以建立。
5.	一旦建立 API 應用程式執行個體，您即可在相同的資源群組中建立邏輯應用程式以使用 Facebook 連接器。 
	- 也可以從邏輯應用程式建立 Facebook 連接器 API 應用程式執行個體。 
	- 開啟邏輯應用程式編輯器，然後按一下右邊組件庫中可用的 Facebook 連接器
	- 這會在邏輯應用程式建立所在的相同資源群組中建立 Facebook 連接器 API 應用程式執行個體。


## 在邏輯應用程式中使用 Facebook 連接器 ##
建立 API 應用程式之後，您現在可以使用 Facebook 連接器做為邏輯應用程式的觸發程序/動作 。若要這麼做，您需要：

1.	建立新的邏輯應用程式，並選擇具有 Facebook 連接器的相同資源群組。
 
	![][2]
2.	開啟 [觸發程序和動作] 以開啟邏輯應用程式設計工具，並設定您的流程。 
 
	![][3]
3.	Facebook 連接器就會出現在右邊組件庫的 [最近使用] 區段中。選取該項目。
 
	![][4]
4.	您可以從右邊組件庫按一下 [最近使用] 之下的 [Facebook 連接器]，將 Facebook 連接器 API 應用程式放入編輯器中。按一下 [授權] 按鈕。提供 Facebook 認證。
  
	![][5]
5.	允許 [Azure AppService 邏輯應用程式] 

	![][6] ![][7] ![][8]     
6.	選取觸發程序。
 
	![][9]
7.	您現在可以在其他動作中使用從 Facebook 觸發程序擷取的貼文。在下列流程中，每當使用者的 Facebook 時間表軸上張貼了新文章時，相同的文章就會成為使用者的 Twitter 時間軸中的貼文。
 
	![][10]
8.	您可以類似的方式，使用 Facebook 連接器動作建立流程。下列流程會擷取 Yammer 群組上張貼的新訊息，並且在使用者所管理的 Facebook 網頁上發佈相同的貼文。
 
	![][11]

**提示** - 若要取得 Facebook 頁面識別碼或 Yammer 群組識別碼，請在 URL 中尋找數值代碼。

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png
 

<!---HONumber=62-->