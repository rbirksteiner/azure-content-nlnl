<properties
	pageTitle="在 Azure App Service 中設定自訂網域名稱 (GoDaddy)"
	description="了解如何搭配使用來自 GoDaddy 的網域名稱與 Azure Web Apps"
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

# 在 Azure App Service 中設定自訂網域名稱 (直接向 GoDaddy 購買)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE 〈[websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)〉]

> [AZURE.NOTE]如果您已透過 Azure App Service Web Apps 購買網域，請參考<a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">購買 Web Apps 網域</a>文章的最後一個步驟。

[AZURE.INCLUDE [簡介](../../includes/custom-dns-web-site-intro.md)]

本文提供搭配使用直接從 [GoDaddy](https://godaddy.com) 購買的自訂網域名稱與 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 的相關指示。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##了解 DNS 記錄

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]


<a name="bkmk_configurecname"></a>
## 新增自訂網域的 DNS 記錄

若要將您的自訂網域與 App Service 中的 Web 應用程式產生關聯，您必須使用由 GoDaddy 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 GoDaddy.com 中，使用下列步驟來尋找 DNS 工具

1. 在 GoDaddy.com 中登入您的帳戶，並依序選取 [我的帳戶]、[管理我的網域]。最後，在下拉式功能表中選取您要與 Azure Web 應用程式搭配使用的網域名稱，然後選取 [管理 DNS]。

	![custom domain page for GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. 在 [網域詳細資料] 頁面中，捲動至 [DNS 區域檔案] 索引標籤。此區段可用來新增與修改網域名稱的 DNS 記錄。

	![DNS Zone File tab](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	選取 [新增記錄] 以新增現有記錄。

	若要 [編輯] 現有記錄，請選取該記錄旁的 [紙筆] 圖示。

	> [AZURE.NOTE]新增記錄之前，請注意，GoDaddy 已為熱門子網域 (在編輯器中稱為「**主機**」) 建立 DNS 記錄，例如**電子郵件**、**檔案**、**郵件**及其他。如果您要使用的名稱已存在，請修改現有記錄，而非建立新記錄。

4. 新增記錄時，您必須先選取記錄類型。

	![選取記錄類型](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	接下來，您必須提供 [主機] \(自訂網域或子網域\) 及其 [指向] 位置。

	![新增區域記錄](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* 新增 [A (主機) 記錄] 時，您必須將 [主機] 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)、* (符合多個子網域的萬用字元)，或您要使用的子網域 (例如 **www**)。 您必須將 [指向] 欄位設為 Azure Web 應用程式的 IP 位址。

		> [AZURE.NOTE]用 [A (主機) 記錄] 時，您也必須透過下列設定新增 CNAME 記錄：
		>
		> * **awverify** 的 [主機] 值，該 awverify 會 [指向] **awverify.&lt;yourwebappname&gt;.azurewebsites.net** 的值。
		>
		> Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

	* 新增 [CNAME (別名) 記錄] 時，您必須將 [主機] 欄位設定為您要使用的子網域。例如 **www**。您必須將 [指向] 欄位設為 Azure Web 應用程式的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。


5. 當您完成新增或修改記錄時，請按一下 [完成] 以儲存變更。

<a name="enabledomain"></a>
## 在 Web 應用程式上啟用網域名稱

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=62-->