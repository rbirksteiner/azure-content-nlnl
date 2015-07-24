

您必須向身分識別提供者註冊應用程式，才能驗證使用者。然後，您必須向 App Service 註冊由提供者產生的用戶端密碼。

1. 登入 [Azure Preview 入口網站]，按一下 [**瀏覽**]、[**資源群組**]，然後選取行動應用程式的資源群組。

2. 選取您的閘道，並記下 [**屬性**] 下的 **URL** 值。註冊應用程式時，您可能需要提供此值給身分識別提供者。

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. 從下列清單中選擇支援的身分識別提供者，並依照步驟向該提供者設定應用程式：

 - <a href="/zh-tw/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/zh-tw/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Facebook 登入</a>
 - <a href="/zh-tw/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Google 登入</a>
 - <a href="/zh-tw/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Microsoft 帳戶</a>
 - <a href="/zh-tw/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Twitter 登入</a>

	您的應用程式現在已設定為搭配您選擇的驗證提供者使用。

4. (選用) 重複先前步驟，以設定您要應用程式支援的任何其他身分識別提供者。 

<!-- URLs. -->
[Azure Preview 入口網站]: https://portal.azure.com/

<!---HONumber=62-->