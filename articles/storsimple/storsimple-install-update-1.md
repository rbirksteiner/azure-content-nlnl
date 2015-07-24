<properties 
   pageTitle="在 StorSimple 裝置上安裝 Update 1"
   description="說明如何在您的裝置上安裝 StorSimple 8000 Series Update 1。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# 在 StorSimple 裝置上安裝 Update 1

## 概觀

本教學課程說明如何在執行 Update 1 之前軟體版本的 StorSimple 裝置上安裝 Update 1。您的裝置可以在正式發行版 (GA)、Update 0.1、Update 0.2 或 Update 0.3 軟體上執行。

安裝過程中，如果您的裝置執行的是 Update 1 之前的版本，則會在您的裝置上進行檢查。這些檢查會就硬體狀態和網路連線，判斷裝置健康狀況。

系統會提示您執行手動前置檢查以確定：

- 控制器固定 IP 能夠路由，且可以連線到網際網路。這些 IP 用於提供 StorSimple 裝置的更新。您可以在每個控制器上執行下列 Cmdlet 來進行測試：

    `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**當固定 IP 能連線到網際網路時測試連線的範例輸出**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    
	    


- 在更新裝置之前，建議您先建立裝置資料的雲端快照。

在驗證並認可的手動檢查 (如上所述) 之後，將會執行一組自動更新前檢查。其中包含：

- **控制器健康狀況檢查**：確認這兩個裝置控制器的狀況良好且在線上。

- **硬體元件健康狀況檢查**：確認您的 StorSimple 裝置上的所有硬體元件的狀況良好。

- **DATA 0 檢查**：確認您的裝置已啟用 DATA 0。如果未啟用此介面，您必須啟用它，然後重試。

- **DATA 2 和 DATA 3 檢查**：確認未啟用 DATA 2 和 DATA 3 網路介面。如果已啟用這些介面，將需要停用它們，然後嘗試更新您的裝置。只有在您要從執行 GA 軟體的裝置更新時，才需要執行這項檢查。執行 0.1、0.2 或 0.3 版的裝置將不需要這項檢查。

- **閘道器檢查**：在任何執行 Update 1 之前版本的裝置上進行。這項檢查只能在已為 DATA 0 以外的網路介面設定閘道器的裝置上執行。
 
只有在所有的更新前檢查都順利完成之後，才會套用 Update 1。在 StorSimple 裝置上套用 Update 1 之後，未來的更新將不會進行 Data 2 和 Data 3 檢查，以及閘道器檢查。但會進行其他前置檢查。

## 使用管理入口網站安裝 Update 1

我們建議您使用 Azure 管理入口網站更新執行 GA 版本的裝置。請執行下列步驟，以更新您的裝置。

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## 更新失敗的疑難排解

**如果您看到升級前檢查失敗的通知，該怎麼辦？**

如果前置檢查失敗，請確定您已經查看頁面底部的詳細通知列。這會提供有關失敗的前置檢查的指引。下圖顯示這類出現的通知當中的執行個體。在此情況下，控制器健康狀況檢查和硬體元件健康狀況檢查失敗。在 [硬體狀態] 區段下，您可以看到控制器 0 及控制器 1 元件需要注意。
 
  ![前置檢查失敗](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

您必須確定兩個控制器是狀況良好且在線上。您也必須確定在 StorSimple 裝置中的所有硬體元件在 [維護] 頁面上都會顯示為狀況良好。接著，您可以嘗試安裝更新。如果您無法修正硬體元件問題，您將需要連絡 Microsoft 支援以了解後續步驟。

**如果您收到「無法安裝更新」錯誤訊息，且建議是參考更新疑難排解指南，以判斷失敗的原因，該怎麼辦？**

其中一個可能的原因是您沒有連線到 Microsoft Update 伺服器。這是需要執行的手動檢查。如果您失去更新伺服器的連線，更新作業將會失敗。您可以從 StorSimple 裝置的 Windows PowerShell 介面執行下列 Cmdlet 以檢查連線：

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

在這兩個控制器上執行 Cmdlet。
 
如果您已經確認連線存在，而且您持續看到這個問題，請連絡 Microsoft 支援以了解後續步驟。

## 後續步驟

深入了解 [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=July15_HO2-->