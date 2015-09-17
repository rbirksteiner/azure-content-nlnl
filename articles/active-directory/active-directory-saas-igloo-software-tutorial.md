<properties pageTitle="教學課程：Azure Active Directory 與 Igloo Software 整合 | Microsoft Azure" description="了解如何使用 Igloo Software 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Igloo Software 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=526509)。
  
本教學課程的目的是要示範 Azure 與 Igloo Software 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 [Igloo Software](http://www.igloosoftware.com/) 單一登入的訂閱
  
完成本教學課程之後，您已指派至 Igloo Software 的 Azure AD 使用者將能夠在 Igloo Software 公司網站 (服務提供者起始登入)，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)執行單一登入來登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Igloo Software 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "案例")
##啟用 Igloo Software 的應用程式整合
  
本節的目的是要說明如何啟用 Igloo Software 的應用程式整合。

###若要啟用 Igloo Software 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在搜尋方塊中，輸入 **Igloo Software**。

    ![應用程式庫](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "應用程式庫")

7.  在結果窗格中，選取 **Igloo Software**，然後按一下 [完成] 以新增應用程式。

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Igloo Software 中進行驗證。在此程序中，您需要上傳 Base-64 編碼憑證到您的 Central Desktop 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Igloo Software] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "設定單一登入")

2.  在 [您希望使用者如何登入 Igloo Software] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD 單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Igloo Software 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："**https://company.igloocommunities.com/?signin*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "設定應用程式 URL")

4.  在 [設定在 Igloo Software 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後在本機電腦上儲存憑證檔案。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Igloo Software 公司網站。

6.  移至 [控制台]。

    ![控制台](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "控制台")

7.  在 [成員] 索引標籤，按一下 [登入設定]。

    ![登入設定](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "登入設定")

8.  在 [SAML 設定] 區段，按一下 [設定 SAML 驗證]。

    ![SAML 設定](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML 設定")

9.  在 [一般設定] 頁面上，執行下列步驟：

    ![一般設定](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "一般設定")

    1.  在 [連接名稱] 文字方塊中，輸入您組態的自訂名稱。
    2.  在 Azure 入口網站中的 [設定在 Igloo Software 單一登入] 對話方塊頁面上，複製 [遠端登入 URL] 值，然後將它貼至 [IdP 登入 URL] 文字方塊中。
    3.  在 Azure 入口網站中的 [設定在 Igloo Software 單一登入] 對話方塊頁面上，複製 [遠端登出 URL] 值，然後將它貼至 [IdP 登出 URL] 文字方塊中。
    4.  在 [登出回應與要求 HTTP 類型]，選取 [POST]。
    5.  從下載的憑證建立文字檔。
        
		>[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)

    6.  從您文字檔版本的憑證中移除第一行 (**) 和最後一行 (**)，複製剩下的憑證文字，然後貼到 [公開憑證] 文字方塊。

10. 在 [回應與驗證設定]，執行以下步驟：

    ![回應與驗證組態](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "回應與驗證組態")

    1.  在 [身分識別提供者]，選取 [Microsoft ADFS]。
    2.  在 [識別元類型]，選取 [電子郵件地址]。
    3.  在 [電子郵件屬性] 文字方塊中，輸入**電子郵件地址**。
    4.  在 [名字屬性] 文字方塊中，輸入**名字**。
    5.  在 [姓氏屬性] 文字方塊中，輸入**姓氏**。

11. 執行以下步驟來完成設定：

    ![登入時建立使用者](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "登入時建立使用者")

    1.  在 [登入時建立使用者]，選取 [使用者登入您的網站時建立新的使用者]。
    2.  在 [登入設定]，選取 [在「登入」畫面使用 SAML 按鈕]。
    3.  按一下 [儲存]。

12. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "設定單一登入")
##設定使用者佈建
  
沒有動作項目可讓您設定 Igloo Software 使用者佈建。當已指派的使用者透過存取面板嘗試登入 Igloo Software 時，Igloo Software 會檢查使用者是否存在。如果尚無可用的使用者帳戶，Igloo Software 會自動予以建立。
##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Igloo Software，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Igloo Software] 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->