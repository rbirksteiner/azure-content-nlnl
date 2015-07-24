<properties
   pageTitle="設定 Operational Insights 的 Proxy 和防火牆設定"
   description="深入了解您需要為使用 Operational Insights 的代理程式類型設定的 Proxy 和防火牆設定"
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
   ms.date="06/04/2015"
   ms.author="banders" />

# 設定 Operational Insights 的 Proxy 和防火牆設定

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

相較於直接連線至伺服器的 Microsoft Monitoring Agent，當您使用 Operations Manager 且它是代理程式時，為 Operational Insights 設定 Proxy 和防火牆設定時須執行的動作是不一樣的。請檢閱下列章節，以了解您所使用的代理程式類型。

## 使用 Microsoft Monitoring Agent 設定 Proxy 和防火牆設定

如果要讓 Microsoft Monitoring Agent 連線並註冊 Operational Insights 服務，該代理程式必須能夠存取您的網域的連接埠號碼和 URL。如果您使用 Proxy 伺服器在代理程式和 Operational Insights 服務之間進行通訊，您必須確保可以存取適當的資源。如果您使用防火牆來限制網際網路存取，您需要設定防火牆以允許存取 Operational Insights。下表列出 Operational Insights 需要的連接埠。

|**代理程式資源**|**連接埠**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443|
|*.oms.opinsights.azure.com|連接埠 443|
|ods.systemcenteradvisor.com|連接埠 443|
|*.blob.core.windows.net *|連接埠 443|

您可以使用以下程序來使用控制台為 Microsoft Monitoring Agent 設定 Proxy 設定。您將需要針對每部伺服器使用此程序。如果您需要設定許多伺服器，使用指令碼將此程序自動化會比較容易。如果是，請參閱下一個程序*使用指令碼設定 Microsoft Monitoring Agent 的 Proxy 設定*。

### 使用控制台設定 Microsoft Monitoring Agent 的 Proxy 設定

1. 開啟 [**控制台**]。

2. 開啟 [**Microsoft Monitoring Agent**].

3. 按一下 [**Proxy 設定**] 索引標籤。![[Proxy 設定] 索引標籤](./media/operational-insights-proxy-firewall/direct-agent-proxy.png)

4. 選取 [**使用 Proxy 伺服器**] 並輸入 URL 與連接埠號碼 (如果需要)，如範例所示。如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼以存取 Proxy 伺服器。

使用下列程序以建立 PowerShell 指令碼，您可以執行該指令碼以設定直接連接到伺服器之每個代理程式的 Proxy 設定。

### 使用指令碼設定 Microsoft Monitoring Agent 的 Proxy 設定


- 複製下列範例、以您的環境的特定資訊進行更新、儲存為 PS1 副檔名，然後在直接連接到 Operational Insights 服務的每一部電腦上執行指令碼。


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## 使用 Operations Manager 設定 Proxy 和防火牆設定

如果要讓 Operations Manager 管理群組連接並且註冊 Operational Insights 服務，該群組必須具有您的網域之連接埠號碼和 URL 的存取權。如果您使用 Proxy 伺服器在 Operations Manager 管理伺服器和 Operational Insights 服務之間進行通訊，您必須確保可以存取適當的資源。如果您使用防火牆來限制網際網路存取，您需要設定防火牆以允許存取 Operational Insights。下表列出這些工作相關的連接埠。

>[AZURE.NOTE]以下部分資源會提及 Advisor。不過，列出的資源將會在未來變更。

