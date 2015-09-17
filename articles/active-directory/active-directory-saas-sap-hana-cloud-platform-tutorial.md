<properties pageTitle="教學課程：Azure Active Directory 與 SAP HANA 雲端平台整合 | Microsoft Azure" description="了解如何使用 SAP HANA 雲端平台搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 SAP HANA 雲端平台整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=529793)。
  
本教學課程的目的是要示範 Azure 與 SAP HANA 雲端平台的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   SAP HANA 雲端平台帳戶
  
完成本教學課程之後，您指派給 SAP HANA 雲端平台的 Azure AD 使用者就能夠使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)對應用程式進行單一登入。

>[AZURE.IMPORTANT]您需要在 SAP HANA 雲端平台帳戶上部署您自己的應用程式或訂閱應用程式來測試單一登入。在本教學課程中，帳戶中已部署一個應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SAP HANA 雲端平台的應用程式整合
2.  設定單一登入
3.  將角色指派給使用者
4.  指派使用者

![案例](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "案例")
##啟用 SAP HANA 雲端平台的應用程式整合
  
本節的目的是要說明如何啟用 SAP HANA 雲端平台的應用程式整合。

###若要啟用 SAP HANA 雲端平台的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中輸入**SAP HANA 雲端平台**。

    ![應用程式庫](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "應用程式庫")

7.  在結果窗格中，選取 [SAP HANA 雲端平台]，然後按一下 [完成] 來新增應用程式。

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 SAP HANA 雲端平台中進行驗證。在此程序中，您需要將 base-64 編碼憑證上傳到您的 SAP HANA 雲端平台租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [SAP HANA 雲端平台] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "設定單一登入")

2.  在 [要如何讓使用者登入 Sugar CRM] 頁面上，選取 [SAP HANA 雲端平台單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "設定單一登入")

3.  在不同的網頁瀏覽器視窗中，在 https://account.\<橫向主機>.ondemand.com/cockpit 登入 SAP HANA 雲端平台駕駛座 (例如：**https://account.hanatrial.ondemand.com/cockpit*)。

4.  按一下 [信任] 索引標籤。

    ![信任](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "信任")

5.  在 [信任管理] 區段中，執行下列步驟：

    ![取得中繼資料](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "取得中繼資料")

    1.  按一下 [本機服務提供者] 索引標籤。
    2.  若要下載 SAP HANA 雲端平台中繼資料檔案，請按一下 [取得中繼資料]。

6.  在 Azure Active Directory 入口網站中的 [設定應用程式 URL] 頁面上，執行下列步驟，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "設定應用程式 URL")

    1.  在 [登入 URL] 文字方塊中，輸入使用者用來登入 **SAP HANA 雲端平台**應用程式的 URL。這是您的 SAP HANA 雲端平台應用程式中受保護資源的帳戶特定 URL。該 URL 是以下列模式為基礎：*https://\<applicationName><accountName>.<landscape host>.ondemand.com/<path\_to\_protected\_resource>* (例如：**https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

		>[AZURE.NOTE]這是您 SAP HANA 雲端平台應用程式中需要使用者以進行驗證的 URL。

    2.  開啟已下載的 SAP HANA 雲端平台中繼資料檔案，然後再找到**ns3:AssertionConsumerService**標記。
    3.  複製 [位置] 屬性的值，然後再將其貼至 [SAP HANA 雲端平台回覆 URL] 文字方塊中。

7.  在 [設定在 SAP HANA 雲端平台單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後將檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "設定單一登入")

8.  在 SAP HANA 雲端平台駕駛座的 [本機服務提供者] 區段中，執行下列步驟：

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "信任管理")

    1.  按一下 [**編輯**]。
    2.  針對 [組態類型]，選取 [自訂]。
    3.  針對 [本機提供者名稱]，保留預設值。
    4.  若要產生**簽署金鑰**和**簽署憑證**金鑰組，請按一下 [產生金鑰組]。
    5.  針對 [主體傳播]，選取 [已停用]。
    6.  針對 [強制驗證]，選取 [已停用]。
    7.  按一下 [儲存]。

9.  按一下 [信任的識別提供者]索引標籤，然後再按一下 [新增信任的識別提供者]。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "信任管理")

    >[AZURE.NOTE]若要管理信任的識別提供者清單，您必須已經在 [本機服務提供者] 區段中選擇了 [自訂組態類型]。針對 [預設組態類型]，您對 SAP ID 服務有不可編輯且隱含的信任。針對 [無]，您不具任何信任設定。

10. 按一下 [一般] 索引標籤，然後再按一下 [瀏覽] 來上傳下載的中繼資料檔案。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "信任管理")

    >[AZURE.NOTE]上傳中繼資料檔之後，**單一登入 URL**、**單一登出 URL** 和**簽署憑證**的值會自動填入。

11. 按一下 [屬性] 索引標籤。

12. 在 [屬性] 索引標籤上，執行下列步驟：

    ![屬性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "屬性")

    1.  透過按一下 [新增判斷提示式的屬性]，新增下列判斷提示式的屬性：

        |判斷提示屬性| 主體屬性|
		|-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname|名字|--------------------|--------------------| |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname|姓氏|---| |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|email|

    >[AZURE.NOTE]屬性的設定取決於 HCP 上的應用程式是如何開發的，意即其預期在 SAML 回應中的是哪個屬性，以及其在程式碼中哪個名稱 (主體屬性) 之下存取該屬性。
    >  
    >a.螢幕擷取畫面中的 [預設屬性] 僅供說明之用。其並不是案例進行所必須的要素。
    >
    >b.螢幕擷取畫面所顯示的**主體屬性**名稱與值取決於應用程式是如何開發的。您的應用程式很可能需要不同的對應。

13. 在 Azure 入口網站的 [設定在 SAP HANA 雲端平台單一登入] 對話方塊上，選取 [單一登入設定確認]，然後按一下 [完成]。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "設定單一登入")
  
在選擇性的步驟中，您可以為 Azure Active Directory 識別提供者設定判斷提示式群組

>[AZURE.NOTE]使用 SAP HANA 雲端平台上的群組可讓您在 SAP HANA 雲端平台應用程式中以動態方式將一或多個使用者指派給一或多個角色，這是由 SAML 2.0 判斷提示中的屬性值所決定。例如，如果判斷提示包含屬性「*合約＝暫存*」，您可能會希望將所有受影響的使用者加入「*暫存*」群組。「*暫存*」群組可能包含來自一或多個部署在 SAP HANA 雲端平台帳戶中之應用程式的一或多個角色。
>  
>如果您想要大量指派許多使用者給 SAP HANA 雲端平台帳戶中之應用程式的一個或多個角色，請使用判斷提示式的群組。如果您只想要指派單一或少數使用者給特定角色，我們建議直接在 SAP HANA 雲端平台駕駛座的 [授權] 索引標籤中加以指派。

##將角色指派給使用者
  
若要讓 Azure AD 使用者能夠登入 SAP HANA 雲端平台，您必須將 SAP HANA 雲端平台中的角色指派給他們。

###若要指派角色給使用者，請執行下列步驟：

1.  登入您的**SAP HANA 雲端平台**駕駛艙。

2.  執行下列步驟：

    ![授權](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "授權")

    1.  按一下 [授權]。
    2.  按一下 [使用者] 索引標籤。
    3.  在 [使用者] 文字方塊中，輸入使用者的電子郵件地址。
    4.  按一下 [指派] 將使用者指派給角色。
    5.  按一下 [儲存]。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 SAP HANA 雲端平台，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [SAP HANA 雲端平台] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->