<properties 
	pageTitle="管理 Azure AD Connect"
	description="了解如何針對 Azure AD Connect 擴充預設組態和作業工作。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# 管理 Azure AD Connect 



以下這些進階的選擇性主題可讓您自訂 Azure Active Directory Connect，以符合您組織的需要和需求。

## 指派授權給 Azure AD Premium 和 Enterprise Mobility 使用者

您的使用者現在已同步到雲端，您將需要指派授權給他們，才能繼續使用雲端應用程式 (例如 Office 365)。

### 指派 Azure AD Premium 或 Enterprise Mobility Suite 授權
--------------------------------------------------------------------------------
1. 以系統管理員身分登入 Azure 入口網站。
2. 選取左邊的 [Active Directory]。
3. 在 [Active Directory] 頁面上，在有您要啟用的使用者之目錄上按兩下。
4. 在目錄頁面頂端，選取 [授權]。
5. 在 [授權] 頁面上，選取 [Active Directory Premium] 或 [Enterprise Mobility Suite]，然後按一下 [指派]。
6. 在對話方塊中，選取您要對其指派授權的使用者，然後按一下核取記號圖示，以儲存變更。


## 確認已排程的同步處理工作
如果想要查看同步處理的狀態，您可以在 Azure 入口網站查看。

### 確認已排程的同步處理工作
--------------------------------------------------------------------------------

1. 以系統管理員身分登入 Azure 入口網站。
2. 選取左邊的 [Active Directory]。
3. 在 [Active Directory] 頁面上，在有您要啟用的使用者之目錄上按兩下。
4. 在 [目錄] 頁面的頂端，選取 [目錄整合]。
5. 請注意與本機 Active Directory 整合下方的上次同步處理時間。

<center>![雲端](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## 啟動已排程的同步處理工作
如果您需要執行同步處理工作，可以再次執行「Azure AD Connect 精靈」。您需要提供 Azure AD 認證。在精靈中，選取 [自訂同步處理選項] 工作，並依精靈指示繼續按 [下一步]。在最後步驟中，確定已核取 [設定一完成，即開始同步處理程序] 方塊。

<center>![雲端](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




## Azure AD Connect 中可用的其他工作
在初始 Azure AD Connect 的安裝之後，您隨時可以從 Azure AD Connect 開始頁面或桌面捷徑啟動精靈。您會在再次執行精靈的過程中發現，一些新的選項以「其他工作」的形式提供。

下表提供這些工作的摘要和個別的簡短描述。

![加入規則](./media/active-directory-aadconnect-whats-next/addtasks.png)


其他工作 | 說明 
------------- | ------------- |
檢視所選取的案例 |可讓您檢視目前的 Azure AD Connect 解決方案。這包括一般設定、同步處理的目錄、同步處理設定等等。
自訂同步處理選項 | 可讓您變更目前的組態，包括新增其他 Active Directory 樹系到組態或啟用同步處理選項，例如使用者、群組、裝置或密碼回寫。
啟用預備模式 | 這可讓您預備稍後將要同步處理的資訊，但不會將任何項目匯出到 Azure AD 或 Active Directory。這可讓您在同步處理發生之前先預覽。


 






## 其他文件
如需使用 Azure AD Connect 的其他文件請參閱下列文件：

- [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
- [變更 Azure AD Connect 預設組態](active-directory-aadconnect-whats-next-change-default-config.md)
- [使用 Azure AD Connect 同步處理規則編輯器](active-directory-aadconnect-whats-next-synch-rules-editor.md)
- [使用宣告式佈建](active-directory-aadconnect-whats-next-declarative-prov.md)

此外，針對 Azure AD Sync 所建立的一些文件仍然有關，而且適用於 Azure AD Connect。雖然致力於將本文件帶到 Azure.com，但是這份文件有些部分還是保留在 MSDN 範圍文件庫中。如需其他文件，請參閱 [MSDN 上的 Azure AD Connect](active-directory-aadconnect.md) 和 [MSDN 上的 Azure AD Sync](https://msdn.microsoft.com/library/azure/dn790204.aspx)。


 

<!---HONumber=August15_HO9-->