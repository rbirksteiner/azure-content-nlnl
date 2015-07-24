<properties 
	pageTitle="在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱" 
	description="在包含負載平衡的流量管理員的 Azure App Service 中使用 Web 應用程式的自訂網域名稱。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="mwasson"/>

#在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [簡介](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供對 Azure App Service (使用流量管理員進行負載平衡的網站) 使用自訂網域名稱的一般指示。

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## 了解 DNS 記錄

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## 將 Web 應用程式設定為標準模式

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## 新增自訂網域的 DNS 記錄


> [AZURE.NOTE]如果您已透過 Azure App Service Web Apps 購買網域，請略過下列步驟，並參閱<a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">購買 Web Apps 網域</a>文張的最後一個步驟。


若要將您的自訂網域與 Azure App Service 的 Web 應用程式建立關聯，必須在 DNS 表格中為您的自訂網域新增項目，方法是使用您向其購買網域名稱之網域註冊機構所提供的工具。使用下列步驟來尋找並使用 DNS 工具。

1. 在網域註冊機構登入您的帳戶，並尋找用於管理 DNS 記錄的頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。通常該頁面的連結可透過檢視您的帳戶資訊，然後尋找**我的網域**之類的連結來找到。

4. 一旦找到網域名稱的管理頁面，請尋找可讓您編輯 DNS 記錄的連結。這可能會以 **Zone file**、**DNS Records** 或 **Advanced** 組態連結的形式列出。

	* 頁面上可能已建立一些記錄，例如將 '**@**' 或 '*' 與 'domain parking' 頁面建立關聯的項目。它也可能包含常見子網域 (如 **www**) 的記錄。
	* 頁面上會提及 **CNAME 記錄**，或提供選取記錄類型的下拉式清單。它也可能提及其他記錄 (如 **A 記錄**和 **MX 記錄**)。在部分情況下，會以其他名稱來稱呼 CNAME 記錄，如**別名記錄**。
	* 頁面上也會有可讓您將**主機名稱**或**網域名稱****對應**為其他網域名稱的欄位。

5. 由於每個註冊機構的特殊要求可能有所不同，一般來說，您會「從」自訂網域名稱 (例如 **contoso.com**) 對應「至」您應用程式使用的流量管理員網域名稱 (**contoso.trafficmanager.net**)。

6. 在註冊機構處將新增或修改 DNS 記錄的作業完成後，請儲存變更。

<a name="enabledomain"></a>
## 啟用流量管理員

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=62-->