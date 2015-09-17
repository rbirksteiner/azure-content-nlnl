<properties pageTitle="教學課程：Azure Active Directory 與 Citrix ShareFile 整合 | Microsoft Azure" description="了解如何使用 Citrix ShareFile 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Citrix ShareFile 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=523754)。

本教學課程的目的是要示範 Azure 與 Citrix ShareFile 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Citrix ShareFile 租用戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Citrix ShareFile 的 Azure AD 使用者就能夠單一登入您 Citrix ShareFile 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Citrix ShareFile 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "案例")
##啟用 Citrix ShareFile 的應用程式整合

本節的目的是要說明如何啟用 Citrix ShareFile 的應用程式整合。

###若要啟用 Citrix ShareFile 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Citrix ShareFile**。

    ![應用程式庫](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "應用程式庫")

7.  在結果窗格中，選取 [Citrix ShareFile]，然後按一下 [完成] 以加入應用程式。

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Citrix ShareFile。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Citrix ShareFile] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入]對話方塊。

    ![啟用單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "啟用單一登入")

2.  在 [要如何讓使用者登入 Citrix ShareFile] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Citrix ShareFile 登入 URL] 文字方塊中，使用下列模式輸入您的 URL：`https://<tenant-name>.shareFile.com`，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "設定應用程式 URL")

4.  於 [在 Citrix ShareFile 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **Citrix ShareFile** 公司網站。

6.  在頂端工具列中，按一下 [管理]。

7.  在左側瀏覽窗格中，選取 [設定單一登入]。

    ![帳戶管理](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "帳戶管理")

8.  在 [單一登入/SAML 2.0 組態] 對話頁面的 [基本設定] 下方，執行下列步驟：

    ![單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "單一登入")

    1.  按一下 [啟用 SAML]。
    2.  在 Azure 入口網站的 [在 Citrix ShareFile 設定單一登入] 對話頁面上，複製**實體識別碼**值，然後將它貼至 [您的 IDP 簽發者/實體識別碼] 文字方塊中。
    3.  在 Azure 入口網站的 [在 Citrix ShareFile 設定單一登入] 對話頁面上，複製**遠端登入 URL** 值，然後將它貼至 [登入 URL] 文字方塊中。
    4.  在 Azure 入口網站的 [在 Citrix ShareFile 設定單一登入] 對話頁面上，複製**遠端登出 URL** 值，然後將它貼至 [登出 URL] 文字方塊中。
    5.  按一下 [X.509 憑證] 欄位旁的 [變更]，然後上傳您從 Azure AD 入口網站下載的憑證。![基本設定](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "基本設定")

9.  在 Citrix ShareFile 管理入口網站上，按一下 [儲存]。

10. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Citrix ShareFile，必須將他們佈建到 Citrix ShareFile。Citrix ShareFile 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **Citrix ShareFile** 租用戶。

2.  按一下 [管理使用者] > [管理使用者首頁] > [+ 建立員工]。

    ![建立員工](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "建立員工")

3.  輸入您想要佈建之有效 Azure AD 帳戶的**電子郵件**、**名字**和**姓氏**。

    ![基本資訊](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "基本資訊")

4.  按一下 [加入使用者]。

    >[AZURE.NOTE]AAD 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

>[AZURE.NOTE]您可以使用任何其他的 Citrix ShareFile 使用者帳戶建立工具或 Citrix ShareFile 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Citrix ShareFile，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Citrix ShareFile] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->