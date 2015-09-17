<properties 
   pageTitle="Microsoft Azure AD Connect - 從 Windows Azure AD 同步作業工具 (DirSync) 升級"
	description="了解如何從 DirSync 升級至 Azure AD Connect。本文說明如何將目前 Windows Azure AD 同步作業工具 (DirSync) 升級至 Azure AD Connect 的步驟。"
	services="active-directory"
	documentationCenter=""
	authors="shoatman"
	manager="terrylanfear"
	editor="billmath"/>

<tags 
   ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="shoatman"/>

# 將 Windows Azure Active Directory 同步作業 (DirSync) 升級至 Azure Active Directory Connect

下列文件將協助您將現有的 DirSync 安裝升級至 Azure AD Connect

## 下載 Azure AD Connect

若要開始使用 Azure AD Connect，您可以使用下列連結下載最新版本：[下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)


## 安裝 Azure AD Connect 之前
安裝 Azure AD Connect 並從 DirSync 升級之前，這裡是您需要的一些事項。

- Azure AD 執行個體之現有全域管理員帳戶的密碼 (安裝將會提醒您使用哪一個帳戶)
- 本機 Active Directory 的企業系統管理員帳戶
- 選用：如果您已設定 DirSync 使用完整版的 SQL Server - 該資料庫執行個體的資訊。

### 平行部署

如果您目前正在同步處理超過 5 萬個物件，則可以選擇執行平行部署。平行部署需要不同的伺服器或一組伺服器 (如果您需要不同的伺服器做為 SQL Server)。平行部署的好處是避免同步處理停機的機會。Azure AD Connect 安裝將嘗試估計我們預期的停機，但是，如果您過去升級過 DirSync，則您自己的經驗可能就是最佳指南。

## 安裝 Azure AD Connect

下載 Azure AD Connect 並將其複製至現有 DirSync 伺服器。

1. 瀏覽並按兩下 AzureADConnect.msi
2. 開始逐步執行精靈

在就地升級中，會發生下列高階步驟：

1. 歡迎使用 Azure AD Connect
2. 目前 DirSync 組態的分析
3. 收集 Azure AD 全域管理員密碼
4. 收集企業系統管理員帳戶的認證 (僅在 Azure AD Connect 安裝期間使用)
5. AAD Connect 的安裝
    * 解除安裝 DirSync
	* 安裝 AAD Connect
	* 選擇性地開始同步處理

發生下列情況時，需要其他步驟/資訊：

* 您目前正在使用完整 SQL Server - 本機或遠端
* 您擁有超過 5 萬個物件的規模可進行同步處理

## 就地升級 - 少於 5 萬個物件 - SQL Express (逐步解說)

0. 啟動 Azure AD Connect Installer (MSI)

1. 檢閱並同意授權條款和隱私權注意事項。

