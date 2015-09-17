<properties 
	pageTitle="Azure AD Connect Health AD FS 代理程式安裝 | Microsoft Azure"
	description="這是說明 Active Directory Federation Services (AD FS) 代理程式安裝的 Azure AD Connect Health 頁面。"
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
	ms.date="08/14/2015"
	ms.author="billmath"/>






# 適用於 AD FS 的 Azure AD Connect Health 代理程式安裝

本文件將逐步引導您在伺服器上安裝和設定適用於 AD FS 的 Azure AD Connect Health 代理程式。

>[AZURE.NOTE]請記住，在 Azure AD Connect Health 執行個體中出現任何資料之前，您必須先在目標伺服器上安裝 Azure AD Connect Health 代理程式。請務必在安裝代理程式之前，先完成[這裡](active-directory-aadconnect-health.md#requirements)的需求。您可以在[這裡](http://go.microsoft.com/fwlink/?LinkID=518973)下載代理程式：


## 代理程式安裝
若要啟動代理程式安裝，請按兩下您所下載的 .exe 檔案。在第一個畫面上，按一下 [安裝]。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

一旦安裝完成之後，按一下 [立即設定]。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

這將會啟動命令提示字元，然後再啟動將執行 Register-AzureADConnectHealthADFSAgent 的特定 PowerShell。系統將提示您登入 Azure。繼續進行並登入。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


登入後，PowerShell 將會繼續。一旦完成之後，您就可以關閉 PowerShell，且設定已完成。

此時，應該會自動啟動服務，且代理程式現在將會監視並收集資料。請注意，如果您還未符合前幾節所述的所有必要條件，您將會在 PowerShell 視窗中看到警告。請務必在安裝代理程式之前，先完成[這裡](active-directory-aadconnect-health.md#requirements)的需求。下列螢幕擷取畫面是這些錯誤的其中一個範例。

![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

若要確認已安裝代理程式，請開啟服務，並尋找下列項目。如果您已完成設定，這些服務應該正在執行。否則，他們將不會啟動，直到設定完成為止。

- Azure AD Connect Health AD FS 診斷服務
- Azure AD Connect Health AD FS Insights 服務
- Azure AD Connect Health AD FS 監視服務
 
![驗證 Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


## Windows Server 2008 R2 伺服器上的代理程式安裝

若是 Windows Server 2008 R2 伺服器，請執行以下操作：

1. 請確定伺服器在 Service Pack 1 或更高版本上執行。
1. 關閉 IE ESC 以安裝代理程式：
1. 在安裝 AD Health 代理程式之前，先在每部伺服器上安裝 Windows PowerShell 4.0。安裝 Windows PowerShell 4.0：
 - 使用下列連結下載離線安裝程式，以安裝 [Microsoft.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)。
 - 安裝 PowerShell ISE (從 Windows 功能)
 - 安裝 [Windows Management Framework 4.0。](https://www.microsoft.com/download/details.aspx?id=40855)
 - 在伺服器上安裝 Internet Explorer 10 或更新版本。這是 Health 服務所必需，才能使用 Azure 系統管理員認證進行驗證。
1. 如需有關在 Windows Server 2008 R2 上安裝 Windows PowerShell 4.0 的其他資訊，請參閱[這裡](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)的 Wiki 文章。

## 相關連結

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->