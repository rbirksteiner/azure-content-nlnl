<properties
   pageTitle="入門：連線到 SQL 資料倉儲 | Microsoft Azure"
   description="開始連線到 SQL 資料倉儲並執行一些查詢。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>
   
# 入門：連接到 SQL 資料倉儲
本快速入門文章將介紹如何使用兩個不同的工具，連接到並查詢已佈建的 SQL 資料倉儲執行個體：

**Visual Studio** - Visual Studio 的整合式程式碼編輯器和偵錯工具 SQL Server Data Tools (SSDT) 與 SQL DW 完全相容，可讓您輕鬆地連接、查詢及管理 SQL 資料倉儲。

**sqlcmd** - sqlcmd 是一種命令列工具，可提供簡單的連線和查詢功能。

完成本文後，您將會：

1. 安裝和更新 Visual Studio 2013
2. 在 SSDT 中建立 SQL 資料倉儲的連線
3. 對 SQL 資料倉儲資料庫執行查詢

>[AZURE.NOTE]假設您已完成佈建指南，或有可用的 SQL 資料倉儲。如果您尚未佈建 SQL 資料倉儲，則請參閱前面的 [佈建快速入門]。

## 設定 Visual Studio 進行開發##
若要進行開發，SQL 資料倉儲團隊建議使用 Visual Studio 2013 或更新版本搭配 SQL Server Data Tools。下面將概述如果您尚未安裝可行的 Visual Studio 版本，要如何下載和更新 Visual Studio 2013。

