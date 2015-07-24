<properties
	pageTitle="在雲端服務中設定自訂網域名稱"
	description="了解如何設定 DNS 設定在自訂網域上公開 Azure 應用程式或資料。"
	services="cloud-services"
	documentationCenter=".net"
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2015"
	ms.author="adegeo"/>

# 設定 Azure 雲端服務的自訂網域名稱

> [AZURE.NOTE]快速完成啟用 -- 使用全新的 Azure [引導式逐步解說](http://support.microsoft.com/kb/2990804)！ 在彈指之間完成自訂網域名稱的關聯，以及與 Azure 雲端服務或 Azure 網站之間的通訊 (SSL) 保護。

當您建立雲端服務時，Azure 會將它指派給 cloudapp.net 的子網域。例如，如果您的雲端服務的名稱為「contoso」，您的使用者可以透過類似 http://&lt;*contoso*>.cloudapp.net 的 URL 存取您的應用程式。Azure 也會指派虛擬 IP 位址。

不過，您也可以在自己的網域名稱 (例如 contoso.com) 上公開您的應用程式。本文說明如何保留或設定雲端服務 Web 角色的自訂網域名稱。

> [AZURE.NOTE]此工作的程序適用於 Azure 雲端服務。如果是網站，請參閱「[設定 Azure App Service Web App 的自訂網域名稱](../web-sites-custom-domain-name.md)」(英文)。關於儲存體帳戶，請參閱＜[設定 Azure 儲存體帳戶的自訂網域名稱](../storage-custom-domain-name.md)＞。


## 了解 CNAME 和 A 記錄

CNAME (或別名記錄) 和 A 記錄都可讓您將網域名稱和特定的伺服器 (在此案例中為服務) 產生關聯，但運作方式不同。針對 Azure 雲端服務來使用 A 記錄時，在決定使用何者之前，還有一些事項應該考慮。

### CNAME 或別名記錄

CNAME 記錄將*特定的*網域 (例如 **contoso.com** 或 **www.contoso.com**) 對應到正式網域名稱。在此案例中，正式的網域名稱為 Azure 託管應用程式的 **&lt;myapp>.cloudapp.net** 網域名稱。建立之後，CNAME 便會建立 **&lt;myapp>.cloudapp.net** 的別名。CNAME 項目會自動解析 **&lt;myapp>.cloudapp.net** 服務的 IP 位址，所以即使雲端服務的 IP 位址變更，您也不需要採取任何動作。

> [AZURE.NOTE]使用 CNAME 記錄時，某些網域註冊機構只允許您對應子網域 (如 www.contoso.com)，而不是根名稱 (如 contoso.com)。如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科 CNAME 記錄條目</a>，或 <a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱 - 實作與規格</a>文件。

### A 記錄

A 記錄將網域 (例如 **contoso.com** 或 **www.contoso.com**) *或萬用字元網域* (例如 ***.contoso.com**) 對應至 IP 位址。以 Azure 雲端服務而言，就是指服務的虛擬 IP。相較於 CNAME 記錄，A 記錄的主要優點在於只需要有一個項目使用 ***.contoso.com** 之類的萬用字元，即可處理多個子網域 (例如 **mail.contoso.com**、**login.contoso.com** 或 **www.contso.com**) 的要求。

> [AZURE.NOTE]因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析雲端服務 IP 位址的變更。第一次將雲端服務部署到空的位置時 (生產或預備)，將會配置雲端服務所使用的 IP 位址。 如果刪除此位置的部署，則 Azure 會釋放此 IP 位址，而未來再部署到此位置時，可能會給予新的 IP 位址。
>
> 在預備和生產部署之間切換時，或就地升級現有的部署時，指定部署位置 (生產或預備) 的 IP 位址會保留下來，相當方便。如需關於執行這些動作的詳細資訊，請參閱[如何管理雲端服務](cloud-services-how-to-manage.md)。


## 新增自訂網域的 CNAME 記錄

若要建立 CNAME 記錄，您必須使用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1. 使用其中一種方法來尋找指派給雲端服務的 **.cloudapp.net** 網域名稱。

  * 登入 [Azure 管理入口網站]，選取您的雲端服務，再選取 [儀表板]，然後在 [Quick Glance] 區段中找出 [網站 URL] 項目。

  		  ![quick glance section showing the site URL][csurl]

  * 安裝並設定 [Azure Powershell](../install-configure-powershell.md)，然後使用下列命令：

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  請將任一方法傳回的 URL 中所使用的網域名稱儲存下來，建立 CNAME 記錄時需要用到。

1.  登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。

2.  現在找出可選取或輸入 CNAME 的地方。您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。請尋找 **CNAME**、**Alias** 或 **Subdomains** 之類的字。

3.  如果要建立 **www.customdomain.com** 的別名，您也必須為 CNAME 提供網域或子網域別名，例如 **www**。如果要建立根網域的別名，註冊機構的 DNS 工具中可能會以 '**@** 符號列出此別名。

4. 接著，您必須提供正式主機名稱，在此案例中為應用程式的 **cloudapp.net** 網域。

例如，下列 CNAME 記錄會將來自 **www.contoso.com** 的所有流量，轉送至 **contoso.cloudapp.net** (已部署之應用程式的自訂網域名稱)：

| 別名/主機名稱/子網域 | 正式網域 |
| ------------------------- | -------------------- |
| www | contoso.cloudapp.net |

**www.contoso.com** 的訪客絕對看不到真正的主機 (contoso.cloudapp.net)，所以使用者不會察覺到轉送過程。

> [AZURE.NOTE]上述範例僅適用於 <strong>www</strong> 子網域的流量。因為 CNAME 記錄不能使用萬用字元，所以您必須為每一個網域/子網域建立一個 CNAME。如果要將子網域 (例如 *.contoso.com) 的流量導向您的 cloudapp.net 位址，您可以在 DNS 設定中設定 <strong>URL 重新導向</strong>或 <strong>URL 轉送</strong>項目，或建立一筆 A 記錄。


## 新增自訂網域的 A 記錄

若要建立 A 記錄，您必須先找出雲端服務的虛擬 IP 位址。然後，利用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。各註冊機構指定 A 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1. 使用下列其中一種方法取得雲端服務的 IP 位址。

  * 登入 [Azure 管理入口網站]，選取您的雲端服務，再選取 [儀表板]，然後在 [Quick Glance] 區段中找出 [Public Virtual IP (VIP) address] 項目。

   		 ![quick glance section showing the VIP][vip]

  * 安裝並設定 [Azure Powershell](../install-configure-powershell.md)，然後使用下列命令：

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    如果雲端服務有多個相關聯的端點，您會收到許多行 IP 位址，但全部都會顯示相同的位址。

  建立 A 記錄時需要用到此 IP 位址，請儲存下來。

1.  登入 DNS 註冊機構的網站，並移至 DNS 管理頁面。在網站中尋找標示為 **Domain Name**、**DNS** 或 **Name Server Management** 的連結或區域。

2.  現在找出可選取或輸入 A 記錄的地方。您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。

3. 選取或輸入將使用此 A 記錄的網域或子網域。例如，若要建立 **www.customdomain.com** 的別名，請選取 **www**。如果要為所有子網域建立萬用字元項目，請輸入 '__*__'。這將會涵蓋所有子網域，例如 **mail.customdomain.com**、**login.customdomain.com** 和 **www.customdomain.com**。

  如果要建立根網域的 A 記錄，註冊機構的 DNS 工具中可能會以 '**@**' 符號列出此記錄。

4. 在提供的欄位中，輸入雲端服務的 IP 位址。這樣會將 A 記錄中使用的網域項目與雲端服務部署的 IP 位址產生關聯。

例如，下列 A 記錄會將來自 **contoso.com** 的所有流量轉送至 **137.135.70.239** (已部署之應用程式的 IP 位址)：

| 主機名稱/子網域 | IP 位址 |
| ------------------- | -------------- |
| @ | 137.135.70.239 |


此範例示範建立根網域的 A 記錄。如果想要建立萬用字元項目來涵蓋所有子網域，請輸入 '__*__' 作為子網域。

## 後續步驟

-   [如何管理雲端服務](cloud-services-how-to-manage.md)
-   [如何將 CDN 內容對應至自訂網域][]

  [Expose Your Application on a Custom Domain]: #access-app
  [Add a CNAME Record for Your Custom Domain]: #add-cname
  [Expose Your Data on a Custom Domain]: #access-data
  [VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
  [Create a CNAME record that associates the subdomain with the storage account]: #create-cname
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [Validate Custom Domain dialog box]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [如何將 CDN 內容對應至自訂網域]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx
  [vip]: ./media/cloud-services-custom-domain-name/csvip.png
  [csurl]: ./media/cloud-services-custom-domain-name/csurl.png
 

<!---HONumber=62-->