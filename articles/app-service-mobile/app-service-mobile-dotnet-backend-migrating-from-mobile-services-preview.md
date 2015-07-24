<properties 
	pageTitle="從行動服務移轉到應用程式服務行動應用程式" 
	description="了解如何輕鬆地將您的行動服務應用程式移轉至應用程式服務行動應用程式" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# 將您現有的 Azure 行動服務移轉至 Azure 應用程式服務行動應用程式

本主題說明如何將現有的應用程式從 Azure 行動服務移轉至新的應用程式服務行動應用程式。所有現有的行動服務應用程式都可以輕鬆地移轉至新的應用程式服務行動應用程式。在移轉期間，現有的行動服務應用程式可以繼續運作。經過一段時間，移轉程序將會變得更加簡單，但是想要立即移轉的人，可以使用下列步驟。

>[AZURE.NOTE]移轉目前僅支援使用行動服務 .NET 後端的客戶。使用 Node.JS 後端的應用程式目前仍須繼續使用行動服務。

##<a name="understand"></a>了解應用程式服務行動應用程式

應用程式服務行動應用程式是使用 Microsoft Azure 建置行動應用程式的新方式。您可以在[什麼是行動應用程式？]主題中進一步了解行動應用程式。

在移轉至行動應用程式時，所有現有的應用程式功能 (和程式碼) 都可保留。此外，新功能可用於應用程式。在行動應用程式模型中，您的程式碼實際上會執行於 Web 應用程式 (新版本的 Azure 網站)。您可以完全掌控 Web 應用程式及其運作方式。此外，過去無法讓行動服務客戶使用的 Web 應用程式功能 (例如流量管理員和 Development Slots)，現在都可供使用。

新的模型也能解決使用行動服務的主要難題之一。現在，您可以部署任何版本的任何 NuGet 封裝，而不需擔心相依性衝突。有關移轉有何優點的詳細資訊，可在[我已經使用網站和行動服務 – 應用程式服務對我有何幫助？]主題中找到。

當您建立應用程式服務行動應用程式時，您會取得：

- 行動應用程式資源，其中包含新功能 
- 行動應用程式程式碼網站，會使用行動應用程式伺服器 SDK 執行您的 Web API 專案
- 應用程式服務閘道，可處理登入，並幫助您將應用程式服務 API 應用程式新增至您的應用程式

##<a name="overview"></a>基本移轉概觀
最簡單的移轉方法，是建立應用程式服務行動應用程式的新執行個體。在許多情況下，只需切換到新的伺服器 SDK 並將程式碼重新發佈至新的行動應用程式，即可完成移轉。但在某些情況下則需要一些額外的設定，例如進階驗證案例和使用排程工作。下列各節將逐一加以討論。

>[AZURE.NOTE]建議您先閱讀並徹底了解本主題的其餘部分，再開始移轉。請記下您使用的下列任何功能。

您可以依照自己的步調移動和測試程式碼。行動應用程式後端準備好時，您可以發行新版的用戶端應用程式。此時，您會有兩份並行執行的應用程式。您必須確定您所做的任何錯誤修正皆套用至兩者。最後，在您的使用者更新為最新版本後，您可以刪除原始的行動服務。

此移轉的完整步驟如下所示：

1. 建立及設定新的行動應用程式
2. 處理任何驗證考量
3. 發行新版的用戶端應用程式
4. 刪除原始的行動服務執行個體


##<a name="mobile-app-version"></a>設定您應用程式的行動應用程式版本
移轉的第一個步驟，是建立將主控新版應用程式的應用程式服務。您可以在[預覽 Azure 管理入口網站]中建立新的行動應用程式。您可以查閱[建立行動應用程式]主題，以取得進一步的詳細資訊。

您可能會想要使用與行動服務中相同的資料庫和通知中心。您可以從 [Azure 管理入口網站]之 [行動服務] 區段的 [**設定**] 索引標籤來複製這些值。在 [**連接字串**] 下，複製 `MS_NotificationHubConnectionString` 和 `MS_TableConnectionString`。導覽至您的行動應用程式程式碼網站，然後選取 [**設定**]、[ **應用程式設定**]，並新增這些連接字串，而覆寫任何現有的值。此外，您也應將這些值新增至行動應用程式資源。若要這麼做，請導覽至行動應用程式分頁、選取 [**設定**]，然後選取 [**屬性**]。按一下標示為 **API 應用程式主機**的連結，以檢視主控行動應用程式資源的網站。移至 [**設定**]、[**應用程式設定**]，並將程式碼網站中的連接字串貼入。請勿變更其他值，因為這可能會中斷行動應用程式功能。此時請注意，即使在此設定步驟之後，行動應用程式分頁仍會繼續顯示現有的連線。行動應用程式使用性更新之後，可能需要執行其他動作。

