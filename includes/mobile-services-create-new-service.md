

依照下列步驟即可建立新的行動服務。

1.	登入[管理入口網站]。在導覽窗格的底端，按一下 **[新增]**。展開 **[計算]** 和 **[行動服務]**，然後按一下 **[建立]**。

	![](./media/mobile-services-create-new-service/mobile-create.png)

	[建立行動服務] 對話方塊隨即出現。

2.	在 [**建立行動服務**] 對話方塊中，選取 [**建立免費的 20 MB SQL Database**]，選取 **JavaScript**] 執行階段，然後在 [**URL**] 文字方塊中輸入新行動服務的子網域名稱。按一下向右鍵按鈕以移至下一個頁面。

	![](./media/mobile-services-create-new-service/mobile-create-page1.png)

	這將顯示 **[指定資料庫設定]** 頁面。
	
	>[AZURE.NOTE]作為本教學課程的一部分，您需要建立新的 SQL 資料庫執行個體和伺服器。You can reuse this new database and administer it as you would any other SQL Database instance.如果新行動服務的區域已經有資料庫存在，您可以改選 **[使用現有資料庫]**，然後選取該資料庫。不建議您使用位在不同區域的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。

3.	在 [名稱] 中輸入新資料庫的名稱，然後輸入 [登入名稱] (也就是新 SQL Database 伺服器的系統管理員登入名稱)，輸入並確認密碼，然後按一下核取按鈕以完成程序。![](./media/mobile-services-create-new-service/mobile-create-page2.png)

您現在已經建立可供行動應用程式使用的新行動服務。



<!-- URLs. -->
[管理入口網站]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->