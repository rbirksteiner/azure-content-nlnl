<properties pageTitle="教學課程：Azure Active Directory 與 Sprinklr 整合 | Microsoft Azure" description="了解如何使用 Sprinklr 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Sprinklr 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=526454)。
  
本教學課程的目的是要示範 Azure 與 Sprinklr 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Sprinklr 租用戶
  
完成本教學課程之後，您指派給 Sprinklr 的 Azure AD 使用者就能夠單一登入您 Sprinklr 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Sprinklr 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "案例")

##啟用 Sprinklr 的應用程式整合
  
本節的目的是概述如何啟用 Sprinklr 的應用程式整合。

###若要啟用 Sprinklr 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **Sprinklr**。

    ![應用程式庫](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "應用程式庫")

7.  在結果窗格中，選取 [Sprinklr]，然後按一下 [完成] 加入應用程式。

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Sprinklr 中進行驗證。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Sprinklr] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "設定單一登入")

2.  在 [要如何讓使用者登入 Sprinklr] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Sprinklr 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.sprinklr.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "設定應用程式 URL")

4.  在 [設定在 Sprinklr 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Sprinklr 公司網站。

6.  移至 [管理] > [設定]。

    ![系統管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "系統管理")

7.  從左窗格移至 [管理夥伴] > [單一登入]。

    ![管理夥伴](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "管理夥伴")

8.  按一下 [新增單一登入]。

    ![單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "單一登入")

9.  在 [單一登入] 頁面上，執行下列步驟：

    ![單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "單一登入")

    1.  在 [名稱] 文字方塊中，輸入您的組態名稱 (例如：*WAADSSOTest*)。
    2.  選取 [啟用]。
    3.  選取 [使用新的 SSO 憑證]。
    4.  從您下載的憑證建立 **base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    5.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼到 [識別提供者憑證] 文字方塊中。
    6.  在 Azure 入口網站中的 [設定在 Sprinklr 單一登入] 對話頁面上，複製 [識別提供者識別碼] 值，然後將它貼至 [實體 ID] 文字方塊中。
    7.  在 Azure 入口網站中的 [設定在 Sprinklr 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [識別提供者登入 URL] 文字方塊中。
    8.  在 Azure 入口網站中的 [設定在 Sprinklr 單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [識別提供者登出 URL] 文字方塊中。
    9.  在 [SAML 使用者識別碼類型] 選取 [判斷提示包含使用者的 sprinklr.com 使用者名稱]。
    10. 在 [SAML 使用者識別碼位置] 選取 [使用者識別碼位於 Subject 陳述式的 NameIdentifier 元素中]。
    11. 關閉 [儲存]。

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "設定單一登入")

##設定使用者佈建
  
必須先針對 Sprinklr 應用程式內的存取佈建 AAD 使用者，才可以讓他們登入。本章節描述如何建立 Sprinklr 內的 AAD 使用者帳戶。

###若要佈建使用者帳戶到 Sprinklr，請執行下列步驟：

1.  以系統管理員身分登入您的 Sprinklr 公司網站。

2.  移至 [管理] > [設定]。

    ![系統管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "系統管理")

3.  從左窗格移至 [管理用戶端] > [使用者]。

    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Settings")

4.  按一下 [新增使用者]。

    ![Settings](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "設定")

5.  在 [編輯使用者] 對話方塊上，執行下列步驟：

    ![編輯使用者](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "編輯使用者")

    1.  在 [電子郵件]、[名字] 和 [姓氏] 文字方塊中，輸入您想要佈建之 Azure AD 使用者帳戶資訊。
    2.  選取 [密碼已停用]。
    3.  選取 [語言]。
    4.  選取 [使用者類型]。
    5.  按一下 [更新]。

    >[AZURE.IMPORTANT]必須選取 [密碼已停用]，才能讓使用者透過識別提供者登入。

6.  請移至 [角色]，然後執行下列步驟：

    ![夥伴角色](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "夥伴角色")

    1.  在 [全域] 清單中選取 [所有權限]。
    2.  按一下 [更新]。

>[AZURE.NOTE]您可以使用任何其他的 Sprinklr 使用者帳戶建立工具或 Sprinklr 提供的 API 來佈建 Azure AD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Sprinklr，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Sprinklr] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資料，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->