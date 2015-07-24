<properties
    pageTitle="Azure RemoteApp 最佳做法"
    description="設定和使用 Azure RemoteApp 的最佳做法。"
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

# 設定和使用 Azure RemoteApp 的最佳做法

下列資訊可協助您有效率地設定和使用 Azure RemoteApp。

## 連線能力


- 請始終使用最新的用戶端版本。使用舊版的用戶端可能會造成連線問題和其他降級的體驗。為您的裝置啟用自動應用程式更新將可確保安裝的永遠是最新的用戶端。
- 請始終使用最穩定和最可靠的網際網路連線。  
- 請使用支援的 Proxy 連線以獲得最佳連線效能。不支援 SOCKS Proxy。

## [應用程式]


- 當您結束應用程式後，請儲存並關閉 RemoteApp 應用程式。未關閉應用程式可能會導致資料遺失。
- 在 Azure RemoteApp 中使用自訂應用程式之前，請先進行驗證。這包括確認它們可在多重工作階段的平台上運作，而且不會使用不必要的資源，例如可能使同一收藏中的其他使用者缺乏的記憶體和 CPU。如需詳細資訊，請下載並檢閱[遠端桌面服務的應用程式相容性最佳做法](http://www.microsoft.com/download/details.aspx?id=18704)。

## 設定和管理


- 隨時更新您的範本映像，視需要安裝軟體更新和其他重要的修正程式。這可確保 Azure RemoteApp 會自動調整大小以符合您的容量，而且會修補每個執行個體。  
- 請確認您有安全和可靠的 Active Directory Federation Services (AD FS) 部署。否則，用戶端驗證可能會失敗，導致使用者無法存取 Azure RemoteApp。
- 使用安裝的應用程式、角色或功能設定範本映像，使它們成為無狀態。它們不應依賴持續性狀態之 RemoteApp 服務中的任何虛擬機器執行個體。
	- 將所有使用者資料儲存在使用者設定檔或服務的其他外部儲存體位置，例如內部部署的檔案共用或 OneDrive。
	- 將共用資料儲存在服務的外部儲存體位置，例如內部部署的檔案共用或 OneDrive。
	- 在範本映像中設定任何全系統的設定，而不是在服務的個別虛擬機器上設定。
	- 停用發佈之應用程式的自動軟體更新 - 改為手動套用至範本映像，並從範本部署之前測試它們。
 

<!---HONumber=62-->