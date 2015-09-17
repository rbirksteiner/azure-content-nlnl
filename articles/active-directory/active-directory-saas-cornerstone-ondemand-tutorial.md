<properties pageTitle="教學課程：Azure Active Directory 與 Cornerstone OnDemand 整合 | Microsoft Azure " description="了解如何使用 Cornerstone OnDemand 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Cornerstone OnDemand 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=526246)。

本教學課程的目的是要示範 Azure 與 Cornerstone OnDemand 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Cornerstone OnDemand 租用戶

完成本教學課程之後，您指派給 Cornerstone OnDemand 的 Azure AD 使用者就能夠單一登入您 Cornerstone OnDemand 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Cornerstone OnDemand 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "案例")
##啟用 Cornerstone OnDemand 的應用程式整合

本節的目的是要說明如何啟用 Cornerstone OnDemand 的應用程式整合。

###若要啟用 Cornerstone OnDemand 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **cornerstone ondemand**。

    ![應用程式庫](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "應用程式庫")

7.  在結果窗格中，選取 [Cornerstone OnDemand]，然後按一下 [完成] 來加入應用程式。

    ![Cornerstone OnDemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Cornerstone OnDemand。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Cornerstone OnDemand] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "啟用單一登入")

2.  在 [要如何讓使用者登入 Cornerstone OnDemand] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD 單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Cornerstone OnDemand 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**http://company.csod.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "設定應用程式 URL")

4.  在 [設定在 Cornerstone OnDemand 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的本機電腦上。

    ![設定單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "設定單一登入")

5.  將下列項目傳送至 Cornerstone OnDemand 支援小組：

    1.  下載的憑證
    2.  **遠端登入 URL**值
    3.  **遠端登入 URL**值。

    >[AZURE.NOTE]單一登入必須由 Cornerstone OnDemand 支援小組設定。設定完成後，您會收到來自支援小組的通知。

6.  選取單一登入設定確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Cornerstone OnDemand，則必須將他們佈建到 Cornerstone OnDemand。Cornerstone OnDemand 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  將您想要佈建的 Azure AD 使用者資訊 (例如姓名、電子郵件) 傳送給 Cornerstone OnDemand 支援小組。

>[AZURE.NOTE]您可以使用任何其他的 Cornerstone OnDemand 使用者帳戶建立工具或 Cornerstone OnDemand 所提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Cornerstone OnDemand，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Cornerstone OnDemand] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![指派使用者](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "指派使用者")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->