<properties pageTitle="教學課程：Azure Active Directory 與 Salesforce 沙箱整合 | Microsoft Azure" description="了解如何使用 Salesforce 沙箱搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Salesforce 沙箱整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=521878)。
  
本教學課程的目的是要示範 Azure 與 Salesforce 沙箱的整合。沙箱讓您能夠針對不同用途 (例如開發、測試和訓練) 在個別環境中建立貴組織的多個複本，而不會危害 Salesforce 生產環境組織中的資料和應用程式。如需詳細資訊，請參閱[沙箱概觀](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   在 Salesforce.com 中的沙箱
  
如果您在 Salesforce.com 中還沒有有效的沙箱，則您需要連絡 Salesforce。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Salesforce 沙箱的應用程式整合
2.  設定單一登入
3.  啟用您的網域
4.  設定使用者佈建
5.  指派使用者

![案例](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "案例")
##啟用 Salesforce 沙箱的應用程式整合
  
本節的目的是要說明如何啟用 Salesforce 沙箱的應用程式整合。

###若要啟用 Salesforce 沙箱的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "[應用程式]")

4.  若要開啟 [應用程式庫]，請按一下 [新增應用程式]，然後按一下 [新增應用程式讓我的組織使用]。

    ![您想要執行什麼動作？](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "您想要執行什麼動作？")

5.  在 [搜尋方塊] 中，輸入 **Salesforce 沙箱**。

    ![應用程式庫](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "應用程式庫")

6.  在結果窗格中，選取 [Salesforce 沙箱]，然後按一下 [完成] 來新增應用程式。

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Salesforce 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Salesforce 沙箱] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "設定單一登入")

2.  在 [要如何讓使用者登入 Salesforce 沙箱] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  在 [設定應用程式 URL] 頁面的 [Salesforce 沙箱登入 URL] 文字方塊中，使用下列模式輸入您的 URL："http://company.my.salesforce.com"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "設定應用程式 URL")

4.  在 [設定在 Salesforce 沙箱單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Salesforce 沙箱。

6.  在頂端的功能表中，按一下 [安裝]。

    ![設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "設定")

7.  在左側的導覽窗格中，按一下 [安全性控制項]，然後按一下 [單一登入設定]。

    ![單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "單一登入設定")

8.  在 [單一登入設定] 區段中，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "單一登入設定")

    1.  選取 [已啟用 SAML]。
    2.  按一下 [新增]。

9.  在 [SAML 單一登入設定] 區段中，執行下列步驟：

    ![SAML 單一登入設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML 單一登入設定")

    1.  在 [名稱] 文字方塊中輸入組態的名稱 (例如：*SPSSOWAAD\_Test*)。
    2.  在 Azure 入口網站中的 [設定在 Salesforce 沙箱單一登入] 對話頁面上，複製 [簽發者 URL] 值，然後將其貼至 [簽發者] 文字方塊中。
    3.  在 [實體 ID] 文字方塊中，輸入 ****https://test.salesforce.com**。
4.  請按一下 [瀏覽] 來上傳已下載的憑證。
    5.  針對 [SAML 識別類型]，選取 [判斷提示包含來自使用者物件的同盟識別碼]。
    6.  針對 [SAML 身分識別位置]，選取 [身分識別位於 Subject 陳述式的 NameIdentifier 元素中]
    7.  在 Azure 入口網站中的 [設定在 Salesforce 沙箱單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將其貼至 [識別提供者登入 URL] 文字方塊中。
    8.  在 Azure 入口網站中的 [設定在 Salesforce 沙箱單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將其貼至 [識別提供者登出 URL] 文字方塊中。
    9.  針對 [服務提供者起始的要求繫結]，選取 [HTTP POST]。
    10. 按一下 [儲存]。

10. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "設定單一登入")
##啟用您的網域
  
本節假設您已經建立了一個網域。如需詳細資訊，請參閱[定義您的網域名稱](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。

###若要啟用您的網域，請執行下列步驟：

1.  在左邊的導覽窗格中按一下 [定義域管理]，然後按一下 [我的網域]。

    ![我的網域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "我的網域")

    >[AZURE.NOTE]請確定您的網域已正確設定。

2.  在 [登入頁面設定] 區段中，按一下 [編輯]，然後針對 [驗證服務]，選取來自前一區段 SAML 單一登入設定的名稱，最後按一下 [儲存]。

    ![我的網域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "我的網域")
  
一旦您設定了網域，您的使用者便應使用該網域 URL 登入至 Salesforce 沙箱。若要取得 URL 的值，請按一下您在上一區段中所建立的 SSO 設定檔。
##設定使用者佈建
  
本節的目的是要說明如何對 Salesforce 沙箱啟用 Active Directory 使用者帳戶的使用者佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在 Salesforce 入口網站上方的導覽列中選取您的名稱來展開使用者功能表：

    ![我的設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "我的設定")

2.  從使用者功能表中，選取 [我的設定] 來開啟您的 [我的設定] 頁面。

3.  在左方導覽窗格中，按一下 [個人] 來展開 [個人] 區段，然後按一下 [重設我的安全性權杖]：

    ![我的設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "我的設定")

4.  在 [重設我的安全性權杖] 頁面上，按一下 [重設安全性權杖] 來要求包含 Salesforce.com 安全性權杖的電子郵件。

    ![新的權杖](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "新的權杖")

5.  檢查您的電子郵件收件匣，尋找來自 Salesforce.com 且主旨為 "**salesforce.com.com 安全性確認 **" 的電子郵件。

6.  檢閱這封電子郵件並複製安全性權杖值。

7.  在 Azure 管理入口網站中的 [salesforce 沙箱] 應用程式整合頁面上，按一下 [設定使用者佈建] 來開啟 [設定使用者佈建] 對話方塊。

    ![設定使用者佈建](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "設定使用者佈建")

8.  在 [輸入您的 Salesforce 沙箱認證來啟用自動使用者佈建] 頁面上，提供以下組態設定：

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    1.  在 [Salesforce 沙箱管理員使用者名稱] 文字方塊中，輸入已在 Salesforce.com 中指派**系統管理員**設定檔的 Salesforce 沙箱帳戶名稱。

    2.  在 [Salesforce 沙箱管理員密碼] 文字方塊中，輸入這個帳戶的密碼。

    3.  在 [使用者安全性權杖] 文字方塊中貼上安全性權杖值。

    4.  按一下 [驗證] 來驗證您的組態。

    5.  按一下 [下一步] 按鈕來開啟 [確認] 頁面。

9.  在 [確認] 頁面上，按一下 [完成] 來儲存您的組態。
##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Salesforce 沙箱，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Salesforce 沙箱] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 來確認指派。

    ![是](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "是")
  
請等候 10 分鐘並確認帳戶已同步至 Salesforce 沙箱。
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->