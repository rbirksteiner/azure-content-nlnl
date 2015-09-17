<properties pageTitle="教學課程：Azure Active Directory 與 Syncplicity 整合 | Microsoft Azure" description="了解如何使用 Syncplicity 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#教學課程：Azure Active Directory 與 Syncplicity 整合
>[AZURE.TIP]如需意見反應，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=522417)。
  
本教學課程的目的是要示範 Azure Active Directory (AAD) 與 Syncplicity 的整合。
  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Syncplicity 租用戶
  
完成本教學課程之後，您指派給 Syncplicity 存取的 AAD 使用者就能夠單一登入您 Syncplicity 公司網站 (服務提供者起始登入) 的應用程式，或是使用 AAD 存取面板進行單一登入。

1.  啟用 Syncplicity 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "案例")

##啟用 Syncplicity 的應用程式整合
  
本節的目的是要說明如何啟用 Syncplicity 的應用程式整合。

###若要啟用 Syncplicity 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]。

    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。

    ![[應用程式]](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "[應用程式]")

4.  按一下頁面底部的 [新增]。

    ![新增應用程式](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "新增應用程式")

5.  在 [欲執行動作] 對話方塊中，按一下 [從組件庫新增應用程式]。

    ![從組件庫新增應用程式](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "從組件庫新增應用程式")

6.  在搜尋方塊中，輸入 **Syncplicity**。

    ![Syncplicity 應用程式庫](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity 應用程式庫")

7.  在結果窗格中，選取 [Syncplicity]，然後按一下 [完成] 以新增應用程式。

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##設定單一登入
  
本節說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure Active Directory 帳戶驗證至 Syncplicity。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站的 [Syncplicity] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "設定單一登入")

2.  在 [您希望使用者如何登入 Syncplicity] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD 單一登入")

3.  在 [設定應用程式 URL] 頁面的 [Syncplicity 登入 URL] 文字方塊中，輸入使用者登入您 Syncplicity 應用程式所使用的 URL，然後按 [下一步]。

    此應用程式 URL 是您 Syncplicity 租用戶的 URL (例如：**http://company.Syncplicity.com*)：

    ![設定應用程式 URL](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "設定應用程式 URL")

4.  於 [在 Syncplicity 設定單一登入] 頁面上，按 [下載憑證] 以下載您的憑證，然後在本機電腦上儲存憑證檔案。

    ![設定單一登入](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "設定單一登入")

5.  登入您的 **Syncplicity** 租用戶。

6.  在上方功能表中按一下 [管理]，選取 [設定]，然後按一下 [自訂網域和單一登入]。

    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  在 [單一登入 (SSO)] 對話方塊頁面執行下列步驟：

    ![單一登入 (SSO)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "單一登入 (SSO)")

    1.  在 [自訂網域] 文字方塊中輸入您的網域名稱。
    2.  在 [單一登入狀態] 中選取 [啟用]。
    3.  在 Microsoft Azure 入口網站的 [在 Syncplicity 設定單一登入] 頁面上，複製**實體識別碼**值，然後將它貼至 [實體識別碼] 文字方塊中。
    4.  在 Microsoft Azure 入口網站的 [在 Syncplicity 設定單一登入] 頁面上，複製**單一登入服務 URL** 值，然後將它貼至 [登入頁面 URL] 文字方塊中。
    5.  在 Microsoft Azure 入口網站的 [在 Syncplicity 設定單一登入] 頁面上，複製**遠端登出 URL**值，然後將它貼至 [登出頁面 URL] 文字方塊中。
    6.  在 [識別提供者憑證] 按一下 [選擇檔案]，然後上傳您已從 Microsoft Azure 入口網站下載的憑證。
    7.  按一下 [儲存變更]。

8.  在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。

    ![確認](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "確認")

##設定使用者佈建
  
AAD 使用者必須先佈建到 Syncplicity 應用程式，才可以登入。本節描述如何建立 Syncplicity 內的 AAD 使用者帳戶。

###若要佈建使用者帳戶到 Syncplicity，請執行下列步驟：

1.  登入您的 **Syncplicity** 租用戶 (例如：**https://company.Syncplicity.com*))。

2.  按一下 [管理員]，然後選取 [使用者帳戶]。

3.  按一下 [加入使用者]。

    ![管理使用者](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "管理使用者")

4.  輸入您想要佈建 AAD 帳戶的 [電子郵件地址]，選取 [使用者] 做為 [角色]，然後按 [下一步]。

    ![帳戶資訊](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "帳戶資訊")

    >[AZURE.NOTE]AAD 帳戶的持有者會收到一封包含連結的電子郵件，以進行確認並啟用帳戶。

5.  選取新使用者應該要加入做為成員的公司內群組，然後再按 [下一步]。

    ![群組成員資格](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "群組成員資格")

    >[AZURE.NOTE]如果沒有列出群組，就只需要按 [下一步] 即可。

6.  選取您想要放置在使用者電腦上受 Syncplicity 控制的資料夾，然後按 [下一步]。

    ![Syncplicity 資料夾](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity 資料夾")

>[AZURE.NOTE]您可以使用任何其他的 Syncplicity 使用者帳戶建立工具或 Syncplicity 提供的 API 來佈建 AAD 使用者帳戶。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Syncplicity，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 [Syncplicity] 應用程式整合頁面上，按一下 [指派使用者]。

    ![指派使用者](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "指派使用者")

3.  選取測試使用者，按一下 [指派]，然後按一下 [是] 以確認指派。

    ![是](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "是")
  
如果要測試您的單一登入設定，請開啟存取面板。如需存取面板的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

<!---HONumber=August15_HO7-->