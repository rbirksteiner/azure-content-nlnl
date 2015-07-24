<properties
	pageTitle="在 Azure App Service 中設定自訂網域名稱"
	description="了解如何在 Azure App Service 中將自訂網域名稱與 Web 應用程式搭配使用。"
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

# 在 Azure App Service 中設定自訂網域名稱

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web 應用程式">購買 Web 應用程式網域</a> <a href="/documentation/articles/web-sites-custom-domain-name" title="Web 應用程式" class="current">內含外部網域的 Web 應用程式</a> <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="內含流量管理員的 Web 應用程式">內含流量管理員的 Web 應用程式</a> <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>

</div>

[AZURE.INCLUDE 〈[websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)〉]

當您建立 Web 應用程式時，Azure 會將它指派給 azurewebsites.net 的子網域。例如，如果您的 Web 應用程式名稱為 **contoso**，則 URL 會是 **contoso.azurewebsites.net**。Azure 也會指派虛擬 IP 位址。

若是實際執行的 Web 應用程式，您可能想讓使用者看到自訂網域名稱。本文章說明如何利用 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 保留或設定自訂網域。(本文提供所有網域註冊機構的一般指示。本文最上方的索引標籤為特定註冊機構的部分文章連結。)

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## 概觀

如果您有網域名稱，或您想要保留來自其他網域註冊機構的網域，以下是產生 Web 應用程式的自訂網域名稱的一般步驟：

1. 保留您的網域名稱。本文不包含此流程。有許多網域註冊機構可供選擇。當您註冊時，這些網站會逐步引導您進行此流程。
1. 建立 DNS 記錄，將網域對應至您的 Azure Web 應用程式。
1. 將網域名稱新增至 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)中。

在這基本大綱中，有一些需要注意的特定情況：

- 對應根網域。根網域是您透過網域註冊機構保留的網域。例如 **contoso.com**。
- 對應子網域。例如 **blogs.contoso.com**。您可以將不同的子網域對應至不同的 Web 應用程式。
- 對應萬用字元。例如，***.contoso.com**。萬用字元項目會套用至網域的所有子網域。

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-modes.md)]


## DNS 記錄類型

網域名稱系統 (DNS) 使用資料記錄，將網域名稱對應至 IP 位址。有數種 DNS 記錄。您要為 Web 應用程式建立 *A* 記錄或 *CNAME* 記錄。

- A **(位址)** 記錄會將網域名稱對應至 IP 位址。
- **CNAME (正式名稱)** 記錄會將網域名稱對應至其他網域名稱。DNS 會使用第二個名稱查詢位址。使用者仍然可以在瀏覽器中看到第一個網域名稱。例如，您可以將 contoso.com 對應至 *&lt;yourwebapp&gt;*.azurewebsites.net。

若 IP 位址變更，CNAME 項目仍會有效，但 A 記錄就必須更新。不過，有部分網域註冊機構不允許 CNAME 記錄使用根網域或萬用字元網域。若有該情況，則必須使用 A 記錄。

> [AZURE.NOTE]若您刪除 Web 應用程式後再重新建立，或是將 Web 應用程式模式變更回免費，則 IP 位址可能會變更。


## 尋找虛擬 IP 位址

若您要建立 CNAME 記錄，可略過此步驟。若要建立 A 記錄，您需要 Web 應用程式的虛擬 IP 位址。取得 IP 位址：

