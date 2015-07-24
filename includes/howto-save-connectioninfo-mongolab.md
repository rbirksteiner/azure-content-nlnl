雖然可以將 MongoLab URI 貼在程式碼中，但建議在環境中設定，以方便管理。如此一來，當 URI 變更時，只要透過 Azure 入口網站來更新即可，不必處理程式碼。


1. 在 Azure 入口網站中，選取 [Web 應用程式]。
1. 在 Web 應用程式清單中，按一下 Web 應用程式的名稱。![WebAppEntry][entry-website]Web 應用程式儀表板隨即顯示。

1. 按一下功能表列的 [設定]。![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. 向下捲動到 [連接字串] 區段。![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. 在 [名稱] 中，輸入 MONGOLAB_URI。
1. 在 [值] 中，貼上我們在上一節取得的連接字串。
1. 在 [類型] 下拉式清單中選取 [自訂] (取代預設的 **SQLAzure**)。
1. 按一下工具列的 [儲存]。![SaveWebApp][button-website-save]

**注意：** Azure 會此變數前面加上 **CUSTOMCONNSTR_**，這就是為什麼上述程式碼會參考 **CUSTOMCONNSTR_MONGOLAB_URI** 的原因。

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=July15_HO2-->