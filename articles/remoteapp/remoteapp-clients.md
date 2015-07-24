
<properties
    pageTitle="存取應用程式"
    description="了解 RemoteApp 支援哪些用戶端以及如何存取您的應用程式。"
    services="remoteapp"
    solutions="" documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2015"
    ms.author="elizapo" />



# 存取應用程式

RemoteApp 的優點之一，就是您可以存取從任何裝置發佈給您的應用程式。更棒的是，您能在一台裝置上運作，然後順暢地轉換至第二台裝置，並從您先前停止的地方繼續進行。若要開始進行，您必須為裝置下載適當的用戶端，然後登入該服務。

在本主題中，我們將檢閱目前支援的用戶端清單，以及向您示範如何下載它們，接著示範
如何從各個用戶端登入 RemoteApp。

## 支持的用戶端

如果您的裝置正在執行下列其中一個作業系統，就能夠使用下列步驟來存取 RemoteApp：

 - Windows 10 Preview
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows RT
 - Windows Phone 8.1
 - iOS
 - Mac OS X
 - Android


 精簡型用戶端呢？ 支援下列 Windows Embedded 精簡型用戶端：

 - Windows Embedded Standard 7 with Service Pack 1
 - Windows Embedded POSReady7
 - Windows Embedded Thin PC
 - Windows Embedded 8.1 Industry

## 下載用戶端

不論您使用的平台為何，都能在 [遠端桌面用戶端下載][](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) 頁面找到您需要存取 RemoteApp 的用戶端。

按一下不同的連結，將直接開始下載用戶端，或將您送到應用程式市集中該平台的用戶端下載頁面。依照畫面上的指定來安裝用戶端。

當您在裝置上安裝用戶端並加以啟動後，請到下面對應的小節，了解如何從該用戶端登入 RemoteApp。

## Android

當您從 Google Play 商店安裝  Microsoft 遠端桌面之後，就可以在 [遠端桌面]**** 下找到您的應用程式清單。

1. 除非您已經開始使用應用程式，否則啟動應用程式將帶您前往空白的「連線中心」。若要開始使用 Azure RemoteApp，請點選加號按鈕 "+"****，然後再點選 [Azure RemoteApp]****。

![空白連線中心](./media/remoteapp-clients/Android1.png)

2. 您必須使用自己的電子郵件地址登入才能存取服務，點選 [開始使用]**** 可開始該程序。

![提示時登入](./media/remoteapp-clients/Android2.png)

3. 在下個頁面上，輸入您的 [電子郵件地址]**** ，然後點選 [繼續]****。這樣就會使用 Azure Active Directory 開始登入程序。

![第一個 Azure Active Directory 頁面](./media/remoteapp-clients/Android3.png)

4. 依照畫面上的指示執行，以使用您的 Microsoft 帳戶 (LiveID) 或組織識別碼登入。登入之後，就會以頁面清單呈現您接收到的所有邀請。若是如此，請選取您信任的邀請，然後點選 [完成]****。

![邀請頁面](./media/remoteapp-clients/Android4.png)

5. 接受您的邀請之後，就會將您擁有存取權的應用程式清單下載到您的裝置，並提供在「連線中心」。點選其中一個應用程式，即可加以啟動並開始使用。

![包含摘要的連線中心](./media/remoteapp-clients/Android5.png)

6. 如果您尚未收到邀請，還是能夠試用該服務。若要這樣做，請在出現提示時，點選 [前往免費試用]****。

![示範摘要提示](./media/remoteapp-clients/Android6.png)

7. 這將提供您存取一組基本應用程式的權限，讓您開始使用 RemoteApp。

![Azure RemoteApp 的示範摘要](./media/remoteapp-clients/Android7.png)

## iOS

當您從 App Store 安裝 Microsoft 遠端桌面應用程式之後，就可以在 [RD 用戶端]**** 下找到您的應用程式清單。

1. 除非您已經開始使用應用程式，否則啟動應用程式將帶您前往空白的「連線中心」。若要開始使用 Azure RemoteApp，請點選加號按鈕 ""+""****，然後再點選 [新增 Azure RemoteApp]****。

![空白連線中心](./media/remoteapp-clients/IOS1.png)

2. 您必須使用自己的電子郵件地址登入才能存取服務，輸入您的 [電子郵件地址]****，然後點選 [繼續]**** 以開始該程序。

![提示時登入](./media/remoteapp-clients/IOS2.png)

3. 依照畫面上的指示執行，以使用您的 Microsoft 帳戶 (LiveID) 或組織識別碼登入。登入之後，就會以頁面清單呈現您接收到的所有邀請。若是如此，請選取您信任的邀請，然後點選 [完成]****。

![邀請頁面](./media/remoteapp-clients/IOS3.png)

4. 接受您的邀請之後，就會將您擁有存取權的應用程式清單下載到您的裝置，並提供在「連線中心」。點選其中一個應用程式，即可加以啟動並開始使用。

![包含摘要的連線中心](./media/remoteapp-clients/IOS4.png)

5. 如果您尚未收到邀請，還是能夠試用該服務。若要這樣做，請在出現提示時，點選 [前往免費試用]****。

![示範摘要提示](./media/remoteapp-clients/IOS5.png)

6. 這將提供您存取一組基本應用程式的權限，讓您開始使用 RemoteApp。

![Azure RemoteApp 的示範摘要](./media/remoteapp-clients/IOS6.png)

## Mac OS X

當您從 App Store 安裝  Microsoft 遠端桌面應用程式之後，就可以在 [Microsoft 遠端桌面]**** 下找到您的應用程式清單。

