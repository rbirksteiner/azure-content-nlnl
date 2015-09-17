<properties pageTitle="教學課程：Azure Active Directory 與 UserVoice 整合 | Microsoft Azure" description="了解如何使用 UserVoice 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 UserVoice 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=524477)。
  
本教學課程的目的是要示範 Azure 與 UserVoice 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   UserVoice 租用戶
  
完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 UserVoice 的 Azure AD 使用者就能夠單一登入您 UserVoice 公司網站 (服務提供者起始登入) 的應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 UserVoice 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-uservoice-tutorial/IC777514.png "案例")

##啟用 UserVoice 的應用程式整合
  
本節的目的是要說明如何啟用 UserVoice 的應用程式整合。

###若要啟用 UserVoice 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-uservoice-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-uservoice-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-uservoice-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **UserVoice**。

    ![應用程式庫](./media/active-directory-saas-uservoice-tutorial/IC777513.png "應用程式庫")

7.  在結果窗格中，選取 [UserVoice]，然後按一下 [完成] 以新增應用程式。

    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 UserVoice。設定 UserVoice 的單一登入需要您從憑證擷取指紋值。如果您不熟悉這個程序，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [UserVoice] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777515.png "設定單一登入")

2.  在 [您希望使用者如何登入 UserVoice] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777516.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [UserVoice 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.UserVoice.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-uservoice-tutorial/IC777517.png "設定應用程式 URL")

4.  於 [在 UserVoice 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後在本機電腦上將憑證檔案儲存為 **c:\\UserVoice.cer**。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777518.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 UserVoice 公司網站。

6.  在頂端工具列中，按一下 [設定]，然後從功能表選取 [Web 入口網站]。

    ![設定](./media/active-directory-saas-uservoice-tutorial/IC777519.png "設定")

7.  在 [Web 入口網站] 索引標籤的 [使用者驗證] 區段中，按一下 [編輯]，以開啟 [編輯使用者驗證] 對話頁面。

    ![Web 入口網站](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web 入口網站")

8.  在 [編輯使用者驗證] 對話頁面上執行下列步驟：

    ![編輯使用者驗證](./media/active-directory-saas-uservoice-tutorial/IC777521.png "編輯使用者驗證")

    1.  按一下 [單一登入 (SSO)]。
    2.  在 Azure 入口網站中的 [在 UserVoice 設定單一登入] 對話頁面上， 複製**遠端登入 URL** 值，然後將它貼至 [SSO 遠端登入] 文字方塊中。
    3.  在 Azure 入口網站中的 [在 UserVoice 設定單一登入] 對話頁面上， 複製**遠端登出 URL** 值，然後將它貼至 [SSO 遠端登出] 文字方塊中。
    4.  從匯出的憑證複製**指紋** 值，然後將它貼入 [目前憑證 SHA1 指紋] 文字方塊。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    5.  按一下 [儲存驗證設定]。

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-uservoice-tutorial/IC777522.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 UserVoice，則必須將他們佈建到 UserVoice。UserVoice 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **UserVoice** 租用戶。

2.  移至 [設定]。

    ![設定](./media/active-directory-saas-uservoice-tutorial/IC777811.png "設定")

3.  按一下 [一般]。

4.  按一下 [代理程式和權限]。

    ![代理程式和權限](./media/active-directory-saas-uservoice-tutorial/IC777812.png "代理程式和權限")

5.  按一下 [加入管理員]。

    ![加入管理員](./media/active-directory-saas-uservoice-tutorial/IC777813.png "加入管理員")

6.  在 [邀請管理員] 對話方塊中，執行下列步驟：

    ![邀請管理員](./media/active-directory-saas-uservoice-tutorial/IC777814.png "邀請管理員")

    1.  在 [電子郵件] 文字方塊中，輸入您要佈建之帳戶的電子郵件地址，然後按一下 [加入]。
    2.  按一下 [邀請]。

>[AZURE.NOTE]您可以使用任何其他的 UserVoice 使用者帳戶建立工具或 UserVoice 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 UserVoice，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [UserVoice 應用程式整合] 頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-uservoice-tutorial/IC777523.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-uservoice-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->