<properties 
	pageTitle="如何探索組織內使用未經批准的雲端應用程式" 
	description="本主題說明什麼是 Cloud App Discvery 以及為什麼要使用它。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# 如何探索組織內使用未經批准的雲端應用程式

在現代企業中，IT 部門通常不會知道使用者執行其工作所使用的所有雲端應用程式。因此，系統管理員對於未經授權存取公司資料、可能的資料外洩以及應用程式中與生俱來的安全性風險通常都有所顧慮。他們不知道使用了多少 App 或使用了哪些 App，因此，即使是開始建置應付這些風險的計劃，似乎都會令人怯步。

您可以使用 Cloud App Discovery 解決這些問題。Cloud App Discovery 是 Azure Active Directory 的一個進階功能，可讓您探索您組織中的員工所使用的雲端應用程式。


**使用 Cloud App Discovery，您可以：**

* 探索使用中的應用程式，並依使用者數目、流量或應用程式的 Web 要求數目，測量使用量。 
* 識別正在使用應用程式的使用者。 
* 匯出資料以進行其他離線分析。 
* 設定讓應用程式受到 IT 控制的優先順序，並輕鬆地整合應用程式，以啟用單一登入和使用者管理功能。 

使用 Cloud App Discovery，資料擷取部分可以透過在您環境中的電腦上執行的代理程式完成。代理程式所擷取的 App 使用情況資訊會透過安全的加密通道，傳送到 Cloud App Discovery 服務。Cloud App Discovery 服務會評估資料，並產生您可以用來分析環境的報告。


<center>![Cloud App Discovery 的運作方式](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##後續步驟


* 若要深入了解 Cloud App Discovery 的運作方式，請參閱[開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) 
* 如需了解安全性和隱私權考量，請參閱[Cloud App Discovery 的安全性和隱私權考量](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) 
* 如需有關在企業環境中使用下列項目部署 Cloud App Discovery 代理程式的資訊： 
 * Active Directory 群組原則管理，請參閱 [Cloud App Discovery 群組原則部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) 
 * Microsoft System Center Configuration Manager，請參閱 [Cloud App Discovery System Center 部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) 
 * 具有自訂連接埠的 Proxy 伺服器，請參閱[具有自訂連接埠的 Proxy 伺服器的 Cloud App Discovery 登錄設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) 





**其他資源**


* [Cloud App Discovery - 代理程式變更記錄](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery - 常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=August15_HO6-->