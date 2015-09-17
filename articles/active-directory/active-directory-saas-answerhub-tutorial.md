<properties pageTitle="教學課程：Azure Active Directory 與 AnswerHub 整合 | Microsoft Azure" description="了解如何使用 AnswerHub 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 AnswerHub 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528077)。

本教學課程的目的是要示範 Azure 與 AnswerHub 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 AnswerHub 單一登入的訂用帳戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 AnswerHub 的 Azure AD 使用者就能夠單一登入您 AnswerHub 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 AnswerHub 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-answerhub-tutorial/IC785165.png "案例")
##啟用 AnswerHub 的應用程式整合

本節的目的是要說明如何啟用 AnswerHub 的應用程式整合。

###若要啟用 AnswerHub 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-answerhub-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-answerhub-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-answerhub-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **AnswerHub**。

    ![應用程式庫](./media/active-directory-saas-answerhub-tutorial/IC785166.png "應用程式庫")

7.  在結果窗格中，選取 [AnswerHub]，然後按一下 [完成] 以加入應用程式。

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 AnswerHub 。在此程序中，您必須建立 base-64 編碼的憑證檔案。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [AnswerHub] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785168.png "設定單一登入")

2.  在 [要如何讓使用者登入 AnswerHub] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785169.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [AnswerHub 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**https://company.answerhub.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-answerhub-tutorial/IC785170.png "設定應用程式 URL")

4.  於 [在 AnswerHub 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後在本機電腦上儲存憑證檔案。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785171.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 AnswerHub 公司網站。

6.  移至 [管理]。

7.  按一下 [使用者和群組] 索引標籤。

8.  在左側瀏覽窗格的 [社交設定] 區段中，按一下 [SAML 設定]。

9.  按一下 [IDP 設定] 索引標籤。

10. 在 [IDP 設定] 索引標籤上，執行下列步驟：

    ![SAML 設定](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML 設定")

    1.  在 Azure 入口網站的 [在 AnswerHub 設定單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [IDP 登入 URL] 文字方塊中。
    2.  在 Azure 入口網站的 [在 AnswerHub 設定單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [IDP 登出 URL] 文字方塊中。
    3.  在 Azure 入口網站的 [在 AnswerHub 設定單一登入] 對話頁面上，複製 [名稱識別碼格式] 值，然後將它貼至 [IDP 名稱識別碼格式] 文字方塊中。
    4.  按一下 [金鑰和憑證]。

11. 在 [金鑰和憑證] 索引標籤上，執行下列步驟：

    ![金鑰和憑證](./media/active-directory-saas-answerhub-tutorial/IC785173.png "金鑰和憑證")

    1.  從您下載的憑證建立 **base-64 編碼**檔案。  

		>[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    2.  在記事本中開啟 base-64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [IDP 公開金鑰 (x509 格式)] 文字方塊中。
    3.  按一下 [儲存]。

12. 在 [IDP 設定] 索引標籤上，按一下 [儲存]。

13. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785174.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 AnswerHub，必須將他們佈建到 AnswerHub。AnswerHub 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **AnswerHub** 公司網站。

2.  移至 [管理]。

3.  按一下 [使用者和群組] 索引標籤。

4.  在左側瀏覽窗格的 [管理使用者] 區段中，按一下 [建立或匯入使用者]。

    ![使用者和群組](./media/active-directory-saas-answerhub-tutorial/IC785175.png "使用者和群組")

5.  在相關的文字方塊中，輸入您要佈建之有效 Azure Active Directory 帳戶的 [電子郵件地址]、[使用者名稱] 和 [密碼]，然後按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 AnswerHub 使用者帳戶建立工具或 AnswerHub 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 AnswerHub，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [AnswerHub] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-answerhub-tutorial/IC785176.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-answerhub-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->