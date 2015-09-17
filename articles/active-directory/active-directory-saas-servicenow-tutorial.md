<properties pageTitle="教學課程：Azure Active Directory 與 ServiceNow 整合 | Microsoft Azure" description="了解如何使用 ServiceNow 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 ServiceNow 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=521880)。
  
本教學課程的目的是要示範 Azure 與 ServiceNow 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   ServiceNow 中的租用戶
  
完成本教學課程之後，您已指派至 ServiceNow 的 Azure AD 使用者將能夠在 ServiceNow 公司網站 (服務提供者起始登入)，或使用[存取面板簡介](active-directory-saas-access-panel-introduction.md)執行單一登入來登入應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ServiceNow 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-servicenow-tutorial/IC769496.png "案例")
##啟用 ServiceNow 的應用程式整合
  
本節的目的是概述如何啟用 ServiceNow 的應用程式整合。

###若要啟用 ServiceNow 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-servicenow-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-servicenow-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在 [搜尋] 對話方塊中，輸入 **ServiceNow**。

    ![應用程式庫](./media/active-directory-saas-servicenow-tutorial/IC701016.png "應用程式庫")

7.  在結果窗格中，選取 [ServiceNow]，然後按一下 [完成] 來加入應用程式。

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 ServiceNow 中進行驗證。

在這個程序中，您需要上傳 Base-64 編碼憑證到您的 Dropbox for Business 租用戶。如果您不熟悉這個程序，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [ServiceNow] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "設定單一登入")

2.  在 [您希望使用者如何登入 ServiceNow] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面的 [ServiceNow 登入 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://<InstanceName>.servicenow.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "設定應用程式 URL")

4.  在 [在 ServiceNow 設定單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "設定單一登入")

5.  在您的 ServiceNow 租用戶中，於左側導覽列上按一下 [屬性] 以開啟 [SAML 2.0 單一登入屬性] 頁面。


6. 在 [SAML 2.0 單一登入屬性] 頁面上，執行下列步驟：

     6\.1.針對 [啟用外部驗證]，選取 [是]。


     6\.2.在 [將發出具有使用者資訊之 SAML2 安全性權杖的身分識別提供者 URL] 文字方塊中，輸入 **https://sts.windows.net/<您的租用戶的 GUID>/**。


     6\.3.在 [身分識別提供者的 AuthnRequest 服務的基礎 URL] 文字方塊中，輸入 **https://login.windows.net/<您的租用戶的 GUID>/saml2**。


     6\.4.在 [身分識別提供者的 SingleLogoutRequest 服務的基礎 URL] 文字方塊中，輸入 **https://login.windows.net/<您的租用戶的 GUID>/saml2**。


     6\.5.在 [身分識別提供者的 SingleLogoutRequest 服務的通訊協定繫結] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**。

     6\.6.針對 [簽署 LogoutRequest]，選取 [是]。

     6\.7.在 [當 SAML 2.0 單一登入因為工作階段未經驗證而失敗，或這是第一次登入，則重新導向至此 URL] 文字方塊中，輸入 **https://login.windows.net/<您的租用戶的 GUID>/saml2**。

  

7. 在 [服務提供者 (Service-Now) 屬性] 區段中，執行下列步驟：

     7\.1.在 [Service-now 執行個體首頁的 URL] 文字方塊中，輸入您的 ServiceNow 執行個體首頁的 URL。ServiceNow 執行個體首頁的 URL 是您的 **ServiceNow 租用戶 URL** 和 **/navpage.do** 的串連：**https://<InstanceName>.service-now.com/navpage.do** <br><br> ![Service-now 執行個體首頁](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Service-now 執行個體首頁")


     7\.2.在 [實體識別碼或簽發者] 文字方塊中，輸入您的租用戶 URL。

     7\.3.在 [接受 SAML2 權杖的對象 URI] 文字方塊中，輸入您的租用戶 URL。

     7\.4.在 [要與 SAMLResponse 中主體的 NameID 元素比對的使用者表格欄位] 文字方塊中，輸入 **email**。

     7\.5.在 [要用於傳回 SAMLResponse 中主體的 NameID 的 NameID 原則] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**。

     7\.6 將 [在 AuthnRequest 陳述式中建立 AuthnContextClass 要求] 保留未選取。

     7\.7 在 [將在我們向身分識別提供者的 SAML 2.0 AuthnRequest 中包含的 AuthnContextClassRef 方法] 文字方塊中，輸入 ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**。



8. 在 [進階設定] 區段中，執行下列步驟：

     8\.1.在 ["notBefore" 限制之前或 "notOnOrAfter" 限制之後仍視為有效的秒數] 文字方塊中，輸入 **60**。


9. 若要儲存組態時，按一下 [儲存]。

10. 在左側導覽列上按一下 [憑證] 以開啟 [憑證] 頁面。



11. 若要上傳您的憑證，請在 [憑證] 頁面上執行下列步驟：

     11\.1.按一下 [新增]。

     11\.2.在 [名稱] 文字方塊中，輸入 **SAML 2.0**。

     11\.3.選取 [使用中]。

     11\.4.針對 [格式]，選取 [PEM]。

     11\.5.從您下載的憑證建立 Base-64 編碼檔案。  > [AZURE.NOTE]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

     11\.6.在**記事本**中，開啟您的 Base-64 編碼檔案，然後將這個檔案的內容複製到剪貼簿。

     11\.7.將剪貼簿的內容貼到 [PEM 憑證] 文字方塊中。

     11\.8.按一下 [提交]。



12. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。<br><br> ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749326.png "設定單一登入")




##設定使用者佈建
  
本節的目的是要說明如何對 ServiceNow 啟用 Active Directory 使用者帳戶的使用者佈建。


### 若要設定使用者佈建，請執行下列步驟：

1. 在 Azure 管理入口網站中的 **ServiceNow** 應用程式整合頁面上，按一下 [設定使用者佈建]。<br><br> ![使用者佈建](./media/active-directory-saas-servicenow-tutorial/IC769498.png "使用者佈建")


2. 在 [輸入您的 ServiceNow 沙箱認證來啟用自動使用者佈建] 頁面上，提供以下組態設定：設定使用者佈建

     2\.1.在 [ServiceNow 執行個體名稱] 文字方塊中，輸入 ServiceNow 執行個體名稱。

     2\.2.在 [ServiceNow 管理使用者名稱] 文字方塊中，輸入 ServiceNow 管理員帳戶的名稱。

     2\.3.在 [ServiceNow 管理員密碼] 文字方塊中，輸入這個帳戶的密碼。

     2\.4.按一下 [驗證] 來驗證您的組態。

     2\.5.按 [下一步] 按鈕以開啟 [後續步驟] 頁面。

     2\.6.如果您想要將所有使用者佈建到此應用程式，請選取 [自動將此目錄中的所有使用者帳戶佈建到此應用程式]。<br><br> ![後續步驟](./media/active-directory-saas-servicenow-tutorial/IC698804.png "後續步驟")

     2\.7.在 [後續步驟] 頁面上，按一下 [完成] 來儲存您的組態。











##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 ServiceNow，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [ServiceNow] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-servicenow-tutorial/IC769499.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-servicenow-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!----HONumber=August15_HO8-->