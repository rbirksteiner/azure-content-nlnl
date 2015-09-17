<properties pageTitle="教學課程：Azure Active Directory 與 Dropbox for Business 整合 | Microsoft Azure" description="了解如何使用 Dropbox for Business 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Dropbox for Business 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=522415)。
  
本教學課程的目的是要示範 Azure 與 Dropbox for Business 的整合。
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Dropbox for Business 中的測試租用戶
  
完成本教學課程之後，您指派給 Dropbox for Business 的 Azure AD 使用者就能夠單一登入 Dropbox for Business 公司網站 (服務提供者起始登入) 的應用程式，或是使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Dropbox for Business 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "案例")



##啟用 Dropbox for Business 的應用程式整合
  
本節的目的是要說明如何啟用 Dropbox for Business 的應用程式整合。

###若要啟用 Dropbox for Business 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Dropbox for Business**。

    ![應用程式庫](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "應用程式庫")

7.  在結果窗格中選取 [Dropbox for Business]，然後按一下 [完成] 來新增應用程式。

    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶向 Dropbox for Business 驗證。

在這個程序中，您需要上傳 Base-64 編碼憑證到您的 Dropbox for Business 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Dropbox for Business] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "設定單一登入")

2.  在 [要如何讓使用者登入 Dropbox for Business] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面上，執行下列步驟：

     3\.1.登入您的 Dropbox for Business 租用戶。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "設定單一登入")

     3\.2.在左側的導覽窗格中，按一下 [管理主控台]。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "設定單一登入")

     3\.3.在 [管理主控台] 上，按一下左側導覽窗格中的 [驗證]。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "設定單一登入")

     3\.4.在 [單一登入] 區段中，選取 [啟用單一登入]，然後按一下 [更多資訊] 以展開此區段。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "設定單一登入")

     3\.5.複製 [使用者可藉由輸入其電子郵件地址進行登入或可直接前往] 旁邊的 URL。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "設定單一登入")

     3\.6.在 Azure 入口網站的 [DropBox for Business 登入 URL] 文字方塊中，貼上此 URL。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "設定單一登入")



4. 在 [設定在 Dropbox for Business 單一登入] 頁面上，按一下 [下載憑證]，然後將中繼資料檔儲存在您的電腦中。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "設定單一登入")


5. 在 Dropbox for Business 租用戶上，於 [驗證] 頁面的 [單一登入驗證] 區段中，執行下列步驟：<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "設定單一登入")

     5\.1.按一下 [必要]。

     5\.2.在 Azure 入口網站的 [設定在 Dropbox for Business 單一登入] 對話方塊頁面上，複製 [登入頁面 URL] 值，然後將它貼入 [登入 URL] 文字方塊。


     5\.3.從您下載的憑證建立 **Base-64 編碼**檔案。 > [AZURE.TIP] 如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。


     5\.4.按一下 [選擇憑證]，然後瀏覽至您的 **Base-64 編碼的憑證檔案**。


     5\.5.按一下 [儲存變更] 以完成 DropBox for Business 租用戶設定。


6. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。<br><br> ![設定單一登入](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "設定單一登入")





##設定使用者佈建
  
本節的目的是要說明如何對 Dropbox for Business 啟用 Active Directory 使用者帳戶的使用者佈建。


### 若要設定使用者佈建，請執行下列步驟：

1. 在 Azure 管理入口網站的 [Dropbox for Business] 應用程式整合頁面上，按一下 [設定使用者佈建] 來開啟 [設定使用者佈建] 對話方塊。

2. 在 [啟用使用者佈建至 DropBox for Business] 頁面上，按一下 [啟用使用者佈建] 以開啟 [登入 Dropbox 來與 Azure AD 連結] 對話方塊。<br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "使用者佈建")

3. 在 [登入 Dropbox 來與 Azure AD 連結] 對話方塊上，登入您的 Dropbox for Business 租用戶。<br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "使用者佈建")



4. 按一下 [允許] 授與 Azure AD 存取 Dropbox。<br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "使用者佈建")



5. 若要完成設定，請按一下 [完成] 按鈕。<br><br> ![使用者佈建](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "使用者佈建")




##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Dropbox for Business，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Dropbox for Business] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "是")
  


請等候 10 分鐘並確認帳戶已同步至企業適用的 Dropbox。

在第一個驗證步驟中，您可以在 Azure 管理入口網站的 [Dropbox for Business] 應用程式整合頁面上，按一下 [儀表板] 來檢查佈建狀態。

<br><br> ![指派使用者](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "指派使用者")


成功完成的使用者佈建週期會以相關狀態表示。

<br><br> ![指派使用者](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "指派使用者")


如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。




## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!-----HONumber=August15_HO8-->