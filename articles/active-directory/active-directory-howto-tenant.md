<properties
	pageTitle="如何取得 Azure AD 租用戶 | Microsoft Azure"
	description="如何取得 Azure Active Directory 租用戶，以供註冊及建置應用程式使用。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/02/2015"
	ms.author="dastrock"/>

# 如何取得 Azure Active Directory 租用戶

在 Azure Active Directory (Azure AD) 中，[租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)代表組織。它是組織在註冊 Microsoft 雲端服務 (例如 Azure、Microsoft Intune 或 Office 365) 時所收到和擁有的專屬 Azure AD 服務執行個體。每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開。

租用戶可裝載公司中的使用者及其相關資訊 (密碼、使用者設定檔資料、權限等)。它還包含群組、應用程式和關於組織及其安全性的其他資訊。

若要允許 Azure AD 使用者登入您的應用程式，您必須在您自己的租用戶中註冊您的應用程式。在 Azure AD 租用戶中發佈應用程式**完全免費**。事實上，大部分的開發人員會建立數個租用戶和應用程式，以供實驗、開發、預備和測試等用途使用。如果註冊和使用應用程式的組織想要充分利用進階的目錄功能，他們可以選擇購買授權。

因此，要如何取得 Azure AD 租用戶？ 此程序可能會有小小的不同，如果您：

- [具有現有的 Office 365 訂用帳戶](#use-an-existing-office-365-subscription)
- [具有與 Microsoft 帳戶相關聯的現有 Azure 訂用帳戶](#use-an-msa-azure-subscription)
- [具有與組織帳戶相關聯的現有 Azure 訂用帳戶](#use-an-organizational-azure-subscription)
- [以上皆無並想要從頭開始](#start-from-scratch)

## 使用現有的 Office 365 訂用帳戶
如果您有現有的 Office 365 訂用帳戶，但沒有 Azure 訂用帳戶 (且無法登入 [Azure 管理入口網站](https://manage.windowsazure.com))，請遵循[這些指示](https://technet.microsoft.com/library/dn832618.aspx)來取得存取 Azure AD 租用戶的權限。

## 使用 MSA Azure 訂用帳戶
如果您先前已使用個別的 Microsoft 帳戶註冊 Azure 訂用帳戶，則您已經有一個租用戶！ 在 [Azure 管理入口網站](https://manage.windowsazure.com) 中，您應該會在 [所有項目] 和 [Active Directory] 底下的列示項目中找到名為 "Default Tenant" 的租用戶。 您可以視需要任意使用此租用戶，但您可能會想要建立組織系統管理員帳戶。

若要這樣做，請遵循下列步驟。或者，您可能會想要建立新的租用戶，並按照類似的程序在該租用戶中建立系統管理員。

1.	使用您的個別帳戶登入 [Azure 管理入口網站](https://manage.windowsazure.com)
2.	瀏覽至入口網站的 [Active Directory] 區段 (您可在左側瀏覽列中找到)
3.	在可用的目錄清單中選取 “Default Directory” 項目
4.	按一下頁面頂端的 [使用者] 連結。您會在清單中的 [源自] 欄中看到值為 “Microsoft account” 的單一使用者
5.	按一下頁面底部的 [新增使用者]
6.	在 [新增使用者表單] 中，請提供下列詳細資料：
    - 使用者類型：貴組織的新使用者
    - 使用者名稱：(為此系統管理員選擇一個使用者名稱)
    - 名字/姓式/顯示名稱：(選擇適當的值)
    - 角色：全域系統管理員
    - 替代電子郵件地址：(輸入適當的值)
    - 選擇性：啟用多因素驗證
    - 最後，按一下綠色的 [建立] 按鈕以完成使用者建立 (並顯示暫時密碼)。
7.	當您完成新增使用者表單，並收到新系統管理使用者的暫時密碼時，請務必記錄此密碼，因為您必須以此新使用者身分登入，才能變更密碼。您也可以使用替代電子郵件，直接將密碼傳送給使用者。
8.	若要變更暫時密碼，請使用這個新使用者帳戶登入 https://login.microsoftonline.com，並在系統要求時變更密碼。


## 使用組織的 Azure 訂用帳戶
如果您先前已使用組織帳戶註冊 Azure 訂用帳戶，則您已經有一個租用戶！ 在 [Azure 管理入口網站](https://manage.windowsazure.com) 中，您應該會在 [所有項目] 和 [Active Directory] 底下的列示項目中找到一個租用戶。 您可以視需要任意使用此租用戶。您也可能想要使用入口網站左下角的 [新增] 按鈕來建立新的租用戶。


## 從頭開始
如果上述對您沒有太大的意義，別擔心。您只需造訪 [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization)，並以新的組織身分註冊 Azure。完成此程序時，您將會有自己專屬的 Azure AD 租用戶，並且它會有您在註冊時選擇的網域名稱。在 [Azure 管理入口網站](https://manage.windowsazure.com) 中，您可以透過瀏覽至左側導覽中的 [Active Directory] 找到租用戶。

註冊 Azure 的過程中，您將需要提供信用卡的詳細資料。您可以放心繼續執行，您將不會被收取在 Azure AD 中發佈應用程式或建立新租用戶的費用。

<!---HONumber=August15_HO6-->