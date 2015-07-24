<properties
	pageTitle="開始使用 Azure 金鑰保存庫 | 概觀"
	description="使用本教學課程可協助您開始使用 Azure 金鑰保存庫，進而在 Azure 中建立強化的容器，以儲存及管理 Azure 中的密碼編譯金鑰和密碼。"
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="cabailey"/>

# 開始使用 Azure 金鑰保存庫 #

## 簡介  
使用本教學課程可協助您開始使用 Azure 金鑰保存庫 (目前為預覽狀態)，進而在 Azure 中建立強化的容器 (保存庫)，以儲存及管理 Azure 中的密碼編譯金鑰和密碼。本教學課程將逐步引導您完成使用 Windows PowerShell 建立包含金鑰或密碼 (稍後可用於 Azure 應用程式) 的保存庫程序。接著，它會說明應用程式後續可以如何使用該金鑰或密碼。

**預估完成時間：**20 分鐘

>[AZURE.NOTE]本教學課程沒有指示說明如何撰寫其中一個步驟所包括的 Azure 應用程式，但會顯示如何授權應用程式使用金鑰保存庫中的金鑰或密碼。
>
>在預覽期間，您無法在 Azure 入口網站中設定 Azure 金鑰保存庫。請改用這些 Azure PowerShell 指示。

如需 Azure 金鑰保存庫的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## 必要條件

若要完成本教學課程，您必須具備下列項目：

- Microsoft Azure 訂用帳戶。如果您沒有訂用帳戶，您可以註冊[免費試用](../../../pricing/free-trial)。
- Azure PowerShell 0.9.1 版或更新版本。若要安裝最新版本，並將它與 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。
- 可設定使用您在本教學課程中所建立之金鑰或密碼的應用程式。您可以在 [Microsoft 下載中心](http://www.microsoft.com/zh-tw/download/details.aspx?id=45343)找到範例應用程式。如需相關指示，請參閱隨附的讀我檔案。


本教學課程是專為 Windows PowerShell 初學者所設計的，但它會假設您已了解基本概念，例如模組、Cmdlet 和工作階段。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx) (英文)。

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

	Get-Help <cmdlet-name> -Detailed

例如，如需取得 Add-AzureAccount Cmdlet 的說明，請輸入：

	Get-Help Add-AzureAccount -Detailed

您也可以閱讀下列教學課程，以熟悉 Windows PowerShell 中的 Azure 資源管理員：

- [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)
- [將 Windows PowerShell 與資源管理員搭配使用](../powershell-azure-resource-manager.md)


## <a id="connect"></a>連線到您的訂用帳戶 ##

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

    Add-AzureAccount

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。Windows PowerShell 會取得與此帳戶相關聯的所有訂用帳戶，並依預設使用第一個訂用帳戶。

如果您有多個訂用帳戶，並想要指定其中一個訂用帳戶供 Azure 金鑰保存庫使用，請輸入下列內容以查看您帳戶的訂用帳戶：

    Get-AzureSubscription

接著，若要指定要使用的訂用帳戶，請輸入：

    Select-AzureSubscription -SubscriptionName <subscription name>

如需設定 Azure PowerShell 的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

## <a id="switch"></a>切換成使用 Azure 資源管理員 ##

金鑰保存庫 Cmdlet 需有 Azure 資源管理員，因此請輸入下列內容以切換至 Azure 資源管理員模式：

	Switch-AzureMode AzureResourceManager

## <a id="resource"></a>建立新的資源群組 ##

使用 Azure 資源管理員時，會在資源群組中建立所有相關資源。在本教學課程中，我們將建立名為 'ContosoResourceGroup' 的新資源群組：

	New-AzureResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'

在 -Location 參數中，請使用 [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn654582.aspx) 命令，以了解如何針對本範例中的位置指定替代位置。如果您需要更多資訊，請輸入：`Get-Help Get-AzureLocation`


## <a id="vault"></a>建立金鑰保存庫 ##

