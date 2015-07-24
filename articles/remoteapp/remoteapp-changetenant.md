
<properties
    pageTitle="變更 Azure RemoteApp 中 Azure Active Directory 租用戶"
    description="了解如何變更與 Azure RemoteApp 相關聯的 Azure Active Directory 租用戶"
    services="remoteapp"
    solutions="" documentationCenter="" 
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2015"
    ms.author="elizapo" />



# 變更 Azure RemoteApp 中 Azure Active Directory 租用戶

Azure RemoteApp 使用 Azure Active Directory (Azure AD) 以允許使用者存取權。您只能使用與 Azure 訂閱相關聯的 Azure AD 租用戶。您可以在入口網站的 [設定] 頁面上檢視相關聯的訂閱。查看 [訂閱] 索引標籤上的 [目錄] 資料行。

> [AZURE.NOTE]若要讓 Azure Active Directory 的變更成功，您應該先從所有 Azure RemoteApp 集合移除現有 Azure Active Directory 租用戶中的所有使用者。若要這樣做，請移至 Azure 入口網站，並移至 [Azure RemoteApp] 索引標籤，然後開啟每個 Azure RemoteApp 集合。移至 [使用者] 索引標籤，並移除屬於您目前 Azure Active Directory 租用戶的使用者。針對所有現有 Azure RemoteApp 集合重複進行。不這麼做，您將無法建立或修正集合。

如果您想要使用不同的租用戶，請使用下列步驟來變更訂閱的關聯：

1. 在入口網站中，移除您獲授與 Azure RemoteApp 集合存取權的任何 Azure AD 使用者。


2. 設定 Microsoft 帳戶 (先前稱為 Live ID) 做為服務系統管理員。(請查看 [設定 -> 系統管理員]。)
	1. 在右上角按一下目前登入的使用者，然後按一下 [檢視我的帳單]。
	2. 選取您的訂用帳戶，然後按一下 [編輯訂用帳戶詳細資料]。
	3. 進行必要的變更。



3. 登出入口網站，然後使用您在上一個步驟指定的 Microsoft 帳戶重新登入。


4. 按一下 [新增 -> 應用程式服務 -> Active Directory -> 自訂建立 -> 使用現有的目錄]。新增您想要與此訂閱關聯的 Azure AD 租用戶。


5. 在 [設定 -> 訂用帳戶] 下，選取您的訂用帳戶，然後按一下 [編輯目錄]。選取您想要使用的 Azure AD 租用戶。



您現在可以使用新的 Azure AD 租用戶來控制 Azure 訂用帳戶的存取權，以及在 Azure RemoteApp 中設定使用者存取權。

<!---HONumber=July15_HO1-->