如果您在尋找詳細資訊，可以利用[完整 SSDT 文件](https://msdn.microsoft.com/library/azure/hh272686.aspx)來解決一般 SSDT 問題。

### 取得 Visual Studio 2013 ###
前往 Visual Studio 2013 網站，下載並安裝一份 Visual Studio。請記住，對於 SQL 資料倉儲，任何免費版本就綽綽有餘。隨意挑選一個符合您需求的版本

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">取得 Visual Studio 2013</a>

### 更新 Visual Studio 2013 ###
是否已安裝 Visual Studio 2013？ 太棒了 ！ 若要確定它是否為最新狀態，只需執行下列步驟。您可以移到下一個步驟。

1. 開啟 Visual Studio 2013
2. 選擇 [工具] 功能表，然後選取 [擴充功能和更新...]
3. 瀏覽樹狀控制項到 [更新] 和 [產品更新]
4. 如果沒有更新可用，您可以安全地關閉 [擴充功能和更新] 視窗並繼續進行本快速入門中的下一個工作。

不過，如果 Visual studio 有更新存在，請按一下 [更新] 按鈕。這會開始下載 Visual Studio 2013 的最新更新。

關閉 [擴充功能和更新] 視窗，在繼續前也要關閉 Visual Studio 2013。

5. 按一下 [執行] 按鈕，將最新更新安裝到 Visual Studio 2013。

6. 同意授權條款，然後按一下 [安裝] 按鈕，以接受任何使用者帳戶控制 (UAC) 提示

7. 按一下 [啟動] 按鈕，即可完成安裝作業

這樣就完成 Visual Studio 2013 的更新。

### 更新 SSDT 
您可能發現您也需要更新 SSDT。這很正常。SSDT 工程師經常以新功能更新其外掛程式，所以您會發現您必須隨時更新。這同樣是非常簡單的程序。若要檢查您是否需要更新 SSDT，請執行下列步驟：

1. 開啟 Visual Studio 2013。  
2. 選擇 [工具] 功能表，然後選取 [擴充功能和更新...]
3. 瀏覽樹狀控制項到 [更新] 和 [產品更新]
4. 如果沒有更新可用，您可以安全地關閉 [擴充功能和更新] 視窗並繼續進行本快速入門中的下一個工作。

不過，如果有名為 [資料庫工具適用的 Microsoft SQL Server 更新] 的更新存在，請按一下 [更新] 按鈕。

這會開始下載最新的 SSDT 版本。下圖顯示 Internet Explorer 下載管理員中的 SSDTSetup.exe。

5. 按一下 [執行] 按鈕，安裝最新的 SSDT 版本。

6. 同意授權條款，然後按一下 [安裝] 按鈕

7. 按一下 [關閉] 按鈕，即可完成 SSDT 更新

8. 關閉 [擴充功能和更新] 視窗

您的桌面上現在有最新版的 Visual Studio 2013 以及最新的 SSDT 延伸模組。

> [AZURE.NOTE]我們目前建議使用 [Visual Studio 2013 的 SSDT 預覽](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409)

## 透過 Visual Studio 2013 連線
如果您目前執行所需的 Visual Studio 版本，您將能夠利用兩個不同的方式來連接到 SQL 資料倉儲執行個體：

### 從 Visual Studio
若要進行連線，我們只需開啟 [SQL Server 物件總管] 並傳入連線資訊

1. 開啟 Visual Studio
2. 選擇 [檢視] 功能表並從下拉式清單中選取 [SQL Server 物件總管]

> [AZURE.NOTE]確定您選擇 [SQL Server 物件總管]，而***不是*** [伺服器總管]。這兩個名稱聽起來很類似，但是非常不同的控制項。兩者的位置相鄰，所以請格外小心並確定您已選取正確的項目！

您現在可以看到 [SQL Server 物件總管]：


3. 按一下 [SQL Server 物件總管] 的 [新增伺服器] 按鈕。這已在下圖中反白顯示：

4. 填入 [連接到伺服器] 對話方塊

使用您建立邏輯伺服器時選擇的值。

請隨意核取 [記住密碼] 勾選方塊。它是一個不錯的省時裝置，但記住這會讓任何人都能實際存取您的設定檔，進而使用此帳戶來執行查詢。

> [AZURE.NOTE]請記住，伺服器名稱必須是完整名稱。因此，您的伺服器名稱值應遵循以下慣例：***ServerName***.database.windows.net，其中 ***ServerName*** 是您提供給邏輯伺服器的名稱。

填妥認證後，請按一下 [連接]

您現已完成連線，並已在 [SQL Server 物件總管] 中註冊您的 SQLDW 邏輯伺服器。
    
### 從 Azure 入口網站
直接從 Azure 入口網站取得 Visual Studio。

1. 登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。
2. 在 [瀏覽] 刀鋒視窗中選取您所需的 SQL DW 執行個體。 
3. 在 SQL DW 刀鋒視窗頂端選取 [在 Visual 中開啟...]
4. 如果您尚未使用用戶端電腦的 IP 設定防火牆，請選取 [設定您的防火牆]。
	1. 輸入 [規則名稱]、[開始 IP] 和 [結束 IP]。 
	2. 按一下刀鋒視窗頂端的 [儲存]。   
5. 按一下 [在 Visual Studio 中開啟]。 
6. Visual Studio 將會開啟，而系統會提示您輸入您的認證 
7. 輸入您的認證並連線之後，您的伺服器和 DW 執行個體將會出現在 [SQL Server 物件總管] 面板中。  

## 使用 sqlcmd 連接到 SQL 資料倉儲

您也可以使用 SQL Server 隨附的 [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) 命令提示字元公用程式或 [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501) 來連接到 SQL DW。sqlcmd 公用程式可讓您在命令提示字元輸入 Transact-SQL 陳述式、系統程序和指令碼檔案。

若要使用 sqlcmd 連接到 SQL DW 的特定執行個體，您必須開啟命令提示字元並輸入 *sqlcmd*，後面接著 SQL DW 資料庫的連接字串。連接字串必須包含下列參數：

+ **使用者 (-U)：**採用 `<`User`>`@`<`Server Name`>`.database.windows.net 格式的伺服器使用者
+ **密碼 (-P)：**與使用者相關聯的密碼
+ **伺服器 (-S)：** 採用 `<`Server Name`>`.database.windows.net 格式的伺服器
+ ** 資料庫 (-D)：**資料庫名稱 
+ **啟用引號識別項 (-I)：**必須啟用引號識別項，才能連接到 SQL DW 執行個體。 

因此若要連接到 SQL DW 執行個體，您要輸入下列資訊：

```
C:\>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
```

連線之後，您可以對執行個體發出任何支援的 Transact-SQL 陳述式。例如，下列陳述式利用 [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) 陳述式來建立新的資料表

```
C:\>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```
	
如需 sqlcmd 的其他資訊，請參閱 [sqlcmd 文件](https://msdn.microsoft.com/library/azure/ms162773.aspx)。

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## 執行範例查詢 ##

我們現已註冊我們的伺服器，接著繼續撰寫查詢。

1. 按一下 SSDT 中的使用者資料庫

2. 按一下新增查詢按鈕

   新視窗現已開啟

3. 撰寫查詢

	將下列內程式碼輸入查詢視窗中：

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. 執行查詢

	若要執行查詢，請按一下下方的綠色箭頭，或使用下列快速鍵 `CTRL`+`SHIFT`+`F5`：

## 後續步驟 ##
[Start developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=July15_HO1-->