<properties 
	pageTitle="在內部部署 VMWare 站台之間部署" 
	description="Azure Site Recovery 中的 InMage Scout 可處理內部部署 VMWare 站台之間的複寫、容錯移轉和復原作業。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# 在內部部署 VMWare 站台之間部署


##概觀

Azure Site Recovery 中的 InMage Scout 可提供內部部署 VMWare 站台之間的即時複寫。InMage Scout 包含在 Azure Site Recovery 服務的訂用帳戶中。


## 必要條件

- **Azure 帳戶** — 您將需要一個 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您可以從[免費試用](pricing/free-trial/)開始。


##步驟 1：建立保存庫並下載 InMage Scout

1. 登入[管理入口網站](https://portal.azure.com)。
2. 按一下 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建] > [快速建立]。
4. 在 [名稱] 中，輸入一個可識別保存庫的易記名稱。
5. 在 [區域] 中，選取保存庫的地理區域。若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](pricing/details/site-recovery/)中的＜各區域上市情況＞。

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫在主要 [復原服務] 頁面上會列為 [使用中]<b></b>。</P>

##步驟 2：設定保存庫

1. 按一下 [建立保存庫]。
2. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。
3. 在下拉式清單中，選取 [在兩個內部部署 VMWare 站台之間]。
4. 下載 InMage Scout。
5. 使用隨 InMage Scout 產品下載的文件設定兩個 VMWare 站台之間的複寫。


##後續步驟

若有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (英文)。

<!---HONumber=July15_HO2-->