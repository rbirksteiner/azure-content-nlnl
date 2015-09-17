<properties pageTitle="教學課程：Azure Active Directory 與 Huddle 整合 | Microsoft Azure" description="了解如何使用 Huddle 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Huddle 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=528081)。
  
本教學課程的目的是要示範 Azure 與 Huddle 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 Huddle 單一登入的訂用帳戶
  
完成本教學課程之後，或是透過[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Huddle 的 Azure AD 使用者就能夠單一登入您 Huddle 公司網站 (服務提供者起始登入) 的應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Huddle 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787830.png "設定單一登入")
##啟用 Huddle 的應用程式整合
  
本節的目的是要說明如何啟用 Huddle 的應用程式整合。

###若要啟用 Huddle 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-huddle-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-huddle-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-huddle-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Huddle**。

    ![應用程式庫](./media/active-directory-saas-huddle-tutorial/IC787831.png "應用程式庫")

7.  在結果窗格中，選取 [Huddle]，然後按一下 [完成] 來新增應用程式。

    ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Huddle。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Huddle] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787833.png "設定單一登入")

2.  在 [要如何讓使用者登入 Huddle] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787834.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Huddle 登入 URL] 文字方塊中，使用下列模式輸入您 Huddle 租用戶的 URL："**http://company.huddle.com*"，然後按一下 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-huddle-tutorial/IC787835.png "設定應用程式 URL")

4.  在 [設定在 Huddle 單一登入] 頁面上，執行下列步驟。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787836.png "設定單一登入")

    1.  按一下 [下載憑證]，然後將憑證檔案儲存在您的電腦上。
    2.  複製 [簽發者 URL] 值、[SAML SSO URL] 值和下載的憑證，然後再傳送至 Huddle 支援小組。

    >[AZURE.NOTE]單一登入必須由 Huddle 支援小組啟用。設定完成後，您將會收到通知。

5.  在 Azure AD 入口網站上，選取 [單一登入設定確認]，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787837.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Huddle，則必須將他們佈建至 Huddle。在 Huddle 的情況下，需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 **Huddle** 公司網站。

2.  按一下 [工作區]。

3.  按一下 [人員] > [邀請人員]。

    ![人員](./media/active-directory-saas-huddle-tutorial/IC787838.png "人員")

4.  在 [建立新的邀請] 區段中，執行下列步驟：

    ![新的邀請](./media/active-directory-saas-huddle-tutorial/IC787839.png "新的邀請")

    1.  在 [選擇要邀請人員加入的小組] 清單中，選取 [小組]。
    2.  在相關的文字方塊中，輸入您要佈建之有效 AAD 帳戶的 [電子郵件地址]。
    3.  按一下 [邀請]。

    >[AZURE.NOTE]Azure AD 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。

>[AZURE.NOTE]您可以使用任何其他的 Huddle 使用者帳戶建立工具或 Huddle 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Huddle，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Huddle] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-huddle-tutorial/IC787840.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-huddle-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->