行動應用程式有新的[行動應用程式伺服器 SDK]，提供許多與行動服務執行階段相同的功能。您應從現有的專案中移除行動服務 NuGet，並改為包含伺服器 SDK。您可能需要修改某些 using 陳述式，這時 Visual Studio 將有所幫助。您可能會發現伺服器 SDK 中遺漏的主要項目是 PushRegistrationHandler 類別。註冊在行動應用程式中處理方式稍有不同，依預設會啟用不具標記的註冊。管理標記可使用自訂 API 來完成。如需詳細資訊，請參閱[將推播通知新增至行動應用程式]主題。

排程工作並未內建於行動應用程式中，因此您在 .NET 後端中的任何現有工作都必須個別移轉。其中一個選項是在行動應用程式程式碼網站上建立排程 [Web 工作]。您也可以設定用來保存工作程式碼的控制器，並設定依預期的排程在端點上執行的 [Azure 排程器]。


##<a name="authentication"></a>驗證考量
行動應用程式與行動服務之間最大的差異之一在於，在行動應用程式中，登入是由應用程式服務閘道所處理，而非程式碼網站。對大部分的應用程式而言，這都不需要額外的動作，但是有幾個進階案例應特別留意。

大部分的應用程式只要使用目標身分識別提供者的新註冊，即可順利運作，您可以依照[將驗證新增至行動應用程式]教學課程的指示，了解如何將身分識別新增至應用程式服務應用程式。

如果您的應用程式依存於使用者識別碼 (例如將它們儲存在資料庫中)，請務必注意，行動服務和應用程式服務行動應用程式的使用者識別碼是不同的。不過，您可以使用下列項目 (以 Facebook 做為範例)，在您的應用程式服務行動應用程式中取得行動服務使用者識別碼：

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

此外，如果您依存於使用者識別碼，請務必盡可能使用相同的身分識別提供者註冊。使用者識別碼的範圍通常限定在已使用的應用程式註冊，因此引入新的註冊，可能會讓使用者在比對其資料時發生問題。如果您的應用程式因故需要使用相同的身分識別提供者註冊，您可以使用下列步驟：

1. 為您的應用程式所使用的每個提供者，複製用戶端識別碼和用戶端密碼連接資訊。
2. 為每個提供者新增閘道的 /signin-* 端點，做為額外的重新導向 URI。 

>[AZURE.NOTE]某些提供者 (如 Twitter、Microsoft 帳戶) 不允許您在不同網域上指定多個重新導向 URI。如果您的應用程式使用其中一個提供者，並且依存於使用者識別碼，建議您此時不要嘗試移轉。

##<a name="updating clients"></a>更新用戶端
在您擁有可運作的行動應用程式後端之後，您可以更新用戶端應用程式，以使用新的行動應用程式。行動應用程式也會包含新版的行動服務用戶端 SDK，可以讓開發人員利用新的應用程式服務功能。在您擁有行動應用程式版本的後端之後，您可以發行運用新 SDK 版本的新版用戶端應用程式。

用戶端程式碼所需的主要變更將是建構函式。除了行動應用程式程式碼網站的 URL 和應用程式金鑰以外，您還必須提供主控驗證設定的應用程式服務閘道的 URL：

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
    );

這可讓用戶端將要求路由傳送至行動應用程式的元件。您可以使用適當的[建立行動應用程式]主題，找到更多目標平台的特定詳細資料。

在相同的更新中，您將必須調整您所做的任何推播通知註冊呼叫。有新的 API 可強化註冊程序 (以 Windows 做為範例)：

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

如需目標平台的特定詳細資料，請參閱[將推播通知新增至行動應用程式]和[傳送跨平台推播通知]主題。

在您的客戶有機會接收這些更新後，您即可刪除應用程式的行動服務版本。此時，您完全移轉至應用程式服務行動應用程式。

<!-- URLs. -->

[預覽 Azure 管理入口網站]: https://portal.azure.com/
[Azure 管理入口網站]: https://manage.windowsazure.com/
[什麼是行動應用程式？]: app-service-mobile-value-prop-preview.md
[我已經使用網站和行動服務 – 應用程式服務對我有何幫助？]: /zh-tw/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[行動應用程式伺服器 SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[建立行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[將推播通知新增至行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[將驗證新增至行動應用程式]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure 排程器]: /zh-tw/documentation/services/scheduler/
[Web 工作]: ../app-service-web/websites-webjobs-resources.md
[傳送跨平台推播通知]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md

<!---HONumber=62-->