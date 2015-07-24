<properties
   pageTitle="Operational Insights 的 Operations Manager 考量"
   description="如果您搭配使用 Microsoft Azure Operational Insights 與 Operations Manager，則您的組態將依賴 Operations Manager 代理程式和管理群組的散佈，以收集資料並傳送給 Operational Insights 服務以進行分析"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Operational Insights 的 Operations Manager 考量

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

如果您搭配使用 Microsoft Azure Operational Insights 與 Operations Manager，則您的組態將依賴 Operations Manager 代理程式和管理群組的散佈，以收集資料並傳送給 Operational Insights 服務以進行分析。不過，如果您使用直接連接到 Web 服務的代理程式，則您不需要 Operations Manager。搭配使用 Operational Insights 與 Operations manager 時請考慮下列問題。

此外，您必須指定工作負載的執行身分認證監視 Operational Insights 的 Operations Manager。

## Operational Insights 軟體功能和需求

Operational Insights 包含雲端中的 Web 服務，及直接連接至 Web 服務的代理程式或 Operations Manager 代理程式，及管理您環境中電腦的 Operations Manager 管理群組。

選取 Operations Manager 代理程式 (用於收集資料) 和管理群組 (用於管理代理程式，並將資料傳送至 Operational Insights 服務) 前，請確定瞭解下列事項：

- Operations Manager 代理程式是安裝在您想要從中收集和分析資料的任何伺服器上。

- Operations Manager 管理群組會將資料從代理程式傳送到 Operational Insights Web 服務。它不會分析任何資料。

- Operations Manager 管理群組必須具有網際網路存取權，才能將資料上傳至 Web 服務。

- 為求最佳效果，請不要在也是網域控制站的電腦上安裝 Operations Manager 管理伺服器。

- Operations Manager 代理程式必須有網路連線到 Operations Manager 管理群組，如此才能傳送資料。

Operational Insights 可以使用 System Center 健全狀況服務來收集並分析資料。Operations Manager 取決於 System Center 健全狀況服務。當您檢視伺服器安裝的程式時，您會看到 System Center Operations Manager 代理程式軟體，特別是在 [新增/移除程式] 中。請勿移除這些軟體，因為 Operational Insights 依存它們。如果移除 Operations Manager 代理程式軟體，將無法再運作 Operational Insights。

## 與 Operations Manager 共存

使用 Operations Manager 時，Operational Insights 只能在 System Center Operations Manager 2012 R2 或 System Center Operations Manager 2012 SP1 中與 Operations Manager 代理程式搭配使用。不支援舊版的 System Center Operations Manager。由於 Operations Manager 代理程式可用來收集資料，因此它會使用特定認證 (動作帳戶或執行身分帳戶) 來支援某些分析的工作負載，例如 SharePoint 2012。

## Operational Insights 和 SQL Server 2012

使用 Operations Manager 時，System Center 健全狀況服務會在本機系統帳戶下執行。在早於 SQL Server 2008 R2 的 SQL Server 版本中，預設會啟用本機系統帳戶，並且是系統管理員伺服器角色的成員。在 SQL Server 2012，本機系統登入不是系統管理員伺服器角色的一部分。因此當您使用 Operational Insights 時，它完全無法監視 SQL Server 2012 執行個體，且不是所有規則都會產生警示。

## 網際網路和內部網路連線能力

使用直接與 Web 服務連接的代理程式時，代理程式需要具備網際網路的存取權，才能將資料傳送至 Web 服務，並從 Web 服務接收更新的組態資訊。

使用 Operational Insights 時，管理伺服器需要網際網路的存取權，才能將資料傳送至 Web 服務，並從 Web 服務接收更新的組態資訊。不過，您的代理程式不需要存取網際網路。如果您在未連線到網際網路的伺服器上有 Operations Manager 代理程式，您可以使用 Web 服務，但前題是它們可與網際網路連線的管理伺服器通訊。

## 叢集支援

執行 Windows Server 2012、Windows Server 2008 R2 或設定為 Windows 容錯移轉叢集一部分的 Windows Server 2008 電腦支援 Operations Manager 代理程式。您可以在 Operational Insights 入口網站中檢視叢集。在 [伺服器] 頁面上，叢集會識別為 TYPE=CLUSTER (而不是 TYPE=AGENT，即識別實體電腦的方式)。

探索和組態規則是在叢集的主動和被動節點上執行，但會忽略被動節點上產生的任何警示。如果節點會從被動轉移為主動，則會自動顯示該節點的警示，而且不需要您介入。

某些警示可能會產生兩次，視產生警示的規則而定。比方說，藉由檢查作業系統為實體伺服器和叢集產生的警示，從而偵測不正確驅動程式的規則

不支援被動節點的組態分析。

