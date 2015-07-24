<properties
	pageTitle="服務匯流排訊息概觀 - Azure"
	description="服務匯流排訊息：雲端彈性資料傳遞"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sethm"/>


# 服務匯流排訊息：雲端彈性資料傳遞

服務匯流排訊息是一項可靠的資訊傳遞服務。此服務的目的是簡化通訊。當兩個或更多對象要交換資訊時，他們就需要通訊機制。服務匯流排訊息是一項代理通訊機制或第三方通訊機制。這類似於實體世界的郵遞服務。郵遞服務讓您能夠很容易地將不同種類的信件與包裹寄送至世界上的任何地方，且提供各種不同的傳遞保證。

Azure 服務匯流排訊息就類似於傳遞信件的郵遞服務，可在傳送者與接收者之間彈性傳遞資訊。即使雙方從未同時在線上，或雙方無法在完全相同的時刻接收訊息，訊息服務也可以確保能夠傳遞資訊。在這種方式下，訊息類似於寄送信件，而非代理式通訊則類似於撥打電話 (在有通話插播與來電顯示功能之前的舊式撥打電話方式則比較類似於代理式訊息)。

訊息傳送者也可能需要各種傳遞特性，包括交易、重複資料檢測、以時間為基礎的到期，以及批次處理特性。這些模式也具備類似郵遞服務的特性：重複傳遞、需要簽名、地址變更或回收。

服務匯流排訊息具有兩種獨立功能：佇列和主題。這兩個訊息實體皆支援上述所有概念，且功能更多。主要差異在於主題支援發佈/訂閱功能，可用於複雜並以內容為基礎的路由和傳遞邏輯，包括傳送給多個接收者。

## 後續步驟

若要深入了解服務匯流排訊息，請參閱下列主題。

- [Azure 服務匯流排架構概觀](fundamentals-service-bus-hybrid-solutions.md)

- [如何使用服務匯流排佇列](service-bus-dotnet-how-to-use-queues.md)

- [如何使用服務匯流排主題](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=July15_HO2-->