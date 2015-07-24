<properties 
	pageTitle="Azure AD Connect - 使用者登入" 
	description="Azure AD Connect 使用者登入 (用於自訂設定)。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>



# Azure AD Connect 使用者登入選項

Azure AD Connect 可讓使用者使用同一組密碼登入雲端和內部部署資源。您可以從數個不同方式中選擇啟用此設定的方式。


### 密碼同步處理
使用密碼同步處理，就可將使用者密碼的雜湊從內部部署 Active Directory 同步至 Azure AD。當密碼在內部部署中變更或重設時，新密碼會立即同步至 Azure AD，讓使用者用來存取雲端資源的密碼永遠與內部部署相同。密碼絕不會傳送至 Azure AD，也不會以未加密文字儲存在 Azure AD 中。密碼同步處理可以搭配密碼回寫功能一起使用，以啟用 Azure AD 中的自助式密碼重設功能。

<center>![雲端](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[密碼同步處理的詳細資訊](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### 使用 Windows Server 2012 R2 伺服器陣列中最新或現有 AD FS 的同盟
使用同盟登入，使用者就可以使用內部部署密碼登入 Azure AD 服務；使用公司網路時，無須再次輸入密碼就可登入服務。使用 AD FS 的同盟選項可讓您在 Windows Server 2012 R2 伺服器陣列中部署新的或指定現有的 AD FS。如果您選擇指定現有的伺服器陣列，則 Azure AD Connect 將會在伺服器陣列與 Azure AD 間設定信任，讓使用者能夠登入。

<center>![雲端](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### 若要在 Windows Server 2012 R2 中部署使用 AD FS 的同盟，您需要下列項目
如果您要部署新的伺服器陣列：

- 同盟伺服器的 Windows Server 2012 R2 伺服器。
- Web 應用程式 Proxy 的 Windows Server 2012 R2 伺服器。
- 含所想要同盟服務名稱的一個 SSL 憑證的 .pfx 檔案，例如 fs.contoso.com。

如果您要部署新的伺服器陣列，或使用現有的伺服器陣列：

- 同盟伺服器上的本機系統管理員認證。
- 在想要部署 Web 應用程式 Proxy 角色之任何工作群組 (非加入網域) 伺服器上的本機系統管理員認證。
- 執行精靈的電腦必須能夠透過 Windows 遠端管理連線到您要安裝 AD FS 或 Web 應用程式 Proxy 的任何其他電腦。

#### 使用較早版本的 AD FS 或協力廠商解決方案登入
如果您已使用較早版本的 AD FS (例如 AD FS 2.0) 或協力廠商同盟提供者設定雲端登入，就能夠透過 Azure AD Connect 選擇略過使用者登入設定。這可讓您取得最新的同步處理和 Azure AD Connect 的其他功能，同時仍使用現有的解決方案用於登入。

### 為您的組織選擇使用者登入方法
由於大部分組織只想要讓使用者登入 Office 365、SaaS 應用程式和其他 Azure AD 資源，因此建議使用預設的密碼同步處理選項。然而，部分組織有必須使用同盟登入選項 (例如 AD FS) 的特定原因。其中包含：

- 組織已部署 AD FS 或協力廠商同盟提供者
- 安全性原則禁止將密碼雜湊同步至雲端
- 要求使用者從公司網路存取加入網域電腦的雲端資源時，使用無縫式 SSO (沒有其他密碼提示)
- 您需要一些特定的 AD FS 功能
	- 使用協力廠商提供者或智慧卡的內部部署多因素驗證 (了解 Windows Server 2012 R2 中適用於 AD FS 的協力廠商 MFA 提供者)
	- Active Directory 整合功能，例如軟帳戶鎖定或 AD 密碼及工作時數原則
	- 使用裝置註冊、Azure AD 聯結或 Intune MDM 原則有條件存取內部部署和雲端資源
 

<!---HONumber=62-->