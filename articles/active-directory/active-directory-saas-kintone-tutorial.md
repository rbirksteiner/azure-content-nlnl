<properties pageTitle="教學課程：Azure Active Directory 與 Kintone 整合 | Microsoft Azure" description="了解如何使用 Kintone 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Kintone 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=526796)。
  
本教學課程的目的是要示範 Azure 與 Kintone 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Kintone 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 Kintone 的 Azure AD 使用者將能夠在 Kintone 公司網站 (服務提供者起始登入) 執行單一登入來登入應用程式，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)執行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Kintone 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-kintone-tutorial/IC785859.png "案例")
##啟用 Kintone 的應用程式整合
  
本節的目的是要說明如何啟用 Kintone 的應用程式整合。

###若要啟用 Kintone 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-kintone-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-kintone-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-kintone-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **Kintone**。

    ![應用程式庫](./media/active-directory-saas-kintone-tutorial/IC785867.png "應用程式庫")

7.  在結果窗格中，選取 [Kintone]，然後按一下 [完成] 以新增應用程式。

    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Kintone 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Kintone] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785872.png "設定單一登入")

2.  在 [要如何讓使用者登入 Kintone] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785873.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Kintone 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**https://company.kintone.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-kintone-tutorial/IC785875.png "設定應用程式 URL")

4.  在 [設定在 Kintone 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785878.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 **Kintone** 公司網站。

6.  按一下 [設定]

    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "設定")

7.  按一下 [使用者與系統管理]。

    ![使用者與系統管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "使用者與系統管理")

8.  在 [系統管理 > 安全性] 底下，按一下 [登入]。

    ![登入](./media/active-directory-saas-kintone-tutorial/IC785881.png "登入")

9.  按一下 [啟用 SAML 驗證]。

    ![SAML 驗證](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML 驗證")

10. 在 [SAML 驗證] 區段中，執行下列步驟：

    ![SAML 驗證](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML 驗證")

    1.  在 Azure 入口網站中的 [設定在 Kintone 單一登入] 對話頁面上， 複製 [遠端登入 URL] 值，然後將它貼至 [登入 URL] 文字方塊中。
    2.  在 Azure 入口網站中的 [設定在 Kintone 單一登入] 對話頁面上， 複製 [遠端登出 URL] 值，然後將它貼至 [登出 URL] 文字方塊中。
    3.  按一下 [瀏覽] 來上傳您下載的憑證。
    4.  按一下 [儲存]。

11. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785884.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Kintone，必須將他們佈建到 Kintone。Kintone 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **Kintone** 公司網站。

2.  按一下 [設定]。

    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "設定")

3.  按一下 [使用者與系統管理]。

    ![使用者與系統管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "使用者與系統管理")

4.  在 [使用者管理] 底下，按一下 [部門與使用者]。

    ![部門與使用者](./media/active-directory-saas-kintone-tutorial/IC785888.png "部門與使用者")

5.  按一下 [新增使用者]。

    ![新增使用者](./media/active-directory-saas-kintone-tutorial/IC785889.png "新增使用者")

6.  在 [新增使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-kintone-tutorial/IC785890.png "新增使用者")

    1.  在相關文字方塊中輸入您想要佈建之有效 ADD 帳戶的 [顯示名稱]、[登入名稱]、[新密碼]、[確認密碼]、[電子郵件地址] 及其他詳細資料。
    2.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 Kintone 使用者帳戶建立工具或 Kintone 提供的 API，佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Kintone，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Kintone] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-kintone-tutorial/IC785891.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-kintone-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->