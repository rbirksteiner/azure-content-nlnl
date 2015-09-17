<properties pageTitle="教學課程：Azure Active Directory 與 CloudBees 整合 | Microsoft Azure" description="了解如何使用 CloudBees 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 CloudBees 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528737)。

本教學課程的目的是要示範 Azure 與 CloudBees 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   CloudBees 租用戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/azure/dn308586.aspx)，您指派給 CloudBees 的 Azure AD 使用者就能夠單一登入您 CloudBees 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 CloudBees 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "案例")

##啟用 CloudBees 的應用程式整合

本節的目的是要說明如何啟用 CloudBees 的應用程式整合。

###若要啟用 CloudBees 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入**CloudBees**。

    ![應用程式庫](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "應用程式庫")

7.  在結果窗格中，選取 [CloudBees]，然後按一下 [完成] 以加入應用程式。

    ![Cloudbees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "Cloudbees")

##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 CloudBees。您必須從憑證擷取指紋值，才能設定 CloudBees 的單一登入。如果您不熟悉這個程序，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [CloudBees] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "設定單一登入")

2.  在 [要如何讓使用者登入 CloudBees] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [CloudBees 登入 URL] 文字方塊中，輸入您的使用者用來登入 CloudBees 的 URL (例如：**https://grandcentral.cloudbees.com/login*")，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "設定應用程式 URL")

4.  於 [在 CloudBees 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 CloudBees 公司網站。

6.  在頂端的功能表中，按一下 [設定]。

    ![設定](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "設定")

7.  按一下 [SSO 整合] 索引標籤。

8.  在 [設定 SAML 2.0 單一登入] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "設定單一登入")

    1.  在 Azure AD 入口網站的 [在 CloudBees 設定單一登入] 頁面上，複製**遠端登入 URL** 值，然後將它貼至 [遠端登入 URL] 文字方塊中。
    2.  從匯出的憑證複製**指紋**值，然後將它貼入 [現有的憑證指紋] 文字方塊。
    
        >[AZURE.TIP]如需詳細資訊，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    3.  在 [驗證網域] 文字方塊中，輸入您公司的網域。
    4.  按一下 [儲存]。

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "設定單一登入")

##設定使用者佈建

若要讓 Azure AD 使用者可以登入 **CloudBees**，必須將他們佈建到 **CloudBees**。**CloudBees** 需以手動方式佈建。

###若要將使用者帳戶佈建到 CloudBees，請執行下列步驟：

1.  以系統管理員身分登入您的 **CloudBees** 公司網站。

2.  移至 [編輯使用者]。

    ![使用者](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "使用者")

3.  按一下 [新增]。

    ![新增](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "新增")

4.  在 [加入使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "新增使用者")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的**電子郵件**、**名字**、**姓氏**和其他屬性。
    2.  按一下 [加入使用者]。

        >[AZURE.NOTE]帳戶的擁有者會收到一封含有登入指示的電子郵件。

>[AZURE.NOTE]您可以使用任何其他的 CloudBees 使用者帳戶建立工具或 CloudBees 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 CloudBees，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [CloudBees] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/azure/dn308586.aspx)。

<!---HONumber=August15_HO7-->