|**管理伺服器資源**|**連接埠**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443|
|service.systemcenteradvisor.com|Port 443|
|scadvisor.accesscontrol.windows.net|Port 443|
|scadvisorservice.accesscontrol.windows.net|Port 443|
|*.blob.core.windows.net/*|連接埠 443|
|data.systemcenteradvisor.com|連接埠 443|
|ods.systemcenteradvisor.com|連接埠 443|
|*.systemcenteradvisor.com|Port 443|


|**Operational Insights 和 Operations Manager 主控台資源**|**連接埠**|
|---|---|
|*.systemcenteradvisor.com|Port 80 and 443|
|*.live.com|連接埠 80 和 443|
|*.microsoftonline.com|Port 80 and 443|
|login.windows.net|Port 80 and 443|


使用下列程序以將您的 Operations Manager 管理群組註冊至 Operational Insights 服務。如果您有管理群組和 Operational Insights 服務之間的通訊問題，請使用驗證程序來疑難排解對 Operational Insights 的資料傳輸。

### 要求 Operational Insights 服務端點的例外狀況

1. 使用先前顯示的第一個表格中的資訊，以確定可以透過您具有的任何防火牆存取 Operations Manager 管理伺服器所需的資源。

2. 使用先前顯示的第二個表格中的資訊，以確定可以透過您具有的任何防火牆存取 Operations Manager 和 Operational Insights 之 Operations 主控台所需的資源。

3. 如果您透過 Internet Explorer 使用 Proxy 伺服器，請確定它已設定且運作正常。若要確認，您可以開啟安全的 Web 連線 (https)，例如 [https://bing.com](https://bing.com)。如果安全的 Web 連線無法在瀏覽器中運作，可能是不適用於具有雲端中 Web 服務的 Operations Manager 管理主控台。

### 在 Operations Manager 主控台中設定 Proxy 伺服器

1. 開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。

2. 展開 [**Operational Insights**]，然後選取 [**Operational Insights 連線**]。![Operations Manager Operational Insights 連線](./media/operational-insights-proxy-firewall/proxy-om01.png)

3. 在 [Operational Insights 連線] 檢視中，按一下 [**設定 Proxy 伺服器**]。![Operations Manager Operational Insights 連線設定 Proxy 伺服器](./media/operational-insights-proxy-firewall/proxy-om02.png)

4. 在 [Operational Insights 設定精靈：Proxy 伺服器] 中，選取 [**使用 Proxy 伺服器來存取 Operational Insights Web 服務**]，然後輸入具有連接埠號碼的 URL，例如，**http://myproxy:80**。![Operations Manager Operational Insights Proxy 位址](./media/operational-insights-proxy-firewall/proxy-om03.png)


### 在 Proxy 伺服器需要驗證時指定認證

1. 開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。

2. 在 [**RunAs 組態**] 底下，選取 [**設定檔**]。

3. 開啟 [**System Center Advisor 執行身份設定檔 Proxy**] 設定檔。![System Center Advisor 執行身份 Proxy 設定檔的影像](./media/operational-insights-proxy-firewall/proxyacct1.png)
4. 在 [執行身分設定檔精靈] 中，按一下 [**新增**] 以使用執行身分帳戶。您可以建立新的執行身分帳戶，或使用現有的帳戶。此帳戶必須有足夠的權限，才能通過 Proxy 伺服器。![[執行身份設定檔精靈] 的影像](./media/operational-insights-proxy-firewall/proxyacct2.png)

5. 若要設定帳戶來管理，請選擇 [**選取的類別、群組或物件**] 來開啟 [物件搜尋] 方塊。![[執行身份設定檔精靈] 的影像](./media/operational-insights-proxy-firewall/proxyacct2-1.png)
6. 搜尋，然後選取 [**Operations Manager 管理伺服器**]。![[物件搜尋] 方塊的影像](./media/operational-insights-proxy-firewall/proxyacct3.png)
7. 按一下 [**確定**] 以關閉 [新增執行身份帳戶] 方塊![[執行身份設定檔精靈] 的影像](./media/operational-insights-proxy-firewall/proxyacct4.png)
8. 完成精靈並儲存變更。![[執行身份設定檔精靈] 的影像](./media/operational-insights-proxy-firewall/proxyacct5.png)


### 針對 WinHTTP 在每個管理伺服器上設定 Proxy 伺服器

1. 如果 Operations Manager 尚未以 Operations Manager 2012 R2 更新彙總套件 3 或 Operations Manager 2012 SP1 更新彙總套件 7 或更新版本進行更新，請在 Operations Manager 管理伺服器上以系統管理員身分開啟命令提示字元視窗。否則，您不需要使用此程序。

2. 輸入 **netsh winhttp set proxy myproxy:80**。

3. 關閉 [命令提示字元] 視窗，並重新啟動 System Center 管理服務 (HealthService)。

4. 在管理群組中的每個管理伺服器上執行步驟 2。

### 在每個管理伺服器上設定 Proxy 伺服器

1. 開啟 Operations Manager 主控台，然後選取 [**管理**] 工作區。

2. 選取 [**裝置管理**]，然後按一下 [**管理伺服器**]。

3. 以滑鼠右鍵按一下每部管理伺服器的名稱，按一下 [**屬性**]，然後在 [**Proxy 設定**] 索引標籤上設定資訊。![[Proxy 設定] 索引標籤](./media/operational-insights-proxy-firewall/proxyms.png)

### 驗證 Operational Insights 管理套件已下載

- 如果您使用 Operational Insights 加入解決方案，您可以在 Operations Manager 主控台的 [**管理**] 底下以管理套件檢視它們。搜尋 *System Center Advisor* 以快速找出它們。![已下載的管理組件](./media/operational-insights-proxy-firewall/mpdownloaded.png)
- 或者，您也可以利用 Operations Manager 管理伺服器中的下列 Windows PowerShell 命令，檢查 Operational Insights 管理套件：

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### 驗證 Operations Manager 已將資料傳送至 Operational Insights 服務

1. 在 Operations Manager 管理伺服器中，開啟效能監視器 (perfmon.exe)，然後選取 [**效能監視器**]。

2. 按一下 [**新增**]，然後選取 [**健康情況服務管理群組**]。

3. 加入開頭為 **HTTP** 的所有計數器。![新增計數器](./media/operational-insights-proxy-firewall/sendingdata1.png)
4. 如果您的 Operations Manager 組態良好，您會看到健康情況服務管理計數器的事件和其他資料項目的活動，根據您在 Operational Insights 中加入的管理套件和設定的記錄收集原則。![顯示活動的效能監視器](./media/operational-insights-proxy-firewall/sendingdata2.png)
 
<!--BugFixing Comment-->

<!---HONumber=July15_HO1-->