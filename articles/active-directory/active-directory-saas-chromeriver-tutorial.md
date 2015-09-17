<properties pageTitle="教學課程：Azure Active Directory 與 Chromeriver 整合 | Microsoft Azure" description="了解如何使用 Chromeriver 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Chromeriver 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=615279)。

本教學課程的目的是要示範 Azure 與 Chromeriver 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Chromeriver 單一登入的訂用帳戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Chromeriver 的 Azure AD 使用者就能夠單一登入您 Chromeriver 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Chromeriver 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "案例")
##啟用 Chromeriver 的應用程式整合

本節的目的是要說明如何啟用 Chromeriver 的應用程式整合。

###若要啟用 Chromeriver 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Chromeriver**。

    ![應用程式庫](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "應用程式庫")

7.  在結果窗格中，選取 [Chromeriver]，然後按一下 [完成] 以加入應用程式。
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Chromeriver。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Chromeriver] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "設定單一登入")

2.  在 [要如何讓使用者登入 Chromeriver] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "設定單一登入")

3.  在 [設定應用程式設定] 頁面上，執行下列步驟：

    ![設定 App 設定](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "設定 App 設定")

    1.  在 [回覆 URL] 文字方塊中，輸入您的 Chromeriver **AssertionConsumerService URL** (例如：**https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*))。

        >[AZURE.NOTE]您可以向 Chromeriver 支援小組取得這個值。

    2.  按 [**下一步**]

4.  於 [在 Chromeriver 設定單一登入] 頁面上，按 [下載中繼資料] 以下載您的中繼資料，然後將中繼資料檔儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "設定單一登入")

5.  將下載的中繼資料檔傳送給 Chromeriver 支援小組。

    >[AZURE.NOTE]Chromeriver 支援小組必須執行實際的 SSO 組態。當您的訂用帳戶啟用 SSO 之後，您會收到通知。

6.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 Chromeriver，必須將他們佈建到 Chromeriver。Chromeriver 的使用者帳戶必須由 Chromeriver 支援小組建立。

>[AZURE.NOTE]您可以使用任何其他的 Chromeriver 使用者帳戶建立工具或 Chromeriver 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Chromeriver，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Chromeriver] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->