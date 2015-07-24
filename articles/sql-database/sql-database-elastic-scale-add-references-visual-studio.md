<properties 
	pageTitle="將 Azure SQL DB Elastic Scale 參考加入至 Visual Studio 專案" 
	description="如何使用 Nuget 將 Elastic Scale API 的 .NET 參照新增到 Visual Studio 專案。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# 如何：將彈性資料庫用戶端程式庫參考加入至 Visual Studio 專案 

### 必要條件： 

- 安裝適用於 Visual Studio 的 [NuGet Visual Studio Extension Gallery](http://docs.nuget.org/docs/start-here/installing-nuget) 

### 在 Visual Studio 中加入彈性資料庫用戶端程式庫參考 

1. 開啟現有專案，或使用位於 [**檔案**] --> [**新增**] --> [**專案**] 中的 [新增專案] 對話方塊建立新的專案 
2. 在 [方案總管] 中以滑鼠右鍵按一下 [**參考**]，然後選取 [**管理 NuGet 封裝**]
3. 在 [管理 NuGet 封裝] 視窗左側的功能表中，依序選取 [ **線上**] 和 [**nuget.org**] 或 [全部] 
4. 在 [**線上搜尋**] 對話方塊中，輸入 **Elastic Database**，選取 [**彈性資料庫用戶端程式庫**]，然後按一下 [**安裝**]。

	![線上搜尋][1]
4. 檢閱授權，並按一下 [**我接受**]。 

用戶端程式庫參考現在已加入至您的專案。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO2-->