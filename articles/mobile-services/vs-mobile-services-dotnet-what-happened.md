<properties 
	pageTitle="" 
	description="說明您在 Visual Studio 中的 Azure 行動服務 .NET 專案的情形 " 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# 我的專案發生什麼情形？

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">我的專案發生什麼情形？</span>

#####加入參考

Azure 行動服務 NuGet 封裝加入至專案。因此，下列 .NET 參考已加入至專案：

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####行動服務的連接字串值

在 App.xaml.cs 檔案中，已使用所選行動服務的應用程式 URL 和應用程式金鑰建立 **MobileServiceClient** 物件。

####已加入行動服務專案

如果在連線服務提供者中建立 .NET 行動服務，則會建立行動服務專案並將其加入至方案。


[深入了解行動服務](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO2-->