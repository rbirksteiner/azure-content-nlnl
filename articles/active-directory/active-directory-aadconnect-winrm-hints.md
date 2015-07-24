<properties 
	pageTitle="Azure AD Connect - Windows 遠端管理提示" 
	description="搭配 AD FS 使用的 Azure AD Connect Windows 遠端管理提示。" 
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

# Azure AD Connect - Windows 遠端管理提示


當您使用 Azure AD Connect 部署 Active Directory Federation Services 或 Web 應用程式 Proxy 時，請檢查提示下方的需求以確定連線能力和組態能夠成功運作：

- 如果目標伺服器已加入網域，請確定已啟用 Windows 遠端管理 
	* 在提高權限的 PSH 命令視窗中，使用 “Enable-PSRemoting –force” 命令 

- 如果目標伺服器是未加入網域的 WAP 電腦，則需要遵循一些額外需求
	- 在目標電腦 (WAP 電腦) 上： 

- 請確定 winrm (Windows 遠端管理/WS-Management) 服務正在透過 [服務] 嵌入式管理單元執行

- 在提高權限的 PSH 命令視窗中，使用 “Enable-PSRemoting –force” 命令
	- 在執行此精靈的電腦上 (如果目標電腦未加入網域或為未受信任的網域)： 

- 在提高權限的 PSH 命令視窗中，使用命令 “Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate”
	- 在伺服器管理員中：
		- 將 DMZ WAP 主機加入至電腦集區 ([伺服器管理員] -> [管理] -> [加入伺服器]...使用 DNS 索引標籤) 
		- 伺服器管理員的 [所有伺服器] 索引標籤：以滑鼠右鍵按一下 WAP 伺服器並選擇 [管理]，然後輸入 WAP 電腦的本機 (非網域) 認證 
		- 若要驗證遠端 PSH 的連線能力，請在伺服器管理員的 [所有伺服器] 索引標籤中：以滑鼠右鍵按一下 WAP 伺服器，然後選擇 [Windows PowerShell]。遠端 PSH 工作階段應隨即開啟，以確保可建立遠端 PowerShell 工作階段。 

**其他資源**


* [Azure AD Connect 帳戶與權限的詳細資訊](active-directory-aadconnect-account-summary.md)
* [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)
* [MSDN 上的 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=62-->