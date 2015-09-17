<properties
	pageTitle="# 為加入網域的 Windows 7 裝置設定自動註冊裝置 | Microsoft Azure"
	description="設定加入網域的 Windows 7 裝置自動向 Azure AD 註冊的步驟。使用軟體發佈系統 (例如 System Center Configuration Manager) 將裝置註冊軟體套件部署到加入網域的 Windows 7 裝置的步驟。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2015"
	ms.author="femila"/>

# 為加入網域的 Windows 7 裝置設定自動註冊裝置

身為 IT 管理員，您可以設定加入網域的 Windows 7 裝置自動向 Azure AD 註冊。若要這樣做，您必須使用軟體發佈系統，例如 System Center Configuration Manager，將裝置註冊軟體套件部署到加入網域的 Windows 7 裝置。請務必詳細閱讀並完成「自動向 Azure Active Directory 註冊加入網域的 Windows 裝置」中所列的必要條件。

##在加入網域的 Windows 7 裝置上安裝裝置註冊軟體套件

Windows 7 的裝置註冊是可下載的 MSI 套件。此套件必須安裝在加入 Active Directory 網域的 Windows 7 電腦上。您應該使用軟體發佈系統 (例如 System Center Configuration Manager) 來部署此套件。MSI 套件使用 /quiet 參數，支援標準的無訊息安裝選項。您可以從 Microsoft Connect 網站下載此軟體套件。您可以在這裡選取並下載 Workplace Join for Windows 7。

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## 加入工作場所與 Azure Active Directory
加入網域的 Windows 7 裝置的裝置註冊不需要或包含使用者介面。一旦安裝在電腦上，任何登入電腦的網域使用者會自動且無訊息地向 Azure AD 中的裝置物件註冊。在 Azure AD 中，實體裝置的每一個已註冊的使用者都有一個裝置物件。

安裝程式會在系統上建立排定的工作，此工作是在使用者的情境脈絡中執行，並於使用者登入時觸發。使用者登入完成之後，此工作會以無訊息方式向 Azure AD 註冊使用者和裝置。此排定的工作可以在 [工作排程器程式庫] 的 [Microsoft] > [加入工作場所] 下找到。此工作會執行並註冊任何和所有登入電腦的 Active Directory 使用者。下圖列出自動註冊裝置的逐步程序。

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. 使用者 (資訊工作者) 使用 Active Directory 網域認證登入 Windows 7 用戶端電腦。
1. 執行「加入工作場所」排定的工作。
1. 使用者使用 Windows 整合式驗證以無訊息方式向 AD FS 驗證。
1. Windows 7 電腦註冊給 Azure AD 中的使用者。
1. Azure AD 中建立裝置物件和憑證。此物件代表 user@device。
1. 「加入工作場所」憑證儲存在電腦上。

## 取消註冊加入網域的 Windows 7 裝置

您可以選擇取消註冊您加入網域的 Windows 7 裝置，方式如下：使用軟體發佈系統 (例如 System Center Configuration Manager)，從已加入網域的 Windows 7 裝置中解除安裝「加入工作場所」軟體套件。

然後在 Windows 7 電腦上開啟命令提示字元，執行下列命令來取消註冊裝置：
    
    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]此命令必須在已登入電腦的每個網域使用者的環境下執行。事件檢視器和已加入網域的 Windows 7 裝置的錯誤。

Windows 7 電腦上的 Windows 事件記錄檔會顯示與「加入工作場所」相關的訊息。您可以找到「加入工作場所」成功及失敗事件的訊息。事件記錄檔可以在 [事件檢視器] 的 [應用程式及服務記錄檔] > [Microsoft 加入工作場所] 下找到。

<!---HONumber=August15_HO9-->