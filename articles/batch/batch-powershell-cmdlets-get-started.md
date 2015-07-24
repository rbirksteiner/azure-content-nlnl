<properties
   pageTitle="開始使用 Azure 批次 PowerShell Cmdlet | Microsoft Azure"
   description="介紹用來管理 Azure 批次服務的 Azure PowerShell Cmdlet"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="05/29/2015"
   ms.author="danlep"/>

# 開始使用 Azure 批次 PowerShell Cmdlet
本文是 Azure PowerShell Cmdlet 的簡介，可用來管理批次帳戶並取得批次工作項目、工作和任務的相關資訊。

如需詳細的 Cmdlet 語法，請輸入 `get-help <Cmdlet_name>`，或請參閱 [Azure 批次 Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)。


## 必要條件

* **批次預覽** - 請申請[批次預覽](https://account.windowsazure.com/PreviewFeatures)以使用服務 (如果您還沒有申請)。
* **Azure PowerShell** - 請參閱＜[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)＞以了解必要條件、下載和安裝指示。自 0.8.10 起及後續版本導入批次 Cmdlet。

## 使用批次 Cmdlet

使用標準程序啟動 Azure PowerShell 並[連接到 Azure 訂用帳戶](../powershell-install-configure.md#Connect)。此外：

* **選取 Azure 訂用帳戶** - 如果您有多個訂用帳戶，請選取您已新增「批次預覽」功能的訂用帳戶：

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **切換到 AzureResourceManage 模式** - Azure 資源管理員模組中隨附的批次 Cmdlet。請參閱＜[將 Windows PowerShell 與資源管理員搭配使用](../powershell-azure-resource-manager.md)＞以獲取詳細資訊。若要使用此模組，請執行 [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) Cmdlet：

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## 管理批次帳戶和金鑰

您可以使用 Azure PowerShell Cmdlet 建立及管理批次帳戶和金鑰。

### 建立批次帳戶：

**New-AzureBatchAccount** 將在指定的資源群組中建立新的批次帳戶。如果您還沒有資源群組，請執行 [New-azureresourcegroup](https://msdn.microsoft.com/library/dn654594.aspx) Cmdlet 建立一個，並在**位置** 參數中指定其中一個 Azure 區域。(您也可以藉由執行 [Get-azurelocation](https://msdn.microsoft.com/library/dn654582.aspx) 尋找不同 Azure 資源可用的區域。) 例如：

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

然後，在資源群組中建立新的批次帳戶，同時為 < * account_name * > 指定帳戶名稱和批次服務可用的位置。建立帳戶可能需要幾分鐘才能完成。例如：

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]批次帳戶名稱對 Azure 必須是唯一的，介於 3 到 24 個字元，並只能使用小寫字母和數字。

### 取得帳戶存取金鑰
**Get-AzureBatchAccountKeys** 將顯示與 Azure 批次帳戶相關聯的存取金鑰。例如，執行下列命令以取得您所建立帳戶的主要和次要金鑰。

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### 產生新的存取金鑰
**New-AzureBatchAccountKey** 將為 Azure 批次帳戶產生新的主要或次要帳戶金鑰。例如，若要為您的批次帳戶產生新的主要金鑰，請輸入：

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]若要產生新的次要金鑰，請將 **KeyType** 參數指定為「次要」。您必須個別重新產生主要和次要金鑰。

### 刪除批次帳戶
**Remove-AzureBatchAccount** 將刪除批次帳戶。例如：

```
Remove-AzureBatchAccount -AccountName <account_name>
```

出現提示時，請確認您想要移除的帳戶。帳戶移除可能需要一些時間來完成。

## 查詢工作項目、 工作和作業

使用 Cmdelt，例如 **Get-AzureBatchWorkItem**、**Get-AzureBatchJob**、**Get-AzureBatchTask** 和 **Get-AzureBatchPool** 在批次帳戶下查詢所建立的實體。

若要使用這些指令程式，您必須先建立一個 AzureBatchContext 物件以儲存您的帳戶名稱和金鑰：

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

使用 **BatchContext** 參數將此內容傳入與批次服務互動的 Cmdlet。

> [AZURE.NOTE]依預設，帳戶的主要金鑰用於驗證，但是您可以透過變更 BatchAccountContext 物件的 **KeyInUse** 屬性，明確地選取要使用的金鑰：```$context.KeyInUse = "Secondary"```。


### 查詢資料

例如，使用 **Get-AzureBatchWorkItem** 以尋找您的工作項目。依預設，這將查詢您的帳戶下的所有工作項目，並假設您已經將 BatchAccountContext 物件儲存在 *$context* 裡：

```
Get-AzureBatchWorkItem -BatchContext $context
```

其他實體 (例如集區) 可以用相同方式處理：

```
Get-AzureBatchPool -BatchContext $context
```
### 使用 OData 篩選

您可以提供**篩選**參數給 OData 篩選，只尋找您感興趣的物件。例如，您可以找到名稱以 “myWork” 開頭的所有工作項目：

```
$filter = "startswith(name,'myWork') and state eq 'active'"
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
```

雖然這個方法比在本機管線中使用 “Where-Object” 較不具有彈性，不過查詢將直接傳送進批次服務，讓所有篩選在伺服器端運作，進而省下網際網路頻寬。

### 使用名稱參數

使用**名稱**參數可作為 OData 篩選的替代方式。若要查詢名為 "myWorkItem" 的特定工作項目：

```
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context

```
**名稱**參數僅支援完整名稱的搜尋，不能使用萬用字元或 OData 樣式的篩選。

### 使用管線

批次 Cmdlet 可以利用 PowerShell 管線在 Cmdlet 之間傳送資料。這和指定參數有相同效果，但讓列出多個實體更容易。例如，您可以在您的帳戶下找到所有的作業：

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### 使用 MaxCount 參數

依預設，每個 Cmdlet 最多傳回 1000 個物件。如果您已經到達此限制，您可以縮小您的篩選以傳回較少的物件，或使用 **MaxCount** 參數明確地設定最大值。例如：

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context

```

若要移除上限，將 **MaxCount** 設定為 0 或更少。

## 相關主題
* ＜[批次技術概觀](batch-technical-overview.md)＞
* ＜[下載 Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)＞
* [Azure 批次 Cmdlet 參考資料](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [有效率的清單查詢](batch-efficient-list-queries.md)

<!---HONumber=62-->