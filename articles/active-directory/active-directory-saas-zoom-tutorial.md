<properties pageTitle="教學課程：Azure Active Directory 與 Zoom 整合 | Microsoft Azure" description="了解如何使用 Zoom 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Zoom 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528083)。
  
本教學課程的目的是要示範 Azure 與 Zoom 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Zoom 租用戶
  
完成本教學課程之後，您指派給 Zoom 的 Azure AD 使用者就能夠單一登入您 Zoom 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Zoom 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-zoom-tutorial/IC784693.png "案例")

##啟用 Zoom 的應用程式整合
  
本節的目的是要說明如何啟用 Zoom 的應用程式整合。

###若要啟用 Zoom 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-zoom-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-zoom-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從資源庫中新增應用程式](./media/active-directory-saas-zoom-tutorial/IC749322.png "從資源庫中新增應用程式")

6.  在**搜尋方塊**中，輸入 **Zoom**。

    ![應用程式資源庫](./media/active-directory-saas-zoom-tutorial/IC784694.png "應用程式資源庫")

7.  在結果窗格中，選取 Zoom，然後按一下 [完成] 以加入應用程式。

    ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Zoom 中進行驗證。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 Zoom 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zoom-tutorial/IC784696.png "設定單一登入")

2.  在 [您希望使用者如何登入 Zoom] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-zoom-tutorial/IC784697.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Zoom 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**http://company.zoom.us*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-zoom-tutorial/IC784698.png "設定應用程式 URL")

4.  在 [設定在 Zoom 單一登入] 頁面上，按一下 [下載憑證]，然後將中繼資料檔儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-zoom-tutorial/IC784699.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Zoom 公司網站。

6.  按一下 [單一登入] 索引標籤。

    ![單一登入](./media/active-directory-saas-zoom-tutorial/IC784700.png "單一登入")

7.  按一下 [安全性控制] 索引標籤，然後移至 [單一登入] 設定。

8.  在 [單一登入] 區段中，執行下列步驟：

    ![單一登入](./media/active-directory-saas-zoom-tutorial/IC784701.png "單一登入")

    1.  在 Azure 入口網站的 [設定在 Zoom 單一登入] 對話方塊頁面上，複製 [單一登入服務 URL] 值，然後將它貼入 [單一登入 URL] 文字方塊。
    2.  在 Azure 入口網站的 [設定在 Zoom 單一登入] 對話方塊頁面上，複製 [單一登出服務 URL] 值，然後將它貼入 [單一登出 URL] 文字方塊。
    3.  從您下載的憑證建立 **base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後貼入 [身分識別提供者憑證] 文字方塊。
    5.  在 Azure 入口網站中的 [設定在 Zoom 單一登入] 對話方塊頁面上，複製 [簽發者 URL] 值，然後將它貼入 [簽發者] 文字方塊。
    6.  按一下 [儲存]。

9.  在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zoom-tutorial/IC784702.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Zoom，必須將他們佈建到 Zoom。Zoom 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **Zoom** 公司網站。

2.  按一下 [帳戶管理] 索引標籤，再按一下 [使用者管理]。

3.  在 [使用者管理] 區段中按一下 [新增使用者]。

    ![使用者管理](./media/active-directory-saas-zoom-tutorial/IC784703.png "使用者管理")

4.  在 [新增使用者] 頁面上，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-zoom-tutorial/IC784704.png "新增使用者")

    1.  選取 [基本] 做為 [使用者類型]。
    2.  在 [電子郵件] 文字方塊中，輸入您要佈建之有效 AAD 帳戶的電子郵件地址。
    3.  按一下 [新增]。

>[AZURE.NOTE]您可以使用任何其他的 Zoom 使用者帳戶建立工具或 Zoom 提供的 API，佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Zoom，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Zoom] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-zoom-tutorial/IC784705.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-zoom-tutorial/IC767830.png "是")
  
如果您想要測試單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->