<properties
   pageTitle="在 Azure Active Directory 中建立工作或學校身分識別"
   description="說明如何使用您的個人身分識別建立工作或學校身分識別，以搭配資源群組範本或以角色為基礎的存取等其他功能使用。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="05/05/2015"
   ms.author="rasquill"/>

# 在 Azure Active Directory 中建立工作或學校身分識別

如果您已建立個人的 Azure 帳戶，或有個人的 MSDN 訂用帳戶，並已建立 Azure 帳戶來運用 MSDN Azure 點數 -- 您已使用 **Microsoft 帳戶**身分識別建立。Azure 有許多很棒的功能 -- 例如[資源群組範本](../resource-group-overview.md) -- 需要工作或學校帳戶 (由 Azure Active Directory 管理的身分識別) 才能運作。

幸運的是，預設的 Azure Active Directory 網域會提供您個人的 Azure 帳戶，讓您可以用來建立新的工作或學校帳戶，以搭配需要這類帳戶使用的 Azure 功能。

> [AZURE.NOTE]如果管理員已提供給您使用者名稱與密碼，很有可能您已經有工作或學校識別碼 (有時稱做*組織識別碼*)。如果是這樣，您可以立即開始使用您的 Azure 帳戶來存取需要該帳戶的 Azure資源。如果您發現您無法使用這些資源，您可能需要返回此主題。如需詳細資訊，請參閱[您可以用來登入的帳戶](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts)及 [Azure 訂用帳戶與 Azure AD 的關係](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir)。

步驟很簡單。您必須在入口網站中找到您已註冊的身分識別、找出您的預設 Azure Active Directory 網域，然後以 Azure 共同管理員的身分新增網域的新使用者。讓我們開始吧。

## 在 Azure 入口網站中找到您的預設目錄

一開始先使用您個人的 Microsoft 帳戶身分識別登入 [Azure 入口網站](https://manage.windowsazure.com)。登入後，請向下捲動左側的藍色面板，然後按一下 [**ACTIVE DIRECTORY**]。

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

讓我們開始在 Azure 中找出您身分識別的部分資訊。您的主窗格應如下所示，顯示您有一個預設目錄。

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

讓我們找到更多的相關資訊。按一下預設的目錄列，會將您帶往預設的目錄屬性。

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

太棒了。現在看一看我們的「預設」網域是什麼...按一下 [**網域**]。

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

在這裡您應該能夠看到 Azure 帳戶何時建立，以及 Azure Active Directory 建立了個人的預設網域，此網域是做為 **onmicrosoft.com** 子網域的個人識別碼雜湊。這是您現在要新增新使用者的網域。

## 在預設網域中建立新的使用者

按一下 [**使用者**] 並在此尋找您單一的個人帳戶。您應該會在 [**源自**] 欄中看到其是 `Microsoft account`。我們想要在您的預設 **.onmicrosoft.com** Azure Active Directory 網域中建立使用者。

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

我們將在接下來的步驟中依照[這些指示](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1)，但使用特定的範例。

按一下頁面底端的 [**+新增使用者**]。在出現的對話方塊中，輸入新的使用者名稱，並讓**使用者類型**為**您組織中的新使用者**。在此範例中，使用者名稱為 `ahmet`。請務必選取您在上述找到的預設網域，做為 `ahmet` 其電子郵件地址的網域。完成時按一下 [下一步] 箭頭。

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

在此輸入 `ahmet` 的詳細資訊，但務必在此選取適當的 [**角色**] 值。您可以很輕鬆地使用**全域管理員**確定系統正在運作中，但如果您可以使用權限較低的角色，也是不錯的主意。此範例採用**使用者**角色。(如需角色的詳細資訊，請參閱[此處](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1)。) 「不要」啟用 Multi-Factor Authentication，除非您想要為作業中的每個記錄檔使用多因素驗證。當您完成時，按一下 [下一步] 箭頭。

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

按一下 [**建立**] 按鈕產生並顯示 `ahmet` 的暫時密碼。

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

複製使用者名稱的電子郵件地址，或輸入要將資訊傳送到哪一個電子郵件地址。在這兩種狀況中，您都需要該資訊才能立即登入。

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

現在您應該會看到新的使用者，本案例中為源自 Azure Active Directory 的 `Ahmet the Developer`。您已使用 Azure Active Directory 建立新的工作或學校身分識別。不過，這個身分識別還沒有使用 Azure 資源的權限。

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

如果您傳送含該資訊的電子郵件，可能如下所示：

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## 為訂用帳戶新增 Azure 的共同管理員權限

現在您必須將新的使用者新增成為訂用帳戶的共同管理員，新的使用者才能夠登入管理入口網站。做法是找到左下角面板的 [**設定**] 圖示並按一下。

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

在主要設定區域中，按一下頂端的 [**管理員**]，您應該只會看到您個人的 Microsoft 帳戶身分識別。在頁面底端按一下 [**+新增**] 以指定共同管理員。在此處輸入您所建立新使用者的電子郵件地址，包括預設網域。如下所示，您應該會看到預設目錄的使用者旁顯示綠色勾號。請記得選取您想要此使用者能夠管理的所有訂用帳戶。

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

完成後，您現在應會看到兩個使用者，包括新的共同管理員身分識別。登出入口網站。

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## 登入並變更新使用者的密碼

以您建立的新使用者身分登入。

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

會立即提示您建立新的密碼。不過，一旦您完成...

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

您應該會看到如下的成功獎勵。

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## 後續步驟

現在您可以試一試。例如，現在您可以使用新的 Azure Active Directory 身分識別來使用 [Azure 資源群組範本](xplat-cli-azure-resource-manager.md)。

     azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass                                        
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups                                                      
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup                                              
    + Creating resource group newgroup                                             
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK


 

<!---HONumber=58-->