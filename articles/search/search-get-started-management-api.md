<properties 
	pageTitle="開始使用 Azure 搜尋服務管理 REST API" 
	description="開始使用 Azure 搜尋服務管理 REST API" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# 開始使用 Azure 搜尋管理 REST API

Azure Search REST 管理 API 是在入口網站中執行管理工作的程式設計替代方式。服務管理作業包括建立或刪除服務、調整服務及管理金鑰。本教學課程隨附的範例用戶端應用程式用以展示服務管理 API。其中也包括在本機開發環境中執行範例所需的設定步驟。

若要完成本教學課程，您需要：

- Visual Studio 2012 或 2013
- 下載範例用戶端應用程式

在完成本教學課程的過程中，將會佈建兩項服務：Azure 搜尋服務和 Azure Active Directory (AD)此外，您會建立 AD 應用程式，以建立起 Azure 中用戶端應用程式與資源管理員端點之間的信任 。

您需有 Azure 帳戶，才能完成本教學課程。


##下載範例應用程式

本教學課程是以 C# 撰寫的 Windows 主控台應用程式為基礎，您可以在 Visual Studio 2012 或 2013年中進行編輯和執行

您可以在 Codeplex 的 [Azure 搜尋服務管理 API 示範](https://azuresearchmgmtapi.codeplex.com/)上尋找此用戶端應用程式。


##設定應用程式

在執行範例應用程式之前，您必須先啟用驗證，才能接受從用戶端應用程式傳送至資源管理員端點的要求。驗證需求源自 [Azure 資源管理員](http://msdn.microsoft.com/library/azure/dn790568.aspx)，這是透過 API 要求的所有入口網站相關作業的基礎，包括搜尋服務管理的相關要求。Azure Search 的服務管理 API 只是 Azure 資源管理員的延伸模組，因此會繼承其相依性。

Azure 資源管理員需要 Azure Active Directory 服務做為其身分識別提供者。

為了取得存取權杖，讓要求到達資源管理員，用戶端應用程式包含了可呼叫 Active Directory 的程式碼片段。此程式碼區段以及使用程式碼片段的先決步驟出自這篇文章：[驗證 Azure 資源管理員要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)。

您可以依照上面連結中的指示，或使用本文件中的步驟 (如果您想要逐步完成本教學課程的話)。

本節中，您將執行下列工作：

1. 建立 AD 服務
1. 建立 AD 應用程式
1. 註冊您所下載之範例用戶端應用程式的相關詳細資料，進而設定 AD 應用程式
1. 載入範例用戶端應用程式以及它將用於取得其要求授權的值

> [AZURE.NOTE]這些連結提供有關使用 Azure Active Directory 驗證對資源管理員之用戶端要求的背景：[Azure 資源管理員](http://msdn.microsoft.com/library/azure/dn790568.aspx)、[驗證 Azure 資源管理員要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)和 [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)。

###建立 Active Directory 服務

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。

2. 向下捲動左側導覽窗格，然後按一下 [**Active Directory**]。

4. 按一下 [**新增**]，開啟 [**應用程式服務**] | [**Active Directory**]。在此步驟中，您會建立新的 Active Directory 服務。這項服務會裝載您從現在起要定義一些步驟的 AD 應用程式。建立新的服務有助於從您可能已在 Azure 中裝載的其他應用程式區隔本教學課程。

5. 按一下 [**目錄**] | [**自訂建立**]。

6. 輸入服務名稱、網域及地理位置。網域必須是唯一的。按一下核取記號以建立服務。

     ![][5]

###為此服務建立新的 AD 應用程式

1. 選取您剛才建立的 "SearchTutorial" Active Directory 服務。

2. 在頂端功能表上，按一下 [**應用程式**]。
 
3. 按一下 [**新增應用程式**]。AD 應用程式會儲存將以其作為身分識別提供者之用戶端應用程式的相關資訊。
 
4. 按一下 [**新增我的組織正在開發的應用程式**]。此選項會對未發佈至應用程式資源庫的應用程式提供註冊設定。由於用戶端應用程式不是應用程式資源庫的一部分，所以這是此教學課程的正確選擇。

     ![][6]
 
5. 輸入名稱，例如 "Azure-Search-Manager"。

6. 選擇 [**原生用戶端應用程式**] 作為應用程式類型。這適用於範例應用程式；它正好是 Windows 用戶端 (主控台) 應用程式，而不是 Web 應用程式。

     ![][7]
 
7. 在 [重新導向 URI] 中，輸入 "http://localhost/Azure-Search-Manager-App"。Azure Active Directory 會將使用者代理程式重新導向至此 URI，以回應 OAuth 2.0 授權要求。此值不必是實體端點，但必須是有效的 URI。

    基於本教學課程的目的，此值可以是任何值，但您所輸入的一切都會變成範例應用程式中管理連線的必要輸入。
 
7. 按一下核取記號以建立 Active Directory 應用程式。您應該會在左側導覽窗格中看到 "Azure-Search-Manager-App"。

###設定 AD 應用程式
 
9. 按一下您剛建立的 AD 應用程式，即 "Azure-Search-Manager-App"。您應該會看到該應用程式列在左側導覽窗格中。

10. 按一下頂端功能表中的 [**設定**]。
 
11. 向下捲動至 [權限] 並選取 [**Azure 管理 API**]。在此步驟中，您會指定用戶端應用程式需要存取的 API (在此例中為 Azure 資源管理員 API)，以及所需的存取層級。

12. 在 [委派權限] 中，按一下下拉式清單並選取 [**存取 Azure 服務管理 (預覽)**]。
 
     ![][8]
 
13. 儲存變更。

讓應用程式設定頁面保持開啟。在下一個步驟中，您將複製這個頁面的值並將其輸入範例應用程式中。

###使用註冊和訂用帳戶值載入範例應用程式

在本節中，您將在 Visual Studio 中編輯此解決方案，並取代從入口網站取得的有效值。您要新增的值會顯示在 Program.cs 的頂端附近：

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

如果您尚未[從 Codeplex 下載範例應用程式](https://azuresearchmgmtapi.codeplex.com/)，您在此步驟中會需要它。

1. 在 Visual Studio 中開啟 **ManagementAPI.sln**。

2. 開啟 Program.cs。

3. 提供 `ClientId`。從上一個步驟保持開啟的 AD 應用程式設定頁面，在入口網站中從 AD 應用程式設定頁面複製用戶端識別碼，並將它貼到 Program.cs 中。

4. 提供 `RedirectUrl`。複製相同入口網站頁面中的 [重新導向 URI]，並將它貼到 Program.cs 中。

	![][9]

5. 提供 `TenantID.`。
	- 返回 Active Directory |SearchTutorial (服務)。 
	- 按一下頂端列中的 [**應用程式**]。 
	- 按一下頁面底部的 [**檢視端點**]。 
	- 複製清單底部的 OAUTH 2.0 授權端點。 
	- 將此端點貼到 TenantID 中，並修剪租用戶識別碼以外所有 URI 參數的值。

    假設 "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0"，請刪除 "55e324c7-1656-4afe-8dc3-43efcd4ffa50" 以外的項目。

	![][10]

6. 提供 `SubscriptionID`。
	- 移至主要入口網站頁面。
	- 按一下左側導覽窗格底部的 [**設定**]。
	- 從 [訂用帳戶] 索引標籤，複製訂用帳戶 ID 並將它貼到 Program.cs 中。

7. 儲存，然後建置解決方案。


##探索應用程式

在第一個方法呼叫加入中斷點，以便逐步執行程式。按 **F5** 執行應用程式，然後按 **F11** 逐步執行程式碼。

範例應用程式會為現有的 Azure 訂用帳戶建立免費的 Azure Search 服務。如果您的訂用帳戶已經有免費服務，則範例應用程式將會失敗。每個訂用帳戶只能有一項免費 Search 服務。

1. 在 [方案總管] 中開啟 Program.cs ，然後移至 Main(string void) 函數。 
 
3. 請注意，**ExecuteArmRequest** 用來執行對 Azure 資源管理員端點 (指定之 `subscriptionID` 的 `https://management.azure.com/subscriptions`) 的要求。此方法在整個程式中使用，利用 Azure 資源管理員 API 或 Search 管理 API 來執行作業。

3. 對 Azure 資源管理員的要求必須經過驗證和授權。使用出自[驗證 Azure 資源管理員要求](http://msdn.microsoft.com/library/azure/dn790557.aspx)的 **GetAuthorizationHeader** 方法 (由 **ExecuteArmRequest** 方法呼叫)，即可達到此目的。請注意，**GetAuthorizationHeader** 會呼叫 `https://management.core.windows.net` 以取得存取權杖。

4. 系統會提示您以適用於您訂用帳戶的使用者名稱和密碼進行登入。

5. 接著，會向 Azure 資源管理員提供者註冊新的 Azure Search 服務。再次使用 **ExecuteArmRequest** 方法，透過 `providers/Microsoft.Search/register` 在 Azure 上為您的訂用帳戶建立搜尋服務。

6. 程式的其餘部分會使用 [Azure 搜尋服務管理 REST API](http://msdn.microsoft.com/library/dn832684.aspx)。請注意，此 API 的 `api-version` 與 Azure 資源管理員 api-version 有所不同。例如，`/listAdminKeys?api-version=2014-07-31-Preview` 顯示 Azure 搜尋服務管理 REST API 的 `api-version`。

	下一系列的作業會擷取您剛建立的服務定義，管理 api-key、重新產生及擷取金鑰、變更複本和分割，以及最後會刪除此服務。

	變更服務複本或資料分割計數時，如果您使用免費版本，則預計這個動作會失敗。只有標準版本可使用額外的資料分割和複本。

	刪除此服務是最後一個作業。

##後續步驟

完成本教學課程之後，您可能想要深入了解 Active Directory 服務的服務管理或驗證：

- 深入了解整合用戶端應用程式與 Active Directory。請參閱[整合 Azure Active Directory 中的應用程式](http://msdn.microsoft.com/library/azure/dn151122.aspx)。
- 深入了解 Azure 中的其他服務管理作業。請參閱[管理您的服務](http://msdn.microsoft.com/library/azure/dn578292.aspx)。

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 

<!---HONumber=July15_HO2-->