<properties pageTitle="教學課程：Azure Active Directory 與 Box 整合 | Microsoft Azure" description="了解如何使用 Box 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />




#教學課程：Azure Active Directory 與 Box 整合


>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=522410)。
  
本教學課程的目的是要示範 Azure 與 Box 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Box 中的測試租用戶
  
完成本教學課程之後，或是使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)，您指派給 Box 的 Azure AD 使用者就能夠單一登入您 Box 公司網站 (服務提供者起始登入) 的應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Box 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-box-tutorial/IC769537.png "案例")



##啟用 Box 的應用程式整合
  
本節的目的是概述如何啟用 Box 的應用程式整合。

###若要啟用 Box 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-box-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-box-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-box-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Box**。

    ![應用程式庫](./media/active-directory-saas-box-tutorial/IC701023.png "應用程式庫")

7.  在結果窗格中，選取 [Box]，然後按一下 [完成] 以加入應用程式。

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Box 中進行驗證。<br>在此程序中，您必須將中繼資料上傳至 Box.com。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Box] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769538.png "設定單一登入")

2.  在 [要如何讓使用者登入 Box] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769539.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Box 租用戶 URL] 文字方塊中，輸入您的 Box 租用戶 URL (例如︰https://<mydomainname>.box.com)，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-box-tutorial/IC669826.png "設定應用程式 URL")

4.  在 [設定在 Box 單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後將資料檔儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC669824.png "設定單一登入")

5.  將該中繼資料檔案轉寄給 Box 支援小組。支援小組需要為您設定單一登入。

6.  選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-box-tutorial/IC769540.png "設定單一登入")
##設定使用者佈建
  
本節的目的是要說明如何對 Box 啟用 Active Directory 使用者帳戶的佈建。

###若要設定單一登入，請執行下列步驟：

1. 在 Azure 管理入口網站中的 [Box] 應用程式整合頁面上，按一下 [設定使用者佈建] 以開啟 [設定使用者佈建] 對話方塊。<br> <br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769541.png "啟用自動使用者佈建")

2. 在 [**啟用使用者佈建到 Box**] 對話方塊頁面上，按一下 [**啟用使用者佈建**]。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769544.png "啟用自動使用者佈建")

3. 在 [登入以授與 Box 存取權] 頁面上，提供必要的認證，然後按一下 [授權]。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769546.png "啟用自動使用者佈建")


4. 按一下 [授與 Box 存取權]，以授權進行此作業並返回 Azure 管理入口網站。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769549.png "啟用自動使用者佈建")

5. 若要完成設定，請按一下 [完成] 按鈕。<br><br> ![啟用自動使用者佈建](./media/active-directory-saas-box-tutorial/IC769551.png "啟用自動使用者佈建")



##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Box，請執行下列步驟：

1. 在 Azure AD 入口網站中建立測試帳戶。

2. 在 [Box] 應用程式整合頁面上，按一下 [指派使用者]。<br><br> ![指派使用者](./media/active-directory-saas-box-tutorial/IC769552.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。<br><br> ![是](./media/active-directory-saas-box-tutorial/IC767830.png "是")
  

請等候 10 分鐘並確認帳戶已同步至 Box。

在第一個驗證步驟中，您可以在 Azure 管理入口網站的 Box 應用程式整合頁面上，按一下 [儀表板] 來檢查佈建狀態。

<br><br> ![儀表板](./media/active-directory-saas-box-tutorial/IC769553.png "儀表板")

成功完成的使用者佈建週期會以相關狀態表示：

<br><br> ![整合狀態](./media/active-directory-saas-box-tutorial/IC769555.png "整合狀態")


在 Box 租用戶中，已同步處理的使用者會列在 [管理主控台] 的 [管理使用者] 之下。

<br><br> ![整合狀態](./media/active-directory-saas-box-tutorial/IC769556.png "整合狀態")


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!---HONumber=August15_HO8-->