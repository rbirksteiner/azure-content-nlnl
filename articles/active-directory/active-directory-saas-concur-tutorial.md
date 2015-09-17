<properties pageTitle="教學課程：Azure Active Directory 與 Concur 整合 | Microsoft Azure" description="了解如何使用 Concur 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Concur 整合  

>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=522413)。

本教學課程的目的是要示範 Azure 與 Concur 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Concur 中的租用戶

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Concur 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-concur-tutorial/IC769766.png "案例")

>[AZURE.NOTE]設定 Concur 訂用帳戶以透過 SAML 進行同盟 SSO 是獨立的工作，您必須連絡 Concur 執行這項工作。

##啟用 Concur 的應用程式整合

本節的目的是要說明如何啟用 Concur 的應用程式整合。

###若要啟用 Concur 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-concur-tutorial/IC700994.png "[應用程式]")

4.  若要開啟 [應用程式庫]，請按一下 [新增應用程式]，然後按一下 [加入應用程式讓我的組織使用]。

    ![欲執行動作](./media/active-directory-saas-concur-tutorial/IC700995.png "欲執行動作")

5.  在**搜尋方塊**中，輸入 **Concur**。

    ![應用程式庫](./media/active-directory-saas-concur-tutorial/IC721727.png "應用程式庫")

6.  在結果窗格中，選取 [Concur]，然後按一下 [完成] 以加入應用程式。

    ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 Concur。

>[AZURE.NOTE]設定 Concur 訂用帳戶以透過 SAML 進行同盟 SSO 是獨立的工作，您必須連絡 Concur 執行這項工作。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Concur] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-concur-tutorial/IC769767.png "設定單一登入")

2.  在 [要如何讓使用者登入 Concur] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-concur-tutorial/IC769768.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Concur 登入 URL] 文字方塊中，輸入您的 Concur 租用戶登入 URL，然後按 [下一步]：

    ![設定登入 URL](./media/active-directory-saas-concur-tutorial/IC769769.png "設定登入 URL")

4.  於 [在 Concur 設定單一登入] 頁面上，執行下列步驟。

    ![設定登入 URL](./media/active-directory-saas-concur-tutorial/IC769770.png "設定登入 URL")

    1.  按 [下載中繼資料]，然後將資料檔儲存在您的電腦中。
    2.  請連絡 Concur 支援小組來設定您的租用戶的 SSO。
    3.  選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。  

	>[AZURE.NOTE]設定 Concur 訂用帳戶以透過 SAML 進行同盟 SSO 是獨立的工作，您必須連絡 Concur 執行這項工作。

##設定使用者佈建

本節的目的是要說明如何對 Concur 啟用 Active Directory 使用者帳戶的佈建。

若要啟用 Expense Service 中的應用程式，您必須適當地設定及使用 Web 服務系統管理員設定檔，而不只是將 WS 系統管理員角色加入用於 T&E 系統管理功能的現有系統管理員設定檔。

Concur 顧問或用戶端系統管理員必須建立不同的 Web 服務系統管理員設定檔，而且用戶端系統管理員必須使用這個設定檔來執行 Web 服務系統管理員功能 (例如啟用應用程式)。這些設定檔必須與用戶端系統管理員的每日 T&E 系統管理員設定檔分開保存 (T&E 系統管理員設定檔不應該指派 WS 系統管理員角色)。

當您建立要用於啟用應用程式的設定檔時，請在使用者設定檔欄位中，輸入用戶端系統管理員的名稱。這會將擁有權指派給設定檔。建立設定檔之後，用戶端必須使用這個設定檔登入，並針對合作夥伴應用程式，按一下 [Web 服務] 功能表中的 [啟用] 按鈕。

基於下列原因，不應該透過用於一般 T&E 系統管理的設定檔完成這項動作。

1.  用戶端必須在啟用應用程式之後所顯示的對話視窗中，按一下 [是]。這個點選動作可確認用戶端是否願意讓合作夥伴應用程式存取其資料，因此您或合作夥伴無法按 [是] 按鈕。
2.  如果使用 T&E 系統管理員設定檔啟用應用程式的用戶端系統管理員已離職 (導致設定檔被停用)，在使用另一個現用 WS 系統管理員設定檔啟用應用程式之前，使用該設定檔啟用的所有應用程式都無法正常運作。因此，您必須建立不同的 WS 系統管理員設定檔。
3.  如果系統管理員離職，與 WS 系統管理員設定檔關聯的名稱可視需要變更為續任的系統管理員，由於不需要停用該設定檔，因此不會影響已啟用的應用程式。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您的 **Concur** 租用戶。

2.  從 [管理] 功能表中，選取 [Web 服務]。

    ![Concur 租用戶](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur 租用戶")

3.  從左側的 [Web 服務] 窗格中，選取 [啟用合作夥伴應用程式]。

    ![啟用合作夥伴應用程式](./media/active-directory-saas-concur-tutorial/IC721730.png "啟用合作夥伴應用程式")

4.  從 [啟用應用程式] 清單中選取 [Azure Active Directory]，然後按一下 [啟用]。

    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  按一下 [是] 關閉 [確認動作] 對話方塊。

    ![確認動作](./media/active-directory-saas-concur-tutorial/IC721732.png "確認動作")

6.  從 Azure 管理入口網站的應用程式清單中選取[Concur]，以開啟 [Concur] 對話頁面。

7.  若要開啟 [設定使用者佈建] 對話頁面，按一下 [設定使用者佈建]。

8.  輸入您的 Concur 系統管理員的使用者名稱和密碼，然後按 [下一步]。

9.  若要完成組態，請在 [確認] 頁面上，按一下 [完成] 按鈕。

您現在可以建立測試帳戶，請等候 10 分鐘並確認帳戶已同步至 Concur。
##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Concur，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Concur] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-concur-tutorial/IC769771.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-concur-tutorial/IC767830.png "是")

請等候 10 分鐘並確認帳戶已同步至 Concur。

如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->