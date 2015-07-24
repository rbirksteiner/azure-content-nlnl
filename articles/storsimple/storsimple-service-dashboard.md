<properties 
   pageTitle="使用 StorSimple Manager 服務儀表板 | Microsoft Azure"
   description="描述服務儀表板，並說明如何使用它來監視您的 StorSimple 解決方案的健全狀況。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/25/2015"
   ms.author="v-sharos" />

# 使用 StorSimple Manager 服務儀表板

## 概觀

StorSimple Manager 服務儀表板頁面提供連線至 StorSimple Manager 服務的所有裝置的摘要檢視，並強調顯示需要系統管理員注意的裝置。本教學課程介紹儀表板頁面、說明儀表板內容和功能，並說明您可以從這個頁面執行的工作。

![服務儀表板](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

**圖 1：StorSimple Manager 服務儀表板**

StorSimple Manager 服務儀表板會顯示下列資訊：

- 在**圖表**區域中，您可以查看裝置的相關度量圖表。您可以檢視所有裝置上使用的主要儲存體，以及經過一段時間裝置已耗用的雲端儲存體。使用圖表右上角的控制項，指定 1 週、1 個月、3 個月或 1 年的時間範圍。

- [**使用量概觀**] 相對於所有裝置可用的總儲存體，顯示所有裝置已佈建和使用的主要儲存體。[**已佈建**] 是指已準備和配置來使用的儲存體數量，而 [**已使用**] 是指向連接至裝置的啟動器所可看的磁碟區用量。

- [**警示**] 區域提供所有裝置上的所有作用中警示的快照集，依警示嚴重性分組。按一下嚴重性層級會開啟 [**警示**] 頁面，只顯示那些警示。在 [**警示**] 頁面上，您可以按一下個別警示來檢視該警示的其他詳細資料，包括任何建議的動作。如果已解決問題，您也可以清除警示。

- [**作業**] 區域提供連線到服務的所有裝置上的最近工作的快照集。您可以利用連結查看目前正在進行的作業、過去 24 小時失敗的作業，或排定在接下來 24 小時內執行的作業。

- [**快速概覽**] 區域提供有用的資訊，例如服務狀態、連接到服務的裝置數目、服務的位置，以及與服務相關聯的訂用帳戶的詳細資料。另外還有作業記錄檔的連結。按一下連結即可查看所有已完成的 StorSimple Manager 服務作業的清單。

您可以使用 StorSimple Manager 服務儀表板頁面來起始下列工作：

- 檢視或重新產生服務註冊金鑰。
- 變更服務資料加密金鑰。
- 檢視作業記錄檔。

## 檢視或重新產生服務註冊金鑰

服務註冊金鑰用於向 StorSimple Manager 服務註冊 Microsoft Azure StorSimple 裝置，之後裝置就會出現在 Microsoft Azure 管理入口網站中，讓您採取進一步的管理動作。金鑰是在第一個裝置上建立，然後與其餘裝置共用。

按一下 [**註冊金鑰**] (在頁面底部) 會開啟 [**服務註冊金鑰**] 對話方塊，您可以在此處將目前的服務註冊金鑰複製到剪貼簿，或重新產生服務註冊金鑰。

重新產生金鑰並不會影響先前註冊的裝置：只會影響重新產生金鑰之後，才向服務註冊的裝置。

如需有關檢視和產生服務註冊金鑰的詳細資訊，請移至[取得服務註冊金鑰](storsimple-manage-service.md#get-the-service-registration-key)

## 變更服務資料加密金鑰

服務資料加密金鑰用來加密從 StorSimple Manager 服務傳送至 StorSimple 裝置的機密客戶資料，例如儲存體帳戶認證。如果您的 IT 組織在存放裝置上有金鑰輪替原則，則您必須定期變更這些金鑰。根據 StorSimple Manager 服務是管理單一裝置或多個裝置而定，金鑰變更程序可能稍有不同。

變更服務資料加密金鑰分成 3 個步驟：

1. 使用管理入口網站，授權裝置來變更服務資料加密金鑰。
2. 使用 Windows PowerShell for StorSimple，起始服務資料加密金鑰變更。
3. 如果您有一個以上的 StorSimple 裝置，請在其他裝置上更新服務資料加密金鑰。

下列步驟將說明服務資料加密金鑰的變換程序。

[AZURE.INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]


## 檢視作業記錄檔

您可以在儀表板的 [**快速概覽**] 窗格中按一下可用的作業記錄檔連結，以檢視作業記錄檔。這樣會移至管理服務頁面，讓您篩選並查看 StorSimple Manager 服務特定的記錄檔。

## 後續步驟

了解如何[進行 StorSimple 裝置的疑難排解](storsimple-troubleshoot-operational-device.md)。

<!---HONumber=July15_HO1-->