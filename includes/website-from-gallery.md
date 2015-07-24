Azure Marketplace 提供由 Microsoft、協力廠商公司及開放原始碼軟體計劃所開發的各種熱門 Web 應用程式。除了用來連線到 [Azure 預覽入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)的瀏覽器以外，從 Azure Marketplace 建立的 Web 應用程式不需要安裝任何軟體。

在本教學課程中，您將了解：

- 如何透過 Azure Marketplace 建立新的 Web 應用程式。

- 如何透過 Azure 預覽入口網站部署 Web 應用程式。
 
您將建立使用預設範本的 WordPress 部落格。下圖顯示完成的應用程式：


![Wordpress 部落格][13]

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 在入口網站中建立 Web 應用程式

1. 登入 Azure 預覽入口網站。

2. 按一下 [Marketplace] 圖示，或按一下儀表板左下方的 [新增] 圖示，選取 [Web + 行動]，然後選取底部的 [Azure Marketplace] 以開啟 Azure Marketplace。
	
	![建立新的][5]
	
3. 選取 [Web Apps]。搜尋 **WordPress**，然後按一下 **WordPress** 圖示。

	![WordPress 來源清單][7]
	
5. 讀取 WordPress 應用程式的描述之後，選取 [建立]。

6. 按一下 [Web 應用程式]，然後提供設定 Web 應用程式所需的值。
	
   ![設定您的應用程式][8]

7. 按一下 [資料庫]，然後提供設定 MySQL 資料庫所需的值。 

   ![設定資料庫][database]

8. 如有必要，按一下 [訂用帳戶]，然後指定要使用的訂用帳戶。 

7. 當您完成定義 Web 應用程式時，按一下 [建立]，然後靜待新的 Web 應用程式建立。

   建立應用程式之後，您將會看到包含 Web 應用程式與資料庫的資源群組。

   ![顯示群組][resourcegroup]

## 啟動和管理 WordPress Web 應用程式
	
1. 按一下您的新 Web 應用程式，以查看應用程式的相關詳細資料。

   ![啟動儀表板][10]

2. 在 [Essentials] 頁面上，按一下 [瀏覽] 或 [Url] 底下的連結，以開啟 Web 應用程式的歡迎頁面。

   ![網站 URL][browse]

3. 如果您尚未安裝 WordPress，請輸入 WordPress 所需的適當組態資訊，然後按一下 [安裝 WordPress] 以完成組態設定，並開啟 Web 應用程式的登入頁面。

4. 按一下 [登入]，然後輸入您的認證。

5. 您的新 WordPress Web 應用程式看起來類似下面的 Web 應用程式。

	![您的 WordPress 網站][13]






[5]: ./media/website-from-gallery/startmarketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/selectwordpress.png
[8]: ./media/website-from-gallery/configureweb.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/seewebapp.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/configuredb.png
[resourcegroup]: ./media/website-from-gallery/showgroup.png
[browse]: ./media/website-from-gallery/browse.png

<!---HONumber=62-->