<properties 
	pageTitle="從 Azure Marketplace 建立 Orchard CMS Web 應用程式" 
	description="指導如何在 Azure 上建立新 Web 應用程式的教學課程。另外也學習如何從 Azure 入口網站啟動和管理您的 Web 應用程式。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# 從 Azure Marketplace 建立 Orchard CMS Web 應用程式

## 概觀

Marketplace 提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。除了用來連線到 [Azure Preview 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)的瀏覽器以外，從 Marketplace 中建立的 Web 應用程式不需要安裝任何軟體。如需 Marketplace 中 Web 應用程式的詳細資訊，請參閱 [Azure Marketplace](/marketplace/)。

在本教學課程中，您將了解：

- 如何從 Marketplace 建立新的 Web 應用程式

- 如何從 Azure Portal 啟動和管理您的 Web 應用程式
 
您將建立一個使用預設範本的 Orchard CMS Web 應用程式。[Orchard](http://www.orchardproject.net/) 是免費、開放原始碼、.NET 架構的 CMS 應用程式，可讓您建立自訂且內容導向的 Web 應用程式和網站。Orchard CMS 包含一個擴充性架構，可讓您[下載其他模組和佈景主題](http://gallery.orchardproject.net/)來自訂您的 Web 應用程式。下圖顯示您將在 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中建立的 Orchard CMS Web 應用程式。

![Orchard blog][13]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 從 Marketplace 建立 Orchard Web 應用程式

1. 登入 [Azure Preview 入口網站](http://portal.azure.com)。

2. 按一下 [新增] > [Web + 行動] > [Azure Marketplace]。
	
	![建立新的][1]

3. 按一下 [Web 應用程式] > [Orchard CMS]。在下一個分頁中，按一下 [建立]。
	
	![從 Marketplace 建立][2]

4. 設定 Web 應用程式的 URL、App Service 計劃、資源群組，以及位置。完成之後，請按一下 [建立]。
	
	![設定應用程式][3]

	Web 應用程式建立之後，[通知] 按鈕會顯示綠色「成功」並顯示 Web 應用程式分頁。

## 啟動和管理 Orchard Web 應用程式

1. 在 Web 應用程式的分頁中，按一下 [瀏覽] 以開啟 Web 應用程式的歡迎頁面。

	![browse button][12]

2. 輸入 Orchard 所需的組態資訊，然後按一下 [完成設定]，以完成組態並開啟 Web 應用程式的首頁。

	![login to Orchard][7]

	您將會有類似以下螢幕擷取畫面的新 Orchard Web 應用程式。

	![您的 Orchard Web 應用程式][13]

3. 若要深入了解 Orchard 和設定您的新 Web 應用程式，請參閱 [Orchard 文件](http://docs.orchardproject.net/)中的詳細資料。

## 後續步驟

* [使用 Microsoft WebMatrix 來開發和部署 Web 應用程式](web-sites-dotnet-using-webmatrix.md) -- 了解如何在 WebMatrix 中編輯 App Service Web 應用程式。 
* [使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)-- 了解如何從 Visual Studio 在 Azure App Service 中建立新 Web 應用程式。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


 

<!---HONumber=62-->