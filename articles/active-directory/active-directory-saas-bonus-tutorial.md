<properties pageTitle="教學課程：Azure Active Directory 與 Bonus.ly 整合 | Microsoft Azure" description="了解如何使用 Bonus.ly 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Bonus.ly 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=523806)。

本教學課程的目的是要示範 Azure 與 Bonus.ly 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Bonus.ly 中的測試租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Bonus.ly 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-bonus-tutorial/IC773679.png "案例")
##啟用 Bonus.ly 的應用程式整合

本節的目的是要說明如何啟用 Bonus.ly 的應用程式整合。

###若要啟用 Bonus.ly 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![啟用單一登入](./media/active-directory-saas-bonus-tutorial/IC773680.png "啟用單一登入")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-bonus-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-bonus-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-bonus-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Bonus.ly**。

    ![應用程式庫](./media/active-directory-saas-bonus-tutorial/IC773681.png "應用程式庫")

7.  在結果窗格中，選取 [Bonus.ly]，然後按一下 [完成] 以加入應用程式。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Bonus.ly。您必須從憑證擷取指紋值，才能設定 Bonus.ly 的單一登入。如果您不熟悉這個程序，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Bonus.ly] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC749323.png "設定單一登入")

2.  在 [要如何讓使用者登入 Bonus.ly] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773683.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Bonus.ly 租用戶 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.Bonus.ly*"，然後按 [下一步]：

    ![設定應用程式 URL](./media/active-directory-saas-bonus-tutorial/IC773684.png "設定應用程式 URL")

4.  在 [設定在 Bonus.ly 單一登入] 頁面上，按 [下載憑證]，然後在本機將憑證檔案另存為 **c:\\Bonusly.cer**。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773685.png "設定單一登入")

5.  在不同的瀏覽器視窗中，登入您的 **Bonus.ly** 租用戶。

6.  在頂端工具列中，按一下 [設定]，然後選取 [整合與應用程式]。

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  在 [單一登入] 下方，選取 [SAML]。

8.  在 [SAML] 對話頁面上，執行下列步驟：

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  在 Azure 入口網站的 [在 Bonus.ly 設定單一登入] 對話頁面上，複製**遠端登入 URL** 值，然後將它貼至 [IdP SSO 目標 URL] 文字方塊中。
    2.  在 Azure 入口網站的 [在 Bonus.ly 設定單一登入] 對話頁面上，複製**簽發者識別碼**值，然後將它貼至 [IdP 簽發者] 文字方塊中。
    3.  在 Azure 入口網站的 [設定在 Bonus.ly 單一登入] 對話頁面上，複製**遠端登入 URL** 值，然後將它貼至 [IdP 登入 URL] 文字方塊中。
    4.  從匯出的憑證複製**指紋**值，然後將它貼入 [憑證指紋] 文字方塊。

        >[AZURE.TIP]如需詳細資訊，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

9.  按一下 [儲存]。

10. 在 Microsoft Azure AD 入口網站上，選取組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773689.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Bonus.ly，必須將他們佈建到 Bonus.ly。Bonus.ly 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在網頁瀏覽器視窗中，登入您的 Bonus.ly 租用戶。

2.  按一下 [設定]。

    ![設定](./media/active-directory-saas-bonus-tutorial/IC781041.png "設定")

3.  按一下 [使用者和獎勵] 索引標籤。

    ![使用者和獎勵](./media/active-directory-saas-bonus-tutorial/IC781042.png "使用者和獎勵")

4.  按一下 [管理使用者]。

    ![管理使用者](./media/active-directory-saas-bonus-tutorial/IC781043.png "管理使用者")

5.  按一下 [加入使用者]。

    ![新增使用者](./media/active-directory-saas-bonus-tutorial/IC781044.png "新增使用者")

6.  在 [加入使用者] 對話方塊上，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-bonus-tutorial/IC781045.png "新增使用者")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 ADD 帳戶的 [電子郵件]、[名字]、[姓氏]。
    2.  按一下 [儲存]。

    >[AZURE.NOTE]AAD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

>[AZURE.NOTE]您可以使用任何其他的 Bonus.ly 使用者帳戶建立工具或 Bonus.ly 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Bonus.ly，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Bonus.ly] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-bonus-tutorial/IC773690.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-bonus-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->