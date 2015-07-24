<properties
   pageTitle="SQL 資料倉儲的管理工具 | Microsoft Azure"
   description="SQL 資料倉儲管理工具的簡介。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/24/2015"
   ms.author="mausher;nicw;barbkess;JRJ@BigBangData.co.uk;"/>

# SQL 資料倉儲的管理工具
本主題將探索和比較用來管理 SQL 資料倉儲的工具和選項，協助您挑選適合您需求的正確工具。要選擇正確的工具就必須考量到您管理多少資料庫、工作本身以及執行工作的頻率。

## Azure 入口網站
[Azure 入口網站][]是網頁型管理入口網站，您可以在當中建立、更新和刪除資料庫以及監視資料庫資源。如果您剛開始使用 Azure、管理少量的資料倉儲資料庫，或是需要快速執行工作，那這正是十分適合您的工具。

入口網站包含的計量涵蓋目前和過去的效能 DWU 設定、所使用的儲存體數量、成功和失敗的 SQL 連接，以及一組視覺效果和資料，可讓您了解在執行個體上執行的查詢和這些查詢的詳細資料。

## Visual Studio 中的 SQL Server Data Tools	
Visual Studio 中的 [SQL Server Data Tools][] (SSDT) 是在您電腦上執行的用戶端工具，可讓您連接、管理和開發雲端中的資料庫。如果您是熟悉 Visual Studio 或其他整合式開發環境 (IDE) 的應用程式開發人員，請嘗試使用 Visual Studio 中的 SSDT。

SSDT 包含的 SQL Server 總管可讓您針對 SQL 資料倉儲資料庫視覺化、連接和執行指令碼。如要快速連接 SQL 資料倉儲，只需在 Azure 入口網站檢視資料庫的詳細資料時，按一下命令列中的 [**在 Visual Studio 中開啟**] 按鈕即可。

您可以下載支援 SQL 資料倉儲的最新版的 [SQL Server Data Tools][] (SSDT)。

## 命令列工具
其中一個選項是使用 PowerShell 或 sqlcmd 命令列工具管理 SQL 資料倉儲，並自動部署 Azure 資源。因為您可為必要的工作編寫指令碼並自動執行這類工作，所以我們建議使用這些工具來管理大量的邏輯伺服器，以及在生產環境中部署資源變更。

## 後續步驟
如要開始使用這些工具，請前往[連接][]主題。

<!--Image references-->

<!--Article references-->
[連接]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/zh-tw/library/mt204009.aspx

<!--Other web references-->
[Azure 入口網站]: http://portal.azure.com/

<!---HONumber=July15_HO1-->