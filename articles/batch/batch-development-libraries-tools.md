<properties 
	pageTitle="Azure 批次開發程式庫和工具" 
	description="取得開發「Azure 批次」和「批次應用程式」應用程式所需的程式庫和工具" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Azure 批次開發程式庫和工具 
<p> 取得下列程式庫和工具來開發「Azure 批次」和「批次應用程式」應用程式。

## 批次

+ [適用於 .NET 的批次用戶端程式庫](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – 開發用戶端應用程式以透過「批次」服務執行工作
+ [批次管理程式庫](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – 管理「批次」服務帳戶
+ [批次總管](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – GUI 應用程式和範例，來瀏覽、存取和更新批次帳戶內的主要元素，包括作業和工作、工作虛擬機器 (TVM) 和集區，以及 TVM 上的檔案。

> [AZURE.TIP]如果您不熟悉「批次」，或是想要監視或疑難排解批次活動，則批次總管是不錯的工具。因為這是程式碼範例，所以原始碼顯示如何使用批次 .NET API 來實作功能。請參閱[部落格文章](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)。

<p>
## 批次應用程式

+ [批次應用程式雲端 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – 可讓應用程式將工作移交給「批次」服務
+ [批次應用程式 Visual Studio 擴充功能](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Visual Studio 組件庫) – 使用「批次應用程式雲端 SDK」在 Visual Studio 中啟用應用程式的雲端功能
+ [批次應用程式用戶端 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – 與能夠將工作移交給「批次」服務的應用程式互動
+ [批次應用程式 Python 用戶端](https://github.com/Azure/azure-batch-apps-python) (GitHub) - 與批次應用程式服務中所設定的應用程式互動的 Python 用戶端模組
+ [批次應用程式 Blender 範例](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Blender 開放原始檔轉譯軟體的附加元件，使用 Batch Apps SDK 和 Python 用戶端來設定雲端轉譯平台


## 其他資源

+ [程式碼範例](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Azure 批次論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [開始使用適用於 .NET 的 Azure 批次程式庫](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=62-->