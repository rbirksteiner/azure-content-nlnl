<properties
	pageTitle="保護 Azure App Service 中的 Web 應用程式"
	description="了解如何保護 Azure Web 應用程式的安全。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>


#保護 Azure App Service 中的 Web 應用程式

開發 Web 應用程式的其中一個挑戰是如何提供客戶安全無虞的服務。在本文中，您將了解可保護 Web 應用程式的 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 功能。

> [AZURE.NOTE]有關 Web 架構應用程式安全性考量的詳細討論超出本文件的範圍。請參閱[開放 Web 軟體安全計劃 (Open Web Application Security Project，OWASP)](https://www.owasp.org/index.php/Main_Page) (英文)，作為保護 Web 應用程式的進一步指引起點，尤其是[十大專案](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) (英文)，其中列出由 OWASP 成員所確認的目前十大主要的 Web 應用程式安全性錯誤。

##<a name="https"></a> 保護通訊的安全

如果您使用為 Web 應用程式建立的 ***.azurewebsites.net** 網域名稱，就可立即使用 HTTPS，因為 SSL 憑證是針對所有 ***.azurewebsites.net** 網域名稱而提供。如果您的網站使用[自訂網域名稱](web-sites-custom-domain-name.md)，您可以上傳 SSL 憑證以為自訂網域[啟用 HTTPS](web-sites-configure-ssl-certificate.md)。

##<a name="develop"></a> 保護開發的安全

### 發行設定檔和發行設定

在開發應用程式及使用公用程式，例如 **Visual Studio**、**Web Matrix**、**Azure PowerShell** 或 **Azure 命令列介面 (Azure CLI)** 執行管理工作或自動化工作時，您可以使用「發佈設定」檔案或「發行設定檔」。這兩個選項皆會向 Azure 驗證您的身分，且應已設定安全性防止未經授權的存取。

* **發行設定**檔案包含

	* 您的 Azure 訂閱識別碼

	* 可讓您執行訂閱管理工作的管理憑證，*而無需提供帳戶名稱或密碼*。

* **發行設定檔**檔案包含

	* 發行至 Web 應用程式的資訊

如果使用透過發行設定或發行設定檔的公用程式，請將包含發行設定或設定檔的檔案匯入公用程式，然後將此檔案**刪除**。如果您必須保留此檔案，以與其他人 (例如參與專案的人員) 共用，請將它儲存在安全位置 (例如具有限制權限的**加密**目錄)。

另外，您應確保匯入的憑證已受到保護。例如，**Azure PowerShell** 和 **Azure 命令列介面 (Azure CLI)** 會將匯入資訊儲存於「主目錄」 (在 Linux 或 OS X 系統上是 *~*，在 Windows 系統上是 */users/yourusername*)。 如需額外的安全性，您可能會使用適用於作業系統的加密工具來**加密**這些位置。

### 組態設定和連接字串
將連接字串、驗證憑證和其他敏感資訊儲存於組態檔中是一種常見的做法。然而，這些檔案可能會暴露在您的網站上，或簽入公開的儲存機制，因而公開此資訊。

Azure App Service 可讓您將設定資訊以「應用程式設定」與「連接字串」的形式，儲存為 Web Apps 執行階段環境的一部分。這些值會在執行階段，透過大多數程式設計語言的*環境變數*，來向您的應用程式公開。若是 .NET 應用程式，則這些值會在執行階段加入您的 .NET 組態。

「應用程式設定」與「連接字串」可使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)或公用程式 (例如 PowerShell 或 Azure CLI) 進行設定。

如需應用程式設定和連接字串的詳細資訊，請參閱[設定 Web 應用程式](web-sites-configure.md)。

### FTPS

Azure 針對 Web 應用程式的檔案系統，提供透過 **FTPS** 的安全 FTP 存取權限。這可讓您以安全的方式存取 Web 應用程式上的應用程式程式碼及診斷記錄。您可以在 [Azure 管理入口網站](https://manage.windowsazure.com)的 [儀表板] 頁面中找到 Web 應用程式的 FTPS 連結。

如需 FTPS 的詳細資訊，請參閱[檔案傳輸通訊協定](http://en.wikipedia.org/wiki/File_Transfer_Protocol)。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 後續步驟

如需 Azure 平台的安全性、報告**安全性事件或不當使用**、或通知 Microsoft 您即將執行網站的**滲透測試**等詳細資訊，請參閱 [Microsoft Azure 信任中心](http://azure.microsoft.com/support/trust-center/security/)的安全性區段。

如需 Web 應用程式的 **web.config** 或 **applicationhost.config** 檔案詳細資訊，請參閱[已在 Azure App Service Web Apps 中解除鎖定的設定選項](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/) (英文)。

如需記錄 Web 應用程式資訊的詳細資訊 (此資訊可能對偵測攻擊很有幫助)，請參閱[啟用診斷記錄](web-sites-enable-diagnostic-log.md)。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->