<properties 
   pageTitle="管理 StorSimple 磁碟區容器"
   description="說明如何使用 StorSimple Manager 服務磁碟區容器頁面新增、修改或刪除磁碟區容器。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/13/2015"
   ms.author="v-sharos" />

# 管理 StorSimple 磁碟區容器

## 概觀

本教學課程說明如何使用 StorSimple Manager 服務建立和管理 StorSimple 磁碟區容器。

Microsoft Azure StorSimple 裝置中的磁碟區容器包含一個或多個可共用儲存體帳戶、加密和頻寬耗用量設定的磁碟區。一個裝置可以有多個磁碟區容器，供其所有磁碟區使用。

磁碟區容器具有下列屬性：

- **磁碟區** – 磁碟區容器內所包含的精簡佈建型 StorSimple 磁碟區。一個磁碟區容器可以包含最多 256 個精簡佈建型 StorSimple 磁碟區。

- **加密** – 可以為每個磁碟區容器定義的加密金鑰。此金鑰用於加密自 StorSimple 裝置傳送至雲端的資料。軍事級的 AES-256 位元金鑰是搭配使用者輸入的金鑰使用。為保護您的資料，建議您務必啟用雲端儲存體加密。

- **儲存體帳戶** – 連結至雲端儲存體服務提供者的儲存體帳戶。位於磁碟區容器中的所有磁碟區都會共用這個儲存體帳戶。當您建立磁碟區容器，然後指定該帳戶的存取認證時，可以從現有的清單中選擇一個儲存體帳戶，或建立新的帳戶。

- **雲端頻寬** – 當資料從裝置傳送至雲端時裝置所耗用的頻寬。當您定義此容器時，可以指定一個介於 1 與 1000 Mbps 之間的值，以強制執行頻寬控制。如果您希望裝置耗用所有可用的頻寬，將此欄位設定為 [無限制]。您也可以建立並套用頻寬範本，以便根據排程配置頻寬。

![磁碟區容器頁面](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

以下程序說明如何使用 StorSimple 的 [磁碟區容器] 頁面，完成下列常見的作業：

- 新增磁碟區容器 
- 修改磁碟區容器 
- 刪除磁碟區容器 

## 新增磁碟區容器

執行下列步驟來建立磁碟區容器。

[AZURE.INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]


## 修改磁碟區容器

執行下列步驟來修改磁碟區容器。

[AZURE.INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]


## 刪除磁碟區容器

磁碟區容器內具有磁碟區。只有在先刪除磁碟區容器內包含的所有磁碟區之後，才可以刪除該磁碟區容器。執行下列步驟來刪除磁碟區容器。

[AZURE.INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## 後續步驟

深入了解[管理 StorSimple 磁碟區](storsimple-manage-volumes.md)。
 

<!---HONumber=July15_HO2-->