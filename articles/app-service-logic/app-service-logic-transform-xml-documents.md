<properties 
	pageTitle="轉換 XML 文件" 
	description="了解如何將 XML 文件轉換為不同的結構描述。" 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2015"
	ms.author="anuragdalmia"/>

#轉換 XML 文件


## BizTalk 轉換 API 應用程式概觀
簡言之，「BizTalk 轉換 API 應用程式」會將資料轉換為不同的格式。例如，您可能會從訂單中取用出貨和帳單地址，並將其插入發票文件中。或者，您的內送訊息中可能包含 *YearMonthDay* 格式的目前日期。您想要將日期重新格式化為 *MonthDayYear* 格式。

您可以使用 Microsoft Azure 應用程式服務中的「轉換 API 應用程式」來執行此動作。轉換或對應由來源 XML 結構描述 (輸入) 和目標 XML 結構描述 (輸出) 所組成。您可以利用不同的內建功能來操控或控制資料，包括字串操作、條件式協議、算術運算式、日期時間格式器，甚至迴圈建構。

對應可使用 [Microsoft Azure BizTalk 服務 SDK](http://www.microsoft.com/download/details.aspx?id=39087) 建立於 Visual Studio 中。當您完成對應的建立和測試對應後，您將對應 (.trfm) 上傳至 BizTalk 轉換 API 應用程式中。

其他功能包括：

- 在對應中建立轉換並不難，例如，只要在不同文件之間複製名稱和位址，即可完成。或者，您可以使用內建的對應作業，建立更複雜的轉換。
- 目前有多個對應作業或函數可供使用，包括字串、日期時間函數等等。
- 可在結構描述間執行直接的資料複製。在 BizTalk 對應工具中，只要繪製一條線連接來源結構描述中的元素與它的目的地結構描述中的對等項目，即可完成此動作。
- 在建立對應時，您可以檢視圖形化的對應，包括查看您所建立的所有關聯性和連結。
- 使用 [**測試對應**] 功能，以新增範例 XML 訊息。只要按一下滑鼠，您即可測試已建立的對應，並檢視產生的輸出。
- 上傳現有的 Azure BizTalk 服務對應 (.trfm)，並利用「轉換 API 應用程式」的所有功能。
- 包括對 XML 格式的支援。


## 建立新的 BizTalk 轉換 API 應用程式

1.	登入 Azure 入口網站，然後移至儀表板 (首頁)。

2.	選取 [**新增**] > [**Web + 行動**] > [**Azure Marketplace**] > [**API 應用程式**]：

	   ![][1]
 
	或者，您可以選取開始面板上的 [**Marketplace**]，然後從可用的清單中選取 [**API 應用程式**]：

	   ![][2]
 
3.	輸入 [**轉換**]，然後選取 [**BizTalk 轉換服務**]，以瀏覽 BizTalk 轉換：

	   ![][4]
 
4.	在 [**BizTalk 轉換服務**] 分頁中，選取 [**建立**]：

       ![][5]
 
5.	在 [**新增 API 應用程式**] 分頁中，輸入下列資訊，然後選取 [**建立**]：

	- 名稱 - 提供轉換 API 應用程式的名稱 
	- 應用程式服務計劃 - 選取或建立新的應用程式服務計劃 
	- 定價層 - 選擇您要讓此應用程式屬於哪個定價層 
	- 資源群組 – 選取或建立應用程式應所屬的資源群組 
	- 位置 – 選擇您要部署應用程式的地理位置
	
	   ![][6]

6.	選取 [**建立**]。BizTalk 轉換 API 應用程式在幾分鐘內就會建立完成。


## 從連接器 API 應用程式下載結構描述
您可以從 [API 應用程式] 摘要頁面下載連接器的 XML 結構描述，例如 SQL、SAP 和 SharePoint。例如，如果您要下載特定 SAP 連接器 API 應用程式的 XML 結構描述，請瀏覽 API 應用程式，然後開啟摘要頁面。選取 [**下載結構描述**]，包含所有對應至 SAP 動作之結構描述的 zip 檔案即會下載至電腦。您可以在 Visual Studio 中使用結構描述來撰寫對應 (.trfm)。

   ![][14]


## 建立及新增對應
轉換或對應可使用 [Microsoft Azure BizTalk 服務 SDK](http://www.microsoft.com/download/details.aspx?id=39087) 建立於 Visual Studio 中，該 SDK 可免費下載取得。

如需建立對應的說明，請參閱[在 Visual Studio 中建立對應](http://aka.ms/createamapinvs)。在對應建立並可用於生產環境後，您可以將對應 (.trfm 檔案) 新增至您在 Azure 管理入口網站中建立的 BizTalk 轉換 API 應用程式。

如果對應在上傳之後有所變更或修改，您可以上傳更新的對應，它會在轉換 API 應用程式中取代現有的對應。

1.	在 Azure 管理入口網站上選取 [**瀏覽**] (畫面的左側)，然後選取 [**API 應用程式**]。如果 [**API 應用程式**] 未顯示，請選取 [**全部**]，然後從可用的清單中選取 [**API 應用程式**]：

	![][7]

2.	在 Azure 訂閱中建立的所有 [**API 應用程式**] 的清單隨即便會顯示：

	![][8]

3.	選取您在上一節中建立的 BizTalk 轉換 API 應用程式。

4.	API 應用程式的設定分頁隨即開啟。您可以在 [元件] 區段中看到 [**對應**]：

	![][9]

5.	選取 [**對應**]，以開啟含有對應清單的新分頁。

6.	選取頂端的 [**新增對應**] 圖示，以開啟 [**新增對應**] 分頁：

	![][10]

7.	選取 [檔案] 圖示，然後從本機電腦瀏覽對應檔案 (.trfm)。

8.  選取 [**確定**]，新的對應隨即建立。它會顯示在對應清單中。


## 在邏輯應用程式中使用 BizTalk 轉換 API 應用程式
對應已撰寫並經過測試後，此時已可供取用。使用者可以建立新的邏輯應用程式 ([**新增**] > [**邏輯應用程式**])。

1. 在邏輯應用程式內，「BizTalk 轉換」會出現在右側的組件庫中。從組件庫中選取 [**BizTalk 轉換服務**]。轉換會新增至流程：

	![][11]

2. 選取 [**轉換**] 動作。輸入參數隨即顯示：

	![][12]

3. 輸入下列參數，以完成 [**轉換**] 動作設定：
		 
	- 輸入 XML
		- 輸入與 API 應用程式中某對應的來源結構描述相符的有效 XML 內容。這可以是邏輯應用程式中上一個動作的輸出，例如「呼叫 RFC – SAP」或「插入資料表中 – SQL」。
		
	- 對應名稱 (選擇性)
		- 輸入已在您的轉換 API 應用程式中上傳的有效對應名稱。如果未輸入對應，則會根據輸入 XML 符合的來源結構描述自動選取對應。

	![][13]

4. 「輸出 XML」動作的輸出可用於邏輯應用程式中的後續動作。

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png



 

<!---HONumber=62-->