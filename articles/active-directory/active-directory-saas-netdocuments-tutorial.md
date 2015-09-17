<properties pageTitle="教學課程：Azure Active Directory 與 NetDocuments 整合 | Microsoft Azure" description="了解如何使用 NetDocuments 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 NetDocuments 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=529696)。
  
本教學課程的目的是要示範 Azure 與 NetDocuments 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   NetDocuments 租用戶
  
完成本教學課程之後，您指派給 NetDocuments 的 Azure AD 使用者就能夠單一登入您 NetDocuments 公司網站 (服務提供者起始登入) 的應用程式，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 NetDocuments 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "案例")
##啟用 NetDocuments 的應用程式整合
  
本節的目的是要說明如何啟用 NetDocuments 的應用程式整合。

###若要啟用 NetDocuments 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在**搜尋方塊**中輸入 **NetDocuments**。

    ![應用程式庫](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "應用程式庫")

7.  在結果窗格中選取 [NetDocuments]，然後按一下 [完成] 以新增應用程式。

    ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 NetDocuments 中進行驗證。  
設定 NetDocuments 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉這個程序，請參閱[如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [NetDocuments] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "設定單一登入")

2.  在 [要如何讓使用者登入 NetDocuments] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面上，執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "設定應用程式 URL")

    1.  在 [登入 URL] 文字方塊中輸入使用者用來登入您 NetDocuments 應用程式的 URL (例如："*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*")。
    2.  在 [NetDocuments 回覆 URL] 文字方塊中，輸入您在 [登入 URL] 文字方塊中輸入的相同值。  

        >[AZURE.NOTE]您可以在 [同盟識別身分] 對話方塊結尾找到正確值 (請參閱步驟 9 的螢幕擷取畫面)。

    3.  按 [**下一步**]

4.  在 [設定在 NetDocuments 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在本機電腦上。

    ![設定單一登入](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 NetDocuments 公司網站。

6.  移至 [管理]。

7.  按一下 [新增與移除使用者和群組]。

    ![存放庫](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "存放庫")

8.  按一下 [設定進階驗證選項]。

    ![設定進階驗證選項](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "設定進階驗證選項")

9.  在 [同盟識別身分] 對話方塊上，執行下列步驟：

    ![同盟識別身分](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "同盟識別身分")

    1.  對於**同盟識別身分伺服器類型**，請選取 [Active Directory 同盟服務]。
    2.  按一下 [選擇檔案] 上傳已下載的中繼資料檔案。
    3.  按一下 [確定]。

10. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "設定單一登入")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 NetDocuments，必須將他們佈建到 NetDocuments 中。在 NetDocuments 的情況下，需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入 **NetDocuments** 公司網站。

2.  在頂端的功能表中，按一下 [管理]。

    ![Admin](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")

3.  按一下 [新增與移除使用者和群組]。

    ![存放庫](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "存放庫")

4.  在 [電子郵件地址] 文字方塊中輸入您想要佈建的有效 Azure Active Directory 帳戶電子郵件地址，然後按一下 [新增使用者]。

    ![電子郵件地址](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "電子郵件地址")

    >[AZURE.NOTE]Azure Active Directory 帳戶的持有者會收到一封電子郵件，其中中包含在帳戶變成作用中之前確認帳戶的連結。

>[AZURE.NOTE]您可以使用任何其他的 NetDocuments 使用者帳戶建立工具或 NetDocuments 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要將使用者指派給 NetDocuments，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [NetDocuments] 應用程式整合頁面，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!-----HONumber=August15_HO7-->