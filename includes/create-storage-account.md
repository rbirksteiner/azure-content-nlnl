## <a name="create-account"> </a>建立 Azure 儲存體帳戶

若要使用 Azure 儲存體，您將需要儲存體帳戶。您 可以依照下列步驟來建立儲存體帳戶。(您也可以
透過使用 Azure 服務管理用戶端程式庫或服務管理 [REST API]，來建立儲存體帳戶。)

1.  登入 [Azure 管理入口網站]。

2.  在瀏覽窗格的底部，按一下 [新增]。

	![+new][plus-new]

3.  依序按一下 [資料服務]、[儲存體]、[快速建立]。

	![Quick create dialog][quick-create-storage]

4.  在 [URL] 中，為儲存體帳戶輸入要在 URI 中使用的子網域名稱。此項目可以包含 3 到 24 個小寫字母
    和數字。此值會變成 URI 中的主機名稱，用來為訂用帳戶的 Blob、 佇列或表格資源定址。

5.  選擇要將儲存體放置於哪個
    「區域/同質群組」。如果您要從 Azure 應用程式使用儲存體，請選取您將部署應用程式的相同區域。

6. 或者，您可以選取帳戶所需的複寫類型。地理區域備援複寫是預設值，而且提供最高的持久性。如需複寫選項的詳細資訊，請參閱 [Azure 儲存體備援選項](http://msdn.microsoft.com/library/azure/dn727290.aspx)和 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)。

6.  按一下 [建立儲存體帳戶]。

[使用 REST API]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
[Azure 管理入口網站]: http://manage.windowsazure.com
[plus-new]: ./media/create-storage-account/plus-new.png
[quick-create-storage]: ./media/create-storage-account/quick-storage-2.png

<!--HONumber=42-->
