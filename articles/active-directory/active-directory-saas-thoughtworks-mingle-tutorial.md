<properties pageTitle="教學課程：Azure Active Directory 與 Thoughtworks Mingle 整合 | Microsoft Azure" description="了解如何使用 Thoughtworks Mingle 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Thoughtworks Mingle 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528082)。
  
本教學課程的目的是要示範 Azure 與 Thoughtworks Mingle 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Thoughtworks Mingle 租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Thoughtworks Mingle 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "案例")

##啟用 Thoughtworks Mingle 的應用程式整合
  
本節的目的是要說明如何啟用 Thoughtworks Mingle 的應用程式整合。

###若要啟用 Thoughtworks Mingle 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **thoughtworks mingle**。

    ![應用程式庫](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "應用程式庫")

7.  在結果窗格中，選取 [Thoughtworks Mingle]，然後按一下 [完成] 以新增應用程式。

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 Thoughtworks Mingle。您必須將憑證上傳至 Thoughtworks Mingle，這是程序的一部分。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Thoughtworks Mingle] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "設定單一登入")

2.  在 [您希望使用者如何登入 Thoughtworks Mingle] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Thoughtworks Mingle 租用戶 URL] 文字方塊中，使用下列模式輸入您的 URL："**http://company.mingle.thoughtworks.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "設定應用程式 URL")

4.  於 [在 Thoughtworks Mingle 設定單一登入] 頁面上，按 [下載中繼資料]，然後將中繼資料儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "設定單一登入")

5.  以系統管理員身分登入您的 **Thoughtworks Mingle** 公司網站。

6.  按一下 [管理] 索引標籤，然後按一下 [SSO 組態]。

    ![SSO 組態](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO 組態")

7.  在 [SSO 組態] 區段中，執行下列步驟：

    ![SSO 組態](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO 組態")

    1.  若要上傳中繼資料檔案，請按一下 [選擇檔案]。
    2.  按一下 [儲存變更]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "設定單一登入")

##設定使用者佈建
  
若要讓 AAD 使用者能夠登入，必須使用他們的 Azure Active Directory 使用者名稱將他們佈建到 Thoughtworks Mingle 應用程式。Thoughtworks Mingle 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 Thoughtworks Mingle 公司網站。

2.  按一下 [設定檔]。

    ![第一個專案](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "第一個專案")

3.  按一下 [管理] 索引標籤，然後按一下 [使用者]。

    ![使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "使用者")

4.  按一下 [新增使用者]。

    ![新增使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "新增使用者")

5.  在 [新增使用者] 對話頁面上，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "新增使用者")

    1.  在相關的文字方塊中，輸入您要佈建之有效 AAD 帳戶的 [登入名稱]、[顯示名稱]、[選擇密碼]、[確認密碼]。
    2.  選取 [完整使用者] 做為 [使用者類型]。
    3.  按一下 [建立這個設定檔]。

>[AZURE.NOTE]您可以使用任何其他的 Thoughtworks Mingle 使用者帳戶建立工具或 Thoughtworks Mingle 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Thoughtworks Mingle，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Thoughtworks Mingle] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->