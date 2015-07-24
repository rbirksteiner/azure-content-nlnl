<properties 
	pageTitle="設定 IIS Express 以便進行行動服務的本機測試" 
	description="了解如何設定 IIS Express 以允許連接到本機行動服務專案進行測試。" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	documentationCenter="" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# 設定本機 Web 伺服器以允許連接到本機行動服務 

Azure 行動服務可讓您在 Visual Studio 中使用其中一個支援的 .NET 語言建立行動服務，然後將其發佈至 Azure。將 .NET 後端用於行動服務的好處之一，是您將可先在本機電腦或虛擬機器上執行、測試及偵錯行動服務，再將其發佈至 Azure。

若要能夠使用執行於模擬器、虛擬機器或個別工作站上的用戶端，以本機方式測試行動服務，您必須設定本機 Web 伺服器和主機電腦，使其允許連接到工作站的 IP 位址和連接埠。本主題將說明如何設定 IIS Express，以允許連接到您本機代管的行動服務。

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

 

<!---HONumber=July15_HO1-->