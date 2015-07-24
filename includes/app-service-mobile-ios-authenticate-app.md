

1. 開啟 **QSTodoListViewController.m**，並加入下列方法：


        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }


    > [AZURE.NOTE]如果您使用的身分識別提供者不是 Facebook，請變更傳遞給 **loginWithProvider** 的值。支援的值如下：_microsoftaccount_、_facebook_、_twitter_、_google_ 或 _windowsazureactivedirectory_。


2. 以下列程式碼取代結尾的 `[self refresh]`，藉此修改 `viewDidLoad`：

        [self loginAndGetData];

3. 按下 [執行] 以啟動應用程式，然後以您選擇的身分識別提供者登入。當您登入時，應該能夠檢視待辦事項清單並進行更新。

<!---HONumber=62-->