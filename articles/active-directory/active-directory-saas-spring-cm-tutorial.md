<properties pageTitle="教學課程：Azure Active Directory 與 SpringCM 整合 | Microsoft Azure" description="了解如何使用 SpringCM 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Spring CM 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=536554)。
  
本教學課程的目的是要示範 Azure Active Directory 與 SpringCM 的整合。
  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 SpringCM 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 SpringCM 的 Azure Active Directory 使用者將能夠使用 [AAD 存取面板] 單一登入。

1.  啟用 SpringCM 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "案例")

##啟用 SpringCM 的應用程式整合
  
本節的目的是概述如何啟用 SpringCM 的應用程式整合。

###若要啟用 SpringCM 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊]中，輸入 **SpringCM**。

    ![應用程式庫](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "應用程式庫")

7.  在結果窗格中，選取 [SpringCM]，然後按一下 [完成] 加入應用程式。

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##設定單一登入
  
本節概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure Active Directory 帳戶在 SpringCM 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [SpringCM] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "設定單一登入")

2.  在 [要如何讓使用者登入 SpringCM] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [SpringCM 登入 URL] 文字方塊中，輸入使用者用來登入 SpringCM 應用程式的 URL，然後按 [下一步]。

    此應用程式 URL 是您 SpringCM 租用戶的 URL (例如：**https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*)：

    ![設定應用程式 URL](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "設定應用程式 URL")

4.  在 [設定在 SpringCM 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後在本機電腦上儲存憑證檔案。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 **SpringCM** 公司網站。

6.  在上方功能表中，依序按一下 [移至]、[喜好設定]，以及 [帳戶喜好設定] 區段中的 [SAML SSO]。

    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  在 [識別提供者組態] 區段執行下列步驟：

    ![識別提供者組態](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "識別提供者組態")

    1.  若要上傳已下載的 Azure Active Directory 憑證，請按一下 [選取簽發者憑證] 或 [變更簽發者憑證]。
    2.  在 Microsoft Azure 入口網站中的 [設定在 SpringCM 單一登入] 對話頁面上， 複製 [簽發者 URL] 值，然後將它貼至 [簽發者] 文字方塊中。
    3.  在 Microsoft Azure 入口網站的 [設定在 SpringCM 單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [服務提供者 (SP) 啟始的端點] 文字方塊中。
    4.  在 [啟用 SAML] 選取 [啟用]。
    5.  按一下 [儲存]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure Active Directory 使用者能夠登入 SpringCM，必須將他們佈建到 SpringCM。SpringCM 需以手動方式佈建。

>[AZURE.NOTE]如需詳細資料，請參閱[建立和編輯 SpringCM 使用者](http://knowledge.springcm.com/create-and-edit-a-springcm-user)。

###若要佈建使用者帳戶到 SpringCM，請執行下列步驟：

1.  以系統管理員身分登入您的 **SpringCM** 公司網站。

2.  按一下 [移至]，然後按一下 [通訊錄]。

    ![建立使用者](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "建立使用者")

3.  按一下 [建立使用者]。

4.  選取 [使用者角色]。

5.  選取 [傳送啟用電子郵件]。

6.  在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 使用者帳戶的名字、姓氏和電子郵件地址。

7.  將該使用者加入 [安全性群組]。

8.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 SpringCM 使用者帳戶建立工具或 SpringCM 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 SpringCM，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [SpringCM] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資料，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->