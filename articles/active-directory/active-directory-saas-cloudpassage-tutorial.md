<properties
	pageTitle="教學課程：Azure Active Directory 與 CloudPassage 整合 | Microsoft Azure"
	description="了解如何設定 Azure Active Directory 與 CloudPassage 之間的單一登入。"
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
	ms.date="08/26/2015"
	ms.author="markvi"/>


# 教學課程：Azure Active Directory 與 CloudPassage 整合

本教學課程的目標是說明如何將 CloudPassage 與 Azure Active Directory (Azure AD) 整合。<br>將 CloudPassage 與 Azure AD 整合可為您提供下列好處：

- 您可以在 Azure AD 中控制可存取 CloudPassage 的人員 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 CloudPassage (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## 先決條件 

若要設定 Azure AD 與 CloudPassage 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 CloudPassage 單一登入的訂用帳戶


> [AZURE.NOTE]若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 測試環境，可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月免費試用的 Azure 試用帳戶。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。<br>本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 CloudPassage 
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 CloudPassage
若要設定 CloudPassage 與 Azure AD 整合，您需要從資源庫將 CloudPassage 新增到受管理的 SaaS App 清單。

### 若要從資源庫新增 CloudPassage，請執行下列步驟：

1. 在 **Azure 管理入口網站**的左側瀏覽窗格中，按一下 [Active Directory]。<br><br> ![Active Directory][1]

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，可在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br><br> ![[應用程式]][2]
4. 按一下頁面底部的 [加入]。<br><br> ![[應用程式]][3]
5. 在 [您想要執行什麼動作] 對話方塊中，按一下 [從資源庫新增應用程式]。<br><br> ![[應用程式]][4]
6. 在搜尋方塊中，輸入 **CloudPassage**。<br><br> ![[應用程式]][5]
7. 在結果窗格中，選取 [CloudPassage]，然後按一下 [完成] 以新增應用程式。<br><br> ![[應用程式]][6]



##  設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 CloudPassage 來設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 CloudPassage 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者和 CloudPassage 中的相關使用者之間建立連結關聯性。<br> 建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 CloudPassage 中 **Username** 的值。
 
若要使用 CloudPassage 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 CloudPassage 測試使用者](#creating-a-halogen-software-test-user)** - 使 CloudPassage 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證設定是否能運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站啟用 Azure AD 單一登入，並在您的 CloudPassage 應用程式中設定單一登入。<br> 您的 CloudPassage 應用程式需要特定格式的 SAML 判斷提示，其會要求您將自訂屬性對應新增到 SAML Token 屬性設定。以下螢幕擷取畫面顯示了上述範例。<br><br> ![設定單一登入][21]

**若要使用 CloudPassage 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站的 [CloudPassage] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。<br><br> ![設定單一登入][7]

2. 在 [您希望使用者如何登入 CloudPassage] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。<br><br> ![設定單一登入][8]

3. 在 [設定應用程式設定] 對話方塊頁面上，執行下列步驟：<br><br>![設定 App 設定][9]
 
     3\.1.在 [登入 URL] 文字方塊中，輸入您的使用者用來登入 CloudPassage App 的 URL (例如：**https://portal.cloudpassage.com/saml/init/accountid*)。

     3\.2.在 [回覆 URL] 文字方塊中，輸入您的 AssertionConsumerService URL (例如：**https://portal.cloudpassage.com/saml/consume/accountid*)。<br> 您也可以在 CloudPassage 入口網站的 [單一登入設定] 區段中按一下 [SSO 安裝文件]，為這個屬性設定值。<br><br>![設定單一登入][10]

     3\.3.按 [下一步]。



4. 在 [設定在 CloudPassage 單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案。<br><br>![設定單一登入][11]

5. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 CloudPassage 公司網站。

6. 在頂端功能表中，按一下 [設定]，然後按一下 [網站管理]。<br><br> ![設定單一登入][12]

7. 按一下 [驗證設定] 索引標籤。<br><br> ![設定單一登入][13]


8. 在 [單一登入設定] 區段中，執行下列步驟：<br><br> ![設定單一登入][14]


     8\.1.在 Azure 入口網站的 [設定在 CloudPassage 單一登入] 對話方塊頁面上，複製 [簽發者 URL] 值，然後將它貼到 [SAML 簽發者] 文字方塊中。

     8\.2.在 Azure 入口網站的 [設定在 CloudPassage 單一登入] 對話方塊頁面上，複製 [服務提供者 (SP) 啟始的端點] 值，然後將它貼到 [SAML 端點 URL] 文字方塊中。

     8\.3.在 Azure 入口網站的 [設定在 CloudPassage 單一登入] 對話方塊頁面上，複製 [登出 URL] 值，然後將它貼到 [登出登陸頁面] 文字方塊中。

     8\.4.從您下載的憑證中建立 **Base-64 ** 編碼的檔案。
          
      >[AZURE.TIP]如需詳細資訊，請參閱[如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

     8\.5.在記事本中開啟 Base-64 編碼的憑證、將其內容複製到剪貼簿，然後將它貼到 [X.509 憑證] 文字方塊中。

     8\.6.按一下 [儲存]。


9. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按 [下一步]。<br><br>![設定單一登入][15]


10. 在 [單一登入確認] 頁面上，按一下 [完成]。<br><br>![設定單一登入][16]



11. 在頂端功能表中，按一下 [屬性] 以開啟 [SAML Token 屬性] 對話方塊。<br><br> ![設定單一登入][17]

12. 若要加入必要的使用者屬性，可針對下表中的每一列執行下列步驟：<br>

| 屬性名稱 | 屬性值 |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| 電子郵件 | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. 在底部功能表上，按一下 [套用變更]。 <br><br> ![設定單一登入][20]



### 建立 Azure AD 測試使用者

本節目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。.<br><br> 在 [使用者] 清單中，選取 [Britta Simon]。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**若要在 Azure AD中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左側瀏覽窗格中，按一下 [Active Directory]。<br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. 從 [目錄] 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，可在頂端功能表中按一下 [使用者]。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. 若要開啟 [新增使用者] 對話方塊，可按一下底部工具列上的 [新增使用者]。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. 在 [告訴我們這位使用者] 對話方塊頁面上，執行下列步驟：<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
  2. 在 [使用者名稱] 文字方塊中，輸入 **Britta Simon**。
  3. 按 [下一步]。

6.  在 [使用者設定檔] 對話方塊頁面上，執行下列步驟：<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. 在 [名字] 文字方塊中，輸入 **Britta**。  
  2. 在 [姓氏] 文字方塊中，輸入 **Simon**。
  3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
  4. 在 [角色] 清單中選取 [使用者]。
  5. 按 [下一步]。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. 在 [取得暫時密碼] 對話方塊頁面上，執行下列步驟：<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. 請記下 [新密碼] 的值。
  2. 按一下 [完成]。   


  
 
### 建立 CloudPassage 測試使用者

本節目標是在 CloudPassage 中建立名為 Britta Simon 的使用者。

#### 若要在 CloudPassage 中建立名為 Britta Simon 的使用者，請執行以下步驟：

1.	以系統管理員身分登入您的 **CloudPassage** 公司網站。 

2.	在頂端工具列中，按一下 [設定]，然後按一下 [網站管理]。<br>![建立 CloudPassage 測試使用者][22]

3.	按一下 [使用者] 索引標籤，然後按一下 [新增使用者]。<br>![建立 CloudPassage 測試使用者][23]
	
4.	在 [新增使用者] 區段中，執行下列步驟：<br>![建立 CloudPassage 測試使用者][24]

     4\.1.在 [名字] 文字方塊中，輸入 Britta。

     4\.2.在 [姓氏] 文字方塊中，輸入 Simon。

     4\.3.在 [使用者名稱] 文字方塊、[電子郵件] 文字方塊和 [重新輸入電子郵件] 文字方塊中，輸入 Britta 在 Azure AD 中的使用者名稱。

     4\.4.針對 [存取類型]，選取 [啟用 Halo 入口網站存取]。

     4\.5.按一下 [加入]。










### 指派 Azure AD 測試使用者

本節目標是授與 Britta Simon 對 CloudPassage 的存取權，讓她能夠使用 Azure 單一登入。<br><br>![指派使用者][30]

**若要將 Britta Simon 指派到 CloudPassage，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]。<br> <br><br>![指派使用者][26]
2. 在應用程式清單中，選取 [CloudPassage]。<br><br>![指派使用者][27]
1. 在頂端功能表中，按一下 [使用者]。<br> <br><br>![指派使用者][25]
1. 在 [使用者] 清單中，選取 [Britta Simon]。

2. 在底部工具列中，按一下 [指派]。<br><br>![指派使用者][29]



### 測試單一登入

本節目標是使用存取面板來測試您的 Azure AD 單一登入設定。<br> 當您在存取面板中按一下 [CloudPassage] 磚時，應該會自動登入您的 CloudPassage 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=August15_HO9-->