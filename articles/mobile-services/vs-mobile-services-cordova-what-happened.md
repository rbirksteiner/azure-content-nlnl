<properties 
	pageTitle="" 
	description="說明您的 Azure 行動服務專案在 Cordova 中的情形" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# 我的專案發生什麼情形？

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">我的專案發生什麼情形？</span>

#####加入參考

所有多重裝置混合應用程式隨附的 Azure 行動服務用戶端外掛程式已啟用。
  
#####行動服務的連接字串值

在`services\mobileServices\settings`下，已產生一個含有 **MobileServiceClient** 的新的 JavaScript (.js) 檔案，內含所選行動服務的應用程式 URL 和應用程式金鑰。此檔案包含行動服務用戶端物件的初始化，類似於下列程式碼。

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[深入了解行動服務](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO2-->