
<properties 
    pageTitle="Azure RemoteApp 有哪些新功能？"
    description="深入了解對 Azure RemoteApp 所做的變更和改進" 
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
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# RemoteApp 有哪些新功能？

RemoteApp 的優點之一是我們不斷地努力改進 RemoteApp。每當 RemoteApp 有新功能時 ，我們便會在這裡發表變更。


## 2015 年 6 月

變更十分多！ 小組在6 月極為忙碌：

- 重新設計 Azure RemoteApp [登陸頁面](https://www.remoteapp.windowsazure.com/) - 立即使用！ 
- 更新可為您訂用帳戶一部分之所有映像中的軟體。
- 改善混合式集合，包括強制通道支援，以及先檢查 IP 子網路大小，再嘗試建立集合。
- 發現 * 萬用字元不適用於網路攝影機。而是，您需要指定執行個體識別碼或 GUID。我們將更新重新導向資訊以反映該資訊。
- 這樣做，以從 Azure 資源庫建立範本映像時，將自訂防毒軟體新增到您的映像。

我們已在 7 月累積其他變更，因此很快就會提供另一個更新。

## 2015 年 5 月

從第一次建立本主題之後，已新增許多資訊，因此清單很大而且是從 3 月開始累積到 5 月。請了解這些新功能：

- 自動化所有項目 - Azure RemoteApp 現在具有 [Azure PowerShell 模組中的 Cmdlet](remoteapp-tutorial-arawithpowershell.md)。 
- [從 Azure 虛擬機器建立 Azure RemoteApp 映像](remoteapp-image-on-azurevm.md)。將自訂映像上傳至 Azure 的速度更為快速。
- 使用 Azure VNET (非 RemoteApp VNET)，以將公司網路資源連線到 Azure。我們已更新[混合式集合指示](remoteapp-create-hybrid-deployment.md)來逐步引導您建立 Azure VNET (它是步驟 1)。
- 對於 VNET，請參閱有關 VNET 大小限制和限制的[新指引](remoteapp-vnetsizing.md)。
- 而且對於限制，就是什麼是[服務限制和預設值](remoteapp-servicelimits.md)？

想要深入了解 Azure RemoteApp 嗎？ RemoteApp 小組在數週之前已致力於 Ignite。請參閱 Eric 的影片：[Microsoft Azure RemoteApp 管理基礎](http://channel9.msdn.com/Events/Ignite/2015/BRK3868) (英文)。

需要查看 Azure RemoteApp 在真實世界的樣子嗎？ 請參閱[在任何位置於任何裝置上執行任何應用程式](remoteapp-anyapp.md)教學課程 - 示範如何與您的使用者共用 Access，包括共用資料庫檔案。我們也有有關 [Office 365](remoteapp-tutorial-o365anywhere.md) 在任何裝置執行相同作業的教學課程。

感謝您持續關注我們 - 下個月還會有其他更新。

<!---HONumber=July15_HO1-->