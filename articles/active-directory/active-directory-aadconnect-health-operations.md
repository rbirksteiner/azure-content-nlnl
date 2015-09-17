<properties 
	pageTitle="Azure AD Connect Health 操作。"
	description="這是 Azure AD Connect Health 頁面，說明可以在您部署 Azure AD Connect Health 之後執行的其他操作。"
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
	ms.date="08/14/2015"
	ms.author="billmath"/>

# Azure AD Connect Health 操作

下列主題說明可以使用 Azure AD Connect Health 執行的各種操作。

## 啟用電子郵件通知
您可以設定 Azure AD Connect Health 服務在產生表示您的同盟服務基礎結構狀況不良的警示時傳送電子郵件通知。產生警示時，以及當該警示標示為已解決時，會發生這個情況。請依照下列指示，設定電子郵件通知。請注意，電子郵件通知預設為停用狀態。


### 啟用 Azure AD Connect Health 電子郵件通知

1. 為您希望接收電子郵件通知的伺服陣列開啟 [警示] 刀鋒視窗。
2. 按一下動作列中的 [通知設定] 按鈕。
3. 將 [電子郵件通知] 開換切換到 [開啟]。
4. 選取設定所有全域租用戶系統管理員接收電子郵件通知的核取方塊。
5. 如果您希望從其他任何電子郵件地址收到電子郵件通知，您可以在 [其他電子郵件收件者] 方塊中指定這些電子郵件地址。若要從此清單中移除某個電子郵件地址，以滑鼠右鍵按一下該項目，然後選取 [刪除]。
6. 若要完成變更，按一下 [儲存]。只有在您選取 [儲存] 之後，所有變更才會生效。






## 從 Azure AD Connect Health 服務刪除伺服器

在某些情況下，您可能希望某個伺服器不受到監視。請依照下列指示，從 Azure AD Connect Health 服務移除伺服器。

刪除伺服器時，請注意下列事項：

- 這個動作將會「停止」從該伺服器收集任何後續的資料。此伺服器將會從監視服務移除。進行此動作之後，您將無法檢視此伺服器的新警示、監視或使用情況分析資料。
- 這個動作將「不會」從伺服器解除安裝或移除 Health 代理程式。如果您在執行此步驟之前還未解除安裝 Health 代理程式，您可能會在伺服器上看到與 Health 代理程式相關的錯誤事件。
- 這個動作將「不會」刪除已從此伺服器收集的資料。根據 Microsoft Azure 資料保留原則，將會刪除該資料。 
- 執行此動作之後，如果您希望再次開始監視相同的伺服器，您必須先解除安裝此伺服器上的 Health 代理程式，然後再重新安裝。 


### 從 Azure AD Connect Health 服務刪除伺服器

1. 選取要移除的伺服器名稱，以便從 [伺服器清單] 刀鋒視窗開啟 [伺服器] 刀鋒視窗。 
2. 在 [伺服器] 刀鋒視窗上，按一下動作列中的 [刪除] 按鈕。
3. 在確認方塊中輸入伺服器名稱，以確認刪除伺服器的動作。
4. 按一下 [刪除] 按鈕。







## 從 Azure AD Connect Health 服務刪除服務執行個體

在某些情況下，您可能希望移除某個服務執行個體。請依照下列指示，從 Azure AD Connect Health 服務移除服務執行個體。

刪除服務執行個體時，請注意下列事項：

- 這個動作將會從監視服務移除目前的服務執行個體。 
- 這個動作將「不會」從當做此服務執行個體一部分受到監視的任何伺服器解除安裝或移除 Health 代理程式。如果您在執行此步驟之前還未解除安裝 Health 代理程式，您可能會在伺服器上看到與 Health 代理程式相關的錯誤事件。 
- 根據 Microsoft Azure 資料保留原則，將會從此伺服器執行個體刪除所有資料。 
- 執行此動作之後，如果您希望開始監視服務，請先解除安裝所有伺服器上將受到監視的 Health 代理程式，然後再重新安裝。執行此動作之後，如果您希望再次開始監視相同的伺服器，您必須先解除安裝此伺服器上的 Health 代理程式，然後再重新安裝。


