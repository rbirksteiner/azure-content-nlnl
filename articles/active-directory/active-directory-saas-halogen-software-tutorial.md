<properties
	pageTitle="教學課程：Azure Active Directory 與 Halogen Software 整合"
	description="了解如何設定 Azure Active Directory 與 Halogen Software 之間的單一登入。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/30/2015"
	ms.author="markusvi"/>


# 教學課程：Azure Active Directory 與 Halogen Software 整合

本教學課程的目標是說明如何將 Halogen Software 與 Azure Active Directory (Azure AD) 整合。<br>將 Halogen Software 與 Azure AD 整合可提供您下列好處：

- 您可以在 Azure AD 中控制可存取 Halogen Software 的人員。 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Halogen Software (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定 Azure AD 與 Halogen Software 整合，您需要以下項目：

- Azure AD 訂用帳戶
- 啟用 Halogen Software 單一登入的訂用帳戶


> [AZURE.NOTE]若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br> 本教學課程中說明的案例由三個主要建置組塊組成：

1. 從資源庫加入 Halogen Software 
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 Halogen Software
若要設定 Halogen Software 與 Azure AD 整合，您需要從資源庫將 Halogen Software 加入 受管理 SaaS app 的清單。

**若要從資源庫新增 Halogen Software，請執行下列步驟：**

1. 在「Azure 管理入口網站」的左瀏覽窗格中，按一下 [Active Directory]。<br><br> ![Active Directory][1]

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![[應用程式]][2]
4. 按一下頁面底部的 [新增]。<br><br> ![[應用程式]][3]
5. 在 [您想要執行什麼動作] 對話方塊中，按一下 [從資源庫新增應用程式]。<br><br> ![[應用程式]][4]
6. 在 [搜尋] 方塊中，輸入 **halogen software**。<br> ![[應用程式]][5]
7. 在結果窗格中，選取 **Halogen Software**，然後按一下 [完成] 以新增應用程式。<br>



##  設定並測試 Azure AD 單一登入
本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 Halogen Software 來設定及測試 Azure AD 單一登入。

單一登入若要運作，Azure AD 必須知道 Halogen Software 與 Azure AD 中互相對應的使用者。換句話說，Azure AD 使用者和 Halogen Software 中的相關使用者之間必須建立連結關聯性。<br> 建立此連結關聯性的方法是將 Azure AD 中的 **user name** 的值指定為 Halogen Software 中 **Username** 的值。
 
若要使用 Halogen Software 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 Halogen Software 測試使用者](#creating-a-halogen-software-test-user)** - 使 Halogen Software 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站啟用 Azure AD 單一登入，並在您的 Halogen Software 應用程式中設定單一登入。<br>

**若要使用 Halogen Software 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站的 **Halogen Software** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入]對話方塊。<br><br>![設定單一登入][8]

2. 在 [您希望使用者如何登入 Halogen Software] 頁面上，選取 [Azure AD 單一登入]，然後按一下 [下一步]。<br><br> ![Azure AD 單一登入][9]

3. 在 [設定 App 設定] 對話方塊頁面執行下列步驟：<br><br>![設定 App 設定][10]
 
     3\.1 在 [登入 URL] 文字方塊中，使用以下模式輸入使用者登入您的 Halogen Software 應用程式時所使用的 URL：**https://global.hgncloud.com/fabrikam/welcome.jsp*

     3\.2.按 [下一步]。
 
4. 在 [設定在 Halogen Software 單一登入] 頁面上，按一下 [下載中繼資料]，然後將中繼資料檔儲存在您的本機電腦中。<br><br>![何謂 Azure AD Connect][11]

5. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 **Halogen Software**。
6. 按一下 [選項] 索引標籤。<br><br>![何謂 Azure AD Connect][12]
7. 在左瀏覽窗格中，按一下 [SAML 組態]。<br><br>![何謂 Azure AD Connect][13]
8. 在 [SAML 組態] 頁面上，執行下列步驟：<br><br>![何謂 Azure AD Connect][14]

     8\.1.在 [唯一識別碼] 中，選取 [NameID]。

     8\.2.在 [唯一識別碼對應到] 中，選取 [Username]。

     8\.3.若要更新您已下載的中繼資料檔，請按一下 [瀏覽] 來選取檔案，然後按一下 [上傳檔案]。

     8\.4.若要測試組態，請按一下 [執行測試]。>[AZURE.NOTE]您需要等候「SAML 測試已完成。請關閉此視窗。」訊息顯示。然後關閉已開啟的瀏覽器視窗。<br>完成測試之後才會啟用 [啟用 SAML] 核取方塊。

     8\.5.選取 [啟用 SAML]。
    
     8\.6.按一下 [儲存變更]。


9. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按一下 [完成] 以關閉 [設定單一登入] 對話方塊。<br><br>![何謂 Azure AD Connect][15]
10. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![何謂 Azure AD Connect][16]




### 建立 Azure AD 測試使用者
本節的目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

**若要在 Azure AD中建立測試使用者，請執行下列步驟：**

1. 在「Azure 管理入口網站」的左瀏覽窗格中，按一下 [Active Directory]。<br><br>![何謂 Azure AD Connect][100] 
2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下功能表頂端的 [使用者]。<br><br>![何謂 Azure AD Connect][101] 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。<br><br>![何謂 Azure AD Connect][102] 
5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行以下步驟：<br><br>![何謂 Azure AD Connect][103] 
  1. 在 [使用者類型] 中，選取 [貴組織的新使用者]。
  2. 在 [使用者名稱] 文字方塊中輸入 **Britta Simon**。
  3. 按 [下一步]。
6.  在 [使用者設定檔]對話方塊頁面上，執行下列步驟：<br><br>![何謂 Azure AD Connect][104] 
  1. 在 [名字] 文字方塊中輸入 **Britta**。  
  2. 在 [姓氏] 文字方塊中輸入 **Simon**。
  3. 在 [顯示名稱] 文字方塊中輸入 **Britta Simon**。
  4. 在 [角色] 清單中選取 [使用者]。
  5. 按 [下一步]。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br><br>![何謂 Azure AD Connect][105]  
8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br><br>![何謂 Azure AD Connect][106]   
  1. 寫下 [新密碼] 的值。
  2. 按一下 [完成]。   
  
 
### 建立 Halogen Software 測試使用者

本節目標是在 Halogen Software 中建立名為 Britta Simon 的使用者。

**若要建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 以系統管理員身分登入您的 **Halogen Software**。
2. 按一下 [使用者中心] 索引標籤，然後按一下 [建立使用者]。<br><br>![何謂 Azure AD Connect][300]  
3. 在 [新增使用者] 對話頁面上，執行以下步驟：<br><br>![何謂 Azure AD Connect][301]
  1. 在 [名字] 文字方塊中輸入 **Britta**。 
  2. 在 [姓氏] 文字方塊中輸入 **Simon**。
  3. 在 [使用者名稱] 文字方塊中輸入 **Brita Simon 在 Azure AD 入口網站的使用者名稱**。
  4. 在 [密碼] 文字方塊中輸入 Britta 的密碼。
  5. 按一下 [儲存]。


### 指派 Azure AD 測試使用者

本節目標是授與 Britta Simon 對 Halogen Software 的存取權，讓她能夠使用 Azure 單一登入。<br><br>![何謂 Azure AD Connect][200]

**若要指派 Britta Simon 到 Halogen Software，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br> <br><br>![何謂 Azure AD Connect][201]
2. 在應用程式清單中，選取 [Halogen Software]。<br><br>![何謂 Azure AD Connect][202]
1. 在頂端的功能表中，按一下 [使用者]。<br> <br><br>![何謂 Azure AD Connect][203]
1. 在 [使用者] 清單中，選取 [Britta Simon]。<br><br>![何謂 Azure AD Connect][204]
2. 在底部的工具列中，按一下 [指派]。<br><br>![何謂 Azure AD Connect][205]



### 測試單一登入

本節目標是使用存取面板測試您的 Azure AD 單一登入組態。<br> 當您在 [存取面板] 中按一下 [Halogen Software] 磚時，您應該會自動登入您的 Halogen Software 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=August15_HO6-->