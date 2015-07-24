<properties
   pageTitle="Azure 資源管理員的流量管理員支援預覽 | Microsoft Azure"
   description="在預覽中使用適用於流量管理員的 PowerShell 與 Azure 資源管理員 (ARM)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma" />






# Azure 資源管理員的 Azure 流量管理員支援預覽
Azure 資源管理員 (ARM) 是 Azure 中新的服務管理架構。Azure 流量管理員設定檔現在可使用以 Azure 資源管理員為基礎的 API 和工具來管理 。若要深入了解 Azure 資源管理員，請參閱[使用資源群組管理您的 Azure 資源](../azure-preview-portal-using-resource-groups.md)。

>[AZURE.NOTE]ARM 的流量管理員支援目前為預覽狀態，包括 REST API、Azure PowerShell、Azure CLI 和 .NET SDK。



## 資源模型

Azure 流量管理員使用名為「流量管理員設定檔」的設定集合進行設定。這包含 DNS 設定、流量路由設定、端點監視設定，以及流量將路由之目標服務端點的清單。

在 ARM 中，每個流量管理員設定檔是以一個 ARM 資源來表示 (類型為 ‘TrafficManagerProfiles’，由 ‘Microsoft.Network’ 資源提供者管理)。在 REST API 層級中，每個設定檔的 URI 如下：

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## 與 Azure 流量管理員服務管理 API 的比較

除了下面列出的預覽限制之外，使用 ARM 設定流量管理員設定檔可提供對和 (非 ARM) 服務管理API 相同之流量管理員功能集的存取。

不過，儘管功能維持相同，但部分術語已經變更：

- 「負載平衡方法」用來決定當流量管理員回應特定的 DNS 要求時，選擇要將流量路由至哪一個端點的方式，已重新命名為「流量路由方法」。

- 「循環配置資源」流量路由方法已重新命名為「加權」。

- 「容錯移轉」流量路由方法已重新命名為 「優先」。

## 預覽限制
由於 Azure 資源管理員對流量管理員的支援是預覽服務，因此目前有一些限制：

- 使用現有的 (非 ARM) 服務管理 API、工具和入口網站建立的流量管理員設定檔無法透過 ARM 使用，反之亦然。目前不支援將設定檔從非 ARM 移轉至 ARM API。

- 	REST API 不支援修補流量管理員設定檔。若要更新設定檔內容，您必須「取得」設定檔，然後「放入」已修改的設定檔。
- 	只支援「外部」端點。這些設定檔仍可用來將流量管理員與 Azure 架構的服務搭配使用，且在這麼做時，端點將以內部端點費率來計費。(使用外部端點的唯一影響是，如果基礎 Azure 服務被停用或被刪除時，不會自動停用或刪除外部端點，您必須手動停用或刪除端點)。
-	Azure 流量管理員尚無法在 Azure 入口網站上使用，只能在傳統入口網站上使用。

## 設定 Azure PowerShell

這些指示使用 Microsoft Azure PowerShell，並且需要使用下列步驟來設定。

針對非 PowerShell 使用者，也可以透過其他介面執行相同作業。

### 步驟 1
安裝最新的 Azure PowerShell (可從 Azure 下載頁面取得)。
### 步驟 2
切換 PowerShell 來使用 ARM Cmdlet。如需詳細資訊，可在＜搭配使用 Windows PowerShell 與資源管理員＞取得。

	PS C:> Switch-AzureMode -Name AzureResourceManager
### 步驟 3
登入您的 Azure 帳戶。

	PS C:> Add-AzureAccount

系統會提示使用您的認證進行驗證。

### 步驟 4
選擇要使用哪一個 Azure 訂用帳戶。

	PS C:> Select-AzureSubscription -SubscriptionName "MySubscription"

若要查看可用訂用帳戶的清單，請使用 ‘Get-AzureSubscription’ Cmdlet。

### 步驟 5

 流量管理員服務由 Microsoft.Network 資源提供者管理。您的 Azure 訂用帳戶必須先註冊以使用此資源提供者，才能透過 ARM 使用流量管理員。每個訂用帳戶只需執行一次此作業。

	PS C:> Register-AzureProvider –ProviderNamespace Microsoft.Network

### 步驟 6
建立資源群組 (如果是使用現有的資源群組，請略過此步驟)

	PS C:> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。然而，因為流量管理員設定檔的所有資源是全域而非區域，資源群組位置的選擇不會影響 Azure 流量管理員。

## 建立流量管理員設定檔

若要建立流量管理員設定檔，請使用 New-AzureTrafficManagerProfile Cmdlet：

	PS C:> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

參數如下所示：

- Name：流量管理員設定檔資源的 ARM 資源名稱。相同資源群組中的設定檔必須有唯一的名稱。這個名稱是從用於 DNS 查詢的 DNS 名稱分割而來。

-	ResourceGroupName：包含設定檔資源的 ARM 資源群組的名稱。

-	TrafficRoutingMethod：指定流量路由方法，用來決定哪一個端點會在傳入的 DNS 查詢回應中傳回。可能的值為 ‘Performance’、‘Weighted’ 或 ‘Priority’。

