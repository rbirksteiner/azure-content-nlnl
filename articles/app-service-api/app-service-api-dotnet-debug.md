<properties 
	pageTitle="偵錯 Azure App Service 中的 API 應用程式" 
	description="了解如何在 API 應用程式於 Azure App Service 執行時使用 Visual Studio 進行偵錯。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradyg;tarcher"/>

# 偵錯 Azure App Service 中的 API 應用程式

## 概觀

在本教學課程中，您將學習如何對 ASP.NET Web API 程式碼進行偵錯，而此程式碼已設定為在 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 中的 [API 應用程式](app-service-api-apps-why-best-platform.md)內執行。您會在本機和遠端進行偵錯 (同時程式碼會在 Azure 中執行)。本教學課程會使用您已在此系列的先前教學課程中[建立](app-service-dotnet-create-api-app.md)和[部署](app-service-dotnet-deploy-api-app.md)的 API 應用程式。

## 遠端偵錯 API 應用程式 

下列步驟可讓您使用 Swagger UI 做為測試用戶端，在 API 應用程式於雲端執行時對其進行偵錯。

1. 在 Visual Studio 的 [方案總管] 中，於您[在上一個教學課程中部署](app-service-dotnet-deploy-api-app.md)的專案上按一下滑鼠右鍵，然後選取 [發行]。

	![發行專案](./media/app-service-api-dotnet-debug/rd-publish.png)

2. 在 [發行 Web] 對話方塊中，選取 [設定] 索引標籤，並確認已選取 [偵錯] 組建組態。完成時，請按一下 [發行]，將 Azure 訂用帳戶的所有變更推送出去。

	![發行專案](./media/app-service-api-dotnet-debug/rd-debug-publish.png)

3. 瀏覽器視窗應會隨即開啟，並顯示訊息，確認已成功建立您的 API 應用程式。

4. 在瀏覽器位址列中，將 /swagger 加到 URL 的結尾，然後按 Enter 鍵。這樣會顯示 Swagger UI 用戶端。

	![Swagger UI](./media/app-service-api-dotnet-debug/rd-swagger-ui.png)

5. 返回 Visual Studio，選取 [檢視] 功能表中的 [伺服器總管]。

6. 在 [伺服器總管] 中，依序展開 [Azure] > [應用程式服務] 節點。

7. 尋找您在部署 API 應用程式時所建立的資源群組。

8. 在資源群組下，以滑鼠右鍵按一下 API 應用程式的節點，然後選取 [**附加偵錯工具**]。

	![附加偵錯工具](./media/app-service-api-dotnet-debug/rd-attach-debugger.png)

	遠端偵錯工具會嘗試連線。在某些情況下，您可能需要重試按一下 [**附加偵錯工具**] 才能建立連線，因此如果失敗，請再試一次。

	![附加偵錯工具](./media/app-service-api-dotnet-debug/rd-attaching.png)

9. 建立連接之後，請開啟 API 應用程式專案中的 **ContactsController.cs** 檔案，然後在 `Get` 和 `Post` 方法新增中斷點。它們一開始可能不會顯示為作用中，但如果已附加遠端偵錯工具，就可以準備開始偵錯。

	![將中斷點套用至控制器](./media/app-service-api-dotnet-debug/rd-breakpoints.png)

10. 返回瀏覽器工作階段，按一下 **Get** 動詞以顯示 *Contact* 物件的結構描述，然後按一下 [試試看]。如果您在控制器的 **Get** 方法中設定中斷點，Visual Studio 會停止程式執行，且您可以對控制器的邏輯進行偵錯。

	![立即試用](./media/app-service-api-dotnet-debug/rd-try-it-out.png)

## 在本機上對 API 應用程式進行偵錯 

您有時可能想要在本機上對 API 應用程式進行偵錯；例如，想要避免來回行程可能會在測試/偵錯期間變慢。下列步驟說明如何使用 Swagger UI 做為測試用戶端，在本機上對 API 應用程式進行偵錯。

1. 在 Visual Studio 中，開啟 API 應用程式專案的 *web.config* 檔案。 
 
