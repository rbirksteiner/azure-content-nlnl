
在先前範例中，每次應用程式啟動時，都會連繫識別提供者和行動服務。相反地，您可以快取授權權杖，並嘗試優先使用它。

* 建議在 iOS 用戶端上用來加密和儲存驗證權杖的方法是使用 iOS Keychain。我們將使用 [SSKeychain](https://github.com/soffes/sskeychain) -- iOS Keychain 的一種簡單的包裝函式。依照 [SSKeychain] 頁面上的指示，將該包裝函式加入至您的專案。確認已在專案的 [建置設定]**Build Settings** ([Apple LLVM - 語言 - 模組] 區段) 中啟用 [啟用模組] 設定。

* 開啟 **QSTodoListViewController.m**，並加入下列程式碼：

```
		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}
```

* 在 `loginAndGetData` 中，修改 `loginWithProvider:controller:animated:completion:` 的 completion 區塊。在 `[self refresh]` 前面加入下列這一行，以儲存使用者識別碼和權杖屬性：

```
				[self saveAuthInfo];
```

* 讓我們在應用程式啟動時載入使用者識別碼和權杖。在 **QSTodoListViewController.m** 的 `viewDidLoad` 中，在 `self.todoService` 初始化之後加入這部分。

```
				[self loadAuthInfo];
```

<!---HONumber=July15_HO2-->