1. 除非您已經開始使用應用程式，否則啟動應用程式將帶您前往空白的「連線中心」。若要開始使用 Azure RemoteApp，請按一下 [Azure RemoteApp]**** 按鈕。

![空白連線中心](./media/remoteapp-clients/Mac1.png)

2. 您必須使用自己的電子郵件地址登入才能存取服務，點選 [開始使用]**** 可開始該程序。

![提示時登入](./media/remoteapp-clients/Mac2.png)

3. 在下個頁面上，輸入您的 [電子郵件地址]**** ，然後點選 [繼續]****。這樣就會使用 Azure Active Directory 開始登入程序。

![第一個 Azure Active Directory 頁面](./media/remoteapp-clients/Mac3.png)

4. 依照畫面上的指示執行，以使用您的 Microsoft 帳戶 (LiveID) 或組織識別碼登入。登入之後，就會以頁面清單呈現您接收到的所有邀請。若是如此，請選取您信任的邀請，然後關閉對話方塊。

![邀請頁面](./media/remoteapp-clients/Mac4.png)

5. 接受您的邀請之後，就會將您擁有存取權的應用程式清單下載到您的裝置，並提供在「連線中心」。按兩下其中一個應用程式，即可加以啟動並開始使用。

![包含摘要的連線中心](./media/remoteapp-clients/Mac5.png)

6. 如果您尚未收到邀請，還是能夠試用該服務。若要這樣做，請在出現提示時，按一下 [前往免費試用]****。

![示範摘要提示](./media/remoteapp-clients/Mac6.png)

7. 這將提供您存取一組基本應用程式的權限，讓您開始使用 RemoteApp。

![Azure RemoteApp 的示範摘要](./media/remoteapp-clients/Mac7.png)

## Windows (所有支援的版本，Windows Phone 除外)

用戶端會在完成安裝後自動啟動，不過之後若需要再次存取時，您可以在 [Azure RemoteApp]**** 這個名稱下找到您的應用程式清單。

1. 啟動用戶端之後，您看到的第一個頁面會歡迎您使用 Azure RemoteApp。若要繼續，請按一下 [開始使用]****。

![Azure RemoteApp 用戶端的歡迎使用頁面](./media/remoteapp-clients/Windows1.png)

2. 下一個頁面會使用 Azure Active Directory，開始進行 Azure RemoteApp 登入程序。這項程序看起來應該類似於您過去使用的 Microsoft 服務。請從輸入 [電子郵件地址]**** 開始，然後按一下 [繼續]****。

![第一個 Azure Active Directory 提示](./media/remoteapp-clients/Windows2.png)

3. 依照畫面上的指示執行，以使用您的 Microsoft 帳戶 (LiveID) 或組織識別碼登入。登入之後，就會以頁面清單呈現您接收到的所有邀請。若是如此，請選取您信任的邀請，然後按一下 [完成]****。

![Azure RemoteApp 用戶端的邀請頁面](./media/remoteapp-clients/Windows3.png)

4. 接受您的邀請之後，就會將您擁有存取權的應用程式清單下載到您的裝置，並提供在「連線中心」。按兩下其中一個應用程式，即可加以啟動並開始使用。

![Azure RemoteApp 用戶端的連線中心](./media/remoteapp-clients/Windows4.png)

5. 如果尚無任何人傳送邀請給您，請別擔心，我們會助您一臂之力！ 您仍將擁有示範集合的存取權，讓您測試該服務。

![Azure RemoteApp 的示範摘要](./media/remoteapp-clients/Windows5.png)

## Windows Phone 8.1

當您從 Windows Phone 8.1 市集安裝  Microsoft 遠端桌面之後，就可以在 [遠端桌面]**** 下找到您的應用程式清單。

1. 除非您已經開始使用應用程式，否則啟動應用程式將直接帶您前往空白的「連線中心」。若要開始使用 Azure RemoteApp，請點選畫面底部的加號按鈕 ""+""****。

![空白連線中心](./media/remoteapp-clients/WinPhone1.png)

2. 接下來，點選 [Azure RemoteApp]****。

![新增項目頁面](./media/remoteapp-clients/WinPhone2.png)

3. 您必須使用自己的電子郵件地址登入才能存取服務，點選 [連線]**** 可開始該程序。

![提示時登入](./media/remoteapp-clients/WinPhone3.png)

4. 在下個頁面上，輸入您的 [電子郵件地址]**** ，然後點選 [繼續]****。這樣就會使用 Azure Active Directory 開始登入程序。

![第一個 Azure Active Directory 頁面](./media/remoteapp-clients/WinPhone4.png)

5. 依照畫面上的指示執行，以使用您的 Microsoft 帳戶 (LiveID) 或組織識別碼登入。登入之後，就會以頁面清單呈現您接收到的所有邀請。若是如此，請選取您信任的邀請，然後點選 [儲存]****。

![邀請頁面](./media/remoteapp-clients/WinPhone5.png)

6. 接受您的邀請之後，就會將您擁有存取權的應用程式清單下載到您的裝置，並提供在「連線中心」。點選其中一個應用程式，即可加以啟動並開始使用。

![包含摘要的連線中心](./media/remoteapp-clients/WinPhone6.png)

7. 如果您尚未收到邀請，還是能夠試用該服務。若要這樣做，請在出現提示時，點選 [是]****。

![示範摘要提示](./media/remoteapp-clients/WinPhone7.png)

8. 這將提供您存取一組基本應用程式的權限，讓您開始使用 RemoteApp。

![Azure RemoteApp 的示範摘要](./media/remoteapp-clients/WinPhone8.png)

<!--HONumber=54--> 