## <a name="register-app-aad"></a>向 Azure Active Directory 註冊您的用戶端應用程式

1. 導覽至 **Azure 管理入口網站**中的 [Active Directory]，然後按一下您的目錄。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. 按一下頂端的 [應用程式] 索引標籤，然後按一下以**新增**應用程式。 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. 按一下 [Add an application my organization is developing]。

4. 在 [新增應用程式精靈] 中，輸入應用程式的 [名稱]，然後按一下 [原生用戶端應用程式] 類型。接著，按一下以繼續。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. 在 [重新導向 URI] 方塊中，輸入行動服務的 /login/done 端點。此值應與 https://todolist.azure-mobile.net/login/done 類似。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. 按一下原生應用程式的 [設定] 索引標籤，並複製 [用戶端識別碼]。稍後您將會需要此資訊。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. 將頁面向下捲動至 [其他應用程式的權限] 區段，然後按一下 [新增應用程式] 按鈕。選擇 [顯示] 功能表中的 [其他]，然後搜尋 todo。按一下 [TodoList]以新增您稍早註冊的行動服務，然後按一下代表完成的勾號。授與行動服務應用程式的存取權限。然後按一下 [儲存]

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

已在 AAD 中設定您的行動服務接收來自應用程式的單一登入。


[Active Directory]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->