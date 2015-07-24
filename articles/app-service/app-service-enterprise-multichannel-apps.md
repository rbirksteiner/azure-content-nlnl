<properties 
	pageTitle="企業多重通道應用程式" 
	description="多重通道應用程式如何橫跨內部部署資源和雲端架構軟體服務的概觀。" 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# 為企業建立多重通道應用程式

## 概觀

企業多重通道應用程式會使用多種技術將資料呈現給客戶。企業 Web、行動及 API 取用者全都會擷取和處理不同來源的資訊。這些來源橫跨執行內部部署以及雲端架構服務的內部系統。

企業應用程式也會有安全存取的需求，讓員工和商務夥伴在企業的直接控制之下，使用安全的身分識別連接資料。

在 Azure 應用程式服務 (AAS) 上執行的企業應用程式可提供上述所有功能。

如下所示的範例員工差旅應用程式會顯示透過 Azure Active Directory (AAD) 保護的企業多重通道應用程式，而該應用程式會與內部部署資源及軟體即服務 (SaaS) 服務 (如 Office 365 和 Salesforce) 整合。

## <a name="acceptablefiles"></a>透過 Azure Active Directory 控制存取

差旅應用程式的使用者必須先對公司的 Active Directory 進行驗證。已採取下列幾個步驟，將應用程式設定為使用 Azure Active Directory (AAD)：

* 為企業建立了 Azure Active Directory。
* 企業的內部部署 Active Directory 已與 Azure Active Directory 結盟。
* 最後，此應用程式使用了 Azure App Service 的簡單 AAD 整合功能向 ADD 註冊。 

最終結果是應用程式提示使用者登入 AAD (以及經由延伸模組，登入公司 AD)。
	
![AAD 登入][AADLogin]

如需設定 AAS 與 AAD 整合的詳細資訊，請參閱 [AAS 與 AAD 整合][AASIntegrationwithAAD]。

## <a name="acceptablefiles"></a>存取內部部署資源

使用者登入 AAD 後，便會看到計劃好的公司差旅清單。因為 Web 應用程式是在 Microsoft Azure 中執行 ，所以需要存取包含此資訊的內部部署 SQL Server。

![來自內部部署 SQL Server 的資料][DatafromOnpremisesSqlServer]

此應用程式已使用點對站 VNET 整合功能進行設定。這可讓應用程式使用標準資料存取邏輯 (在此情況下為 Entity Framework)，明確地存取在公司網路中執行的遠端 SQL Server。

如需點對站 VNET 整合的詳細資訊，請參閱 [VNET 整合][VNETIntegration]。

## <a name="acceptablefiles"></a>與 Office 365 整合

員工每次建立差旅記錄時，Web 應用程式就會在 SharePoint Online 清單中建立差旅要求。員工可以使用應用程式中的連結來輕鬆存取 Sharepoint 清單：

![SharePoint 清單連結][SharepointListLink]

按一下此連結，就會自動導覽至 SharePoint 清單。因為員工已登入其公司 AAD，所以會使用 AAD 所簽發的安全性權杖明確地向 Office 365 驗證他們。

![SharePoint 清單][SharepointList]

Web 應用程式也會在 Sharepoint Online 文件庫中建立差旅文件。

![SharePoint 文件庫][SharepointDocumentLibrary]

在 SharePoint Online 中建立的資產可讓 Web 應用程式運用 Office 365 中的功能。例如，每次在 Sharepoint 清單中建立項目時，就可能會觸發決策/核准工作流程。

如需 Office 365 整合的詳細資訊，請參閱 [Office 365 整合][Office365Integration]

## <a name="acceptablefiles"></a>與 SaaS 服務整合

現今的公司會使用各種 SaaS 服務，而且需要與來自其他應用程式的 SaaS 資料進行互動。差旅應用程式顯示此案例的範例。每次員工計劃客戶帳戶的差旅時，就會在 Salesforce 中更新客戶帳戶資訊。

![Salesforce 整合][SalesforceIntegration]

已使用 AAD 設定公司的 Salesforce 帳戶，以便使用 AAD 認證向 Salesforce 進行透明驗證。因此，一旦員工使用 AAD 登入差旅應用程式，應用程式即可讀取和寫入儲存在 Salesforce 中的資料。

如需 SaaS 整合的詳細資訊，請參閱 [SaaS 整合][SaaSIntegration]

## <a name="NextSteps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 應用程式服務][AzureApplicationServices]
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: ../app-service-cloud-app-platform.md
[SaaSIntegration]: ../app-service-cloud-app-platform.md
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!---HONumber=62-->