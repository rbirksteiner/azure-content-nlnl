<properties 
	pageTitle="Azure SQL Database Elastic Scale" 
	description="文件地圖，以視覺化的方式呈現 Azure SQL DB 的 Elastic Scale 功能" 
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
	ms.date="04/24/2015" 
	ms.author="sidneyh"/>

# Azure SQL Database 彈性資料庫集區主題

Azure SQL DB 的彈性資料庫功能是設計來簡化資料層開發和管理，特別是針對軟體代管服務 (SaaS) 開發人員 — 大量資料庫會用於支援動態一般客戶基底。

**彈性資料庫工具**會協助您建置及管理應用程式，該應用程式會利用分區化模式跨許多資料庫向外延展。包括適用於 ADO.Net 的彈性資料庫用戶端程式庫支援分區管理、資料相依路由及跨分區查詢功能。也包含分割合併工具，簡化分區管理，方法是讓您從一個分區將資料移動 (分割) 到另一個分區 (合併)。這些工具補充彈性資料庫集區，讓任意數目的資料庫定價可預測並且具有保證的效能特性。相關聯的工作功能提供您簡單及可靠的工具，使用 T-SQL 指令碼來升級和管理集區中的每個資料庫。

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/ElasticScaleMapcoded.svg" width="100%" height="100%"> </object>
 

<!---HONumber=July15_HO2-->