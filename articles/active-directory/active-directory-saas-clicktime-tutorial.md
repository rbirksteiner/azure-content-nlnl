<properties pageTitle="教學課程：Azure Active Directory 與 ClickTime 整合 | Microsoft Azure" description="了解如何使用 ClickTime 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 ClickTime 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=%20524782)。

本教學課程的目的是要示範 Azure 與 ClickTime 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ClickTime 租用戶

完成本教學課程之後，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 ClickTime 的 Azure AD 使用者就能夠單一登入您 ClickTime 公司網站 (服務提供者起始登入) 的應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ClickTime 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-clicktime-tutorial/IC777274.png "案例")
##啟用 ClickTime 的應用程式整合

本節的目的是要說明如何啟用 ClickTime 的應用程式整合。

###若要啟用 ClickTime 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-clicktime-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-clicktime-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-clicktime-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **ClickTime**。

    ![應用程式庫](./media/active-directory-saas-clicktime-tutorial/IC777275.png "應用程式庫")

7.  在結果窗格中，選取 [ClickTime]，然後按一下 [完成] 以加入應用程式。

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 ClickTime。在這個程序中，您需要上傳 base-64 編碼憑證到您的 ClickTime 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

>[AZURE.IMPORTANT]為了能夠設定對您的 ClickTime 租用戶進行單一登入，您必須先連絡 ClickTime 技術支援人員以啟用這項功能。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [ClickTime] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-clicktime-tutorial/IC777277.png "啟用單一登入")

2.  在 [要如何讓使用者登入 ClickTime] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/IC777278.png "設定單一登入")

3.  於 [在 ClickTime 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/IC777279.png "設定單一登入")

4.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 ClickTime 公司網站。

5.  在頂端工具列中，按一下 [喜好設定]，然後按一下 [安全性設定]。

6.  在 [單一登入喜好設定] 組態區段中，執行下列步驟：

    ![安全性設定](./media/active-directory-saas-clicktime-tutorial/IC777280.png "安全性設定")

    1.  選取 [允許]，以搭配 **OneLogin** 使用單一登入 (SSO) 進行登入。
    2.  在 Azure 入口網站的 [在 ClickTime 設定單一登入] 對話頁面上，複製**單一登入服務 URL** 值，然後將它貼至 [識別提供者端點] 文字方塊中。
    3.  從您下載的憑證建立 **base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    4.  在**記事本**中開啟 base-64 編碼的憑證，複製其內容，然後貼到 [X.509 憑證] 文字方塊中。
    5.  按一下 [儲存]。

7.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clicktime-tutorial/IC777281.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 ClickTime，必須將他們佈建到 ClickTime。ClickTime 需以手動方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **ClickTime** 租用戶。

2.  在頂端工具列中，按一下 [公司]，然後按一下 [人員]。

    ![人員](./media/active-directory-saas-clicktime-tutorial/IC777282.png "人員")

3.  按一下 [新增人員]。

    ![新增人員](./media/active-directory-saas-clicktime-tutorial/IC777283.png "新增人員")

4.  在 [新人員] 區段中，執行下列步驟：

    ![人員](./media/active-directory-saas-clicktime-tutorial/IC777284.png "人員")

    1.  在 [電子郵件地址] 文字方塊中，輸入您的 Azure AD 帳戶的電子郵件地址。
    2.  在 [全名] 文字方塊中，輸入您 Azure AD 帳戶的名稱。  

        >[AZURE.NOTE]您可以視需要設定新人員物件的其他屬性。

    3.  按一下 [儲存]。

>[AZURE.NOTE]您可以使用任何其他的 ClickTime 使用者帳戶建立工具或 ClickTime 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 ClickTime，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [ClickTime] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-clicktime-tutorial/IC777285.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-clicktime-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->