<properties pageTitle="教學課程：Azure Active Directory 與 Replicon 整合 | Microsoft Azure" description="了解如何使用 Replicon 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Replicon 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=525008)。
  
本教學課程的目的是要示範 Azure 與 Replicon 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Replicon 租用戶
  
完成本教學課程之後，或是透過[存取面板簡介](https://msdn.microsoft.com/library/dn308586)，您指派給 Replicon 的 Azure AD 使用者就能夠單一登入您 Replicon 公司網站 (服務提供者起始登入) 的應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Replicon 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-replicon-tutorial/IC777798.png "案例")
##啟用 Replicon 的應用程式整合
  
本節的目的是要說明如何啟用 Replicon 的應用程式整合。

###若要啟用 Replicon 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-replicon-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-replicon-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-replicon-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **Replicon**。

    ![應用程式庫](./media/active-directory-saas-replicon-tutorial/IC777799.png "應用程式庫")

7.  在結果窗格中，選取 [Replicon]，然後按一下 [完成] 來新增應用程式。

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Replicon。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Replicon] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC777801.png "設定單一登入")

2.  在 [要如何讓使用者登入 Replicon] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC777802.png "設定單一登入")

3.  在 [設定應用程式 URL] 頁面上，執行下列步驟：

    ![設定應用程式 URL](./media/active-directory-saas-replicon-tutorial/IC777803.png "設定應用程式 URL")

    1.  在 [Replicon 登入 URL] 文字方塊中，輸入您的 Replicon 租用戶 URL (例如：**https://na2.replicon.com/company/saml2/sp-sso/post*)。
2.  在 [Replicon 回覆 URL] 文字方塊中，輸入您的 Replicon **AssertionConsumerService** URL (例如：**https://global.replicon.com/!/saml2/company/sso/post*)。

        >[AZURE.NOTE]您可以從以下位置的 Replicon 中繼資料取得 URL：**https://global.replicon.com/!/saml2/\<您公司的金鑰>**。

    3.  按 [**下一步**]

4.  在 [設定在 Replicon 單一登入] 頁面上，按一下 [下載中繼資料] 來下載您的中繼資料，然後將中繼資料儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC777804.png "設定單一登入")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Replicon 公司網站。

6.  若要設定 SAML 2.0，請執行下列步驟：

    ![啟用 SAML 驗證](./media/active-directory-saas-replicon-tutorial/IC777805.png "啟用 SAML 驗證")

    1.  若要顯示 [EnableSAML Authentication2] 對話方塊，請將以下內容附加至您 URL 的公司金鑰後面：**/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2** 以下顯示完整 URL 的結構描述：**https://na2.replicon.com/\<您公司的金鑰>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**。
    2.  按一下 [+] 來展開 [v20Configuration] 區段。
    3.  按一下 [+] 來展開 [metaDataConfiguration] 區段。
    4.  按一下 [選擇檔案]，選取您的識別提供者中繼資料 XML 檔案，然後按一下 [提交]。

7.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 來關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-replicon-tutorial/IC778418.png "設定單一登入")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Replicon，則必須將他們佈建至 Replicon。在 Replicon 的情況下，需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  在 Web 瀏覽器視窗中，以系統管理員身分登入您的 Replicon 公司網站。

2.  移至 [管理] > [使用者]。

    ![使用者](./media/active-directory-saas-replicon-tutorial/IC777806.png "使用者")

3.  按一下 [新增使用者]。

    ![新增使用者](./media/active-directory-saas-replicon-tutorial/IC777807.png "新增使用者")

4.  在 [使用者設定檔] 區段中，執行下列步驟：

    ![使用者設定檔](./media/active-directory-saas-replicon-tutorial/IC777808.png "使用者設定檔")

    1.  在 [登入名稱] 文字方塊中，輸入您要佈建之 Azure AD 使用者的 Azure AD 電子郵件地址。
    2.  針對 [驗證類型] 選取 [SSO]。
    3.  在 [部門] 文字方塊中，輸入使用者的部門。
    4.  針對 [員工類型] 選取 [系統管理員]。
    5.  按一下 [儲存使用者設定檔]。

>[AZURE.NOTE]您可以使用任何其他的 Replicon 使用者帳戶建立工具或 Replicon 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Replicon，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Replicon] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-replicon-tutorial/IC777809.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 來確認指派。

    ![是](./media/active-directory-saas-replicon-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->