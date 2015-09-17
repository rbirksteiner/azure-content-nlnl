<properties pageTitle="教學課程：Azure Active Directory 與 SmarterU 整合 | Microsoft Azure" description="了解如何使用 SmarterU 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 SmarterU 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=524463)。
  
本教學課程的目的是要示範 Azure 與 SmarterU 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   SmarterU 租用戶
  
完成本教學課程之後，您指派給 SmarterU 的 Azure AD 使用者就能夠單一登入您 SmarterU 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SmarterU 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-smarteru-tutorial/IC777320.png "案例")

##啟用 SmarterU 的應用程式整合
  
本節的目的是概述如何啟用 SmarterU 的應用程式整合。

###若要啟用 SmarterU 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-smarteru-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-smarteru-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-smarteru-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊]中，輸入 **SmarterU**。

    ![應用程式庫](./media/active-directory-saas-smarteru-tutorial/IC777321.png "應用程式庫")

7.  在結果窗格中，選取 [SmarterU]，然後按一下 [完成] 加入應用程式。

    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 SmarterU 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [SmarterU] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-smarteru-tutorial/IC777323.png "設定單一登入")

2.  在 [要如何讓使用者登入 SmarterU] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-smarteru-tutorial/IC777324.png "設定單一登入")

3.  在 [設定在 SmarterU 單一登入] 頁面上，按一下 [下載中繼資料] 以下載您的中繼資料，然後在您的本機電腦中將資料檔儲存為 **c:\\SmarterUMetaData.cer**。

    ![設定單一登入](./media/active-directory-saas-smarteru-tutorial/IC777325.png "設定單一登入")

4.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 SmarterU 公司網站。

5.  在上方工具列中按一下 [帳戶設定]。

    ![帳戶設定](./media/active-directory-saas-smarteru-tutorial/IC777326.png "帳戶設定")

6.  在 [帳戶組態] 頁面上，執行下列步驟：

    ![外部授權](./media/active-directory-saas-smarteru-tutorial/IC777327.png "外部授權")

    1.  選取 [啟用外部授權]。
    2.  在 [主要登入控制] 區段中，選取 [SmarterU] 索引標籤。
    3.  在 [使用者預設登入] 區段中，選取 [SmarterU] 索引標籤。
    4.  選取 [啟用 Okta]。
    5.  複製下載的中繼資料檔案內容，然後貼到 [Okta 中繼資料] 文字方塊中。
    6.  按一下 [儲存]。

7.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-smarteru-tutorial/IC777328.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 SmarterU，必須將他們佈建到 SmarterU。SmarterU 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **SmarterU** 租用戶。

2.  移至 [使用者]。

3.  在 [使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-smarteru-tutorial/IC777329.png "新增使用者")

    1.  按一下 [新增使用者]。
    2.  在下列文字方塊中輸入該 Azure AD 使用者帳戶的相關屬性值：**主要電子郵件地**、**員工識別碼**、**密碼**、**確認密碼**、**名字**、**姓氏**。
    3.  按一下 [作用中]。
    4.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 SmarterU 使用者帳戶建立工具或 SmarterU 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 SmarterU，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [SmarterU] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-smarteru-tutorial/IC777330.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-smarteru-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資料，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->