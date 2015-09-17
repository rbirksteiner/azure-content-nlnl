<properties pageTitle="教學課程：Azure Active Directory 與 Adaptive Suite 整合 | Microsoft Azure" description="了解如何使用 Adaptive Suite 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Adaptive Suite 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=615749)。

本教學課程的目的是要示範 Azure 與 Adaptive Suite 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Adaptive Suite 租用戶

完成本教學課程之後，您指派給 Adaptive Suite 的 Azure AD 使用者就能夠單一登入您 Adaptive Suite 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Adaptive Suite 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "案例")
##啟用 Adaptive Suite 的應用程式整合

本節的目的是概述如何啟用 Adaptive Suite 的應用程式整合。

###若要啟用 Adaptive Suite 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Adaptive Suite**。

    ![應用程式庫](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "應用程式庫")

7.  在結果窗格中，選取 [Adaptive Suite]，然後按一下 [完成] 加入應用程式。

    ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Adaptive Suite 中進行驗證。您必須從憑證擷取指紋值，才能設定 Adaptive Suite 的單一登入。如果您不熟悉這個程序，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Adaptive Suite] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "設定單一登入")

2.  在 [要如何讓使用者登入 Adaptive Suite] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "設定單一登入")

3.  在 [設定應用程式設定] 頁面的 [回覆 URL] 文字方塊中，使用下列模式輸入您的 URL："**https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*"，然後按 [下一步]。

    >[AZURE.NOTE]您可以從 Adaptive Suite 的 [SAML SSO 設定] 頁面取得這個值。

    ![設定 App 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "設定 App 設定")

4.  在 [設定在 Adaptive Suite 單一登入] 頁面上，按一下 [下載憑證] 下載您的憑證，然後將憑證檔案儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Adaptive Suite 公司網站。

6.  移至 [管理員]。

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

7.  在 [使用者和角色] 區段中，按一下 [管理 SAML SSO 設定]。

    ![管理 SAML SSO 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "管理 SAML SSO 設定")

8.  在 [SAML SSO 設定] 頁面上，執行下列步驟：

    ![SAML SSO 設定](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO 設定")

    1.  在 [識別提供者名稱] 文字方塊中，輸入您組態的名稱。
    2.  在 Azure 入口網站的 [設定在 Adaptive Suite 單一登入] 對話頁面上，複製 [實體 ID] 值，然後將它貼至 [識別提供者實體 ID] 文字方塊中。
    3.  在 Azure 入口網站的 [設定在 Adaptive Suite 單一登入] 對話頁面上，複製 [SAML SSO URL] 值，然後將它貼至 [識別提供者 SSO URL] 文字方塊中。
    4.  在 Azure 入口網站的 [設定在 Adaptive Suite 單一登入] 對話頁面上，複製 [SAML SSO URL] 值，然後將它貼至 [自訂登出 URL] 文字方塊中。
    5.  若要上傳您下載的憑證，請按一下 [選擇檔案]。
    6.  針對 [SAML 使用者識別碼]，選取 [使用者的 Adaptive Insights 使用者名稱]。
    7.  針對 [SAML 使用者識別碼位置]，選取 [主旨 NameID 中的使用者識別碼]。
    8.  針對 [SAML NameID 格式]，選取 [電子郵件地址]。
    9.  針對 [啟用 SAML]，選取 [允許 SAML SSO 和直接 Adaptive Insights 登入]。
    10. 按一下 [儲存]。

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Adaptive Suite，必須將他們佈建到 Adaptive Suite。Adaptive Suite 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Adaptive Suite** 公司網站。

2.  移至 [管理員]。

    ![Admin](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

3.  在 [使用者和角色] 區段中，按一下 [新增使用者]。

    ![新增使用者](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "新增使用者")

4.  在 [新使用者] 區段中，執行下列步驟：

    ![提交](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "提交")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 使用者的 [名稱]、[登入]、[電子郵件]、[密碼]。
    2.  選取 [角色]。
    3.  按一下 [提交]。

>[AZURE.NOTE]您可以使用任何其他的 Adaptive Suite 使用者帳戶建立工具或 Adaptive Suite 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Adaptive Suite，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Adaptive Suite] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->