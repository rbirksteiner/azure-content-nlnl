<properties pageTitle="教學課程：Azure Active Directory 與 Mozy Enterprise 整合 | Microsoft Azure" description="了解如何使用 Mozy Enterprise 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Mozy Enterprise 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=524186)。
  
本教學課程的目的是要示範 Azure 與 Mozy Enterprise 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Mozy Enterprise 租用戶
  
完成本教學課程之後，您指派給 Mozy Enterprise 的 Azure AD 使用者就能夠單一登入您 Mozy Enterprise 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Mozy Enterprise 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "案例")
##啟用 Mozy Enterprise 的應用程式整合
  
本節的目的是要說明如何啟用 Mozy Enterprise 的應用程式整合。

###若要啟用 Mozy Enterprise 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在**搜尋方塊**中輸入 **mozy enterprise**。

    ![應用程式庫](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "應用程式庫")

7.  在結果窗格中選取 [Mozy Enterprise]，然後按一下 [完成] 以新增應用程式。

    ![Mozy Enterprise](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy Enterprise")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Mozy Enterprise 中進行驗證。在此程序中，您需要上傳 base-64 編碼憑證到您的 Mozy Enterprise 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 **Mozy Enterprise** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "設定單一登入")

2.  在 [要如何讓使用者登入 Mozy Enterprise] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Mozy Enterprise 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<tenant-name>.Mozyenterprise.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "設定應用程式 URL")

4.  在 [設定在 Mozy Enterprise 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Mozy Enterprise 公司網站。

6.  在 [組態] 區段中，按一下 [驗證原則]。

    ![驗證原則](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "驗證原則")

7.  在 [驗證原則] 區段中，執行下列步驟：

    ![驗證原則](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "驗證原則")

    1.  選取 [目錄服務] 做為**提供者**。
    2.  選取 [使用 LDAP 推送]。
    3.  按一下 [SAML 驗證] 索引標籤。
    4.  在 Azure 入口網站上 [設定在 Mozy Enterprise 單一登入] 對話方塊頁面，複製 [驗證要求 URL] 值，然後將它貼至 [驗證 URL] 文字方塊中。
    5.  在 Azure 入口網站上 [設定在 Mozy Enterprise 單一登入] 對話方塊頁面，複製 [識別提供者 ID] 值，然後將它貼至 [SAML 端點] 文字方塊中。
    6.  從您下載的憑證建立「Base-64 編碼」檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    7.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後將整個憑證貼至 [SAML 憑證] 文字方塊中。
    8.  選取 [針對管理員啟用 SSO 以使用其網路認證登入]。
    9.  按一下 [儲存變更]。

8.  在 Azure 入口網站的 [設定在 Mozy Enterprise 單一登入] 對話方塊上，選取單一登入設定確認，然後按一下 [完成]。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "設定單一登入")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 Mozy Enterprise，必須將他們佈建到 Mozy Enterprise 中。在 Mozy Enterprise 的情況下，需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **Mozy Enterprise** 租用戶。

2.  按一下 [使用者]，然後按一下 [新增使用者]。

    ![使用者](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "使用者")

    >[AZURE.NOTE]只有在 [驗證原則]底下選取 [Mozy] 做為提供者時，才會顯示 [新增使用者] 選項。如果已設定 SAML 驗證，則會在使用者透過單一登入第一次登入時自動新增他們。

3.  在 [新增使用者] 對話方塊上，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "新增使用者")

    1.  從 [選擇群組] 清單中選取一個群組。
    2.  從 [使用者類型] 清單中選取一種類型。
    3.  在 [使用者名稱] 文字方塊中，輸入 Azure AD 使用者的名稱。
    4.  在 [電子郵件] 文字方塊中，輸入 Azure AD 使用者的電子郵件地址。
    5.  選取 [傳送使用者指示電子郵件]。
    6.  按一下 [新增使用者]。

    >[AZURE.NOTE]建立使用者之後，會寄送一封電子郵件給 Azure AD 使用者，其中包含在帳戶變成作用中之前確認帳戶的連結。

>[AZURE.NOTE]您可以使用任何其他的 Mozy Enterprise 使用者帳戶建立工具或 Mozy Enterprise 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
 
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要將使用者指派給 Mozy Enterprise，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Mozy Enterprise] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->