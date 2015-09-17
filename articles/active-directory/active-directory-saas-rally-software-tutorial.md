<properties pageTitle="教學課程：Azure Active Directory 與 Rally Software 整合 | Microsoft Azure" description="了解如何使用 Rally Software 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Rally Software 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=521865)。
  
本教學課程的目的是要示範 Azure 與 Rally Software 的整合。本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Rally Software 租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Rally Software 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-rally-software-tutorial/IC769525.png "案例")
##啟用 Rally Software 的應用程式整合
  
本節的目的是要說明如何啟用 Rally Software 的應用程式整合。

###若要啟用 Rally Software 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-rally-software-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-rally-software-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-rally-software-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在**搜尋方塊**中，輸入 **rally**。

    ![應用程式庫](./media/active-directory-saas-rally-software-tutorial/IC769526.png "應用程式庫")

7.  在結果窗格中，選取 [Rally Software]，然後按一下 [完成] 來新增應用程式。

    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Rally Software。在此程序中，您必須將憑證上傳至 Rally Software。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Rally Software] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC749323.png "設定單一登入")

2.  在 [要如何讓使用者登入 Rally] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按一下 [下一步]。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD 單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Rally Software 租用戶 URL] 文字方塊中，使用下列模式輸入您的 URL："*https://\<租用戶名稱>.rally.com*"，然後按 [下一步]。

    ![設定應用程式 URL](./media/active-directory-saas-rally-software-tutorial/IC769529.png "設定應用程式 URL")

4.  在 [設定在 Rally 單一登入] 頁面上，按一下 [下載中繼資料]，然後將中繼資料儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC769530.png "設定單一登入")

5.  登入您的 **Rally Software** 租用戶。

6.  在頂端工具列中，按一下 [設定]，然後選取 [訂用帳戶]。

    ![訂閱](./media/active-directory-saas-rally-software-tutorial/IC769531.png "訂閱")

7.  按一下右上方工具列中的 [動作] 按鈕，然後選取 [編輯訂用帳戶]。

8.  在 [訂用帳戶] 對話方塊頁面上，執行下列步驟，然後按一下 [儲存並關閉]：

    ![驗證](./media/active-directory-saas-rally-software-tutorial/IC769542.png "驗證")

    1.  從 [驗證] 下拉式清單中選取 [Rally 或 SSO 驗證]。
    2.  在 Azure 入口網站中的 [設定在 Rally Software 單一登入] 對話方塊頁面上，複製 [識別提供者 ID] 值，然後將其貼至 [識別提供者 URL] 文字方塊中。
    3.  在 Azure 入口網站中的 [設定在 Rally Software 單一登入] 對話方塊頁面上，複製 [遠端登出 URL] 值。

9.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC769547.png "設定單一登入")
##設定使用者佈建
  
若要讓 AAD 使用者可以登入，則必須使用他們的 Azure Active Directory 使用者名稱將其佈建至 Rally Software 應用程式。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您的 Rally Software 租用戶。

2.  按一下 [設定] > [使用者]，然後按一下 [新增使用者]。

    ![使用者](./media/active-directory-saas-rally-software-tutorial/IC781039.png "使用者")

3.  在 [新使用者] 文字方塊中輸入名稱，然後按一下 [新增詳細資料]。

4.  在 [建立使用者] 區段中，執行下列步驟：

    ![建立使用者](./media/active-directory-saas-rally-software-tutorial/IC781040.png "建立使用者")

    1.  在 [使用者名稱] 文字方塊中，輸入您要佈建的 Azure AD 帳戶名稱。
    2.  在 [電子郵件地址] 文字方塊中，輸入您要佈建的 Azure AD 的電子郵件地址。
    3.  按一下 [儲存並關閉]。

>[AZURE.NOTE]您可以使用任何其他的 Rally Software 使用者帳戶建立工具或 Rally Software 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Rally Software，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Rally Software] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-rally-software-tutorial/IC769548.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 確認指派。

    ![是](./media/active-directory-saas-rally-software-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->