![歡迎使用 Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. 按 [下一步] 分析現有的 DirSync 安裝

![分析現有目錄同步作業安裝](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. 分析完成時，我們將提出如何繼續的建議。在使用 SQL Express 且少於 5 萬個物件的情況下，會顯示下列畫面。

![分析已完成，準備好從 DirSync 升級](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. 提供您目前用來連線到 Azure AD 之帳戶的密碼。

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. 提供 Active Directory 的企業系統管理員帳戶。

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. 您現在已經可以進行設定。按 [下一步] 時，將會解除安裝 DirSync，而 Azure AD Connect 將會設定並開始同步處理。  

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## 就地升級 - 超過 5 萬個物件
如果您擁有超過 5 萬個物件的規模可進行同步處理，則會在步驟 #3 中看到不同的訊息。將會顯示與下面類似的畫面：

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

在此情況下，建議您考慮在不同的伺服器上進行平行升級。這項建議的原因是，根據您組織的大小，在有關本機 Active Directory 中的變更有多快反映在 Azure AD/Office 365 中的部分，就地升級可能會影響您企業的服務等級協定。我們嘗試估計使用 Azure AD Connect 進行第一次同步處理時所需的時間。如上所述，您自己的 DirSync 原始安裝或升級至 DirSync 的經驗可能是最佳的指標。

平行部署需要一部或多部不同的伺服器 (如果您需要從 Azure AD Connect 在不同的伺服器上執行 SQL Server)。因此，如果就地升級可以排定成避免組織內的影響，則可以完全合理地考慮進行就地升級。

若要繼續進行就地升級，請按一下訊息旁的核取方塊：[繼續在此電腦上升級 DirSync]。

## 就地升級 - 完整 SQL Server

如果 DirSync 安裝使用本機或遠端完整版的 SQL Server，則會在步驟 #3 中看到不同的訊息。將會顯示與下面類似的畫面：

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

系統會向您顯示有關 DirSync 所使用之現有 SQL Server 資料庫伺服器的資訊。如果需要，請進行適當的調整。按 [下一步] 將會繼續安裝。

## 平行部署 - 超過 5 萬個物件

在步驟 #3，如果您擁有超過 5 萬個物件，則 Azure AD Connect 安裝會建議進行平行部署。如需選擇對 Azure AD Connect 進行就地還是平行部署的詳細資訊，請參閱上面的＜就地升級 - 超過 5 萬個物件＞。將會顯示與下面類似的畫面：

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

如果您想要繼續進行平行部署，則需要執行下列步驟：

- 按一下 [匯出設定] 按鈕。在不同的伺服器上安裝 Azure AD Connect 時，會匯入這些設定，以將目前 DirSync 中的任何設定移轉到新的 AAD Connect 安裝。

順利匯出設定之後，即可結束 DirSync 伺服器上的 Azure AD Connect 精靈。

### 不同伺服器上的 Azure AD Connect 安裝

在新的伺服器上安裝 Azure AD Connect 時，會找不到 DirSync，並假設您想要執行 Azure AD Connect 的全新安裝。這裡提供數個特殊步驟：

1. 執行 Azure AD Connect Installer (MSI)
2. 當您看到 [歡迎使用 Azure AD Connect] 畫面時。按一下視窗右上角的 "X"，以結束精靈。
3. 開啟命令提示字元
4. 從 Azure AD Connect 的安裝位置 (預設值：C:\\Program Files\\Microsoft Azure Active Directory Connect)，執行下列命令：
    * AzureADConnect.exe /migrate

Azure AD Connect 會連線並向您呈現下列 UI：

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. 選取從 DirSync 安裝所匯出的設定檔案。
6. 設定任何進階選項，包括：
    * Azure AD Connect 的自訂安裝位置
	* 現有 SQL Server 執行個體 (預設值：Azure AD Connect 會安裝 SQL Server 2012 Express)
	* 用來連線到 SQL Server 的服務帳戶 (如果您的 SQL Server 資料庫位於遠端，則此帳戶必須是網域服務帳戶)

請看到下列 UI 中的這些選項：

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. 按 [下一步]。 
8. 在 [準備好設定] 頁面上，保持核取 [設定一完成，即開始同步處理程序]。[AZURE.NOTE]將會開始 Windows Server Active Directory 和 Azure Active Directory 之間的同步處理作業，但沒有變更會匯出到 Azure AD。一次只能有一個作用中的同步處理工具匯出變更。
9. 按一下 [安裝]。

[AZURE.NOTE]取消核取開始同步處理核取方塊，確保 DirSync (其仍安裝並執行中) 和 Azure AD Connect 未嘗試同時寫入到 AAD。

### 確認 Azure AD Connect 已準備好開始同步處理

若要判斷 Azure AD Connect 是否準備好接收自 DirSync，您需要開啟 Azure AD Connect Synchronization Service Manager。在 Windows [開始] 功能表使用「同步處理」進行搜尋，將會顯示此應用程式。

在應用程式內，您需要檢視 [作業] 索引標籤。在此索引標籤上，您想要確認已完成下列作業：

- AD 管理代理程式上的匯入
- Azure AD 管理代理程式上的匯入
- AD 管理代理程式上的完整同步處理
- Azure AD 管理代理程式上的完整同步處理

這 4 項作業完成之後，即準備好解除安裝 DirSync，並啟用 Azure AD Connect 同步處理。

### 解除安裝 DirSync (舊伺服器)

- 從 [新增或移除程式] 中，尋找 [Windows Azure Active Directory 同步作業工具]
- 解除安裝 [Windows Azure Active Directory 同步作業工具]

### 開啟 Azure AD Connect (新伺服器)
安裝之後，重新開啟 Azure AD Connect 時會提供您的組態體驗。開啟 Azure AD Connect。

您應該會看見下列內容：

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* 選取 [設定預備模式]
    * 使用匯出的設定自動從 DirSync 升級時，會讓 Azure AD Connect 進入預備模式。預備模式基本上表示將在 Azure AD Connect 內進行同步處理，但是變更將不會匯出至 Azure AD 或 AD。
* 取消核取 [啟用預備模式] 核取方塊，即會關閉預備。

![輸入您的 Azure AD 認證](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* 按一下 [安裝] 按鈕

恭喜，您已使用平行部署順利移轉至 Azure AD Connect。

## Azure AD Connect 支援元件

下列是每個必要條件和支援元件的清單，而 Azure AD Connect 將這些必要條件和支援元件安裝於在其上設定 Azure AD Connect 的伺服器。此清單適用於基本快速安裝。如果您選擇在 [安裝同步處理服務] 頁面上使用不同的 SQL Server，則不會安裝下面所列的 SQL Server 2012 元件。

- Forefront Identity Manager Azure Active Directory Connector
- Microsoft SQL Server 2012 命令列公用程式
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Azure Active Directory Module for Windows PowerShell
- Microsoft Online Services Sign-In Assistant for IT Professionals
- Microsoft Visual C++ 2013 Redistribution Package


**其他資源**

* [在雲端中使用內部部署身分識別基礎結構](active-directory-aadconnect.md)
* [Azure AD Connect 運作方式](active-directory-aadconnect-how-it-works.md)
* [使用 Azure AD Connect 的下一步](active-directory-aadconnect-whats-next.md)
* [深入了解](active-directory-aadconnect-learn-more.md)
* [MSDN 上的 Azure AD Connect](active-directory-aadconnect.md)
 

<!---HONumber=August15_HO9-->