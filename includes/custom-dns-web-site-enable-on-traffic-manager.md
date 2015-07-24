傳播網域名稱的記錄後，您應該要能夠使用瀏覽器來確認您自訂的網域名稱可用來存取 Azure App Service 中的 Web 應用程式。

> [AZURE.NOTE]需要一些時間，CNAME 才能傳播至整個 DNS 系統。您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 CNAME 已生效。

若尚未新增 Web 應用程式作為流量管理員端點，則必須先執行此作業，名稱解析才能正常運作，因為自訂網域名稱會路由至流量管理員。接著會將流量管理員路由至您的 Web 應用程式。使用[新增或刪除端點](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx)中的資訊，將 Web 應用程式新增為流量管理員設定檔中的端點。

> [AZURE.NOTE]如果在新增端點時未列出您的 Web 應用程式，請確認模式是設為 [**標準**] App Service 計劃模式。您必須讓 Web 應用程式使用 [**標準**] 模式，才能與流量管理員搭配使用。

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 [**Web Apps**] 索引標籤中，按一下您 Web 應用程式的名稱，並選取 [**設定**]，然後選取 [**自訂網域和 SSL**]。

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. 在 [**自訂網域和 SSL**] 刀鋒視窗中，按一下 [**帶出外部網域**]。

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. 使用 [**網域名稱**] 文字方塊輸入與此 Web 應用程式相關聯的流量管理員網域名稱 (contoso.trafficmanager.net)。

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. 按一下 [**儲存**] 儲存網域名稱設定。

	完成設定後，自訂網域名稱將列在 Web 應用程式的 [**網域名稱**] 區段中。

此時，您應該能夠在瀏覽器中輸入流量管理員網域名稱 (contoso.trafficmanager.net)，並且能成功移至您的 Web 應用程式。

<!---HONumber=62-->