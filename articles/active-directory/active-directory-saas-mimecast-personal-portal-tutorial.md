<properties pageTitle="教學課程：Azure Active Directory 與 Mimecast Personal Portal 整合 | Microsoft Azure" description="了解如何使用 Mimecast Personal Portal 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Mimecast Personal Portal 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=529832)。
  
本教學課程的目的是要示範 Azure 與 Mimecast Personal Portal 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Mimecast Personal Portal 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 Mimecast Personal Portal 的 Azure AD 使用者將能夠在 Mimecast Personal Portal 公司網站 (服務提供者起始登入) 執行單一登入來登入應用程式，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)執行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Mimecast Personal Portal 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "案例")
##啟用 Mimecast Personal Portal 的應用程式整合
  
本節的目的是要說明如何啟用 Mimecast Personal Portal 的應用程式整合。

###若要啟用 Mimecast Personal Portal 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在搜尋方塊中，輸入 **Mimecast Personal Portal**。

    ![應用程式庫](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "應用程式庫")

7.  在結果窗格中，選取 [Mimecast Personal Portal]，然後按一下 [完成] 以新增應用程式。

    ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Mimecast Personal Portal 中進行驗證。在此程序中，您必須建立 Base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Mimecast Personal Portal] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "設定單一登入")

2.  在 [您希望使用者如何登入 Mimecast Personal Portal] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Mimecast Personal Portal 登入 URL] 文字方塊中，輸入使用者登入您的 Mimecast Personal Portal 應用程式時所使用的 URL (如：“https://webmail-uk.mimecast.com” 或 “https://webmail-us.mimecast.com”)，然後按一下 [下一步]。

    >[AZURE.NOTE]登入 URL 是地區特定的 URL。

    ![設定應用程式 URL](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "設定應用程式 URL")

4.  在 [設定在 Mimecast Personal Portal 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Mimecast Personal Portal 公司網站。

6.  移至 [服務 > 應用程式]。

    ![[應用程式]](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "[應用程式]")

7.  按一下 [驗證設定檔]。

    ![驗證設定檔](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "驗證設定檔")

8.  按一下 [新驗證設定檔]。

    ![新驗證設定檔](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "新驗證設定檔")

9.  在 [驗證設定檔] 區段中，執行下列步驟：

    ![驗證設定檔](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "驗證設定檔")

    1.  在 [描述] 文字方塊中，輸入您的組態名稱。
    2.  選取 [強制執行 Mimecast Personal Portal 的 SAML 驗證]。
    3.  在 [提供者] 中選取 [Azure Active Directory]。
    4.  在 Azure 入口網站中的 [設定在 Mimecast Personal Portal 單一登入] 對話頁面上，複製 [簽發者 URL] 值，然後將它貼至 [簽發者 URL] 文字方塊中。
    5.  在 Azure 入口網站中的 [設定在 Mimecast Personal Portal 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [登入 URL] 文字方塊中。
    6.  在 Azure 入口網站中的 [設定在 Mimecast Personal Portal 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [登出 URL] 文字方塊中。  

        >[AZURE.NOTE]Mimecast Personal Portal 的登入 URL 值與登出 URL 值是相同的。

    7.  從您下載的憑證建立「Base-64 編碼」檔案。

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

    8.  在記事本中開啟您的 base-64 編碼的憑證，移除第一行 (“*--*“) 與最後一行 (“*--*“)，將它的其餘內容複製到您的剪貼簿，然後貼到 [識別提供者憑證 (中繼資料)] 文字方塊中。
    9.  選取 [允許單一登入]。
    10. 按一下 [儲存]。

10. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Mimecast Personal Portal，必須將它們佈建到 Mimecast Personal Portal。Mimecast Personal Portal 需以手動的方式佈建。
  
您需要先註冊網域才能建立使用者。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身份登入您的 **Mimecast Personal Portal**。

2.  移至 [目錄 > 內部]。

    ![目錄](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "目錄")

3.  按一下 [註冊新網域]。

    ![註冊新網域](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "註冊新網域")

4.  在您建立好新網域之後，，按一下 [新位址]。

    ![新位址](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "新位址")

5.  在 [新位址] 對話方塊中，執行下列步驟：

    ![儲存](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "儲存")

    1.  在相關的文字方塊中，輸入您要佈建之有效 AAD 帳戶的 [電子郵件地址]、[全域名稱]、[密碼]、[確認密碼] 屬性。
    2.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 Mimecast Personal Portal 使用者帳戶建立工具或 Mimecast Personal Portal 提供的 API，佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Mimecast Personal Portal，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Mimecast Personal Portal] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->