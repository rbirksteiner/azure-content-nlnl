<properties
	pageTitle="如何安裝和設定 Azure PowerShell"
	description="了解如何安裝和設定 Azure PowerShell。"
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# 如何安裝和設定 Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

您可以使用 Windows PowerShell (不論是透過在命令提示字元中互動輸入，還是透過指令碼自動執行) 在 Azure 中執行各種工作。Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。您可以使用 Cmdlet 來建立、測試、部署和管理透過 Azure 平台傳遞的解決方案和服務。您可透過 Azure 管理入口網站執行的工作，大多也可使用 Cmdlet 來執行。例如，您可以建立和設定雲端服務、虛擬機器、虛擬網路和 Web 應用程式。

此模組是以可下載檔案的形式散布，且原始程式碼是透過公開可用的存放庫受到管理。本主題稍後的安裝指示將提供可下載檔案的連結。如需原始程式碼的詳細資訊，請參閱 [Azure PowerShell 程式碼存放庫](https://github.com/Azure/azure-powershell)。

本指南提供關於安裝和設定 Azure PowerShell 來管理 Azure 平台的基本資訊。

### <a id="Prereq"></a>使用 Azure PowerShell 的先決條件

Azure 是訂用帳戶型平台。這表示您需要有訂閱才能使用平台。在大部分情況下，這也表示 Cmdlet 需要有訂閱資訊，才能透過您的訂閱來執行工作(有些儲存體相關 Cmdlet 不需要有這些資訊就能使用。) 為了提供這些資訊，您必須設定電腦來連線至您的訂閱。本文下方有相關指示：「如何：連線到您的訂用帳戶。」

> [AZURE.NOTE]自 0.8.5 版開始，Azure PowerShell 模組需要 Microsoft .NET Framework 4.5。

安裝此模組時，安裝程式會檢查系統中是否有必要軟體並且安裝所有相依性，例如正確版本的 Windows PowerShell 和 .NET Framework。

<a id="Install"></a>
## 如何：安裝 Azure PowerShell

您可以執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376) 來下載和安裝 Azure PowerShell 模組。出現提示時，請按一下 [執行]。Web Platform Installer 會安裝 Azure PowerShell 模組和所有相依項目。請依照提示來完成安裝。

> [AZURE.NOTE]如果您只想要下載 PowerShell 安裝程式，請瀏覽 https://github.com/Azure/azure-powershell/releases。PowerShell Cmdlet 的原始程式碼也可以在此儲存機制中找到

