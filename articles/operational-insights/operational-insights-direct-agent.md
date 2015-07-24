<properties
	pageTitle="將電腦直接連線到 Operational Insights"
	description="您可以在想要連線的每一部電腦安裝 Operational Insights 代理程式，將電腦直接連線到 Operational Insights。"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# 將電腦直接連線到 Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

您可以在想要連線的每一部電腦上安裝 Operational Insights 代理程式，將電腦直接連線到 Operational Insights。

> [AZURE.TIP]針對在 Azure 中執行的虛擬機器，遵循[從 Microsoft Azure 中的伺服器分析資料](operational-insights-analyze-data-azure.md)中的步驟，安裝代理程式

## 下載並安裝代理程式
使用下列程序下載並安裝 Operational Insights 代理程式。

### 下載代理程式安裝檔案
1. 在 Operations Insights 入口網站的 [**概觀**] 頁面中，按一下 [**設定**] 磚。按一下頂端的 [**連接的來源**] 索引標籤。![設定頁面](./media/operational-insights-direct-agent/direct-agent01.png)
2. 在**直接附加伺服器 (64 位元)** 之下，按一下 [下載代理程式] 按鈕，以下載安裝檔案。
3. 在**工作區識別碼**的右邊，按一下複製圖示，然後將識別碼貼入記事本中。
4. 在**主要金鑰**的右邊，按一下複製圖示，然後將識別碼貼入記事本中。![設定頁面](./media/operational-insights-direct-agent/direct-agent02.png)

### 使用安裝程式安裝代理程式
1. 在您想要管理的電腦上執行安裝程式以安裝代理程式。
2. 選取 [**將代理程式連線至 Microsoft Azure Operational Insights**]，然後按 [**下一步**]。
3. 出現提示時，輸入您在上一個程序中複製到記事本中的**工作區識別碼**和**主要金鑰**。

4. 按 [下一步]。代理程式會確認它可以連線到 Operational Insights。
5. 完成時，[**Microsoft 管理代理程式**] 會出現在 [**控制台**] 中。

### 使用命令列安裝代理程式
- 修改，然後搭配使用下列範例與命令列來安裝代理程式。```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## 設定 Microsoft Monitoring Agent (選擇性)
您可以使用下列資訊，讓代理程式直接與 Microsoft Azure Operational Insights 服務通訊。設定代理程式之後，它會註冊代理程式服務，並將獲得所需的組態資訊及包含解決方案資訊的管理組件。

從代理程式所監視的電腦收集資料之後，受監視的電腦數目會出現在 [**直接附加伺服器 (64 位元)**] 下 [**設定**] 中 [**連線來源**] 索引標籤的 Operational Insights 入口網站。對於傳送資料的任何電腦，您可以在 Operational Insights 入口網站中檢視其資料和評估資訊。

您也可以停用代理程式 (如有需要)，或使用命令列或指令碼加以啟用。

### 設定代理程式
1. 安裝 **Microsoft Monitoring Agent** 後，開啟 [ **控制台**]。
2. 開啟 Microsoft Monitoring Agent，然後按一下 [Azure Operational Insights] 索引標籤。
3. 選取 [**連線至 Azure Operational Insights**]。
4. 在 [**工作區識別碼**] 方塊中，貼上來自 Operational Insights 入口網站的工作區識別碼。
5. 在 [**帳戶金鑰**] 方塊中，貼上來自 Operational Insights 入口網站的**主要金鑰**，然後按一下 [**確定**]。

### 停用代理程式
1. 安裝代理程式之後，開啟 [**控制台**]。
2. 開啟 Microsoft Monitoring Agent，然後按一下 [**Azure Operational Insights**] 索引標籤。
3. 清除**至 Azure Operational Insights 的連線**。

### 使用命令列或指令碼啟用代理程式
- 您可以參考下列範例來使用 Windows PowerShell 或 VB 指令碼。

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## 設定 Proxy 和防火牆設定 (選擇性)
如果您的環境中有限制存取網際網路的 Proxy 伺服器或防火牆，您可能需要依照下列程序，讓 Operations Manager 或代理程式與 Operational Insights 服務進行通訊

- [設定 Proxy 和防火牆設定 (選擇性)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO2-->