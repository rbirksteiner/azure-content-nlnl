<properties pageTitle="教學課程：Azure Active Directory 與 ITRP 整合 | Microsoft Azure" description="了解如何使用 ITRP 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 ITRP 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=524329)。
  
本教學課程的目的是要示範 Azure 與 ITRP 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ITRP 租用戶
  
完成本教學課程之後，您已指派至 ITRP 的 Azure AD 使用者將能夠在 ITRP 公司網站 (服務提供者起始登入) ，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)執行單一登入來登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ITRP 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-itrp-tutorial/IC775551.png "案例")
##啟用 ITRP 的應用程式整合
  
本節的目的是要說明如何啟用 ITRP 的應用程式整合。

###若要啟用 ITRP 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-itrp-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-itrp-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-itrp-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在搜尋方塊中，輸入 **ITRP**。

    ![應用程式庫](./media/active-directory-saas-itrp-tutorial/IC775565.png "應用程式庫")

7.  在結果窗格中，選取 [ITRP]，然後按一下 [完成] 以加入應用程式。

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 ITRP 中進行驗證。設定 ITRP 的單一登入需要您從憑證抓取指紋值。如果您不熟悉這個程序，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [ITRP] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC771709.png "設定單一登入")

2.  在 [您希望使用者如何登入 ITRP] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC775567.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [ITRP 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<tenant-name>.ITRP.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-itrp-tutorial/IC775568.png "設定應用程式 URL")

4.  在 [設定在 ITRP 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後在本機電腦上將憑證檔案儲存為 **c:\\ITRP.cer**。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC775569.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 ITRP 公司網站。

6.  在頂端的工具列中，按一下 [設定]。

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  在左導覽窗格中，選取 [單一登入]。

    ![單一登入](./media/active-directory-saas-itrp-tutorial/IC775571.png "單一登入")

8.  在 [單一登入] 設定頁面上，執行下列步驟：

    ![單一登入](./media/active-directory-saas-itrp-tutorial/IC775572.png "單一登入")

    ![單一登入](./media/active-directory-saas-itrp-tutorial/IC775573.png "單一登入")

    1.  按一下 [啟用]。
    2.  在 Azure 入口網站中的 [設定在 ITRP 單一登入] 對話方塊頁面上， 複製 [遠端登出 URL] 值，然後將它貼至 [遠端登出] 文字方塊中。
    3.  在 Azure 入口網站中的 [設定在 ITRP 單一登入] 對話方塊頁面上， 複製 [SAML SSO URL] 值，然後將它貼至 [SAML SSO URL] 文字方塊中。
    4.  從匯出的憑證複製 [指紋] 值，然後將它貼入 [憑證指紋] 文字方塊。
        
		>[AZURE.TIP]如需詳細資訊，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    5.  按一下 [儲存]。

9.  在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC775574.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 ITRP，必須將他們佈建到 ITRP。在 ITRP 的情況下，佈建是手動工作。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **ITRP** 租用戶。

2.  在頂端的工具列中，按一下 [記錄]。

    ![Admin](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3.  選取快顯功能表中的 [人員]。

    ![人員](./media/active-directory-saas-itrp-tutorial/IC775587.png "人員")

4.  按一下 [新增人員] (“+”)。

    ![Admin](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5.  在 [新增人員] 對話方塊上，執行下列步驟：

    ![使用者](./media/active-directory-saas-itrp-tutorial/IC775577.png "使用者")

    1.  輸入您想要佈建之有效 AAD 帳戶的 [名稱]、[電子郵件]。
    2.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 ITRP 使用者帳戶建立工具或 ITRP 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 ITRP，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [ITRP] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-itrp-tutorial/IC775588.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-itrp-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->