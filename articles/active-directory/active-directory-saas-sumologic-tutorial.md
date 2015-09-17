<properties pageTitle="教學課程：Azure Active Directory 與 SumoLogic 整合 | Microsoft Azure" description="了解如何使用 SumoLogic 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 SumoLogic 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=525318)。
  
本教學課程的目的是要示範 Azure 與 SumoLogic 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   SumoLogic 租用戶
  
完成本教學課程之後，您指派給 SumoLogic 的 Azure AD 使用者將能夠單一登入您 SumoLogic 公司網站 (服務提供者起始登入) 的應用程式，或使用[存取面板](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SumoLogic 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-sumologic-tutorial/IC778549.png "案例")

##啟用 SumoLogic 的應用程式整合
  
本節的目的是概述如何啟用 SumoLogic 的應用程式整合。

###若要啟用 SumoLogic 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-sumologic-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-sumologic-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-sumologic-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **sumologic**。

    ![應用程式庫](./media/active-directory-saas-sumologic-tutorial/IC778550.png "應用程式庫")

7.  在結果窗格中，選取 [SumoLogic]，然後按一下 [完成] 加入應用程式。

    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 SumoLogic 中進行驗證。在此程序中，您需要將 base-64 編碼憑證上傳到您的 SumoLogic 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [SumoLogic] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778552.png "設定單一登入")

2.  在 [要如何讓使用者登入 SumoLogic] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778553.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [SumoLogic 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.SumoLogic.com*"，然後按 [下一步]。

    ![設定 aoo URL](./media/active-directory-saas-sumologic-tutorial/IC778554.png "設定 aoo URL")

4.  在 [設定在 SumoLogic 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778555.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 SumoLogic 公司網站。

6.  移至 [管理] > [安全性]。

    ![管理](./media/active-directory-saas-sumologic-tutorial/IC778556.png "管理")

7.  按一下 [SAML]。

    ![全域安全性設定](./media/active-directory-saas-sumologic-tutorial/IC778557.png "全域安全性設定")

8.  從 [選取組態或建立新的組態] 清單中選取 [Azure AD]，然後按一下 [設定]。

    ![設定 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "設定 SAML 2.0")

9.  在 [設定 SAML 2.0] 對話方塊上，執行下列步驟：

    ![設定 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "設定 SAML 2.0")

    1.  在 [組態名稱] 文字方塊中，輸入 **Azure AD**。
    2.  選取 [偵錯模式]。
    3.  在 Azure 入口網站中的 [設定在 SumoLogic 單一登入] 對話頁面上， 複製 [簽發者 URL] 值，然後將它貼至 [簽發者] 文字方塊中。
    4.  在 Azure 入口網站中的 [設定在 SumoLogic 單一登入] 對話頁面上， 複製 [驗證要求 URL] 值，然後將它貼至 [驗證要求 URL] 文字方塊中。
    5.  從您下載的憑證建立 **Base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後將整個憑證貼到 [X.509 憑證] 文字方塊中。
    7.  在 [電子郵件屬性] 選取 [使用 SAML 主體]。
    8.  選取 [服務提供者起始登入組態]。
    9.  在 [登入路徑] 文字方塊中輸入 **Azure**。
    10. 按一下 [儲存]。

10. 在 Azure 入口網站的 [設定在 SumoLogic 單一登入] 對話方塊上，選取 [單一登入設定確認]，然後按一下 [完成]。

    ![設定單一登入](./media/active-directory-saas-sumologic-tutorial/IC778560.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 SumoLogic，必須將他們佈建到 SumoLogic。SumoLogic 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **SumoLogic** 租用戶。

2.  移至 [管理] > [使用者]。

    ![使用者](./media/active-directory-saas-sumologic-tutorial/IC778561.png "使用者")

3.  按一下 [新增]。

    ![使用者](./media/active-directory-saas-sumologic-tutorial/IC778562.png "使用者")

4.  在 [新增使用者] 對話方塊上，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-sumologic-tutorial/IC778563.png "新增使用者")

    1.  在 [名字]、[姓氏] 和 [電子郵件] 文字方塊中，輸入您想要佈建之 Azure AD 帳戶的相關資訊。
    2.  選取角色。
    3.  在 [狀態] 選取 [作用中]。
    4.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 SumoLogic 使用者帳戶建立工具或 SumoLogic 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 SumoLogic，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [SumoLogic] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-sumologic-tutorial/IC778564.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-sumologic-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->