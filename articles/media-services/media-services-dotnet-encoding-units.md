<properties 
	pageTitle="如何新增編碼單元" 
	description="了解如何使用 .NET 新增編碼單元"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>




#如何使用 .NET SDK 調整編碼

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。
  
##概觀

媒體服務帳戶是與可決定編碼工作處理速度的保留單元類型相關聯。您可以在下列的保留單元類型之間選擇：Basic、Standard 或 Premium。例如，在執行相同編碼工作的前提下，使用 Standard 保留單元類型的速度會比 Basic 類型快。如需詳細資訊，請參閱 [Milan Gada](http://azure.microsoft.com/blog/author/milanga/) 撰寫的部落格「編碼保留單元類型」。

除了指定保留單元類型之外，您還可以指定使用編碼保留單元來佈建帳戶。佈建的編碼保留單元數目可決定給定帳戶中可同時處理的媒體工作數目。例如，如果帳戶有 5 個保留單元，則只要有工作需要處理，就會同時執行 5 個媒體工作。剩餘的工作會在佇列中等待，且隨著執行中的工作完成，就立即循序地挑選來開始處理。如果帳戶未佈建任何保留單元，則會循序地挑選工作。在此情況下，一件工作完成與下一件工作開始之間的等待時間，視系統中的資源可用性而定。

若要使用 .NET SDK　變更保留單元類型以及編碼保留單元數目，請執行下列動作：

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##建立支援票證

依預設，每一個媒體服務帳戶可調整為最多 25 個編碼保留單元和 5 個隨選串流保留單元。您可以建立支援票證來要求更高的限制。

###開啟支援票證

若要建立支援票證，請執行下列動作：

1. 按一下[取得支援](https://manage.windowsazure.com/?getsupport=true)。如果您未登入，系統會提示您輸入認證。

1. 選取您的訂閱。
 
1. 在支援類型下，選取 [技術]。
 
1. 按一下 [建立票證]。
 
1. 在下一頁顯示的產品清單中，選取 [Azure 媒體服務]。
 
1. 選取適合您的問題的 [問題類型]。
 
1. 按一下 [繼續]。
 
1. 遵循下一頁的指示，然後輸入問題的詳細資訊。
 
1. 按一下 [提交] 來建立票證。
 

 

<!---HONumber=62-->