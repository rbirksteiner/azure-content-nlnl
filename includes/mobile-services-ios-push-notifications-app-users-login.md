
接下來，您需要變更註冊推播通知的方式，如此可以在使用者通過驗證後，再嘗試註冊。

1. 在 **QSAppDelegate.m** 中，一併移除 **didFinishLaunchingWithOptions** 的實作。

2. 開啟 **QSTodoListViewController.m**，然後在 **viewDidLoad** 方法的結尾加入下列程式碼行：

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=July15_HO2-->