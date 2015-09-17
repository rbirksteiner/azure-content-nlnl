<properties pageTitle="教學課程：Azure Active Directory 與 Gigya 整合 | Microsoft Azure" description="了解如何使用 Gigya 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Gigya 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528575)。
  
本教學課程的目的是要示範 Azure 與 Gigya 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Gigya 單一登入的訂用帳戶
  
完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Gigya 的 Azure AD 使用者就能夠單一登入您 Gigya 公司網站 (服務提供者起始登入) 的應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Gigya 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789512.png "設定單一登入")
##啟用 Gigya 的應用程式整合
  
本節的目的是要說明如何啟用 Gigya 的應用程式整合。

###若要啟用 Gigya 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-gigya-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-gigya-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-gigya-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Gigya**。

    ![應用程式庫](./media/active-directory-saas-gigya-tutorial/IC789513.png "應用程式庫")

7.  在結果窗格中，選取 [Gigya]，然後按一下 [完成] 以加入應用程式。

    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Gigya。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Gigya] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789528.png "設定單一登入")

2.  在 [您希望使用者如何登入 Gigya] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789529.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Gigya 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**http://company.gigya.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-gigya-tutorial/IC789530.png "設定應用程式 URL")

4.  於 [在 Gigya 設定單一登入] 頁面上，按 [下載憑證]，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789531.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Gigya 公司網站。

6.  移至 [設定] > [SAML 登入]，然後按一下 [新增] 按鈕。

    ![SAML 登入](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML 登入")

7.  在 [SAML 登入] 區段中，執行下列步驟：

    ![SAML 組態](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML 組態")

    1.  在 [名稱] 文字方塊中，輸入您的組態名稱。
    2.  在 Azure 入口網站中的 [在 Gigya 設定單一登入] 對話頁面上，複製 **簽發者 URL** 值，然後將它貼至 [簽發者] 文字方塊中。
    3.  在 Azure 入口網站中的 [設定在 Gigya 單一登入] 對話頁面上，複製**單一登入服務 URL** 值，然後將它貼至 [單一登入服務 URL] 文字方塊中。
    4.  在 Azure 入口網站中的 [設定在 Gigya 單一登入] 對話頁面上，複製**名稱識別碼格式**值，然後將它貼至 [名稱識別碼格式] 文字方塊中。
    5.  從您下載的憑證建立 **base-64 編碼**檔案。
        
		>[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼到 [X.509 憑證] 文字方塊中。
    7.  按一下 [儲存設定]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789534.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Gigya，則必須將他們佈建到 Gigya。Gigya 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **Gigya** 公司網站。

2.  移至 [系統管理員] > [管理使用者]，然後按一下 [邀請使用者]。

    ![管理使用者](./media/active-directory-saas-gigya-tutorial/IC789535.png "管理使用者")

3.  在 [邀請使用者] 對話方塊中，執行下列步驟：

    ![邀請使用者](./media/active-directory-saas-gigya-tutorial/IC789536.png "邀請使用者")

    1.  在 [電子郵件] 文字方塊中，輸入您要佈建之有效 Azure Active Directory 帳戶的電子郵件別名。
    2.  按一下 [邀請使用者]。
    
        >[AZURE.NOTE]Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

>[AZURE.NOTE]您可以使用任何其他的 Gigya 使用者帳戶建立工具或 Gigya 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Gigya，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Gigya] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-gigya-tutorial/IC789537.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-gigya-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->