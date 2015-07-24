<properties 
	pageTitle="開始使用 Azure 儲存體" 
	description="描述在 Visual Studio ASP.NET 5 專案中建立 Azure 儲存體時會帶來什麼影響" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# 我的專案發生什麼情形？

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

###<span id="whathappened">我的專案發生什麼情形？</span>

##### 加入參考

Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案。這個封裝會加入下列 .NET 參考：

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

另外也加入了 NuGet 封裝 **Microsoft.Framework.Configuration.Json**。

#####加入 Azure 儲存體的連接字串 
在專案的 config.json 檔案中，已使用所選儲存體帳戶的連接字串和金鑰建立一個元素。

如需詳細資訊，請參閱 [ASP.NET 5](http://www.asp.net/vnext)。
 

<!---HONumber=July15_HO2-->