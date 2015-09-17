<properties pageTitle="教學課程：Azure Active Directory 與 Onit 整合 | Microsoft Azure" description="了解如何使用 Onit 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Onit 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=530341)。
  
本教學課程的目的是要示範 Azure 與 Onit 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Onit 單一登入的訂用帳戶
  
完成本教學課程之後，您指派給 Onit 的 Azure AD 使用者就能夠單一登入您 Onit 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Onit 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-onit-tutorial/IC791166.png "案例")
##啟用 Onit 的應用程式整合
  
本節的目的是要說明如何啟用 Onit 的應用程式整合。

###若要啟用 Onit 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-onit-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-onit-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-onit-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在**搜尋方塊**中，輸入 **Onit**。

    ![應用程式庫](./media/active-directory-saas-onit-tutorial/IC791167.png "應用程式庫")

7.  在結果窗格中，選取 [Onit]，然後按一下 [完成] 以加入應用程式。

    ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Onit 中進行驗證。設定 Onit 的單一登入需要您從憑證抓取指紋值。如果您不熟悉這個程序，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。
  
Onit 應用程式需要特定格式的 SAML 判斷提示，需要您加入自訂屬性對應到您的 **saml token 屬性**設定。以下螢幕擷取畫面顯示上述的範例。

![單一登入](./media/active-directory-saas-onit-tutorial/IC791168.png "單一登入")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Onit] 應用程式整合頁面中，按一下最上面功能表中的 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。

    ![屬性](./media/active-directory-saas-onit-tutorial/IC791169.png "屬性")

2.  若要加入必要的屬性對應，請執行下列步驟：

    
    |屬性名稱|屬性值|
	|---|---|
	|名稱|User.userprincipalname|
    |電子郵件|User.mail|

    1.  針對上表中的每個資料列，按一下 [新增使用者屬性]。
    2.  在 [屬性名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 [屬性值] 清單中，選取該資料列所顯示的屬性名稱。
    4.  按一下 [完成]。

3.  按一下 [套用變更]。

4.  在您的瀏覽器中，按一下 [返回] 以再次開啟 [快速啟動] 對話方塊。

5.  按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791170.png "設定單一登入")

6.  在 [要如何讓使用者登入 Onit] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791171.png "設定單一登入")

7.  在 [設定應用程式 URL] 頁面的 [Onit 單一登入 URL] 文字方塊中，輸入使用者登入您 Onit 應用程式所使用的 URL (如："**https://ms-sso-test.onit.com*”)，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-onit-tutorial/IC791172.png "設定應用程式 URL")

8.  在 [設定在 Onit 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在本機電腦上。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791173.png "設定單一登入")

9.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Onit 公司網站。

10. 在頂端的功能表中，按一下 [系統管理]。

    ![系統管理](./media/active-directory-saas-onit-tutorial/IC791174.png "系統管理")

11. 按一下 [編輯公司]。

    ![編輯公司](./media/active-directory-saas-onit-tutorial/IC791175.png "編輯公司")

12. 按一下 [安全性] 索引標籤。

    ![編輯公司資訊](./media/active-directory-saas-onit-tutorial/IC791176.png "編輯公司資訊")

13. 在 [安全性] 索引標籤上執行下列步驟：。

    ![單一登入](./media/active-directory-saas-onit-tutorial/IC791177.png "單一登入")

    1.  對於**驗證策略**，請選取 [單一登入和密碼]。
    2.  在 Azure 入口網站中的 [設定在 Onit 單一登入] 對話頁面上， 複製 [遠端登入 URL] 值，然後將它貼至 [IDP 目標 URL] 文字方塊中。
    3.  在 Azure 入口網站中的 [設定在 Onit 單一登入] 對話頁面上， 複製 [遠端登出 URL] 值，然後將它貼至 [IDP 登出 URL] 文字方塊中。
    4.  從匯出的憑證複製**憑證指紋**值，然後將它貼至 [IDP 憑證指紋 (SHA1)] 文字方塊中。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    5.  **SSO 類型**請選取 **SAML**。
    6.  在 [SSO 登入按鈕文字] 文字方塊中輸入您想要的按鈕文字。
    7.  選取 [使用 SSO 登入：針對下列網域/使用者為必要]，將測試使用者的電子郵件地址輸入相關的文字方塊，然後按一下 [更新]。![編輯公司](./media/active-directory-saas-onit-tutorial/IC791178.png "編輯公司")

14. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-onit-tutorial/IC791179.png "設定單一登入")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 Onit，必須將他們佈建到 Onit。在 Onit 的情況下，需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Onit** 公司網站。

2.  按一下 [新增使用者]。

    ![系統管理](./media/active-directory-saas-onit-tutorial/IC791180.png "系統管理")

3.  在 [新增使用者] 對話頁面上，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-onit-tutorial/IC791181.png "新增使用者")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 AAD 帳戶的 [名稱] 與 [電子郵件地址]。
    2.  按一下 [建立]。  

        >[AZURE.NOTE]帳戶擁有者會收到一封電子郵件，其中中包含在帳戶變成作用中之前確認帳戶的連結。

>[AZURE.NOTE]您可以使用任何其他的 Onit 使用者帳戶建立工具或 Onit 提供的 API，佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Onit，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Onit] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-onit-tutorial/IC791182.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-onit-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->