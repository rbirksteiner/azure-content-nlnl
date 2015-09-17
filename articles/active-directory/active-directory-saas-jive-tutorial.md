<properties pageTitle="教學課程：Azure Active Directory 與 Jive 整合 | Microsoft Azure" description="了解如何使用 Jive 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Jive 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=330042)。如需本主題的詳細資訊，請參閱[管理 Azure Active Directory 應用程式存取增強功能的最佳做法](http://go.microsoft.com/fwlink/?LinkId=329963)。
  
本教學課程的目的是要示範 Azure 與 Jive 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Jive 中的租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Jive 的應用程式整合
2.  設定使用者佈建

##啟用 Jive 的應用程式整合
  
本節的目的是要說明如何啟用 Jive 的應用程式整合。

###若要啟用 Jive 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-jive-tutorial/IC700994.png "[應用程式]")

4.  若要開啟 [應用程式庫]，按一下 [新增應用程式]，然後按一下 [加入應用程式讓我的組織使用]。

    ![您要如何處理？](./media/active-directory-saas-jive-tutorial/IC700995.png "您要如何處理？")

5.  在搜尋方塊中，輸入 **Jive**。

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  在結果窗格中，選取 [Jive]，然後按一下 [完成] 以加入應用程式。

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##設定使用者佈建
  
本節的目的是要說明如何對 Jive 啟用 Active Directory 使用者帳戶的使用者佈建。在此程序中，您必須提供從 Jive.com 要求所需的使用者安全性權杖。
  
下列螢幕擷取畫面顯示 Azure AD 中相關的對話方塊範例：

![設定使用者佈建](./media/active-directory-saas-jive-tutorial/IC698794.png "設定使用者佈建")

###若要設定使用者佈建，請執行下列步驟：

1.  在 Azure 管理入口網站中的 [Jive] 應用程式整合頁面上，按一下 [設定使用者佈建] 以開啟 [設定使用者佈建] 對話方塊。

2.  在 [輸入您的 Jive 認證來啟用自動使用者佈建] 頁面上，提供以下組態設定：

    1.  在 [Jive 管理員使用者名稱] 文字方塊中，輸入在 Jive.com 已指派 **System Administrator** 設定檔的 Jive 帳戶名稱。

    2.  在 [Jive 管理員密碼] 文字方塊中，輸入這個帳戶的密碼。

    3.  在 [Jive 租用戶 URL] 文字方塊中，輸入 Jive 租用戶 URL。

        >[AZURE.NOTE]Jive 租用戶 URL 是您的組織登入 Jive 所使用的 URL。一般來說，該 URL 的格式如下：**www.<organization>.jive.com**。

    4.  按一下 [驗證] 來驗證您的組態。

    5.  按一下 [下一步] 按鈕以開啟 [確認] 頁面。

3.  在 [確認] 頁面上，按一下核取記號以儲存您的組態。
  
您現在可以建立測試帳戶，等待 10 分鐘，然後確認帳戶已同步至 Jive.com。

<!---HONumber=August15_HO7-->