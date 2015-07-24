<properties
   pageTitle="搭配使用 Azure 串流分析與 SQL 資料倉儲 | Microsoft Azure"
   description="搭配使用 Azure 串流分析與 SQL 資料倉儲以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# 搭配使用 Azure 串流分析與 SQL 資料倉儲

Azure 串流分析是完全受管理的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。如需基本概念，請參閱 [Azure 串流分析簡介][]。您可以接著透過[開始使用教學課程][]，了解如何使用串流分析建立端對端解決方案。

在本文中，您將學習如何使用 Azure SQL 資料倉儲資料庫做為串流分析工作的輸出接收器。

## 必要條件

首先，請完成[開始使用教學課程][]中的下列步驟。

1. 建立事件中樞輸入
2. 設定並啟動事件產生器應用程式
3. 佈建資料流分析工作
4. 指定工作輸入和查詢

接著，建立 Azure SQL 資料倉儲資料庫

## 指定工作輸出：Azure SQL 資料倉儲資料庫

### 步驟 1
在串流分析工作中，按一下頁面上方的 [輸出]，然後按一下 [新增輸出]。

### 步驟 2
選取 SQL Database，然後按 [下一步]。![][Add Output]

### 步驟 3
在下一頁上輸入下列值 - 輸出別名：輸入此工作輸出的易記名稱。 - 訂用帳戶：- 如果您的 SQL 資料倉儲資料庫位於與串流分析工作相同的訂用帳戶中，請選取 [從目前的訂用帳戶使用 SQL Database]。- 如果您的資料庫是在不同的訂用帳戶中，請選取 [從另一個訂用帳戶使用 SQL 資Database]。- 資料庫：指定目的地資料庫的名稱。- 伺服器名稱：為您剛指定的資料庫指定伺服器名稱。您可以使用 Azure 入口網站進行搜尋。

![][Server Name]

- 使用者名稱：指定具有資料庫寫入權限的帳戶的使用者名稱。
- 密碼：提供指定之使用者帳戶的密碼。
- 資料表：指定資料庫中目標資料表的名稱。

![][Add Database]

### 步驟 4
按一下核取按鈕以新增此工作輸出，並確認串流分析可成功連接到資料庫。

![][Test Connection]

成功連接到資料庫時，您將會在入口網站的底部看到通知。您可以按一下底部的 [測試連線]，以測試資料庫的連線。




## 後續步驟
如需整合概觀，請參閱 [SQL 資料倉儲整合概觀][]。如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Azure 串流分析簡介]: ./stream-analytics-introduction/
[開始使用教學課程]: ./articles/stream-analytics-get-started/
[SQL 資料倉儲開發概觀]: ./sql-data-warehouse-overview-develop/
[SQL 資料倉儲整合概觀]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO1-->