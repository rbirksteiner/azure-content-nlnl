<properties
	pageTitle="開始使用 Azure Active Directory Premium"
	description="說明如何註冊 Azure Active Directory Premium Edition 的主題。"
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo" 
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/14/2015"
	ms.author="markvi"/>

# 開始使用 Azure Active Directory Premium

Azure Active Directory 有三種版本：免費、基本和進階。免費版本隨附於 Azure 或 Office 365 訂用帳戶。基本和進階版本可透過 [Microsoft Enterprise 合約](https://www.microsoft.com/zh-tw/licensing/licensing-programs/enterprise.aspx)或[開啟大量授權](https://www.microsoft.com/zh-tw/licensing/licensing-programs/open-license.aspx) 程式取得。Azure 和 Office 365 訂閱者也可以線上購買 Active Directory Premium。[在此登入](https://portal.office.com/Commerce/Catalog.aspx)購買。

> [AZURE.NOTE]Azure Active Directory Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。如需詳細資訊，請透過 [Azure Active Directory 論壇](http://feedback.azure.com/forums/169401-azure-active-directory)與我們連絡。

Azure Active Directory Premium 也包含在 **Enterprise Mobility Suite** 中。Enterprise Mobility Suite 是一個符合成本效益的方式，可供組織根據單一授權方案來搭配使用 Microsoft Intune、Azure 版權管理和 Active Directory Premium 服務。如需詳細資訊，請參閱 [Enterprise Mobility Suite](https://www.microsoft.com/zh-tw/server-cloud/enterprise-mobility/overview.aspx) 網站。

若要立即開始使用 Azure Active Directory Premium 功能，請遵照下列步驟進行。相同的步驟適用於 Azure Active Directory Basic 版本。

## 步驟 1：註冊 Active Directory Premium

若要註冊，請參閱[大量授權](http://www.microsoft.com/zh-tw/licensing/how-to-buy/how-to-buy.aspx)網站。

## 步驟 2：啟用您的授權方案

如果這是您第一次透過 Microsoft 的企業大量授權方案購買授權方案，則您必須先啟動您的授權方案，才能開始在 Azure Active Directory 目錄內指派授權。若要這樣做，您必須按一下 [登入] 或 [註冊] 連結，您可以在完成第一個授權方案購買之後所收到的確認電子郵件 (請參閱下文) 中找到這些連結。後續針對此目錄的任何購買，授權將會自動在相同的目錄中啟用。

![][1]

如果您有現有的租用戶，請選取 [**登入**] 連結並使用現有的系統管理員帳戶登入。請務必使用目錄的全域管理員認證進行登入，且目錄必須已啟用授權。

如果您想要建立新的 Azure Active Directory 租用戶來與授權方案搭配使用，則您應該選取將會帶您到下列畫面的 [**註冊**] 連結。

![][2]

在完成從電子郵件啟動的註冊程序或登入程序之後，您將會看到下列畫面，確認已為您的租用戶啟動授權方案。

![][3]

## 步驟 3：啟用 Azure Active Directory 存取

一旦授權佈建至您的目錄後，您將會收到一封歡迎使用電子郵件 (請見下文)，確認您已經可以開始管理 Azure Active Directory Premium 或 Enterprise Mobility Suite 的授權和功能。如果您之前用過 Microsoft Azure，您可以繼續執行 http://manage.windowsazure.com 以指派新的授權 (如需進一步的指示，請參閱下面的步驟 4)。如果您不熟悉 Microsoft Azure，請選取電子郵件上 [登入] 連結，或移至[存取 Azure Active Directory 啟用頁面](https://account.windowsazure.com/signup?offer=MS-AZR-0110P)，這將帶您完成一系列的步驟，並協助您透過 Azure 管理入口網站來存取您的目錄。

![][4]

登入成功之後，您必須提供行動電話號碼並加以驗證，才能完成第二個因素驗證畫面 (如下)。在此之後，您便能夠選取 [**註冊**] 來啟用您的 Azure Active directory 存取。

![][5]

如下圖所示，啟用作業可能需要幾分鐘的時間，在啟用存取後，棕色列便會消失，且您將能夠按一下右上角的 [入口網站] 連結，或瀏覽至 [Azure 管理入口網站](http://manage.windowsazure.com)。

![][6]

在此案例中，您的 Azure 存取將僅限於 Azure Active Directory。

![][7]

在先前的使用中，您可能已經存取過 Azure。此外，您可以透過啟用其他 Azure 訂用帳戶，將 Access Azure Active Directory 升級為完整 Azure 存取。在這些情況下，管理入口網站將會擁有更多功能，如下列所示。

![][8]

如果您嘗試在收到上述歡迎使用電子郵件之前就先啟用 Azure Active Directory 存取，則您有可能會看到下列錯誤訊息。請在收到電子郵件的幾分鐘後再試一次。

![][9]

訂用帳戶中的新系統管理員也可透過此連結來啟用管理入口網站的存取。

## 步驟 4：將授權指派給使用者帳戶

在您可以開始使用所購買的方案之前，您必須以手動方式將授權指派給組織內的使用者帳戶，好讓他們可以使用 Premium 所提供的豐富功能。使用下列步驟來將授權指派給使用者，好讓他們可以使用 Azure Active Directory Premium 功能。

將授權指派給使用者：

1. 以想要自訂之目錄的全域系統管理員身分登入管理入口網站。
2. 按一下 [**Active Directory**]，然後選取您要指派授權的目錄。
3. 選取 [**授權**] 索引標籤，選取 [**Active Directory Premium**] 或 [**Enterprise Mobility Suite**]，然後按一下 [**指派**]。

    ![][10]

4. 在對話方塊中，選取您要對其指派授權的使用者，然後按一下核取記號圖示，以儲存變更。

    ![][11]

## 授權限制

部分的授權方案屬於其他授權方案的子集或超集。在大部分的情況下，您無法將授權方案重複指派給使用者。如果您打算指派超集授權方案，則您必須先移除子集授權方案。

## 授權需求

當您將授權指派給使用者時，您可以在他們的帳戶屬性中指定一個主要使用位置，如下所示。如果未指定使用位置，則系統會自動將租用戶的位置指派給使用者。

![][12]

Microsoft 雲端服務的服務和功能可用性會依國家或地區而有所不同。服務 (例如網路電話，VoIP) 有可能在一個國家或地區可以使用，但在另一個國家或地區卻無法使用。基於某些國家或地區的法律問題，服務中的功能可能會受到限制。若要查看服務或功能在使用上有無限制，請到服務授權限制網站並尋找您的國家或地區。

## 接下來

- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [檢視存取和使用情況報告](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=August15_HO8-->