1.	在瀏覽器中，開啟 [[Azure 管理入口網站](https://portal.azure.com)]。
2.	按一下頁面左側的 [**瀏覽**] 選項。
3.	按一下 [**Web Apps**] 刀鋒視窗。
4.	按一下您的 Web 應用程式的名稱。
5.	在 [**基本功能**] 頁面上，按一下 [**所有設定**]。
6.	按一下 [**自訂網域和 SSL**]。IP 位址位於接近頁面底部的地方 (就在 [**SSL 繫結**] 區段正上方)。

## 建立 DNS 記錄

登入網域註冊機構，並使用它們的工具加入 A 記錄或 CNAME 記錄。每個註冊機構的 Web 應用程式都稍有不同，但以下是部分一般方針。

1.	尋找管理 DNS 記錄的頁面。在網站中尋找標示為**網域名稱**、**DNS** 或**名稱伺服器管理**的連結或區域。通常可透過檢視您的帳戶資訊，然後尋找**我的網域**之類的連結來找到此連結。
2.	當您找到管理頁面時，請尋找可讓您加入或編輯 DNS 記錄的連結。這可能會以 **Zone file**、**DNS Records** 或 **Advanced** 組態連結的形式列出。

頁面可能會分別列出 A 記錄與 CNAME 記錄，或提供選取記錄類型的下拉式清單。此外，頁面也可能會使用其他名稱做為記錄類型，例如 **IP 位址記錄**而不是 A 記錄，或使用**別名記錄**而不是 CNAME 記錄。註冊機構通常會為您建立一些記錄，所以可能已經有根網域或一般子網域的記錄，例如 **www**。

當您建立或編輯記錄時，可使用欄位將網域名稱對應至 IP 位址 (針對 A 記錄)，或對應至其他網域 (針對 CNAME 記錄)。若為 CNAME 記錄，您要「從」自訂網域對應「到」azurewebsites.net 子網域。

在許多註冊機構的工具中，您只要輸入網域的子網域部分即可，不必輸入整個網域名稱。此外，許多工具都會使用‘@’表示根網域。例如：

<table cellspacing="0" border="1">
  <tr>
    <th>主機</th>
    <th>記錄類型</th>
    <th>IP 位址或 URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A (位址)</td>
    <td>127.0.0.1</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (別名)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

假設自訂網域名稱為 'contoso.com'，則會建立以下記錄：

- 對應至 127.0.0.1 的 **contoso.com**。
- 對應至 **contoso.azurewebsites.net** 的 **www.contoso.com**。

>[AZURE.NOTE]您可以使用 Azure DNS 來裝載 web 應用程式所需的網域記錄。若要在 Azure DNS 設定您的自訂網域，並建立您的記錄，請參閱[建立 Web 應用程式的自訂 DNS 記錄](../dns-web-sites-custom-domain)。

<a name="awverify" /></a>
## 建立 awverify 記錄 (僅限 A 記錄)

若您建立 A 記錄，Web 應用程式也會要求特定的 CNAME 記錄，用於確認您是否擁有嘗試使用的網域。此 CNAME 記錄必須具有下列形式。

- *如果 A 記錄對應根網域或萬用字元網域：* 請建立從 **awverify.&lt;yourdomain&gt;** 對應至 **awverify.&lt;yourwebappname&gt;.azurewebsites.net** 的 CNAME 記錄。例如，針對 **contoso.com** 的 A 記錄，建立 **awverify.contoso.com** 的 CNAME 記錄。
- *如果 A 記錄對應至特定子網域網域：* 請建立從 **awverify.&lt;subdomain&gt;** 對應至 **awverify.&lt;yourwebappname&gt;.azurewebsites.net** 的 CNAME 記錄。例如，針對 **blogs.contoso.com** 的 A 記錄，建立 **awverify.blogs.contoso.com** 的 CNAME 記錄。

您 Web 應用程式的訪客將不會看到 awverify 子網域；這只是供 Azure 用於驗證您的網域。

## 在 Web 應用程式上啟用網域名稱

[AZURE.INCLUDE [模式](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。


## 後續步驟

如需詳細資訊，請參閱：[開始使用 Azure DNS](http://azure.microsoft.com/zh-tw/documentation/articles/dns-getstarted-create-dnszone) 和[將網域委派給 Azure DNS](http://azure.microsoft.com/zh-tw/documentation/articles/dns-domain-delegation)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[巡覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
 

<!---HONumber=62-->