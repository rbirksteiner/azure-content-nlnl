<properties pageTitle="教學課程：Azure Active Directory 與 AirWatch 整合 | Microsoft Azure" description="了解如何使用 AirWatch 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 AirWatch 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=529791)。

本教學課程的目的是要示範 Azure 與 AirWatch 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 AirWatch 單一登入的訂用帳戶

完成本教學課程之後，您指派給 AirWatch 的 Azure AD 使用者就能夠單一登入您 AirWatch 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 AirWatch 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##啟用 AirWatch 的應用程式整合

本節的目的是概述如何啟用 AirWatch 的應用程式整合。

###若要啟用 AirWatch 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-airwatch-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-airwatch-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-airwatch-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **AirWatch**。

    ![應用程式庫](./media/active-directory-saas-airwatch-tutorial/IC791914.png "應用程式庫")

7.  在結果窗格中，選取 [AirWatch]，然後按一下 [完成] 加入應用程式。

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 AirWatch 中進行驗證。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [AirWatch] 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791916.png "設定單一登入")

2.  在 [要如何讓使用者登入 AirWatch] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791917.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [AirWatch 登入 URL] 文字方塊中，輸入使用者用來登入 AirWatch 應用程式的 URL (例如："*https://companycode.awmdm.com/AirWatch/Login?gid=companycode*")，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-airwatch-tutorial/IC791918.png "設定應用程式 URL")

4.  在 [設定在 AirWatch 單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791919.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 AirWatch 公司網站。

6.  在左側導覽窗格中按一下 [帳戶]，然後按一下 [系統管理員]。

    ![系統管理員](./media/active-directory-saas-airwatch-tutorial/IC791920.png "系統管理員")

7.  展開 [設定] 功能表，然後按一下 [目錄服務]。

    ![設定](./media/active-directory-saas-airwatch-tutorial/IC791921.png "設定")

8.  按一下 [使用者] 索引標籤，在 [基準 DN] 文字欄位中輸入您的網域名稱，然後按一下 [儲存]。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791922.png "使用者")

9.  按一下 [伺服器] 索引標籤。

    ![伺服器](./media/active-directory-saas-airwatch-tutorial/IC791923.png "伺服器")

10. 執行下列步驟：

    ![上傳](./media/active-directory-saas-airwatch-tutorial/IC791924.png "上傳")

    1.  針對 [目錄類型]，選取 [無]。
    2.  選取 [使用 SAML 進行驗證]。
    3.  若要上傳已下載的憑證，請按一下 [上傳]。

11. 在 [要求] 區段中，執行下列步驟：

    ![要求](./media/active-directory-saas-airwatch-tutorial/IC791925.png "要求")

    1.  針對 [要求繫結類型]，選取 [POST]。
    2.  在 Azure 入口網站的 [設定在 Airwatch 單一登入] 對話頁面上，複製 [單一登入服務 URL] 值，然後將它貼至 [識別提供者單一登入 URL] 文字方塊中。
    3.  針對 [NameID 格式]，選取 [電子郵件地址]。
    4.  按一下 [儲存]。

12. 再按一次 [使用者] 索引標籤。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791926.png "使用者")

13. 在 [屬性] 區段中，執行下列步驟：

    ![屬性](./media/active-directory-saas-airwatch-tutorial/IC791927.png "屬性")

    1.  在 [物件識別碼] 文字方塊中，輸入 **http://schemas.microsoft.com/identity/claims/objectidentifier**。
    2.  在 [使用者名稱] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    3.  在 [顯示名稱] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    4.  在 [名字] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    5.  在 [姓氏] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
    6.  在 [電子郵件] 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    7.  按一下 [儲存]。

14. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791928.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 AirWatch，必須將他們佈建到 AirWatch。AirWatch 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  以系統管理員身分登入您的 **AirWatch** 公司網站。

2.  在左側導覽窗格中按一下 [帳戶]，然後按一下 [使用者]。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791929.png "使用者")

3.  在 [使用者] 功能表中，按一下 [清單檢視]，然後按一下 [新增] > [新增使用者]。

    ![新增使用者](./media/active-directory-saas-airwatch-tutorial/IC791930.png "新增使用者")

4.  在 [新增/編輯使用者] 對話方塊中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-airwatch-tutorial/IC791931.png "新增使用者")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的 [使用者名稱]、[密碼]、[確認密碼]、[名字]、[姓氏]、[電子郵件地址]。
    2.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 AirWatch 使用者帳戶建立工具或 AirWatch 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 AirWatch，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [AirWatch] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-airwatch-tutorial/IC791932.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-airwatch-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!----HONumber=August15_HO7-->