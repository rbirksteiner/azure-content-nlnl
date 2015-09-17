<properties pageTitle="教學課程：Azure Active Directory 與 Work.com 整合 | Microsoft Azure" description="了解如何使用 Work.com 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Work.com 整合
  
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=529836)。
  
本教學課程的目的是要示範 Azure 與 Work.com 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Work.com 單一登入的訂用帳戶
  
完成本教學課程之後，或使用[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，指派了 Work.com 存取的 AAD 使用者就能夠在 Work.com 公司網站 (服務提供者起始登入) 的應用程式單一登入。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Work.com 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-work-com-tutorial/IC794105.png "案例")

##啟用 Work.com 的應用程式整合
  
本節的目的是概述如何啟用 Work.com 的應用程式整合。

###若要啟用 Work.com 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-work-com-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-work-com-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-work-com-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Work.com**。

    ![應用程式庫](./media/active-directory-saas-work-com-tutorial/IC794106.png "應用程式庫")

7.  在結果窗格中，選取 [Work.com]，然後按一下 [完成] 加入應用程式。

    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Work.com 中進行驗證。您必須將憑證上傳至 Work.com.com，這是程序的一部分。

>[AZURE.NOTE]若要設定單一登入，您還需要設定自訂的 Work.com 網域名稱。您至少需要定義一個網域名稱、測試網域名稱，並將它部署到整個組織。

###若要設定單一登入，請執行下列步驟：

1.  以系統管理員身分登入 Work.com 租用戶。

2.  移到 [設定]。

    ![設定](./media/active-directory-saas-work-com-tutorial/IC794108.png "設定")

3.  在 [系統管理員] 區段的左方導覽窗格中，按一下 [定義域管理] 展開相關的區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。

    ![我的網域](./media/active-directory-saas-work-com-tutorial/IC767825.png "我的網域")

4.  若要確認您的網域已經正確設定，請確定它有在 [步驟 4：部署至使用者] 中並檢閱您的 [我的網域設定]。

    ![已部署到使用者的網域](./media/active-directory-saas-work-com-tutorial/IC784377.png "已部署到使用者的網域")

5.  在不同的 Web 瀏覽器視窗中登入您的 Azure 入口網站。

6.  在 [Work.com] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794109.png "設定單一登入")

7.  在 [要如何讓使用者登入 Work.com] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794110.png "設定單一登入")

8.  在 [設定應用程式 URL] 頁面的 [Work.com 登入 URL] 文字方塊中，輸入使用者用來登入 Work.com 應用程式的 URL (例如：” **http://company.my.salesforce.com*”)，然後按 [下一步]：

    ![設定應用程式 URL](./media/active-directory-saas-work-com-tutorial/IC794111.png "設定應用程式 URL")

9.  在 [在 Work.com 設定單一登入] 頁面上，按一下 [下載憑證] 以下載您的憑證，然後在本機將憑證檔案儲存在您的電腦中。

    ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794112.png "設定單一登入")

10. 登入 Work.com 租用戶。

11. 移到 [設定]。

    ![設定](./media/active-directory-saas-work-com-tutorial/IC794108.png "設定")

12. 展開 [安全性控制] 功能表，然後再按一下 [單一登入設定]。

    ![單一登入設定](./media/active-directory-saas-work-com-tutorial/IC794113.png "單一登入設定")

13. 在 [單一登入設定] 對話方塊頁面執行下列步驟：

    ![啟用 SAML](./media/active-directory-saas-work-com-tutorial/IC781026.png "啟用 SAML")

    1.  選取 [啟用 SAML]。
    2.  按一下 [新增]。

14. 在 [SAML 單一登入設定] 區段中，執行下列步驟：

    ![SAML 單一登入設定](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML 單一登入設定")

    1.  在 [名稱] 文字方塊中，輸入您的組態名稱。  

        >[AZURE.NOTE]提供 [名稱] 的值，[API 名稱] 文字方塊就會自動填入內容。

    2.  在 Azure 入口網站中的 [設定在 Work.com 單一登入] 對話頁面上， 複製 [簽發者 URL] 值，然後將它貼至 [簽發者] 文字方塊中。
    3.  若要上傳已下載的憑證，請按一下 [瀏覽]。
    4.  在 [實體 ID] 文字方塊中，輸入 ****https://salesforce-work.com**。
5.  在 [SAML 識別類型] 中選取 [判斷提示包含來自使用者物件的同盟識別碼]。
    6.  在 [SAML 識別位置]，請選取 [識別位於 Subject 陳述式的 NameIdentifier 元素中]。
    7.  在 Azure 入口網站中的 [設定在 Work.com 單一登入] 對話頁面上， 複製 [遠端登入 URL] 值，然後將它貼至 [識別提供者登入 URL] 文字方塊中。
    8.  在 Azure 入口網站中的 [設定在 Work.com 單一登入] 對話頁面上， 複製 [遠端登出 URL] 值，然後將它貼至 [識別提供者登出 URL] 文字方塊中。
    9.  在 [服務提供者起始的要求繫結]，請選取 [HTTP Post]。
    10. 按一下 [儲存]。

15. 在 Work.com 入口網站的左側導覽窗格中，按一下 [定義域管理] 展開相關區段，然後按一下 [我的網域] 來開啟 [我的網域] 頁面。

    ![我的網域](./media/active-directory-saas-work-com-tutorial/IC794115.png "我的網域")

16. 在 [我的網域] 頁面的 [登入頁面商標] 區段中，按一下 [編輯]。

    ![登入頁面商標](./media/active-directory-saas-work-com-tutorial/IC767826.png "登入頁面商標")

17. [登入頁面商標] 頁面的 [驗證服務] 區段中，就會顯示您的 [SAML SSO 設定] 的名稱。請選取該名稱，然後按一下 [儲存]。

    ![登入頁面商標](./media/active-directory-saas-work-com-tutorial/IC784366.png "登入頁面商標")

18. 在 Azure AD 入口網站上，選取 [單一登入組態確認]，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-work-com-tutorial/IC794116.png "設定單一登入")

##設定使用者佈建
  
Azure Active Directory 使用者必須先佈建到 Work.com，才可以登入。Work.com 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 Work.com 公司網站。

2.  移到 [設定]。

    ![設定](./media/active-directory-saas-work-com-tutorial/IC794108.png "設定")

3.  移至 [管理使用者 > 使用者]。

    ![管理使用者](./media/active-directory-saas-work-com-tutorial/IC784369.png "管理使用者")

4.  按一下 [新使用者]。

    ![所有使用者](./media/active-directory-saas-work-com-tutorial/IC794117.png "所有使用者")

5.  在 [使用者編輯] 區段中，執行下列步驟：

    ![使用者編輯](./media/active-directory-saas-work-com-tutorial/IC794118.png "使用者編輯")

    1.  輸入要佈建到相關文字方塊的有效 Azure Active Directory 帳戶的 [姓氏]、[別名]、[電子郵件]、[使用者名稱] 和 [暱稱] 屬性。
    2.  選取 [角色]、[使用者授權] 和 [設定檔]。
    3.  按一下 [儲存]。  

        >[AZURE.NOTE]Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以便在帳戶啟用前加以確認。

>[AZURE.NOTE]您可以使用任何其他的 Work.com 使用者帳戶建立工具或 Work.com 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Work.com，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 Work.com 應用程式整合頁面中，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-work-com-tutorial/IC794119.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-work-com-tutorial/IC767830.png "是")
  
請等候 10 分鐘並確認帳戶已同步處理至 Work.com.com。
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->