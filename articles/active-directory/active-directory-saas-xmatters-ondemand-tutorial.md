<properties pageTitle="教學課程：Azure Active Directory 與 xMatters OnDemand 整合 | Microsoft Azure " description="了解如何使用 xMatters OnDemand 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 xMatters OnDemand 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=524330)。
  
本教學課程的目的是要示範 Azure 與 xMatters OnDemand 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   XMatters OnDemand 租用戶
  
完成本教學課程之後，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您已指派給 xMatters OnDemand 的 Azure AD 使用者將能夠在 xMatters OnDemand 公司網站 (服務提供者起始登入) 的應用程式單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 xMatters OnDemand 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "案例")

##啟用 xMatters OnDemand 的應用程式整合
  
本節的目的是要說明如何啟用 xMatters OnDemand 的應用程式整合。

###若要啟用 xMatters OnDemand 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從資源庫中新增應用程式]。

    ![從資源庫中新增應用程式](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "從資源庫中新增應用程式")

6.  在**搜尋方塊**中，輸入 **xMatters OnDemand**。

    ![應用程式資源庫](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "應用程式資源庫")

7.  在結果窗格中，選取 [xMatters OnDemand]，然後按一下 [完成] 以新增應用程式。

    ![OnDemand xMatters](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "OnDemand xMatters")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 xMatters OnDemand 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [xMatters OnDemand] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "設定單一登入")

2.  在 [您希望使用者如何登入 xMatters OnDemand] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [xMatters OnDemand 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<tenant-name>.XMattersOnDemandapp.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "設定應用程式 URL")

4.  在 [設定在 xMatters OnDemand 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後在本機電腦上將憑證檔案儲存為 **c:\\XMatters OnDemand.cer**。

    >[AZURE.IMPORTANT]您需要將憑證轉送給 xMatters 支援小組。xMatters 支援小組需要先上傳憑證，您才能完成單一登入組態。

    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 xMatters OnDemand 公司網站。

6.  在頂端工具列中按一下 [管理]，然後按一下左側導覽列中的 [公司詳細資料]。

    ![Admin](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7.  在 [SAML 組態] 頁面上，執行下列步驟：

    ![SAM 組態](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAM 組態")

    1.  選取 [啟用 SAML]。
    2.  在 Azure 入口網站中的 [設定在 xMatters OnDemand 單一登入] 對話頁面上，複製 [識別提供者 ID] 值，然後將它貼至 [識別提供者 ID] 文字方塊中。
    3.  在 Azure 入口網站中的 [設定在 xMatters OnDemand 單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [單一登入 URL] 文字方塊中。
    4.  在 Azure 入口網站中的 [設定在 xMatters OnDemand 單一登入] 對話頁面上，複製 [單一登出服務 URL] 值，然後將它貼至 [單一登出 URL] 文字方塊中。
    5.  在 [公司詳細資料] 頁面頂端，按一下 [儲存變更]。![公司詳細資料](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "公司詳細資料")

8.  在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 xMatters OnDemand，則必須將他們佈建到 xMatters OnDemand。xMatters OnDemand 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入 **xMatters OnDemand** 租用戶。

2.  按一下 [使用者] 索引標籤。

3.  按一下 [新增使用者]。

    ![使用者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "使用者")

4.  選取 [使用中]。

5.  在 [加入使用者] 區段中，執行下列步驟：

    ![加入使用者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "加入使用者")

    1.  輸入您想要佈建之有效 AAD 帳戶的 [使用者識別碼]、[名字]、[姓氏]、[網站]。
    2.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 xMatters OnDemand 使用者帳戶建立工具或 xMatters OnDemand 所提供的 API，佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 xMatters OnDemand，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [xMatters OnDemand] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "是")
  
如果您想要測試單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->