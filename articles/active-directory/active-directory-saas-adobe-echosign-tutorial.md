<properties pageTitle="教學課程：Azure Active Directory 與 Adobe EchoSign 整合 | Microsoft Azure" description="了解如何使用 Adobe EchoSign 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Adobe EchoSign 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528195)。

本教學課程的目的是要示範 Azure 與 Adobe EchoSign 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Adobe EchoSign 單一登入的訂用帳戶

完成本教學課程之後，您指派給 Adobe EchoSign 的 Azure AD 使用者就能夠單一登入您 Adobe EchoSign 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Adobe EchoSign 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "案例")
##啟用 Adobe EchoSign 的應用程式整合

本節的目的是概述如何啟用 Adobe EchoSign 的應用程式整合。

###若要啟用 Adobe EchoSign 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Adobe EchoSign**。

    ![應用程式庫](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "應用程式庫")

7.  在結果窗格中，選取 [Adobe EchoSign]，然後按一下 [完成] 加入應用程式。

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Adobe EchoSign 中進行驗證。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Adobe EchoSign] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "設定單一登入")

2.  在 [要如何讓使用者登入 Adobe EchoSign] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Adobe EchoSign 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**https://company.echosign.com/*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "設定應用程式 URL")

4.  在 [設定在 Adobe EchoSign 單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Adobe EchoSign 公司網站。

6.  在頂端的功能表中，按一下 [帳戶]，然後在左側瀏覽窗格中，按一下 [帳戶設定] 下的 [SAML 設定]。

    ![帳戶](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "帳戶")

7.  在 [SAML 設定] 區段中，執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML 設定")

    1.  針對 [SAML 模式]，選取 [SAML 強制]。
    2.  選取 [允許 EchoSign 帳戶管理員使用其 EchoSign 認證登入]。
    3.  針對 [使用者建立]，選取 [透過 SAML 自動新增已驗證的使用者]。

8.  繼續執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML 設定")

    1.  在 Azure 入口網站的 [設定在 Adobe EchoSign 單一登入] 對話頁面上，複製 [實體ID] 值，然後將它貼至 [IdP 實體ID] 文字方塊中。
    2.  在 Azure 入口網站的 [設定在 Adobe EchoSign 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [IdP 登入 URL] 文字方塊中。
    3.  在 Azure 入口網站的 [設定在 Adobe EchoSign 單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [IdP 登出 URL] 文字方塊中。
    4.  從您下載的憑證建立 **base-64 編碼**檔案。  

		>[AZURE.TIP]如需詳細資料，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)
    5.  在記事本中開啟您的 base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼至 [IdP 憑證] 文字方塊中。
    6.  按一下 [儲存變更]。

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Adobe EchoSign，必須將他們佈建到 Adobe EchoSign。Adobe EchoSign 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **Adobe EchoSign** 公司網站。

2.  在頂端的功能表中，按一下 [帳戶]，然後在左側瀏覽窗格中，按一下 [使用者和群組]，接著按一下 [建立新的使用者]。

    ![帳戶](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "帳戶")

3.  在 [建立新的使用者] 區段中，執行下列步驟：

    ![建立使用者](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "建立使用者")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 ADD 帳戶的 [電子郵件地址]、[名字] 和 [姓氏]。
    2.  按一下 [建立使用者]。

		>[AZURE.NOTE]Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以便在帳戶啟用前加以確認。

>[AZURE.NOTE]您可以使用任何其他的 Adobe EchoSign 使用者帳戶建立工具或 Adobe EchoSign 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Adobe EchoSign，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Adobe EchoSign] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->