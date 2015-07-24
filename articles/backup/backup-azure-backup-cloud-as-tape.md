<properties
   pageTitle="使用 Azure 備份來取代您的磁帶基礎結構"
   description="了解 Azure 備份如何提供可讓您在 Azure 中備份和還原資料的磁帶式語意"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="03/27/2015"
   ms.author="prvijay"/>

# 使用 Azure 備份來取代您的磁帶基礎結構

使用 Azure 備份和 System Center Data Protection Manager 的客戶可以進行下列操作：

+ 依照最適合自身組織需求的排程備份資料

+ 保留備份資料可達較長期間

+ 讓 Azure (而非磁帶) 成為長期保留需求的一部分。

此文章說明客戶如何啟用備份和保留原則。使用磁帶處理長期保留需求的客戶，透過此功能的可用性現在擁有功能強大且可行的替代方案。最新版的 Azure 備份已啟用此功能 (可從 [這裡](http://aka.ms/azurebackup_agent)取得)。SCDPM 客戶必須移至 UR5 才能使用這項功能。

## 什麼是備份排程？
備份排程表示備份作業的頻率 (或多久執行一次)，例如下方畫面中的設定表示在每日下午 6:00 以及午夜進行備份。<br/>

![每日排程][1]

客戶也可以排程每週備份，例如下方畫面中的設定表示在每個交替的星期日和星期三上午 9:30 和 1:00 進行備份。<br/>

![每週排程][2]

## 什麼是保留原則？
保留原則會指定備份必須儲存的期間。除了僅針對所有備份點指定「一般原則」之外，客戶可以指定在進行備份時根據不同的保留原則。例如，為了稽核目的，每一季結尾所進行的備份可能需要保留較長期間，而每日進行的備份 (可充當作業的復原點) 則需要保留 90 天。<br/>

![保留原則][3]

此原則中指定的「保留點」總數是 90 (每日保留點) + 40 (每季一點，連續 10 年) = 130。

## 範例 – 將兩者綜合比較
<br/> ![範例畫面][4]

1. **每日的保留原則**：每日所進行的備份會儲存 7 天。
2. **每週的保留原則**：每個星期六午夜和下午 6:00 所進行的備份會保留 4 週
3. **每月的保留原則**：每月最後一個星期六午夜和下午 6:00 所進行的備份會保留 12 個月
4. **每年的保留原則**：每年三月最後一個星期六午夜所進行的備份會保留 10 年

上圖中計算「保留點」總數 (客戶可從該保留點還原資料) 的方式如下：

+ 7 天中每天 2 個保留點 = 14 個復原點

+ 4 週中每週 2 個保留點 = 8 個復原點

+ 12 個月中每月 2 個保留點 = 24 個復原點

+ 10 年中每年 1 個保留點 = 10 個復原點

因此復原點的總數是 56。

## 進階組態

按一下上方畫面中的 [**修改**]，客戶在指定保留排程時擁有更進一步的彈性。<br/>

![修改][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png
 

<!---HONumber=62-->