<properties pageTitle="教學課程：Azure Active Directory 與 Jobscience 整合 | Microsoft Azure" description="了解如何使用 Jobscience 搭配 Azure Active Directory 來啟用單一登入、自動化佈建及更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Jobscience 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=526255)。
  
本教學課程的目的是要示範 Azure 與 Jobscience 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Jobscience 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 Jobscience 的 Azure AD 使用者將能夠在 Jobscience 公司網站 (服務提供者起始登入) 執行單一登入來登入應用程式，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)執行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Jobscience 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-jobscience-tutorial/IC784341.png "案例")
##啟用 Jobscience 的應用程式整合
  
本節的目的是要說明如何啟用 Jobscience 的應用程式整合。

###若要啟用 Jobscience 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-jobscience-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-jobscience-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-jobscience-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在**搜尋方塊**中，輸入 **jobscience**。

    ![應用程式庫](./media/active-directory-saas-jobscience-tutorial/IC784342.png "應用程式庫")

7.  在結果窗格中，選取 [Jobscience]，然後按一下 [完成] 以新增應用程式。

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Jobscience 中進行驗證。  
設定 Jobscience 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉這個程序，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  以系統管理員身分登入您的 Jobscience 公司網站。

2.  移到 [設定]。

    ![設定](./media/active-directory-saas-jobscience-tutorial/IC784358.png "設定")

3.  在 [系統管理員] 區段的左側導覽窗格中，按一下 [網域管理] 展開相關區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。

    ![我的網域](./media/active-directory-saas-jobscience-tutorial/IC767825.png "我的網域")

4.  若要確認您的網域已經正確設定，請確定它有在 [步驟 4：部署至使用者] 中並檢閱您的 [我的網域設定]。

    ![已部署到使用者的網域](./media/active-directory-saas-jobscience-tutorial/IC784377.png "已部署到使用者的網域")

5.  在不同的 Web 瀏覽器視窗中登入您的 Azure 入口網站。

6.  在 [Jobscience] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784360.png "設定單一登入")

7.  在 [您希望使用者如何登入 Jobscience] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784361.png "設定單一登入")

8.  在 [設定應用程式 URL] 頁面的 [Jobscience 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**http://company.my.salesforce.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-jobscience-tutorial/IC784362.png "設定應用程式 URL")

9.  在 [設定在 Jobscience 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784363.png "設定單一登入")

10. 在 Jobscience 公司網站上，按一下 [安全性控制項]，然後按一下 [單一登入設定]。

    ![安全性控制項](./media/active-directory-saas-jobscience-tutorial/IC784364.png "安全性控制項")

11. 在 [單一登入設定] 區段中，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-jobscience-tutorial/IC781026.png "單一登入設定")

    1.  選取 [啟用 SAML]。
    2.  按一下 [新增]。

12. 在 [SAML 單一登入設定編輯] 對話方塊中，執行下列步驟：

    ![SAML 單一登入設定](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML 單一登入設定")

    1.  在 [名稱] 文字方塊中，輸入您的組態名稱。
    2.  在 Azure 入口網站中的 [設定在 Jobscience 單一登入] 對話頁面上， 複製 [簽發者 URL] 值，然後將它貼至 [簽發者] 文字方塊中。
    3.  在 [實體識別碼] 文字方塊中，輸入 ****https://salesforce-jobscience.com**
4.  按一下 [瀏覽] 來上傳您的 Azure AD 憑證。
    5.  在 [SAML 識別類型] 中選取 [判斷提示包含來自使用者物件的同盟識別碼]。
    6.  在 [SAML 識別位置] 中選取 [識位於 Subject 陳述式的 NameIdentifier 元素中]。
    7.  在 Azure 入口網站中的 [設定在 Jobscience 單一登入] 對話頁面上， 複製 [遠端登入 URL] 值，然後將它貼至 [識別提供者登入 URL] 文字方塊中。
    8.  在 Azure 入口網站中的 [設定在 Jobscience 單一登入] 對話頁面上， 複製 [遠端登出 URL] 值，然後將它貼至 [識別提供者登出 URL] 文字方塊中。
    9.  按一下 [儲存]。

13. 在 [系統管理員] 區段的左側導覽窗格中，按一下 [網域管理] 展開相關區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。

    ![我的網域](./media/active-directory-saas-jobscience-tutorial/IC767825.png "我的網域")

14. 在 [我的網域] 頁面的 [登入頁面商標] 區段中，按一下 [編輯]。

    ![登入頁面商標](./media/active-directory-saas-jobscience-tutorial/IC767826.png "登入頁面商標")

15. [登入頁面商標] 頁面的 [驗證服務] 區段中，就會顯示您的 [SAML SSO 設定] 的名稱。請選取該名稱，然後按一下 [儲存]。

    ![登入頁面商標](./media/active-directory-saas-jobscience-tutorial/IC784366.png "登入頁面商標")

16. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784367.png "設定單一登入")
  
若要取得 SP 初始化的單一登入 URL，請按一下 [安全性控制項] 功能表區段中的 [單一登入設定]。

![安全性控制項](./media/active-directory-saas-jobscience-tutorial/IC784368.png "安全性控制項")
  
按一下您已經在上述步驟中建立的 SSO 設定檔。此頁面會顯示您的公司的單一登入 URL (例如 **https://companyname.my.salesforce.com?so=companyid*))。
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Jobscience，必須將他們佈建到 Jobscience。Jobscience 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Jobscience** 公司網站。

2.  移到 [設定]

    ![設定](./media/active-directory-saas-jobscience-tutorial/IC784358.png "設定")

3.  移至 [管理使用者 > 使用者]。

    ![使用者](./media/active-directory-saas-jobscience-tutorial/IC784369.png "使用者")

4.  按一下 [新增使用者]。

    ![所有使用者](./media/active-directory-saas-jobscience-tutorial/IC784370.png "所有使用者")

5.  在 [編輯使用者] 對話頁面上，執行下列步驟：

    ![使用者編輯](./media/active-directory-saas-jobscience-tutorial/IC784371.png "使用者編輯")

    1.  在相關的文字方塊中輸入您想要佈建之 Azure AD 使用者的名字、姓氏、別名、電子郵件、使用者名稱與暱稱屬性。
    2.  按一下 [儲存]。

    >[AZURE.NOTE]Azure AD 帳戶的持有者將會收到一封電子郵件，郵件中包含在啟用帳戶之前先行確認帳戶的連結。

>[AZURE.NOTE]您可以使用 Jobscience 提供的任何其他 Jobscience 使用者帳戶建立工具或 API，藉此佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Jobscience，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Jobscience] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-jobscience-tutorial/IC784372.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-jobscience-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!-----HONumber=August15_HO7-->