2. 在瀏覽器中導覽至 [Azure 預覽入口網站](http://portal.azure.com)。

3. 按一下側邊列上的 [**瀏覽**] 按鈕，然後選取 [**API 應用程式**]。

	![瀏覽 Azure 入口網站上的選項](./media/app-service-api-dotnet-debug/ld-browse.png)

4. 在訂用帳戶的 API 應用程式清單中，選取您建立的 API 應用程式。

	![API 應用程式清單](./media/app-service-api-dotnet-debug/ld-api-app-list.png)

5. 在 API 應用程式的刀鋒視窗中，按一下 [API 應用程式主機]。

	![API 應用程式主機](./media/app-service-api-dotnet-debug/ld-api-app-blade-api-app-host.png)

6. 在 API 應用程式主機的刀鋒視窗中，按一下 [所有設定]。

	![API 應用程式主機的所有設定](./media/app-service-api-dotnet-debug/ld-api-app-host-all-settings.png)

7. 按一下 [設定] 刀鋒視窗中的 [應用程式設定]。

	![API 應用程式主機的應用程式設定](./media/app-service-api-dotnet-debug/ld-application-settings.png)

8. 在 [Web 應用程式設定] 刀鋒視窗中，向下捲動至 [應用程式設定] 區段。

	![API 應用程式主機針對本機偵錯的應用程式設定](./media/app-service-api-dotnet-debug/ld-app-settings-for-local-debugging.png)

9. 在 [應用程式設定] 中，找出下列每個值，並將這些值加入 *web.config* 檔案的 **appSettings** 區段。
	- **EMA_MicroserviceId**
	- **EMA_Secret**
	- **EMA_RuntimeUrl**

	完成之後，*web.config* 的 **appSettings** 區段應該會類似以下螢幕擷取畫面。

	![API 應用程式主機針對本機偵錯的應用程式設定](./media/app-service-api-dotnet-debug/ld-debug-settings.png)

	**注意：**您在本節中新增至 *web.config* 檔案的 *EMA_* 值包含機密的授權資訊。因此，將此檔案送到公開的原始檔控制媒介時 (例如 *github*)，請務必謹慎小心，因為其他人會看見這些機密資訊。如需詳細資訊，請參閱[將密碼和其他機密資料部署到 ASP.NET 和 Azure App Service 的最佳作法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) (英文)。

10. 在 API 應用程式的控制器程式碼中，放置一個或多個中斷點 (在 `Get` 和 `Post` 方法中)。

	![設定中斷點](./media/app-service-api-dotnet-debug/ld-breakpoints.png)

11. 按一下 &lt;F5> 啟動 Visual Studio 偵錯工作階段。當瀏覽器載入頁面時，您應該會看到一則錯誤訊息。在瀏覽器位址列中，將 */swagger* 加到 URL 的結尾，然後按 Enter 鍵。

12. 載入 Swagger UI 後，請按一下瀏覽器視窗中的 **Get** 動詞，以顯示 Contact 物件的結構描述，然後按一下 [試試看]。Visual Studio 現在會在您稍早設定的中斷點上停止程式執行，且您可以對控制器的邏輯進行偵錯。

	![立即試用](./media/app-service-api-dotnet-debug/ld-try-it-out.png)

## 後續步驟

API 應用程式的遠端偵錯可讓您輕鬆地查看您的程式碼在 Azure App Service 中執行的狀況。在 Visual Studio IDE 中，可以立即取得 Azure API 應用程式的豐富診斷和偵錯資料。

App Service API 應用程式是具有額外功能 (用於裝載 Web 服務) 的 App Service Web 應用程式，因此您可對 API 應用程式使用您對 Web 應用程式使用的相同偵錯和疑難排解工具。如需詳細資訊，請參閱[使用 Visual Studio 疑難排解 Azure App Service 中的 Web 應用程式](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)。

任何人皆可公開呼叫您在此系列中所建立的 API 應用程式。如需如何保護 API 應用程式，以便只有已驗證的使用者可以呼叫它的相關資訊，請參閱[保護 API 應用程式：新增 Azure Active Directory 或社交提供者驗證](app-service-api-dotnet-add-authentication.md)。
 

<!---HONumber=62-->