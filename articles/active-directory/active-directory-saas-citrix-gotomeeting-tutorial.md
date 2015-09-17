<properties pageTitle="教學課程：Azure Active Directory 與 Citrix GoToMeeting 整合 | Microsoft Azure" description="了解如何使用 Citrix GoToMeeting 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Citrix GoToMeeting 整合  
適用對象：Azure

>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=522412)。

本教學課程的目的是要示範 Azure 與 Citrix GoToMeeting 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Citrix GoToMeeting 租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Citrix GoToMeeting 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![組態](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "組態")
##啟用 Citrix GoToMeeting 的應用程式整合

本節的目的是要說明如何啟用 Citrix GoToMeeting 的應用程式整合。

###若要啟用 Citrix GoToMeeting 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Citrix GoToMeeting**。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  在結果窗格中，選取 [Citrix GoToMeeting]，然後按一下 [完成] 以加入應用程式。

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Citrix GoToMeeting。在這個程序中，您需要上傳 base-64 編碼憑證到您的 Citrix GoToMeeting 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 [Citrix GoToMeeting] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "啟用單一登入")

2.  在 [要如何讓使用者登入 Citrix GoToMeeting] 頁面上，選取 [Microsoft Azure AD 單一登入]。

    ![設定單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "設定單一登入")

3.  於 [在 Citrix GoToMeeting 設定單一登入] 頁面上，按 [下載憑證]，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "設定單一登入")

4.  在不同的瀏覽器視窗中，以系統管理員身分登入您的 Citrix GoToMeeting 租用戶。

5.  開啟 [設定 SAML 2.0 單一登入 (SSO) 組態][](https://login.citrixonline.com/saml/settings.html) 頁面，然後執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC689232.png "SAML 設定")

    1.  選取 [手動設定]。
    2.  在 Azure 入口網站的 [在 Citrix GoToMeeting 設定單一登入] 對話頁面上，複製**登出頁面 URL** 值，然後將它貼至 [登出 URL] 文字方塊中。
    3.  在 Azure 入口網站的 [在 Citrix GoToMeeting 設定單一登入] 對話頁面上，複製**登入頁面 URL** 值，然後將它貼至 [登入 URL] 文字方塊中。
    4.  從您下載的憑證建立 **Base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    5.  按一下 [取代憑證]，然後上傳您的 **base-64 編碼的憑證檔案**。
    6.  按一下 [儲存]。

6.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "設定單一登入")
##設定使用者佈建

本節的目的是要說明如何對 Citrix GoToMeeting 啟用 Active Directory 使用者帳戶的佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在 Azure 管理入口網站的 [Citrix GoToMeeting] 應用程式整合頁面上，按一下 [設定使用者佈建] 以開啟 [設定使用者佈建] 對話方塊。

    ![設定使用者佈建](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "設定使用者佈建")

2.  在 [Citrix GotoMeeting 管理員使用者名稱] 頁面上，輸入具有您 Citrix GoToMeeting 租用戶管理權限的 Citrix GoToMeeting 帳戶名稱，然後按 [下一步]。

    ![設定使用者佈建](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "設定使用者佈建")

3.  在 [確認] 頁面上，按一下核取記號以儲存您的組態。

4.  按一下 [驗證] 按鈕來驗證您的組態。
##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Citrix GoToMeeting，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Citrix GoToMeeting] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "是")

請等候 10 分鐘並確認帳戶已同步至企業適用的 Dropbox。

在第一個驗證步驟中，您可以在 Azure 管理入口網站的 [Citrix GoToMeeting] 應用程式整合頁面上，按一下 D 的儀表板，來檢查佈建狀態。

![儀表板](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "儀表板")

成功完成的使用者佈建週期會以相關狀態表示：

![整合狀態](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "整合狀態")

如果要測試您的單一登入設定，請開啟存取面板。

如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->