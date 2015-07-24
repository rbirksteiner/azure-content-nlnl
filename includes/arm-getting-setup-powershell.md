## 設定資源管理員範本的 PowerShell
 
您必須擁有正確的 Windows PowerShell 和 Azure PowerShell 版本，才能搭配使用 Azure PowerShell 與資源管理員。

### 確認 PowerShell 版本

確認您有 Windows PowerShell 3.0 或 4.0 版。若要尋找 Windows PowerShell 版本，請在 Windows PowerShell 命令提示字元中輸入這個命令。

	$PSVersionTable

您會收到下列類型的資訊：

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


確認 **PSVersion** 的值是 3.0 或 4.0。如果為否，請參閱 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)。

您也必須有 Azure PowerShell 0.9.0 版或更新版本。如果您尚未安裝和設定 Azure PowerShell，請按一下[這裡](powershell-install-configure.md)以取得指示。

您可以在 Azure PowerShell 命令提示字元下使用這個命令來檢查已安裝的 Azure PowerShell 版本。

	Get-Module azure | format-table version

您會收到下列類型的資訊：

	Version
	-------
	0.9.0

如果您沒有 0.9.0 或更新版本，則必須使用 [控制台] 中的 [程式和功能] 移除 Azure PowerShell，然後安裝最新版本。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。

### 設定 Azure 帳戶和訂用帳戶

如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或申請[免費試用](http://azure.microsoft.com/pricing/free-trial/)。

使用這個命令，開啟 Azure PowerShell 命令提示字元，並登入 Azure。

	Add-AzureAccount

如果您有多個 Azure 訂用帳戶，則可以使用這個命令列出 Azure 訂用帳戶。

	Get-AzureSubscription

您會收到下列類型的資訊：

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

您可以在 Azure PowerShell 命令提示字元下執行這些命令，設定目前 Azure 訂用帳戶。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

如需 Azure 訂用帳戶和帳戶的詳細資訊，請參閱[如何：連線至訂用帳戶](powershell-install-configure.md#Connect)。

### 切換至 Azure 資源管理員模組

若要使用 Azure 資源管理員模組，您必須從預設 Azure 命令集切換至 Azure 資源管理員命令集。執行這個命令。

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]您可以使用 **Switch-AzureMode AzureServiceManagement** 命令切換回預設命令集。

<!---HONumber=62-->