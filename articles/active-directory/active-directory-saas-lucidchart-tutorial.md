<properties pageTitle="教學課程：Azure Active Directory 與 Lucidchart 整合 | Microsoft Azure" description="了解如何使用 Lucidchart 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Lucidchart 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=532346)。
  
本教學課程的目的是要示範 Azure 與 Lucidchart 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Lucidchart 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 Lucidchart 的 Azure AD 使用者將能夠在 Lucidchart 公司網站 (服務提供者起始登入) 執行單一登入來登入應用程式，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)執行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Lucidchart 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "案例")
##啟用 Lucidchart 的應用程式整合
  
本節的目的是要說明如何啟用 Lucidchart 的應用程式整合。

###若要啟用 Lucidchart 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "新增應用程式")

5.  在 [您要如何處理] 對話方塊中，按一下 [從組件庫中新增應用程式]。

    ![從組件庫中新增應用程式](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "從組件庫中新增應用程式")

6.  在搜尋方塊中，輸入 **Lucidchart**。

    ![應用程式庫](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "應用程式庫")

7.  在結果窗格中，選取 [Lucidchart]，然後按一下 [完成] 以加入應用程式。

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Lucidchart 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Lucidchart] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "設定單一登入")

2.  在 [您希望使用者如何登入 Lucidchart] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Lucidchart 單一登入 URL] 文字方塊中，輸入使用者登入您的 Lucidchart 應用程式時所使用的 URL (如："**https://chart2.office.lucidchart.com/saml/sso/azure*"))，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "設定應用程式 URL")

4.  在 [設定在 Lucidchart 單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後將資料檔儲存在您的本機電腦中。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Lucidchart 公司網站。

6.  在頂端的功能表中，按一下 [小組]。

    ![小組](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "小組")

7.  按一下 [應用程式 > 管理 SAML]。

    ![管理 SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "管理 SAML")

8.  在 [SAML 驗證設定] 對話方塊頁面上執行下列步驟：

    1.  選取 [啟用 SAML 驗證]，然後按一下 [選用]。![基本驗證設定](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "基本驗證設定")
    2.  在 [網域] 文字方塊中，輸入您的網域，然後按一下 [變更憑證]。 ![變更憑證](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "變更憑證")
    3.  開啟您下載的中繼資料檔案，然後複製內容並貼到並 [上傳中繼資料] 文字方塊中。![上傳中繼資料](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "上傳中繼資料")
    4.  選取 [自動新增使用者至小組]，然後按一下 [儲存變更]。![儲存變更](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "儲存變更")

9.  選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "設定單一登入")
##設定使用者佈建
  
沒有動作項目可讓您設定 Lucidchart 使用者佈建。當指派的使用者嘗試使用存取面板登入 Lucidchart 時，Lucidchart 會檢查使用者是否存在。如果尚無可用的使用者帳戶，Lucidchart 會自動予以建立。
##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Lucidchart，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Lucidchart] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "是")
  
如果您想要測試您的單一登入設定，請開啟 [存取面板]。如需 [存取面板] 的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->