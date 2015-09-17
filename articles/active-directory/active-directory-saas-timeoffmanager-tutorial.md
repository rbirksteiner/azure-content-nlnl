<properties pageTitle="教學課程：Azure Active Directory 與 TimeOffManager 整合 | Microsoft Azure" description="了解如何使用 TimeOffManager 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 TimeOffManager 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=534748)。
  
本教學課程的目的是要示範 Azure 與 TimeOffManager 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 TimeOffManager 單一登入的訂用帳戶
  
完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 TimeOffManager 的 Azure AD 使用者就能夠單一登入您 TimeOffManager 公司網站 (服務提供者起始登入) 的應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 TimeOffManager 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "案例")

##啟用 TimeOffManager 的應用程式整合
  
本節的目的是要說明如何啟用 TimeOffManager 的應用程式整合。

###若要啟用 TimeOffManager 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **TimeOffManager**。

    ![應用程式庫](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "應用程式庫")

7.  在結果窗格中，選取 [TimeOffManager]，然後按一下 [完成] 以加入應用程式。

    ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 TimeOffManager。  
在此程序中，您需要上傳 base-64 編碼憑證到您的 TimeOffManager 租用戶。  
如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [TimeOffManager] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "設定單一登入")

2.  在 [您希望使用者如何登入 TimeOffManager] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [TimeOffManager 回覆 URL] 文字方塊中輸入您的 TimeOffManager AssertionConsumerService URL (例如："*範例：https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*")，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "設定應用程式 URL")

    您可以從 [TimeOffManager 單一登入設定] 頁面取得回覆 URL。

    ![單一登入設定](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "單一登入設定")

4.  於 [在 TimeOffManager 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 TimeOffManager 公司網站。

6.  移至 [帳戶] > [帳戶選項] > [單一登入設定]。

    ![單一登入設定](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "單一登入設定")

7.  在 [單一登入設定] 區段中，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "單一登入設定")

    1.  從您下載的憑證建立 **Base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    2.  在記事本中開啟您的 base-64 編碼的憑證，將它的內容複製到您的剪貼簿，然後將整個憑證貼到 [X.509 憑證] 文字方塊中。
    3.  在 Azure 入口網站中的 [在 TimeOffManager 設定單一登入] 對話頁面上，複製**簽發者 URL** 值，然後將它貼至 [IdP 簽發者] 文字方塊中。
    4.  在 Azure 入口網站的 [在 TimeOffManager 設定單一登入] 對話頁面上，複製**遠端登入 URL** 值，然後將它貼至 [IdP 端點 URL] 文字方塊中。
    5.  在 [強制 SAML] 選取 [否]。
    6.  在 [自動建立使用者] 選取 [是]。
    7.  在 Azure 入口網站中的 [在 TimeOffManager 設定單一登入] 對話頁面上， 複製**遠端登出 URL** 值，然後將它貼至 [登出 URL] 文字方塊中。
    8.  按一下 [儲存變更]。

8.  在 Azure 入口網站的 [在 TimeOffManager 設定單一登入] 對話方塊上，選取單一登入組態確認，然後按一下 [完成]。

    ![設定單一登入](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "設定單一登入")

9.  在頂端的功能表中，按一下 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。

    ![屬性](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "屬性")

10. 若要加入必要的屬性對應，請執行下列步驟：

    ![SAML Token 屬性](./media/active-directory-saas-timeoffmanager-tutorial/IC795921.png "SAML Token 屬性")

    |屬性名稱|屬性值|
	|---|---|
    |Firstname|User.givenname|
	|lastname|User.surname|

    1.  針對上表中的每個資料列，按一下 [加入使用者屬性]。
    2.  在 [屬性名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 [屬性值] 文字方塊中，選取該資料列所顯示的屬性值。
    4.  按一下 [完成]。

11. 按一下 [套用變更]。

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 TimeOffManager，則必須將他們佈建到 TimeOffManager。  
TimeOffManager 支援即時使用者佈建。沒有您適用的動作項目。  
在第一次登入時使用單一登入，便會自動加入使用者。

>[AZURE.NOTE]您可以使用任何其他的 TimeOffManager 使用者帳戶建立工具或 TimeOffManager 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 TimeOffManager，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [TimeOffManager] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!----HONumber=August15_HO7-->