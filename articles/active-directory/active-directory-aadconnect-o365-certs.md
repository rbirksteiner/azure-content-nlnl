<properties 
	pageTitle="Office 365 和 Azure AD 使用者的憑證續約指引。"
	description="本文說明 Office 365 使用者如何解決收到電子郵件通知續約憑證的問題。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>


# 續約 Office 365 和 Azure AD 的同盟憑證

如果您收到一封電子郵件或入口網站通知，要求您續約 Office 365 的憑證，本文可協助您解決問題並防止這個情形再次發生。本文假設您使用 AD FS 做為同盟伺服器。

## 請檢查您是否需要執行任何動作

如果您使用 AD FS 2.0 或更新版本，Office 365 和 Azure AD 在您的憑證到期之前會自動進行更新。您不需要執行任何手動步驟，或排程執行指令碼。如果要成功，下列兩個預設的 AD FS 組態設定必須生效：

- AD FS 屬性 AutoCertificateRollover 必須設定為 True，表示 AD FS 會在舊的憑證到期之前，自動產生新的權杖簽署和權杖解密憑證。
	- 如果值為 False，您就是使用自訂憑證設定。請到[這裡](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)取得完整的指引。
- 您的同盟中繼資料必須能夠使用公用網際網路。
	
	檢查方法如下：

	- 在主要同盟伺服器的 PowerShell 命令視窗執行下列命令，確認 AD FS 安裝使用自動憑證變換：

	`PS C:\> Get-ADFSProperties`

(請注意，如果您使用 AD FS 2.0，您必須先執行 Add-Pssnapin Microsoft.Adfs.Powershell)。

從公用網際網路 (離開公司網路) 的電腦瀏覽到下列 URL 檢查同盟中繼資料是否可公開存取：


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

將其中的 `(your_FS_name) ` 取代為您的組織使用的同盟服務主機名稱，例如 fs.contoso.com。如果您能夠成功確認上述兩個設定，您就不必執行任何動作。

範例：https://fs.contos.com/federationmetadata/2007-06/federationmetadata.xml

## 如果 AutoCertificateRollover 屬性設定為 False

如果 AutoCertificateRollover 屬性設定為 False，您就是使用非預設的 AD FS 憑證設定。最常見的原因是您的組織管理從組織的憑證授權單位註冊的 AD FS 憑證。在此情況下，您就必須自行續訂並更新您的憑證。請使用[這裡](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)的指引來進行。

## 如果您的中繼資料無法公開存取
如果 AutocertificateRollover 設定為 True，但同盟中繼資料無法公開取得，請使用下列程序來確保您的憑證可同時在內部部署和在雲端中更新：

### 確認您的 AD FS 系統已產生新的憑證。 

- 確認您已登入主要 AD FS 伺服器。
- 開啟 PowerShell 命令視窗並執行下列命令，檢查 AD FS 中目前的簽署憑證： 

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(請注意，如果您使用 AD FS 2.0，您必須先執行 Add-Pssnapin Microsoft.Adfs.Powershell)


- 查看命令輸出中所列的任何憑證。如果 AD FS 已產生新的憑證，您應該會在輸出中看到兩個憑證：一個 IsPrimary 值是 True，而 NotAfter 日期是 5 天內，另一個 IsPrimary 是 False，而 NotAfter 大約在未來一年。
	
- 如果您只看到一個憑證，而 NotAfter 日期為 5 天內，則您必須執行下列步驟產生新的憑證。

- 若要產生新憑證，請在 PowerShell 命令提示字元中執行下列命令：`PS C:\>Update-ADFSCertificate –CertificateType token-signing`。

- 再次執行下列命令驗證更新：PS C:\>Get-ADFSCertificate –CertificateType token-signing
- 接下來，若要手動更新 Office 365 同盟信任內容，請依照下列步驟。

現在應該會列出兩個憑證，一個的 NotAfter 日期大約在未來一年，且 IsPrimary 值是 False。


### 依照下列步驟，手動更新 Office 365 同盟信任內容。

1.	開啟適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組。
2.	執行 $cred=Get-Credential。當此 Cmdlet 提示您輸入認證時，請輸入您的雲端服務系統管理員帳戶認證。
3.	執行 Connect-MsolService –Credential $cred。此 Cmdlet 可讓您連線到雲端服務。在您執行由工具安裝的任何其他 Cmdlet 之前，必須先建立讓您連線到雲端服務的環境。
4.	如果您不是在 AD FS 主要同盟伺服器的電腦上執行這些命令，請執行 Set-MSOLAdfscontext -Computer <AD FS primary server>，其中 <AD FS primary server> 是主要 AD FS 伺服器的內部 FQDN 名稱。此 Cmdlet 會建立讓您連線到 AD FS 的環境。 
5.	執行 Update-MSOLFederatedDomain –DomainName <domain>。此 Cmdlet 會將 AD FS 的設定更新成雲端服務，並設定兩者之間的信任關係。

>[AZURE.NOTE]如果您需要支援多個頂層網域，例如 contoso.com 和 fabrikam.com，則您使用任何 Cmdlet 時必須搭配使用 SupportMultipleDomain 參數。如需詳細資訊，請參閱〈支援多個頂層網域〉。最後，確定使用 [Windows Server 2014 年 5 月](http://support.microsoft.com/kb/2955164)彙總套件來更新所有 Web 應用程式 Proxy 伺服器，否則 Proxy 可能無法使用新的憑證自行更新，而導致服務中斷。

<!---HONumber=August15_HO9-->