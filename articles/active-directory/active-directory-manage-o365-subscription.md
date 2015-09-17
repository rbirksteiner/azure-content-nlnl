<properties
   pageTitle="在 Azure 中管理 Office 365 訂用帳戶的目錄。"
   description="使用 Azure Active Directory 和管理入口網站管理 Office 365 訂用帳戶目錄"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevepo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/03/2015"
   ms.author="curtand"/>

#在 Azure 中管理 Office 365 訂用帳戶的目錄。

本文說明如何在 Azure 管理入口網站內，管理針對 Office 365 訂用帳戶建立的目錄。根據您是否已經建立適用於 Azure 的訂用帳戶而定，完成此動作的步驟會有所不同。 您必須是服務管理員或 Azure 訂用帳戶的共同管理員，才能登入 Azure 管理入口網站。

如果您尚未擁有 Azure 訂用帳戶，只需使用您用來登入 Office 365 的相同工作或學校帳戶來註冊 Azure 即可。

![](./media/active-directory-manage-o365-subscription/AAD_O365_01.png)

系統找不到適用於 Azure 的對應訂用帳戶，但是您可以按一下 [註冊 Azure]，註冊表單中即會預先填入來自 Office 365 帳戶的相關資訊。根據預設，會將同一個帳戶指派給服務管理員角色。

![](./media/active-directory-manage-o365-subscription/AAD_O365_02.png)

完成 Azure 訂用帳戶之後，您可以登入 Azure 管理入口網站並存取 Azure 服務。按一下 Active Directory 延伸模組，以便管理用來驗證您 Office 365 使用者的相同目錄。

如果您已經擁有 Azure 訂用帳戶，則管理其他目錄的程序也很簡單。下圖可能有助於說明此程序。

在此範例中，Michael Smith 擁有適用於 Contoso.com 的 Office 365 訂用帳戶。他擁有的 Azure 訂用帳戶是使用他的 Microsoft 帳戶 msmith@hotmail.com 來註冊。在此情況下，他要管理兩個目錄。

| 訂閱 | Office 365 | Azure |
|  -------------- | ------------- | ------------------------------- |
| 顯示名稱 | Contoso | 預設目錄 |
| 網域名稱 | contoso.com | msmithhotmail.onmicrosoft.com |

他想要在使用 Microsoft 帳戶登入 Azure 時，管理 Contoso 目錄中的使用者身分識別，如此就能啟用 Azure AD 功能，例如多重要素驗證 。

![](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

在此情況下，這兩個目錄是彼此獨立的。

##管理兩個獨立的目錄
為了讓 Michael Smith 能夠在使用 msmith@hotmail.com 登入 Azure 時管理這兩個目錄，他必須完成下列步驟：

> [AZURE.NOTE]只有當使用者使用 Microsoft 帳戶登入時，才能完成下列步驟。如果使用者使用工作或學校帳戶登入，則無法使用 [使用現有的目錄] 選項，因為工作或學校帳戶只能透過其主目錄 (也就是儲存工作或學校帳戶，且由公司或學校所擁有的目錄) 進行驗證。

1.	使用 msmith@hotmail.com 登入 Azure 管理入口網站。
2.	依序按一下 [**新增**] > [**應用程式服務**] > [**Active Directory**] > [**目錄**] > [**自訂建立**]。
3.	按一下 [使用現有的目錄]，然後選取 [我現在已經可以登出] 核取方塊。
4.	以 Contoso.onmicrosoft.com 的全域管理員身分登入 Azure 管理入口網站 (例如 msmith@contoso.com)。
5.	當系統出現 [搭配使用 Contoso 目錄和 Azure？] 提示時，按一下 [繼續]。
6.	按一下 [**立即登出**]。
7.	使用 msmith@hotmail.com 登入管理入口網站。Contoso 目錄和預設目錄會出現在 Active Directory 延伸模組中。

完成這些步驟之後，msmith@hotmail.com 會成為 Contoso 目錄中的全域管理員。

##以全域管理員身分管理資源
現在我們假設 John Doe 需要登入 Azure 管理入口網站，並管理與 msmith@hotmail.com 的 Azure 訂用帳戶相關聯的網站和資料庫資源。若要這麼做，Michael Smith 必須完成下列額外步驟：

1.	使用 Azure 訂用帳戶的服務管理員帳戶登入管理入口網站 (在此範例中為 msmith@hotmail.com)。
2.	將訂用帳戶移轉至 Contoso 目錄：按一下 [設定] > [訂用帳戶] > 選取訂用帳戶 > [編輯目錄] > 選取 [Contoso (Contoso.com)]。在移轉過程中，如果有工作或學校帳戶是訂用帳戶的共同管理員，則將移除這類帳戶。
3.	新增 John Doe 做為訂用帳戶的共同管理員：按一下 [設定] > [系統管理員] > 選取訂用帳戶 > [新增] > 輸入 ****JohnDoe@Contoso.com**。

##後續步驟
如需訂用帳戶與目錄間的關聯性詳細資訊，請參閱[如何將訂用帳戶如何關聯至目錄](active-directory-how-subscriptions-associated-directory.md)。

<!---HONumber=August15_HO6-->