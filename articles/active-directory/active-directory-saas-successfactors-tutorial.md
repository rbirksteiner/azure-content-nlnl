<properties pageTitle="教學課程：Azure Active Directory 與 SuccessFactors 整合 | Microsoft Azure" description="了解如何使用 SuccessFactors 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 SuccessFactors 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=529792)。
  
本教學課程的目的是要示範在 [服務提供者起始單一登入模式] 之 Azure 與 SuccessFactors 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   在 [服務提供者起始模式] 中啟用 SuccessFactors 單一登入的訂用帳戶
  
完成本教學課程之後，您已指派給 SuccessFactors 的 Azure AD 使用者將能夠單一登入您 SuccessFactors 公司網站 (服務提供者起始登入) 的應用程式，或使用[存取面板](https://msdn.microsoft.com/library/dn308586)進行單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SuccessFactors 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-successfactors-tutorial/IC791135.png "案例")

##啟用 SuccessFactors 的應用程式整合
  
本節的目的是概述如何啟用 SuccessFactors 的應用程式整合。

###若要啟用 SuccessFactors 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-successfactors-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-successfactors-tutorial/IC749321.png "新增應用程式")

5.  在 [您想要執行什麼動作] 對話方塊中，按一下 [從資源庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-successfactors-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋方塊] 中，輸入 **SuccessFactors**。

    ![應用程式庫](./media/active-directory-saas-successfactors-tutorial/IC791136.png "應用程式庫")

7.  在結果窗格中，選取 [SuccessFactors]，然後按一下 [完成] 加入應用程式。

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 SuccessFactors 中進行驗證。
  
若要設定單一登入，您必須連絡您的 SuccessFactors 支援小組。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 **SuccessFactors** 應用程式整合頁面上，按一下 [設定單一登入] 開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791138.png "設定單一登入")

2.  在 [要如何讓使用者登入 SuccessFactors] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791139.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面上，執行下列步驟，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-successfactors-tutorial/IC791140.png "設定應用程式 URL")

    1.  在 [SuccessFactors 登入 URL] 文字方塊中，輸入使用者用來登入 SuccessFactors 應用程式的 URL (例如："**https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*"))。
2.  在 [SuccessFactors 回覆 URL] 文字方塊中，輸入 ****https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**。

        >[AZURE.NOTE]這個值只是暫時的預留位置。您會從 SuccessFactors 支援小組取得實際的值。稍後在本教學課程中，您會找到連絡 SuccessFactors 支援小組的指示。在此交談內容中，您會收到實際 SuccessFactors 回覆 URL。

4.  在 [設定在 SuccessFactors 單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791141.png "設定單一登入")

5.  若要設定以 SAML 為基礎的單一登入，請連絡您的 SuccessFactors 支援小組，並提供下列項目：

    1.  下載的憑證
    2.  遠端登入 URL
    3.  遠端登出 URL

    >[AZURE.IMPORTANT]請要求您的 SuccessFactors 支援小組將 NameId 格式參數設定為「未指定」。

    您的 Successfactors 支援小組會將 [設定應用程式 URL] 對話方塊所需的正確 **Successfactors 回覆 URL** 傳送給您]。

6.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791142.png "設定單一登入")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 SuccessFactors，必須將他們佈建到 SuccessFactors。SuccessFactors 需以手動方式佈建。
  
若要在 SuccessFactors 建立使用者，您需要連絡 SuccessFactors 支援小組。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 SuccessFactors，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [SuccessFactors 應用程式整合] 頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-successfactors-tutorial/IC791143.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-successfactors-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資料，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->