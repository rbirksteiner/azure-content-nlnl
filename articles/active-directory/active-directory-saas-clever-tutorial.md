<properties pageTitle="教學課程：Azure Active Directory 與 Clever 整合 | Microsoft Azure" description="了解如何使用 Clever 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Clever 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=551005)。

本教學課程的目的是要示範 Azure 與 Clever 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Clever 租用戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Clever 的 Azure AD 使用者就能夠單一登入您 Clever 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Clever 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-clever-tutorial/IC798977.png "案例")
##啟用 Clever 的應用程式整合

本節的目的是要說明如何啟用 Clever 的應用程式整合。

###若要啟用 Clever 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-clever-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-clever-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-clever-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Clever**。

    ![應用程式庫](./media/active-directory-saas-clever-tutorial/IC798978.png "應用程式庫")

7.  在結果窗格中，選取 [Clever]，然後按一下 [完成] 以加入應用程式。

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Clever。Clever 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應加入 **SAML Token 屬性**組態。以下螢幕擷取畫面顯示上述的範例。

![屬性](./media/active-directory-saas-clever-tutorial/IC798980.png "屬性")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Clever] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC784682.png "設定單一登入")

2.  在 [要如何讓使用者登入 Clever] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798981.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Clever 登入 URL] 文字方塊中，輸入您的使用者用來登入 Clever 應用程式的 URL (例如：**https://clever.com/in/azsandbox*))，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-clever-tutorial/IC798982.png "設定應用程式 URL")

4.  於 [在 Clever 設定單一登入] 頁面上，按 [下載中繼資料] 以下載您的中繼資料，然後將中繼資料檔儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798983.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Clever 公司網站。

6.  在工具列中，按一下 [立即登入]。

    ![立即登入](./media/active-directory-saas-clever-tutorial/IC798984.png "立即登入")

7.  在 [立即登入] 頁面上，執行下列步驟：

    ![立即登入](./media/active-directory-saas-clever-tutorial/IC798985.png "立即登入")

    1.  輸入 [登入 URL]。  

        >[AZURE.NOTE][登入 URL] 是自訂值。您可以向 Clever 支援小組取得實際值。

    2.  針對 [識別系統]，選取 [ADFS]。
    3.  按一下 [儲存]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798986.png "設定單一登入")

9.  在頂端的功能表中，按一下 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。

    ![屬性](./media/active-directory-saas-clever-tutorial/IC795920.png "屬性")

10. 若要加入必要的屬性對應，請執行下列步驟：

    ![SAML Token 屬性](./media/active-directory-saas-clever-tutorial/IC795921.png "SAML Token 屬性")

	|屬性名稱|屬性值|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  針對上表中的每個資料列，按一下 [加入使用者屬性]。
    2.  在 [屬性名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 [屬性值] 文字方塊中，選取該資料列所顯示的屬性值。
    4.  按一下 [完成]。

11. 按一下 [套用變更]。

##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Clever，必須將他們佈建到 Clever。Clever 的佈建工作必須由 Clever 支援小組以手動方式執行。

>[AZURE.NOTE]您可以使用任何其他的 Clever 使用者帳戶建立工具或 Clever 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Clever，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Clever] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-clever-tutorial/IC798987.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-clever-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->