如需 Azure 之可用命令列工具的詳細資訊，請參閱[命令列工具](http://go.microsoft.com/fwlink/?LinkId=320796)。

安裝此模組也會安裝 Azure PowerShell 的自訂主控台。您可以從標準 Windows PowerShell 主控台或 Azure PowerShell 主控台執行 Cmdlet。

開啟這些主控台的方法視您正在執行的 Windows 版本而定。

- 在至少執行 Windows 8 或 Windows Server 2012 的電腦上，可使用內建的 [搜尋]。在 [開始] 畫面上開始輸入 **power**。如此會傳回有限範圍的應用程式清單，包括 Windows PowerShell 和 Azure PowerShell。若要開啟主控台，請按一下任一應用程式。(若要將應用程式固定到 [開始] 畫面，請在圖示上按一下滑鼠右鍵)。

- 在執行比 Windows 8 或 Windows Server 2012 更舊版本的電腦上，使用 [開始] 功能表。從 [開始] 功能表，依序按一下 [所有程式]、[Azure] 及 [Azure PowerShell]。

<a id="Connect"></a>
## 如何：連線至您的訂用帳戶

要使用 Azure 就必須有訂閱。如果您沒有訂閱，請參閱[開始使用 Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795)。

Cmdlet 需要有您的訂閱，才能用來管理您的服務。有兩種方法可提供訂閱資訊給 Windows PowerShell。您可以使用包含這些資訊的管理憑證，或者使用您的 Microsoft 帳戶或公司或學校帳戶登入 Azure。登入時，Azure Active Directory (Azure AD) 會驗證認證，並傳回存取權杖，讓 Azure PowerShell 可管理您的帳戶。

為協助您選擇最符合自身需求的驗證方法，請考慮下列事項：

- Azure AD 是建議的驗證方法，因為使用這個方法可更輕鬆地管理對訂閱的存取。透過 0.8.6 版的更新，如果使用公司或學校帳戶，也可啟用 Azure AD 驗證的自動化案例。另外也適用於 Azure 資源管理員 API。
- 使用憑證方法時，只要訂閱和憑證有效，即可使用訂閱資訊。不過，此方法會讓您更難以管理對共用訂閱的存取，例如當有多個人獲授權來存取帳戶時。此外，Azure 資源管理員 API 不接受憑證驗證。

如需 Azure 中之驗證與訂閱管理的詳細資訊，請參閱[管理帳戶、訂閱和系統管理角色](http://go.microsoft.com/fwlink/?LinkId=324796)。

### 使用 Azure AD 方法

1. 依照[如何：安裝 Azure PowerShell](#Install) 的操作方法，開啟 Azure PowerShell 主控台。

2. 輸入以下命令：

		Add-AzureAccount

3. 在視窗中，輸入與您的帳戶相關聯的電子郵件地址和密碼。

4. Azure 會驗證並儲存認證資訊，然後關閉視窗。

5. 自 0.8.6 起，如果您使用公司或學校帳戶登入，則可以輸入下列命令略過快顯視窗。這會快顯標準 Windows PowerShell 認證視窗，以供您輸入公司或學校帳戶的使用者名稱和密碼。

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	> [AZURE.NOTE]如需有關安全性和使用認證的詳細資訊，請參閱[將密碼和其他機密資料部署到 ASP.NET 和 Azure 網站的最佳作法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)。

	> [AZURE.NOTE]此非互動式登入方法僅適用於公司或學校帳戶。公司或學校帳戶是由您的公司或學校所管理的使用者，並為您的公司或學校定義於 Azure Active Directory 執行個體中。如果您目前沒有公司或學校帳戶，而是使用 Microsoft 帳戶登入您的 Azure 訂閱，則您可以透過下列步驟輕鬆地建立帳戶。
	>
	> 1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)，然後按一下 [Active Directory]。
	>
	> 2. 如果不存在任何目錄，請選取 [Create your directory] 並提供要求的資訊。
	>
	> 3. 選取您的目錄並新增使用者。這個新的使用者可以使用公司或學校帳戶登入。
	>
	>     在建立使用者期間，系統會提供您該使用者的電子郵件地址與臨時密碼。請儲存此資訊，其他步驟中將會使用該資訊。
	>
	> 4. 從管理入口網站，選取 [設定]，然後選取 [管理員]。選取 [新增]，然後將新使用者新增為共同管理員。這麼做可讓公司或學校帳戶管理您的 Azure 訂閱。
	>
	> 5. 最後，登出 Azure 入口網站，然後使用公司或學校帳戶重新登入。如果這是您第一次使用此帳戶登入，系統會提示您變更密碼。
	>
	>如需使用公司或學校帳戶註冊 Microsoft Azure 的詳細資訊，請參閱[以組織身分註冊 Microsoft Azure](sign-up-organization.md)。

### 使用憑證方法

Azure 模組包含可協助您下載和匯入憑證的 Cmdlet。

> [AZURE.NOTE]AzureResourceManager 模組中的 Cmdlet 需要 Azure AD 方法 (Add-AzureAccount)。這些 Cmdlet 不支援發佈設定檔。如需 AzureResourceManager 模組中 Cmdlet 的詳細資訊，請參閱 [Azure 資源管理員 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765) (英文)。


- **Get-AzurePublishSettingsFile** Cmdlet 會在 Azure 管理入口網站上開啟網頁，供您從中下載訂閱資訊。這些資訊內含於 .publishsettings 檔案中。

- **Import-AzurePublishSettingsFile** 會匯入 .publishsettings 檔案供此模組使用。此檔案包含具有安全性認證的管理憑證。

> [AZURE.IMPORTANT]建議您在匯入那些設定後，刪除您以 <b>Get-AzurePublishSettingsFile</b> 下載的發佈設定檔。管理憑證由於包含安全性認證，因此不應讓未經授權的使用者存取。如果您需要本身之訂閱的詳細資訊，可到 [Azure 管理入口網站](http://manage.windowsazure.com/)或 [Microsoft Online Services 客戶入口網站](http://go.microsoft.com/fwlink/p/?LinkId=324875)取得。

1. 使用您的 Azure 帳戶認證登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2. 依照[如何：安裝 Azure PowerShell](#Install) 的操作方法，開啟 Azure PowerShell 主控台。

3. 輸入以下命令：

		Get-AzurePublishSettingsFile

4. 出現提示時，下載並儲存發佈設定檔，並記下 .publishsettings 檔案的路徑和名稱。當您執行 **Import-AzurePublishSettingsFile** Cmdlet 來匯入設定時，需要有這些資訊。預設位置和檔案名稱格式為：

			C:\Users<UserProfile>\Download[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. 輸入類似如下的命令，將各預留位置替換成您的 Windows 帳戶名稱以及檔案路徑加名稱：

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE]如果您在匯入自己的發佈設定之後，被加到其他訂閱中成為共同管理員，則需要重複此程序來下載新的 .publishsettings 檔，然後再匯入那些設定。如需關於新增共同管理員來協助管理訂閱服務的詳細資訊，請參閱[加入及移除 Azure 訂用帳戶的共同管理員](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx)。

### 檢視帳戶與訂閱詳細資料

您可以有多個帳戶和訂閱供 Azure PowerShell 使用。您可以執行 Add-AzureAccount 多次來新增多個帳戶。

若要取得可用的 Azure 帳戶，請輸入：

	Get-AzureAccount

若要取得您的 Azure 訂閱，請輸入：

	Get-AzureSubscription

## <a id="Ex"></a>如何使用 Cmdlet：範例 ##

安裝這個模組並設定電腦來連線至您的訂閱後，您可以建立一個 Azure Web 應用程式。這個範例協助您開始使用 Azure Cmdlet。

1. 啟動 Azure PowerShell 主控台。

2. 選擇您的 Web 應用程式的名稱。請選取符合 DNS 命名慣例的名稱。有效的名稱只能包含字母 'a' 到 'z'、數字 '0' 到 '9' 和連字號 ('-')。

	Web 應用程式名稱在 Azure 中必須唯一。我們將在這個範例中使用 "mySite"，但請務必選擇其他名稱，例如您的帳戶名稱接著一個數字。

	挑選名稱後，輸入類似如下的命令。以您的 Web 應用程式名稱替代 "mySite"。

		New-AzureWebsite mySite

	Cmdlet 會建立 Web 應用程式，然後傳回代表新 Web 應用程式的物件。物件屬性包含有關 Web 應用程式的實用資訊。

3. 若要取得 Web 應用程式的相關資訊，請輸入下列命令。該命令會傳回訂閱中所有 Web 應用程式的一些資訊，包括您剛建立的網站。

		Get-AzureWebsite

4. 若要取得 Web 應用程式的詳細資訊，請在命令中包含 Web 應用程式名稱。請務必以您的 Web 應用程式名稱替代 "mySite"。

		Get-AzureWebsite -Name mySite

5. Web 應用程式在建立後就會啟動。若要停止 Web 應用程式，請輸入下列包含您 Web 應用程式名稱的命令。

		Stop-AzureWebsite -Name mySite

6. 若要確認網站的狀態為 'stopped'，請再次執行 Get-AzureWebsite 命令。

		Get-AzureWebsite

7. 若要完成這項測試，請刪除 Web 應用程式。輸入：

		Remove-AzureWebsite -Name mySite

7. 若要完成這項工作，請確認已刪除 Web 應用程式。

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>取得說明##

下列資源提供特定 Cmdlet 的說明：


-   從主控台中，您可以使用內建的說明系統。**Get-Help** Cmdlet 可用來存取此系統。下表提供可用於取得說明的部分命令範例。您可以在主控台中輸入 **help** 取得更多資訊。

    <table border="1" cellspacing="4" cellpadding="4">
<tbody>
<tr align="left" valign="top">
	<td><b>命令</b></td>
	<td><b>結果</b></td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help</td>
	<td>說明如何使用說明系統。<p><b>注意</b>：此說明包含不適用於 Azure 模組之說明檔案的部分相關資訊。具體而言，說明檔會隨此模組安裝。無法個別下載說明檔。</p>
</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help Azure</td>
	<td>取得 Azure 模組中的所有 Cmdlet。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>語言</b>>-dev</td>
	<td>取得以特定語言開發及管理應用程式的 Cmdlet。例如，help node-dev、help php-dev 或 help python-dev。</td>
</tr>
    <tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>></td>
	<td>取得有關 Windows PowerShell Cmdlet 的說明。將 <cmdlet> 取代為 Cmdlet 名稱。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Parameter *</td>
	<td>取得 Cmdlet 參數的描述。星號 (*) 表示「全部」。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Examples</td>
	<td>取得使用這個 Cmdlet 的語法和範例。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Full</td>
	<td>取得 Cmdlet 的說明，包括技術性詳細資料。</td>
</tr>
</tbody>
</table>



- 您也可以在 Azure 程式庫中取得 Azure PowerShell 模組中各 Cmdlet 的參考資訊。如需詳細資訊，請參閱 [Azure Cmdlet 參考](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx)。

如需從社群獲得說明，請試試下列熱門論壇：

- [MSDN 上的 Azure 論壇](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>其他資源 ##

有一些資源可供您用來學習如何使用 Azure 和 Windows PowerShell。

- 若要深入了解如何存取 Azure 儲存體元件，請參閱[使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)。

- 若要提供對 Cmdlet 的意見反應、報告問題或存取原始程式碼，請參閱 [Azure PowerShell 程式碼存放庫](https://github.com/WindowsAzure/azure-sdk-tools)。

- 若要深入了解 Windows PowerShell 命令列和指令碼環境，請參閱 [TechNet 指令碼中心](http://go.microsoft.com/fwlink/p/?LinkId=320211)。

- 如需有關安裝、了解、使用和自訂 Windows PowerShell 的詳細資訊，請參閱[使用 Windows PowerShell 撰寫指令碼](http://go.microsoft.com/fwlink/p/?LinkId=320210)。

- 如需指令碼的作用，以及如何在 Windows PowerShell 中執行指令碼等詳細資訊，請參閱[執行指令碼](http://go.microsoft.com/fwlink/p/?LinkId=320627)。本文包含關於建立指令碼及設定電腦來執行指令碼的基本資訊。

- 如需 Azure AD 之 Cmdlet 的詳細資訊，請參閱[使用 Windows PowerShell 管理 Azure AD](http://go.microsoft.com/fwlink/p/?LinkId=320628)。





  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx

<!---HONumber=July15_HO1-->