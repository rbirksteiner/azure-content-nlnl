<properties pageTitle="教學課程：Azure Active Directory 與 Samanage 整合 | Microsoft Azure" description="了解如何使用 Samanage 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Samanage 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=522516)。
  
本教學課程的目的是要示範 Azure 與 Samanage 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Samanage 租用戶
  
完成本教學課程之後，您指派給 Samanage 的 Azure AD 使用者就能夠單一登入您 Samanage 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Samanage 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-samanage-tutorial/IC771705.png "案例")
##啟用 Samanage 的應用程式整合
  
本節的目的是要說明如何啟用 Samanage 的應用程式整合。

###若要啟用 Samanage 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-samanage-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-samanage-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-samanage-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-samanage-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊]中，輸入 **Samanage**。

    ![應用程式庫](./media/active-directory-saas-samanage-tutorial/IC771707.png "應用程式庫")

7.  在結果窗格中，選取 [Samanage]，然後按一下 [完成] 來新增應用程式。

    ![Samanage](./media/active-directory-saas-samanage-tutorial/IC771708.png "Samanage")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Samanage 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Samanage] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-samanage-tutorial/IC771709.png "設定單一登入")

2.  在 [要如何讓使用者登入 Samanage] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-samanage-tutorial/IC771710.png "Microsoft Azure AD 單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Samanage 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.samanage.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-samanage-tutorial/IC771711.png "設定應用程式 URL")

4.  在 [設定在 Samanage 單一登入] 頁面上，按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-samanage-tutorial/IC777613.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Samanage 公司網站。

6.  按一下 [儀表板] 並選取左側導覽窗格中的 [安裝]。

    ![儀表板](./media/active-directory-saas-samanage-tutorial/IC771712.png "儀表板")

7.  按一下 [單一登入]。

    ![單一登入](./media/active-directory-saas-samanage-tutorial/IC771713.png "單一登入")

8.  在 [使用 SAML 登入] 對話方塊頁面上，執行下列步驟，然後按一下 [儲存變更]：

    1.  按一下 [使用 SAML 啟用單一登入]。
        ![使用 SAML 登入](./media/active-directory-saas-samanage-tutorial/IC771719.png "使用 SAML 登入")
    2.  在 Azure 入口網站中的 [設定在 Samanage 單一登入] 對話頁面上，複製 [識別提供者 ID] 值，然後將其貼至 [識別提供者 URL] 文字方塊中。
        ![設定單一登入](./media/active-directory-saas-samanage-tutorial/IC771720.png "設定單一登入")
    3.  在 Azure 入口網站中的 [設定在 Samanage 單一登入] 對話頁面上，複製 [遠端登入 URL] 值，然後將其貼至 [登入 URL] 文字方塊中。
    4.  在 Azure 入口網站中的 [設定在 Samanage 單一登入] 對話頁面上，複製 [遠端登出 URL] 值，然後將其貼至 [登出 URL] 文字方塊中。
    5.  從您下載的憑證建立 **base-64 編碼**檔案。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您的 base-64 編碼的憑證，將其內容複製到您的剪貼簿，然後貼到 [X.509 憑證] 文字方塊中
    7.  按一下 [若 Samanage 中不存在使用者則加以建立]。
        ![更新](./media/active-directory-saas-samanage-tutorial/IC771722.png "更新")
    8.  按一下 [更新]。

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-samanage-tutorial/IC771723.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Samanage，則必須將他們佈建至 Samanage。Samanage 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您的 **Samanage** 租用戶。

2.  移至 [儀表板] > [安裝]。

    ![設定](./media/active-directory-saas-samanage-tutorial/IC771724.png "設定")

3.  按一下 [使用者] 索引標籤

    ![使用者](./media/active-directory-saas-samanage-tutorial/IC771725.png "使用者")

4.  按一下 [新增使用者]。

    ![新增使用者](./media/active-directory-saas-samanage-tutorial/IC771726.png "新增使用者")

5.  輸入您想要佈建之 Azure AD 帳戶的**電子郵件地址**與**名稱**並按一下 [建立使用者]。

    >[AZURE.NOTE]AAD 帳戶的持有者會收到一封電子郵件，並遵循連結在啟用其帳戶前進行確認。

    ![建立使用者](./media/active-directory-saas-samanage-tutorial/IC771727.png "建立使用者")

>[AZURE.NOTE]您可以使用任何其他的 Samanage 使用者帳戶建立工具或 Samanage 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Samanage，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Samanage] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-samanage-tutorial/IC771728.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 來確認指派。

    ![是](./media/active-directory-saas-samanage-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!-------HONumber=August15_HO7-->