Operational Insights 不支援將執行 Windows Server 的電腦 (屬於相同的容錯移轉叢集) 分組或連結。

## 調整 Operational Insights 環境

當您計劃 Operational Insights 部署 (特別是當您分析想要透過單一管理群組轉送資料的 Operations Manager 代理程式數目時)，請考慮伺服器的檔案空間容量。

請考慮下列變數：

- 每個管理群組的代理程式數目

- 每日從代理程式傳送到管理群組的資料平均大小。根據預設，每個代理程式每日會將 CAB 檔案上傳到管理群組兩次。CAB 檔案的大小取決於伺服器的組態 (例如 SQL Server 引擎和數字資料庫的數目) 和伺服器的健全狀況 (例如產生的警示數目)。在大部分情況下，每日上傳大小通常小於 100 KB。

- 管理群組中保存資料的保存期間 (預設值為 5 天)

例如，如果您假設每個代理程式在預設的保存期間內，每日上傳的大小為 100 KB，您需要為管理群組準備下列儲存體：

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>代理程式數目</b></td>
		<td><b>管理群組所需的估計空間</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2.5 MB (5 個代理程式 x 100 KB 資料/天 x 5 天 = 2,500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 個代理程式 x 100 KB 資料/天 x 5 天 = 25,000 KB)</td>
    </tr>

    </tbody>
    </table>

## Operational Insights 的 Operations Manager 執行身分帳戶

Operational Insights 會使用 Operations Manager 代理程式及管理群組，收集資料並將資料傳送給 Operational Insights 服務。Operational Insights 會建立工作負載的管理套件以提供加值服務。每個工作負載都需要具有特定的工作負載權限，才能在不同的安全性內容中執行管理套件，例如網域帳戶。您需要藉由設定 Operations Manager 執行身分帳戶來提供認證資訊。

下列章節描述如何為下列工作負載設定 Operations Manager 執行身分帳戶：

- SQL 評估
- Virtual Machine Manager
- Lync Server
- SharePoint

### 設定 SQL 評估的執行身分帳戶

 如果您已經在使用 SQL Server 管理套件，您應該使用該執行身分帳戶。

#### 在 Operations 主控台中設定 SQL 執行身分帳戶

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights SQL 評估執行身分設定檔**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取包含 SQL Server 所需認證的 Windows 執行身分帳戶，或按一下 [**新增**] 建立一個。
	>[AZURE.NOTE]執行身分帳戶類型必須是 Windows。執行身分帳戶也必須屬於裝載 SQL Server 執行個體的所有 Windows 伺服器上的本機系統管理員群組。

5. 按一下 [儲存]。

6. 修改，然後在每個 SQL Server 執行個體上執行下列 T-SQL 範例，授與執行身分帳戶所需的最小權限授以執行 SQL 評估。不過，如果執行身分帳戶已是 SQL Server 執行個體上 sysadmin 伺服器角色的一部分，您就不需要這樣做。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```


### 設定 Virtual Machine Manager 的執行身分帳戶

請確定執行身分帳戶擁有下列動作的權限：

- 使用 VMM Windows PowerShell 模組

- 查詢 VMM 資料庫

- 從遠端管理虛擬化主機上執行的 VMM 代理程式

將 Operational Insights 連線到 Operations Manager 時，使用下列步驟來設定此帳戶。

#### 設定 VMM 的認證

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights VMM 執行身分帳戶**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取包含 VMM 所需認證的 Windows 執行身分帳戶，或按一下 [**新增**] 建立一個。
	>[AZURE.NOTE]執行身分帳戶類型必須是 Windows。

5. 按一下 [儲存]。


### 設定 Lync Server 的執行身分帳戶

 執行身分帳戶必須是本機系統管理員和 Lync RTCUniversalUserAdmins security 安全性群組的成員。

#### 設定 Lync 帳戶的認證

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights Lync 執行身分帳戶**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取同時為本機系統管理員和 Lync RTCUniversalUserAdmins 安全性群組成員的 Windows 執行身分帳戶。
	>[AZURE.NOTE]執行身分帳戶類型必須是 Windows。

5. 按一下 [儲存]。


### 為 SharePoint 設定執行身分帳戶


#### 設定 SharePoint 帳戶的認證

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights SharePoint 執行身分帳戶**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取包含 SharePoint 所需認證的 Windows 執行身分帳戶，或按一下 [**新增**] 建立一個。
	>[AZURE.NOTE]執行身分帳戶類型必須是 Windows。

5. 按一下 [儲存]。



## 地理位置

如果您想要分析來自不同地理位置的伺服器資料，請考慮在每個位置設定一個管理群組。這可以改善從代理程式到管理群組傳輸資料的效能。

<!---HONumber=July15_HO2-->