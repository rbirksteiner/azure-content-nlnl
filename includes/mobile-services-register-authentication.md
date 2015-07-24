
首先，您必須到識別提供者的網站上註冊您的應用程式，然後在您的行動服務中設定這些認證。

1. 在 [Azure 管理入口網站] 中，瀏覽至您的行動服務，然後按一下 [**儀表板**]，並記下 [**行動服務 URL**] 值。

2. 向下列其中一個支援的識別提供者註冊您的應用程式。

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md)。  
	
    >[AZURE.IMPORTANT]請務必在識別提供者開發人員網站，正確設定您的行動服務的重新導向 URI。如上方每個提供者的連結指示所述，相較於 JavaScript 後端行動服務 (`/login/<provider>`)，.NET 後端行動服務 (`/signin-<provider>`) 的重新導向 URI 路徑有所不同。設定不正確的重新導向 URI 會導致用戶端無法登入您的應用程式。<br/>請勿散布或分享用戶端密碼。

3. 在 [Azure 管理入口網站]中回到您的行動服務，按一下 [**身分識別**] 索引標籤，並輸入您剛從識別提供者取得的應用程式識別碼和密碼值。

既然您已設定您的應用程式和行動服務來支援一個識別提供者進行驗證，您可以重複這些步驟來新增支援其他識別提供者。

[Azure 管理入口網站]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->