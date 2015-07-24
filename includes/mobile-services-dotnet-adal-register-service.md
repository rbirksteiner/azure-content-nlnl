## <a name="register-mobile-service-aad"></a>向 Azure Active Directory 註冊您的行動服務


在本節中，您將向 Azure Active Directory 註冊您的行動服務，並設定權限以允許單一登入模擬。

1. 您可以依照[如何註冊 Azure Active Directory] 主題的指示，向 Azure Active Directory 註冊應用程式。

2. 在 [Azure 管理入口網站] 中，回到 Azure Active Directory 擴充功能，並按一下您的 Active Directory。

3. 按一下 [應用程式] 索引標籤，然後按一下您的應用程式。

4. 按一下 [Manage Manifest]。然後按一下 [Download Manifest]，並將應用程式資訊清單儲存到本機目錄。

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. 在 Visual Studio 中開啟應用程式資訊清單。在檔案的頂端尋找應用程式權限行，如下所示：

        "oauth2Permissions": [],

    使用下列應用程式權限來取代該行，並儲存檔案。

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. 在 Azure 管理入口網站中，再次按一下應用程式的 [Manage Manifest]，然後按一下 [Upload Manifest]。瀏覽到您剛剛更新的應用程式資訊清單位置，並上傳此資訊清單。

<!-- URLs. -->
[如何註冊 Azure Active Directory]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md
[Azure 管理入口網站]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->