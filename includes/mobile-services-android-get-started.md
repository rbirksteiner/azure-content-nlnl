本教學課程的最後階段是建立並執行新的應用程式。

1. 瀏覽至您儲存此壓縮專案檔案的位置，並將檔案展開到您電腦上的 Android Studio 專案目錄。

2. 開啟 Android Studio。如果您正在使用專案，而它出現的話，請關閉專案 ([檔案] => [關閉專案])。

3. 選取 [開啟現有的 Android Studio 專案]，瀏覽至專案位置，然後按一下 [確定]。

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. 在左側 [專案總管] 視窗中，確定已選取 [專案] 索引標籤，然後依序開啟 [app]、[src]、[java]，並連按兩下 [ToDoactivity]。

   	![](./media/mobile-services-android-get-started/Android-Studio-quickstart.png)


5. 如果下載的 SDK 是 2.0 版，則需要以您行動服務的 Url 和索引鍵來更新程式碼：
	- 	在 **TodoActivity.java** 中找出 **OnCreate** 方法，然後找出具現化行動服務用戶端的程式碼。在前一個影像中可看到此程式碼。
	- 	以您行動服務的實際 Url 取代 "MobileServiceUrl"。
	- 	以您行動服務的金鑰取代 "AppKey"。
	- 	如需詳細資訊，請參閱教學課程[將行動服務新增至現有的應用程式](../articles/mobile-services/mobile-services-android-get-started-data.md)。 

6. 從 [執行] 功能表中，按一下 [執行] 在 Android 模擬器中啟動專案。

	> [AZURE.IMPORTANT]若要能夠在 Android 模擬器中執行此專案，您必須至少定義一個 Android 虛擬裝置 (AVD)。請使用 AVD 管理員來建立和管理這些裝置。

7. 在應用程式中輸入有意義的文字 (例如 _Complete the tutorial_)，然後按一下 [新增]。

   	![][10]

   	如此會傳送 POST 要求到 Azure 中代管的新行動服務。要求中的資料會插入 TodoItem 資料表中。Items stored in the table are returned by the mobile service, and the data is displayed in the list.

	> [AZURE.NOTE]您可以檢閱存取行動服務來查詢和插入資料的程式碼，這可以在 ToDoActivity.java 檔案中找到。

8. 回到管理入口網站中，按一下 [資料] 索引標籤，然後按一下 [TodoItems] 資料表。

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	如此可讓您瀏覽由應用程式插入資料表中的資料。

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Add Mobile Services to an existing app]: ../articles/mobile-services/mobile-services-android-get-started-data.md
[Get started with authentication]: ../articles/mobile-services/mobile-services-android-get-started-users.md
[Get started with push notifications]: ../articles/mobile-services/mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->