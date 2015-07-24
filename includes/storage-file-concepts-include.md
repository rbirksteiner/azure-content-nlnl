## 什麼是 Azure 檔案儲存體？

檔案儲存體為使用標準 SMB 2.1 通訊協定的應用程式提供共用儲存體。Microsoft Azure 虛擬機器和雲端服務可以透過掛接的共用，在應用程式元件之間共用檔案資料，而內部部署應用程式可以透過檔案儲存體 API，存取共用中的檔案資料。

在 Azure 虛擬機器或雲端服務中執行的應用程式，可掛接檔案儲存體共用來存取檔案資枓，就像桌面應用程式掛接一般 SMB 共用一樣。可同時掛接和存取檔案儲存體共用的 Azure 虛擬機器或角色數量沒有限制。

因為檔案儲存體是標準 SMB 2.1 檔案共用，所以 Azure 中執行的應用程式可透過檔案 I/O API 來存取共用中的資料。因此，開發人員可利用現有的程式碼和技能來移轉現有的應用程式。IT 專業人員在管理 Azure 應用程式時，可以使用 PowerShell Cmdlet 來建立、掛接和管理檔案儲存體共用。本指南將說明這兩者的範例。

檔案儲存體的一般用途包括：

- 移轉必須在 Azure 虛擬機器或雲端服務上執行檔案共用的內部部署應用程式，而無需支付昂貴的重新撰寫程式費用。
- 儲存共用應用程式設定 (例如，在組態檔中)
- 在共用位置儲存診斷資料 (例如記錄、度量和損毀傾印) 
- 儲存開發或管理 Azure 虛擬機器或雲端服務所需的工具和公用程式

## 檔案儲存體概念

檔案儲存體包含下列元件：

![files-concepts][files-concepts]

-   **儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。如需關於儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)＞(英文)。

-   **共用：**檔案儲存體共用是指在 Azure 中共用的 SMB 2.1 檔案。所有的目錄和檔案必須在上層共用中建立。帳戶可包含無限制數目的共用，而共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。

-   **目錄：**選擇性的目錄階層。

-	**檔案：**共用中的檔案。檔案的大小可高達 1 TB。

-   **URL 格式：**可利用下列 URL 格式定址檔案：https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`
    
    下列範例 URL 可用來定址上圖的其中一個檔案：`http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

如需有關如何為共用、目錄和檔案命名的詳細資料，請參閱[命名和參考共用、目錄、檔案及中繼資料](http://msdn.microsoft.com/library/azure/dn167011.aspx)。

[files-concepts]: ./media/storage-file-concepts-include/files-concepts.png

<!---HONumber=July15_HO2-->