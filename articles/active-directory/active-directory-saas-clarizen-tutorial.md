<properties pageTitle="教學課程：Azure Active Directory 與 Clarizen 整合 | Microsoft Azure" description="了解如何使用 Clarizen 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Clarizen 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=526793)。

本教學課程的目的是要示範 Azure 與 Clarizen 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Clarizen 單一登入的訂用帳戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Clarizen 的 Azure AD 使用者就能夠單一登入您 Clarizen 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Clarizen 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-clarizen-tutorial/IC784679.png "案例")
##啟用 Clarizen 的應用程式整合

本節的目的是要說明如何啟用 Clarizen 的應用程式整合。

###若要啟用 Clarizen 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-clarizen-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-clarizen-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-clarizen-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Clarizen**。

    ![應用程式庫](./media/active-directory-saas-clarizen-tutorial/IC784680.png "應用程式庫")

7.  在結果窗格中，選取 [Clarizen]，然後按一下 [完成] 以加入應用程式。

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Clarizen。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Clarizen] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784682.png "設定單一登入")

2.  在 [要如何讓使用者登入 Clarizen] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784683.png "設定單一登入")

3.  於 [在 Clarizen 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784684.png "設定單一登入")

4.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **Clarizen** 公司網站 (例如：**https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*))。

5.  按一下您的使用者名稱，然後按一下 [設定]。

    ![設定](./media/active-directory-saas-clarizen-tutorial/IC784685.png "設定")

6.  按一下 [全域設定] 索引標籤，然後按一下 [同盟驗證] 旁的 [編輯]。

    ![全域設定](./media/active-directory-saas-clarizen-tutorial/IC786906.png "全域設定")

7.  在 [同盟驗證] 對話方塊中，執行下列步驟：

    ![同盟驗證](./media/active-directory-saas-clarizen-tutorial/IC785892.png "同盟驗證")

    1.  按一下 [上傳] 來上傳您下載的憑證。
    2.  在 Azure 入口網站的 [在 Clarizen 設定單一登入] 對話頁面上，複製**單一登入服務 URL** 值，然後將它貼至 [登入 URL] 文字方塊中。
    3.  在 Azure 入口網站的 [在 Clarizen 設定單一登出] 對話頁面上，複製**單一登出服務 URL** 值，然後將它貼至 [登出 URL] 文字方塊中。
    4.  選取 [使用 POST]。
    5.  按一下 [儲存]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784688.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Clarizen，必須將他們佈建到 Clarizen。Clarizen 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **Clarizen** 公司網站。

2.  按一下 [人員]。

    ![人員](./media/active-directory-saas-clarizen-tutorial/IC784689.png "人員")

3.  按一下 [邀請使用者]。

    ![邀請使用者](./media/active-directory-saas-clarizen-tutorial/IC784690.png "邀請使用者")

4.  在 [邀請人員] 對話頁面上，執行下列步驟：

    ![邀請人員](./media/active-directory-saas-clarizen-tutorial/IC784691.png "邀請人員")

    1.  在 [電子郵件] 文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的電子郵件地址。
    2.  按一下 [邀請]。

    >[AZURE.NOTE]Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Clarizen，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Clarizen] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-clarizen-tutorial/IC784692.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-clarizen-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->