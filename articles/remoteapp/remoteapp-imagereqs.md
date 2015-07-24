
<properties 
    pageTitle="RemoteApp 映像需求"
    description="深入了解建立可用於 RemoteApp 的映像需求" 
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
    ms.date="05/28/2015" 
    ms.author="elizapo" />



# RemoteApp 映像的需求
RemoteApp 會使用 Windows Server 2012 R2 映像來主控您要與使用者共用的所有程式。若要建立自訂 RemoteApp 映像，您可以從現有的映像建立，或[建立新映像](remoteapp-create-custom-image.md)。

> [AZURE.TIP]是否知道 Azure RemoteApp 訂用帳戶可讓您存取 Azure VM 資源庫中可用來建立專屬範本映像的建置前 Windows Server 2012 R2 映像？ [立即使用](remoteapp-image-on-azurevm.md)。


可上傳用於 Azure RemoteApp 的映像有下列需求：


- 自訂應用程式不會在映像上本機儲存資料。這些都是無狀態的映像，而且應該只包含應用程式。
- 映像不包含可能會遺失的資料。
- 映像大小應為 MB 的倍數。如果您嘗試上傳的映像大小不是正確的倍數，上傳作業會失敗。
- 映像大小必須為 127 GB 或更小。 
- 必須在 VHD 檔案上 (VHDX 檔案目前不受支援)。
- VHD 不能是第 2 代虛擬機器。
- VHD 可以固定大小或動態擴充。建議採用動態擴充 VHD 的做法，因為這會比固定大小 VHD 檔案更快速地上傳至 Azure。
- 磁碟必須使用主開機記錄 (MBR) 分割樣式進行初始化。GUID 磁碟分割資料表 (GPT) 磁碟分割樣式不受支援。 
- VHD 必須包含單一 Windows Server 2012 R2 安裝。它可包含多個磁碟區，但只有其中一個包含 Windows 安裝。 
- 必須安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能。
- *請勿*安裝「遠端桌面連線代理人」角色。
- 必須停用「加密檔案系統 (EFS)」。
- 映像必須使用參數 **/oobe /generalize /shutdown** 進行 SYSPREP 處理 (請不要使用 **/mode:vm** 參數)。
- 不支援從快照鏈結上傳您 VHD。
 

<!---HONumber=July15_HO1-->