### 從 Azure AD Connect Health 服務刪除服務執行個體

1. 選取您想要移除的服務識別項 (伺服陣列名稱)，以便從 [服務清單] 刀鋒視窗開啟 [服務] 刀鋒視窗。 
2. 在 [伺服器] 刀鋒視窗上，按一下動作列中的 [刪除] 按鈕。
3. 在確認方塊中輸入服務名稱以進行確認 (例如：sts.contoso.com)。 
4. 按一下 [刪除] 按鈕。

## 啟用 AD FS 的稽核

若要讓使用情況分析功能收集並分析資料，Azure AD Connect Health 代理程式需要 AD FS 稽核記錄檔中的資訊。預設不會啟用這些記錄檔。這只適用於 AD FS 同盟伺服器。您不需要在 AD FS Proxy 伺服器或 Web 應用程式 Proxy 伺服器上啟用稽核。使用下列程序啟用 AD FS 稽核，並找出 AD FS 稽核記錄檔。

#### 啟用 AD FS 2.0 的稽核

1. 按一下 [開始]，依序指向 [程式集] 和 [系統管理工具]，然後按一下 [本機安全性原則]。
2. 瀏覽至 **Security Settings\\Local Policies\\User Rights Management** 資料夾，然後按兩下 [產生安全性稽核]。
3. 在 [本機安全性設定] 索引標籤上，確認已列出 AD FS 2.0 服務帳戶。如果不存在，按一下 [新增使用者或群組]，並將其新增至清單中，然後按一下 [確定]。
4. 使用提高的權限開啟命令提示字元，然後執行下列命令以啟用稽核。<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. 關閉 [本機安全性原則]，然後開啟 [管理嵌入式管理單元]。若要開啟 [管理嵌入式管理單元]，按一下 [開始]，依序指向 [程式集] 和 [系統管理工具]，然後按一下 [AD FS 2.0 管理]。
6. 在 [動作] 窗格中，按一下 [編輯同盟服務屬性]。
7. 在 [同盟服務屬性] 對話方塊中，按一下 [事件] 索引標籤。
8. 選取 [成功稽核] 和 [失敗稽核] 核取方塊。
9. 按一下 [確定]。

#### 在 Windows Server 2012 R2 上啟用 AD FS 的稽核

1. 在 [開始] 畫面上開啟 [伺服器管理員]，或在桌面上的工作列中開啟 [伺服器管理員]，以開啟 [本機安全性原則]，然後按一下 [工具/本機安全性原則]。
2. 瀏覽至 **Security Settings\\Local Policies\\User Rights Assignment** 資料夾，然後按兩下 [產生安全性稽核]。
3. 在 [本機安全性設定] 索引標籤上，確認已列出 AD FS 服務帳戶。如果不存在，按一下 [新增使用者或群組]，並將其新增至清單中，然後按一下 [確定]。
4. 使用提高的權限開啟命令提示字元，然後執行下列命令以啟用稽核：<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. 關閉 [本機安全性原則]，然後開啟 [AD FS 管理] 嵌入式管理單元 (在 [伺服器管理員] 中，按一下 [工具]，然後選取 [AD FS 管理])。
6. 在 [動作] 窗格中，按一下 [編輯同盟服務屬性]。
7. 在 [同盟服務屬性] 對話方塊中，按一下 [事件] 索引標籤。
8. 選取 [成功稽核] 和 [失敗稽核] 核取方塊，然後按一下 [確定]。






#### 找出 AD FS 稽核記錄檔


1. 開啟 [事件檢視器]。
2. 移至 [Windows 記錄]，然後選取 [安全性]。
3. 按一下右側的 [篩選目前的記錄檔]。
4. 在 [事件來源] 下，選取 [AD FS 稽核]。

![AD FS 稽核記錄](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]如果您有即將停用 AD FS 稽核的群組原則，則 Azure AD Connect Health 代理程式將無法收集資訊。請確定您沒有可能即將停用稽核的群組原則。


## 相關連結

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [適用於 AD FS 的 Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install-adfs.md)
* [在 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->