-	RelativeDnsName：指定由此流量管理員設定檔所提供的相對 DNS 名稱。這個值會與由 Azure 流量管理員使用的 DNS 網域名稱結合，形成設定檔的完整網域名稱 (FQDN)。例如，’contoso’ 的值會提供流量管理員設定檔完整的名稱 ‘contoso.trafficmanager.net’。

-	TTL：指定 DNS 存留時間 (TTL)，以秒為單位。這會通知本機 DNS 解析程式和 DNS 用戶端快取此流量管理員設定檔所提供之 DNS 回應的時間長度。

-	MonitorProtocol：指定要用來監視端點健全狀況的通訊協定。可能的值為 ‘HTTP’ 和 ‘HTTPS’。

-	MonitorPort：指定用來監視端點健全狀況的 TCP 連接埠。

-	MonitorPath：指定用來探查端點健全狀況之端點網域名稱的相對路徑。

Cmdlet 在 Azure 流量管理員中建立流量管理員設定檔，並傳回對應的設定檔物件。此時，設定檔不包含任何端點，請參閱 [更新流量管理員設定檔](#update-a-traffic-manager-profile)以取得有關如何新增端點到流量管理員設定檔的詳細資料。

## 取得流量管理員設定檔

若要擷取現有的流量管理員設定檔物件，請使用 Get-AzureTrafficManagerProfle Cmdlet：

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

此 Cmdlet 會傳回流量管理員設定檔物件。

## 更新流量管理員設定檔 [](#update-traffic-manager-profile)

修改流量管理員設定檔 (例如新增或移除端點，或是修改設定檔設定) 時，請遵循以下的 3 步驟程序：

1.	使用 Get-AzureTrafficManagerProfile 擷取設定檔 (或使用 New-AzureTrafficManagerProfile 所傳回的設定檔)。

2.	透過新增端點、移除端點、變更端點參數或是變更設定檔參數來修改設定檔。這些變更是離線的 — 只有本機物件會顯示設定檔已經變更。

3.	請使用 Set-AzureTrafficManagerProfile Cmdlet 認可您的變更。這會使用提供的設定檔取代 Azure 流量管理員中現有的設定檔。

下面的範例可進一步解釋此功能：

### 新增端點到設定檔

使用 ‘Add-AzureTrafficManagerEndpointConfig’ Cmdlet 可新增端點到流量管理員設定檔：

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Add-AzureTrafficManagerEndpointConfig 的參數如下所示：

- EndpointName：端點的名稱。相同設定檔中的端點必須具有不同的名稱。這是用來在服務管理作業期間參照端點的名稱，並不是端點的 DNS 名稱。

-	TrafficManagerProfile：要新增端點的流量管理員設定檔物件。

-	Type：流量管理員端點類型。目前，只有 ‘ExternalEndpoint’ 類型透過 ARM API 支援 (請參閱[預覽限制](#preview-limitations))。

-	Target：端點的完整 DNS 名稱。流量管理員會在 DNS 回應中回傳這個值，以將流量導向到此端點。

-	EndpointStatus：指定端點的狀態。如果端點已啟用，將會探查端點健全狀況，並包含在流量路由方法中。可能的值為 ‘Enabled’ 或 ‘Disabled’。

-	Weight：指定指派至端點的權數。只有在流量管理員設定檔設定為使用「加權」流量路由方法時才會使用。可能的值為 1 到 1000。

-	Priority：當使用「優先」流量路由方法時指定此端點的優先權。Priority 必須在 1...1000 的範圍內。較低的值表示較高的優先權。

-	EndpointLocation：指定外部端點的位置，用來與「效能」流量路由方法搭配使用。若要取得可能位置的清單，請參閱 Get-AzureLocation。

EndpointStatus、Weight 和 Priority 是選擇性的參數。如果省略，PowerShell 將不會傳遞它們，且伺服器端會套用預設值。

### 從設定檔移除端點

若要從設定檔移除端點，請使用 ‘Remove-AzureTrafficmanagerEndpointConfig’，指定要移除的端點名稱：

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

新增或移除端點的作業順序也可以「輸送」，透過管道而非作為參數來傳送設定檔物件。例如：

	PS C:> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### 變更設定檔或端點的設定

設定檔和端點參數都可以離線變更，並使用 Set-AzureTrafficManagerProfile 認可變更。唯一的例外是設定檔 RelativeDnsName 無法在設定檔建立後變更 (若要變更這個值，請刪除然後重新建立設定檔)。例如，若要變更設定檔 TTL 和第一個端點的狀態：

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> $profile.Ttl = 300
	PS C:> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### 刪除流量管理員設定檔
若要刪除流量管理員設定檔，請使用 Remove-AzureTrafficManagerProfile Cmdlet，指定設定檔名稱和資源群組名稱：

	PS C:> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

這個 Cmdlet 會提示進行確認。選擇性的 ’-Force’ 參數可用來隱藏這個提示。要刪除的設定檔也可以使用設定檔物件來指定：

	PS C:> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

也可輸送以下順序：

	PS C:> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## 另請參閱

[流量管理員概觀](traffic-manager-overview.md)

[Azure Cmdlet 使用者入門](https://msdn.microsoft.com/library/jj554332.aspx)
 

<!---HONumber=July15_HO2-->