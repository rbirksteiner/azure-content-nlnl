<properties pageTitle="教學課程：Azure Active Directory 與 Panorama9 整合 | Microsoft Azure" description="了解如何使用 Panorama9 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Panorama9 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528724)。
  
本教學課程的目的是要示範 Azure 與 Panorama9 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Panorama9 單一登入的訂用帳戶
  
完成本教學課程之後，您指派給 Panorama9 的 Azure AD 使用者就能夠單一登入您 Panorama9 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Panorama9 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-panorama9-tutorial/IC790016.png "案例")
##啟用 Panorama9 的應用程式整合
  
本節的目的是要說明如何啟用 Panorama9 的應用程式整合。

###若要啟用 Panorama9 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-panorama9-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-panorama9-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-panorama9-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在**搜尋方塊**中輸入 **Panorama9**。

    ![應用程式庫](./media/active-directory-saas-panorama9-tutorial/IC790017.png "應用程式庫")

7.  在結果窗格中，選取 [Panorama9]，然後按一下 [完成] 以新增應用程式。

    ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Panorama9 中進行驗證。設定 Panorama9 的單一登入需要您從憑證抓取憑證指紋值。如果您不熟悉這個程序，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Panorama9] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-panorama9-tutorial/IC790019.png "設定單一登入")

2.  在 [您希望使用者如何登入 Panorama9] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-panorama9-tutorial/IC790020.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Panorama9 登入 URL] 文字方塊中，輸入使用者登入您的 Panorama9 時所使用的 URL (如："*https://dashboard.panorama9.com/saml/access/3262*"))，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-panorama9-tutorial/IC790021.png "設定應用程式 URL")

4.  在 [設定在 Panorama9 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-panorama9-tutorial/IC790022.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Panorama9 公司網站。

6.  在最上面的工具列中，按一下 [管理]，然後按一下 [擴充功能]。

    ![擴充功能](./media/active-directory-saas-panorama9-tutorial/IC790023.png "擴充功能")

7.  在 [擴充功能] 對話方塊中，按一下 [單一登入]。

    ![單一登入](./media/active-directory-saas-panorama9-tutorial/IC790024.png "單一登入")

8.  在 [設定] 區段中，執行下列步驟：

    ![設定](./media/active-directory-saas-panorama9-tutorial/IC790025.png "設定")

    1.  在 Azure 入口網站的 [設定在 Panorama9 單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [識別提供者 URL] 文字方塊中。
    2.  從匯出的憑證複製 [指紋] 值，然後將它貼入 [憑證指紋] 文字方塊。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    3.  按一下 [儲存]。

9.  在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-panorama9-tutorial/IC790026.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Panorama9，必須將他們佈建到 Panorama9。Panorama9 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Panorama9** 公司網站。

2.  在頂端的功能表中，按一下 [管理]，然後按一下 [使用者]。

    ![使用者](./media/active-directory-saas-panorama9-tutorial/IC790027.png "使用者")

3.  按一下 **+**。

4.  在 [使用者資料] 區段中，執行下列步驟：

    ![使用者](./media/active-directory-saas-panorama9-tutorial/IC790028.png "使用者")

    1.  在 [電子郵件] 文字方塊中輸入您想要佈建的有效 Azure Active Directory 使用者電子郵件地址。
    2.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 Panorama9 使用者帳戶建立工具或 Panorama9 提供的 API，佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要將使用者指派給 Panorama9，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Panorama9] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-panorama9-tutorial/IC790029.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-panorama9-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->