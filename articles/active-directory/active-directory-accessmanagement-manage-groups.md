<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="Covers how to sign up for Azure and first steps you can try with Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


#在 Azure Active Directory 中管理安全性群組


##如何建立及管理安全性群組嗎

**從 Azure 管理入口網站建立群組**

1. 在管理入口網站中，按一下 [Active Directory]，然後按一下您組織目錄的名稱。
2. 按一下 [群組] 索引標籤。
3. 在 [群組] 頁面中，按一下 [新增群組]。
4. 在 [新增群組] 視窗中，指定群組的名稱和描述。
5. 您可以使用 Office 365 帳戶入口網站、Windows Intune 帳戶入口網站或 Azure 管理入口網站完成這項工作 (視您的組織所訂閱的服務而定)。如需有關使用入口網站管理 Azure Active Directory 的詳細資訊，請參閱「管理 Azure AD 目錄」。

## 如何指派或移除安全性群組中的使用者

**將成員從 Azure 管理入口網站新增到群組**

1. 在管理入口網站中，按一下 [Active Directory]，然後按一下您組織目錄的名稱。
2. 按一下 [群組] 索引標籤。
3. 在 [群組] 頁面上，按一下您想要在其中新增成員的群組的名稱。根據預設，這會顯示所選群組的 [成員] 索引標籤。
4. 在該群組的頁面上，按一下 [新增成員]。
5. 在 [新增成員] 頁面上，按一下您想要新增為此群組成員的使用者或群組的名稱，並確定這個名稱新增至 [已選取] 窗格中。


**從 Azure 管理入口網站移除群組中的成員**

1. 在管理入口網站中，按一下 [Active Directory]，然後按一下您組織目錄的名稱。
2. 按一下 [群組] 索引標籤。
3. 在 [群組] 頁面上，按一下您想要從其中移除成員的群組的名稱。
4. 在該群組的頁面上，按一下 [成員] 索引標籤。
5. 在該群組的頁面上，按一下您想要從這個群組移除的成員的名稱，然後按一下 [移除]。
6. 按一下 [是] 做為動作確認問題的回答，以確認您想要從群組中移除此成員。


##如何使用規則動態管理安全性群組的成員
**若要啟用特定群組的動態成員資格，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [群組] 索引標籤底下，選取您想要編輯的群組，然後在此群組的 [設定] 索引標籤中，將 [啟用動態成員資格] 參數設定為 [是]。
2. 您現在可以為將控制此群組的動態成員資格如何運作的群組，設定一個簡單的單一規則。請確定已核取 [新增使用者位置] 選項按鈕，然後從下拉式功能表 (例如，department、jobTitle 等) 中選取一個使用者屬性。 
3. 接著，選取一個條件 (Not Equals、Equals、Not Starts With、Starts With、Not Contains、Contains、Not Match，Match)，最後指定所選使用者屬性的值。
4. 例如，如果某個群組指派給 SaaS 應用程式 (如需詳細資訊，請參閱「在 Azure AD 中將群組存取權指派給 SaaS 應用程式」)，而且您透過設定一個規則，藉以新增設定為 Equals(-eq)Sales Rep 的 jobTitle 的使用者，以啟用此群組的動態成員資格，則在您的 Azure AD 目錄中，其職稱設定為 Sales Rep 的所有使用者將可以存取此 SaaS 應用程式。

以下是將提供一些 Azure Active Directory 的其他相關資訊的部分主題

* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->