使用 [New-AzureKeyVault](https://msdn.microsoft.com/library/azure/dn903602.aspx) Cmdlet 來建立金鑰保存庫。這個 Cmdlet 包含三個必要參數：**資源群組名稱**、**金鑰保存庫名稱**和**地理位置**。

例如，如果使用保存庫名稱為 **ContosoKeyVault**、資源群組名稱為 **ContosoResourceGroup** 及位置為**東亞**，請輸入：

    New-AzureKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

此 Cmdlet 的輸出會顯示您剛剛建立的金鑰保存庫屬性。兩個最重要屬性是：

- **名稱**：在此範例中是 ContosoKeyVault。您將在其他金鑰保存庫 Cmdlet 中使用此名稱。
- **vaultUri**：在此範例中是 https://contosokeyvault.vault.azure.net/。透過其 REST API 使用保存庫的應用程式必須使用此 URI。

您的 Azure 帳戶現已取得在此金鑰保存庫上執行任何作業的授權。而且，沒有其他人有此授權。

## <a id="add"></a>新增金鑰或密碼至金鑰保存庫 ##

如果您要 Azure 金鑰保存庫為您建立一個軟體防護金鑰，請使用 [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) Cmdlet，並輸入下列內容：

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'


不過，如果您的 C:\ 槽中已儲存一個包含現有軟體防護金鑰，且檔案名稱為 softkey.pfx 的 .PFX 檔案，且您想要將它上傳至 Azure 金鑰保存庫，則請輸入下列內容來設定 **securepfxpwd** 變數，以將 .PFX 檔案的密碼設為 **123**：

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

接著，輸入下列內容從 .PFX 檔案匯入金鑰，如此一來便會使用金鑰保存庫服務中的軟體來保護金鑰：

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


透過使用其 URI，您現在可以參照您所建立或上傳至 Azure 金鑰保存庫的金鑰。例如：**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 

若要顯示此金鑰的 URI，請輸入：

	$Key.key.kid

若要將名為 SQLPassword 且其 Azure 金鑰保存庫的值為 Pa$$w0rd 的密碼新增至保存庫，首先您必須輸入下列內容來將 Pa$$w0rd 值轉換成安全字串：

	$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

接著輸入下列內容：

	$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

透過使用其 URI，您現在可以參照您新增至 Azure 金鑰保存庫的密碼。例如：**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**

若要顯示此密碼的 URI，請輸入：

	$secret.Id

我們來檢視一下剛剛建立的金鑰或密碼：

- 若要檢視您的金鑰，請輸入：`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- 若要檢視您的密碼，請輸入：`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

現在，您可以在應用程式中使用金鑰保存庫和金鑰或密碼。您必須授權應用程式才能使用他們。

## <a id="register"></a>向 Azure Active Directory 註冊應用程式 ##

這步驟通常會由開發人員在個別電腦上完成。這並非 Azure 金鑰保存庫的特有狀況，在此列出是為了讓程式完整。


>[AZURE.IMPORTANT]若要完成本教學課程，您的帳戶、保存庫及將在本步驟中註冊的應用程式全都必須位於相同的 Azure 目錄中。

使用金鑰保存庫的應用程式必須使用 Azure Active Directory 的權杖進行驗證。若要達到此目的，應用程式擁有者首先必須在其 Azure Active Directory 中註冊該應用程式。註冊結束時，應用程式擁有者會取得下列值：


- **應用程式識別碼** (亦稱為用戶端識別碼) 和**驗證金鑰** (亦稱為共用密碼)。應用程式必須向 Azure Active Directory 出示這兩個值才能取得權杖。如何設定應用程式執行此作業會取決於應用程式。在金鑰保存庫範例應用程式中，應用程式擁有者會在 app.config 檔案中設定這些值。



在 Azure Active Directory 中註冊應用程式：

1. 登入 Azure 入口網站。
2. 按一下左側的 [**Active Directory**]，然後選取您將要註冊應用程式的目錄。<br> <br> 注意：您必須選取包含用來建立金鑰保存庫之 Azure 訂用帳戶的相同目錄。如果您不知道是哪個目錄，請按一下 [**設定**]，找出建立金鑰保存庫所用的訂用帳戶，並記下最後一欄中顯示的目錄名稱。

3. 按一下 [**應用程式**]。如果您的目錄中尚未新增任何應用程式，則此頁面將僅會顯示 [**新增應用程式**] 連結。按一下此連結，或者您可以按一下命令列上的 [**新增**]。
4.	在 [**新增應用程式**] 精靈的 [**您想做什麼？**] 頁面上，按一下 [**新增我的組織正在開發的應用程式**]。
5.	在 [**告訴我們您的應用程式**] 頁面上，指定您的應用程式名稱，並選取 [**WEB 應用程式和/或 WEB API**] (預設值)。按 [下一步] 圖示。
6.	在 [**應用程式屬性**] 頁面上，為您的 Web 應用程式指定 [**登入 URL**] 和 [**應用程式識別碼 URI**]。如果您的應用程式沒有這些值，您可以在此步驟中虛構這些值 (例如，您可以在這兩個方塊中指定 http://test1.contoso.com)。這些網站是否存在並沒有影響；重要的是目錄中每個應用程式的應用程式識別碼 URI 都會有所不同。目錄會使用此字串來識別您的應用程式。
7.	按一下 [完成] 圖示以在精靈中儲存變更。
8.	在 [快速入門] 頁面上，按一下 [**設定**]。
9.	捲動到 [**金鑰**] 區段，選取持續時間，然後按一下 [**儲存**]。頁面會重新整理，並顯示金鑰值。您必須使用此金鑰值和 [**用戶端識別碼**] 值來設定您的應用程式。(有關此設定的指示僅適用於特定應用程式。)
10.	複製此頁面的用戶端識別碼，您將在後續步驟中使用此識別碼來設定保存庫上的權限。




## <a id="authorize"></a>授權應用程式使用金鑰或密碼 ##


若要授權應用程式存取保存庫中的金鑰或密碼，請使用 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) Cmdlet。

例如，如果您的保存庫名稱是 ContosoKeyVault，且您要授權的應用程式具有 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed 的用戶端識別碼，您想要授權應用程式使用保存庫中的金鑰來進行解密並簽署，則請執行下列作業：

	Set-AzureKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign



## <a id="HSM"></a>如果您想要使用硬體安全模組 (HSM) ##

為了加強保證，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。HSM 已獲得 FIPS 140-2 Level 2 認證。如果此需求對您不適用，請略過本節並移至[刪除金鑰保存庫及相關聯的金鑰和密碼](#delete)。

若要建立這些受 HSM 保護的金鑰，您必須具備[支援受 HSM 保護之金鑰的保存庫訂用帳戶](../../../pricing/free-trial)。


建立保存庫時，請新增 'SKU' 參數：

	New-AzureKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'

您可以將軟體防護金鑰 (如稍早所示) 和受 HSM 保護的金鑰新增至此保存庫。若要建立受 HSM 保護的金鑰，請將 [目的地] 參數設為 'HSM'：

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

您可以使用下列命令，來從電腦上的 .PFX 檔案匯入金鑰。此命令會將金鑰匯入金鑰保存庫服務中的 HSM：

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'

下一個命令會匯入「自備金鑰」(BYOK) 封包。這可讓您在您的本機 HSM 中產生金鑰，且在金鑰無需離開 HSM 界限的情況下，即可將它傳輸到金鑰保存庫服務中的 HSM：

	$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

如需有關如何產生此 BYOK 封包的詳細指示，請參閱[如何使用 Azure 金鑰保存庫中受 HSM 保護的金鑰](https://msdn.microsoft.com/library/azure/dn903624.aspx)。

## <a id="delete"></a>刪除金鑰保存庫及相關聯的金鑰和密碼 ##

如果您不再需要金鑰保存庫及其所包含的金鑰或密碼，您可以使用 [Remove-AzureKeyVault](https://msdn.microsoft.com/library/azure/dn903603.aspx) Cmdlet 來將金鑰保存庫刪除：

	Remove-AzureKeyVault -VaultName 'ContosoKeyVault'

或者，您可以刪除整個 Azure 資源群組，其中包括金鑰保存庫和您加入該群組的任何其他資源：

	Remove-AzureResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>其他 Azure PowerShell Cmdlet ##

可能有助於管理 Azure 金鑰保存庫的其他命令：

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`此命令會取得以表格形式顯示的所有金鑰和所選屬性。
- `$Keys[0]`此命令會顯示特定金鑰的完整屬性清單。
- `Get-AzureKeyVaultSecret`此命令會列出以表格形式顯示的所有密碼名稱和所選屬性。
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`：如何移除特定金鑰範例。
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`：如何移除特定密碼範例。






## <a id="next"></a>接續步驟 ##

如需在 Web 應用程式中使用 Azure 金鑰保存庫的的教學課程，請參閱[從 Web 應用程式使用 Azure 金鑰保存庫](key-vault-use-from-web-application.md)

如需 Azure 金鑰保存庫的 Windows PowerShell Cmdlet 清單，請參閱 [Azure 金鑰保存庫 Cmdlet](https://msdn.microsoft.com/library/azure/dn868052.aspx)。

如需程式設計參考，請參閱 [Azure 金鑰保存庫 REST API 參考](https://msdn.microsoft.com/library/azure/dn903609.aspx)和 [Azure 金鑰保存庫 C# 用戶端 API 參考](https://msdn.microsoft.com/library/azure/dn903628.aspx)。
 

<!---HONumber=62-->