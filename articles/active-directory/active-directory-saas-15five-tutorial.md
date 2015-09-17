<properties pageTitle="教學課程：Azure Active Directory 與 15Five 整合 | Microsoft Azure" description="了解如何使用 15Five 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 15Five 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528017)。

本教學課程的目的是要示範 Azure 與 15Five 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 15Five 單一登入的訂用帳戶

完成本教學課程之後，您指派給 15Five 的 Azure AD 使用者就能夠單一登入您 15Five 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 15Five 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-15five-tutorial/IC784667.png "案例")
##啟用 15Five 的應用程式整合

本節的目的是概述如何啟用 15Five 的應用程式整合。

###若要啟用 15Five 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-15five-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-15five-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-15five-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **15Five**。

    ![應用程式庫](./media/active-directory-saas-15five-tutorial/IC784668.png "應用程式庫")

7.  在結果窗格中，選取 [15Five]，然後按一下 [完成] 加入應用程式。

    ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶對 15Five 進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [15Five] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784670.png "設定單一登入")

2.  在 [要如何讓使用者登入 15Five] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784671.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [15Five 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**https://company.15Five.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-15five-tutorial/IC784672.png "設定應用程式 URL")

4.  在 [設定在 15Five 單一登入] 頁面上，按一下 [下載中繼資料]，然後將中繼資料檔轉寄給 15Five 支援小組。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784673.png "設定單一登入")

    >[AZURE.NOTE]單一登入必須由 15Five 支援小組啟用。

5.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-15five-tutorial/IC784674.png "設定單一登入")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 15Five，必須將他們佈建到 15Five。15Five 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **15Five** 公司網站。

2.  移至 [管理公司]。

    ![管理公司](./media/active-directory-saas-15five-tutorial/IC784675.png "管理公司")

3.  移至 [人員] > [新增人員]。

    ![人員](./media/active-directory-saas-15five-tutorial/IC784676.png "人員")

4.  在 [新增人員] 區段中，執行下列步驟：

    ![新增人員](./media/active-directory-saas-15five-tutorial/IC784677.png "新增人員")

    1.  在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 帳戶的 [名字]、[姓氏]、[職稱]、[電子郵件地址]。
    2.  按一下 [完成]。

    >[AZURE.NOTE]Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以便在帳戶啟用前加以確認。

>[AZURE.NOTE]您可以使用任何其他的 15Five 使用者帳戶建立工具或 15Five 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 15Five，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [15Five] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-15five-tutorial/IC784678.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-15five-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->