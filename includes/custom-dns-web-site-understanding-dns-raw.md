網域名稱系統 (DNS) 主要用來尋找網際網路上的資源。例如，當您在瀏覽器中輸入 Web 應用程式位址，或按一下網頁上的連結時，它會使用 DNS 將網域轉譯成 IP 位址。IP 位址如同街道地址，但它不是很容易使用。例如，記憶像 **contoso.com** 的 DNS 名稱，比記憶如 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 之類的 IP 位址容易得多。

DNS 系統是根據*記錄*。記錄會將特定的*名稱* (如 **contoso.com**)，與 IP 位址或其他 DNS 名稱相關聯。當應用程式 (如網頁瀏覽器) 查詢 DNS 中的名稱時，它會尋找記錄，並使用它指向為位址的任何值。如果指向的值為 IP 位址，瀏覽器將使用該值。如果指向其他 DNS 名稱，則應用程式必須重新解析。所有名稱解析最終會導出一個 IP 位址。

當您在 App Service 中建立 Web 應用程式時，會將 DNS 名稱自動指派給 Web 應用程式。此名稱將採用 **您的應用程式名稱.azurewebsites.net** 的格式。建立 DNS 記錄時也可以使用虛擬 IP 位址，因此您可建立指向 **.azurewebsites.net**，或指向 IP 位址的記錄。

> [AZURE.NOTE]如果您刪除後重新建立 Web 應用程式，或在 App Service 計劃模式設定為 [**基本**]、[**共用**] 或 [**標準**] 之後變更為 [**免費**]，則您 Web 應用程式的 IP 位址將會變更。

有多種記錄類型，每種記錄都有其本身的功能與限制，但對於 Web 應用程式只需要關注兩種記錄，即 *A* 和 *CNAME*。

###位址記錄 (A 記錄)

A 記錄將網域 (例如 **contoso.com** 或 **www.contoso.com**) *或萬用字元網域* (例如 **\*.contoso.com**) 對應至 IP 位址。以 App Service 中的 Web 應用程式而言，就是指服務的虛擬 IP 或您為 Web 應用程式購買的特定 IP 位址。

相較於 CNAME 記錄，A 記錄的主要優點為：

* 您可以將根網域 (如 **contoso.com**) 對應至 IP 位址；許多註冊機報僅允許使用 A 記錄執行此動作

* 您可以擁有一個使用萬用字元的項目，例如 **\*.contoso.com**，即可處理多個子網域的要求，例如 **mail.contoso.com**、**blogs.contoso.com** 或 **www.contso.com**。

> [AZURE.NOTE]因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析 Web 應用程式 IP 位址的變更。您在設定 Web 應用程式的自訂網域名稱設定時會提供搭配 A 記錄所使用的 IP 位址。不過，如果您刪除後重新建立 Web 應用程式，或將 App Service 計劃模式變回 [**免費**]，此值就可能會變更。

###別名記錄 (CNAME 記錄)

CNAME 記錄將*特定的*網域 (例如 **mail.contoso.com** 或 **www.contoso.com**) 對應到其他 (正式) 網域名稱。若是 App Service Web 應用程式，正式網域名稱是 Web 應用程式的 **&lt;您的應用程式名稱>.azurewebsites.net** 網域名稱。建立之後，CNAME 還會建立 **&lt;您的應用程式名稱>.azurewebsites.net** 網域名稱的別名。CNAME 項目將會自動解析 **&lt;您的應用程式名稱>.azurewebsites.net** 網域名稱的 IP 位址，就算 Web 應用程式的 IP 位址變更，您也不需要採取任何動作。

> [AZURE.NOTE]使用 CNAME 記錄時，某些網域註冊機構只允許您對應子網域 (如 **www.contoso.com**)，而不是根名稱 (如 **contoso.com**)。如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科 CNAME 記錄條目</a>，或 <a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱 - 實作與規格</a>文件。

###Web 應用程式 DNS 詳細規格

若要搭配 Web 應用程式使用 A 記錄，您必須先建立下列其中一筆 CNAME 記錄：

* **對於根網域或萬用字元子網域** - 從 **awverify** 至 **awverify.&lt;您的應用程式名稱&gt;.azurewebsites.net** 的 DNS 名稱。

* **對於特定子網域** - 從 **awverify.&lt;sub-domain>** 至 **awverify.&lt;您的應用程式名稱&gt;.azurewebsites.net** 的 DNS 名稱。例如，**awverify.blogs** (如果 A 記錄要提供給 **blogs.contoso.com** 使用)。

此 CNAME 記錄可用來驗證您擁有正在嘗試使用的網域。這是建立指向 Web 應用程式之虛擬 IP 位址的 A 記錄以外的動作。

您可以執行下列步驟來尋找您 Web 應用程式的 IP 位址，以及 **awverify** 名稱和 **.azurewebsites.net** 名稱。

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 [**Web 應用程式**] 刀鋒視窗中，按一下您 Web 應用程式的名稱，選取 [**所有設定**]，然後從頁面底部選取 [**自訂網域和 SSL**]。

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE]如果未啟用 [**管理網域**]，您將使用 [**免費**]的 Web 應用程式。您無法搭配 [**免費**] 的 Web 應用程式使用自訂網域名稱，因此必須將 App Service 計劃升級到 [**共用**]、[**基本**] 或 [**標準**] 模式。如需有關 App Service 計劃模式的詳細資訊，包括如何變更 Web 應用程式的模式，請參閱[如何調整 Web 應用程式](../articles/web-sites-scale.md)。

6. 在 [管理自訂網域] 對話方塊中，您會看到 **awverify** 資訊、目前指派的 **.azurewebsites.net** 網域名稱，以及虛擬 IP 位址。儲存此資訊，在建立 DNS 記錄時會用得到。

	![](./media/custom-dns-web-site/managecustomdomains.png)

<!---HONumber=62-->