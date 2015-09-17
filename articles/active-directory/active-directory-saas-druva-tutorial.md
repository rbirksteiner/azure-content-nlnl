<properties pageTitle="教學課程：Azure Active Directory 與 Druva 整合 | Microsoft Azure" description="了解如何使用 Druva 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Druva 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=534089)。

本教學課程的目的是要示範 Azure 與 Druva 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 Druva 單一登入的訂用帳戶

完成本教學課程之後，您指派給 Druva 的 Azure AD 使用者就能夠單一登入您 Druva 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Druva 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-druva-tutorial/IC795084.png "案例")
##啟用 Druva 的應用程式整合

本節的目的是要說明如何啟用 Druva 的應用程式整合。

###若要啟用 Druva 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-druva-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-druva-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-druva-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Druva**。

    ![應用程式庫](./media/active-directory-saas-druva-tutorial/IC795085.png "應用程式庫")

7.  在結果窗格中，選取 [Druva]，然後按一下 [完成] 來加入應用程式。

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Druva。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

Druva 應用程式需要特定格式的 SAML 判斷提示，您需要將自訂屬性對應加入您的 **SAML Token 屬性**組態。以下螢幕擷取畫面顯示上述的範例。

![SAML Token 屬性](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML Token 屬性")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure Active Directory 入口網站的 [Druva] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795027.png "設定單一登入")

2.  在 [要如何讓使用者登入 Druva] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795088.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Druva 單一登入 URL] 文字方塊中，輸入使用者登入您 Druva 應用程式所使用的 URL (如："**https://cloud.druva.com/home/*”)，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-druva-tutorial/IC795089.png "設定應用程式 URL")

4.  在 [設定在 Druva 單一登入] 頁面上，按一下 [下載憑證] 來下載您的憑證，然後將憑證檔案儲存在本機電腦上。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795090.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Druva 公司網站。

6.  移至 [管理] > [設定]。

    ![設定](./media/active-directory-saas-druva-tutorial/IC795091.png "設定")

7.  在 [單一登入設定] 對話方塊中，執行下列步驟：

    ![單一登入設定](./media/active-directory-saas-druva-tutorial/IC795092.png "單一登入設定")

    1.  在 Azure 入口網站中的 [設定在 Druva 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將其貼至 [識別提供者登入 URL] 文字方塊中。
    2.  在 Azure 入口網站中的 [設定在 Druva 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將其貼至 [識別碼提供者登入 URL] 文字方塊中。
    3.  從您下載的憑證建立 **base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您的 base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [識別提供者憑證] 文字方塊中。
    5.  按一下 [儲存] 來開啟 [設定] 頁面。

8.  在 [設定] 頁面上，按一下 [產生 SSO Token]。

    ![設定](./media/active-directory-saas-druva-tutorial/IC795093.png "設定")

9.  在 [單一登入驗證 Token] 對話方塊中，執行下列步驟：

    ![SSO Token](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")

    1.  按一下 [複製]。
    2.  按一下 [關閉]。

10. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795095.png "設定單一登入")

11. 在頂端的功能表中，按一下 [屬性] 來開啟 [SAML Token 屬性] 對話方塊。

    ![屬性](./media/active-directory-saas-druva-tutorial/IC795096.png "屬性")

12. 若要加入必要的屬性對應，請執行下列步驟：

	|屬性名稱|屬性值|
    |---|---|
    |insync\_auth\_token|<*剪貼簿值*>|

    1.  針對上表中的每個資料列，按一下 [加入使用者屬性]。
    2.  在 [屬性名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。
    3.  在 [屬性值] 文字方塊中，輸入該資料列所顯示的屬性值。
    4.  按一下 [完成]。

13. 按一下 [套用變更]。
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Druva，則必須將他們佈建到 Druva。Druva 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Druva** 公司網站。

2.  移至 [管理] > [使用者]。

    ![管理使用者](./media/active-directory-saas-druva-tutorial/IC795097.png "管理使用者")

3.  按一下 [新建]。

    ![管理使用者](./media/active-directory-saas-druva-tutorial/IC795098.png "管理使用者")

4.  在 [建立新的使用者] 對話方塊中，執行下列步驟：

    ![建立新的使用者](./media/active-directory-saas-druva-tutorial/IC795099.png "建立新的使用者")

    1.  在相關的文字方塊中，輸入您要佈建之有效 Azure Active Directory 使用者帳戶的電子郵件地址和姓名。
    2.  按一下 [建立使用者]。

>[AZURE.NOTE]您可以使用任何其他的 Druva 使用者帳戶建立工具或 Druva 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Druva，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Druva] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-druva-tutorial/IC795100.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-druva-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->