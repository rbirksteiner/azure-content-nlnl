<properties 
	pageTitle="在 Visual Studio 2013 中匯入您的發佈設定檔案 | 行動服務" 
	description="了解如何在 Visual Studio 2013 中匯入 Azure Mobile Services 應用程式的訂閱發布設定檔。" 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# 在 Visual Studio 2013 中匯入您的訂閱發佈設定檔案

您必須先從 Azure 訂閱將發佈設定檔案匯入 Visual Studio 中，才能建立行動服務。這可讓 Visual Studio 代表您連線到 Azure。

>[AZURE.NOTE]從 Visual Studio 2013 Update 2 開始，您不再需要使用發佈設定檔案。Visual Studio 可使用您提供的認證來直接連線至 Azure。

1. 在 Visual Studio 2013 中，開啟 [方案總管]、在專案上按一下滑鼠右鍵，然後依序按一下 **[新增]**、**[已連接服務]**。 

	![新增已連接服務](./media/mobile-services-windows-how-to-import-publishsettings/mobile-add-connected-service.png)

2. 在 [服務管理員] 對話方塊中，按一下 [建立服務]，然後在 [建立行動服務] 對話方塊的 [訂閱] 中，選取 [&lt;匯入...&gt;]。

	![從 VS 2013 建立新的行動服務](./media/mobile-services-windows-how-to-import-publishsettings/mobile-create-service-from-vs2013.png)

3. 在 [匯入 Azure 訂閱] 中按一下 [下載訂閱檔案]，登入您的 Azure 帳戶 (如有必要)，在瀏覽器要求儲存檔案時，按一下 [儲存]。

	![在 VS 中下載訂閱檔案](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]登入視窗會顯示在瀏覽器中，它有可能會在 Visual Studio 視窗下面。請記得記下儲存 .publishsettings 檔案的下載位置。如果您的專案已連線到 Azure 訂閱，您可以略過此步驟。

4. 按一下 [瀏覽] 並瀏覽至儲存 .publishsettings 檔案的位置，選取此檔案，然後依序按一下 [開啟]、[匯入]。

	![在 VS 中匯入訂閱](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription-2.png)

	Visual Studio 會匯入連線到 Azure 訂閱所需的資料。當訂閱已經有一或多個現有的行動服務時，便會顯示這些服務名稱。

	> [AZURE.NOTE]在匯入發佈設定之後，請考慮刪除下載的 .publishsettings 檔案，因為它包含了他人可用來存取您帳戶的資訊。如果您打算保留此檔案以供其他連線應用程式專案使用，請確保此檔案安全無虞。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->