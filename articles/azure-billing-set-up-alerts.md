<properties 
	pageTitle="為您的 Microsoft Azure 訂用帳戶設定帳單通知" 
	description="描述如何設定您的 Azure 帳單上的警示，以避免計費出現意外的狀況。" 
	services="" 
	documentationCenter="" 
	authors="vikdesai" 
	manager="msmbaldwin" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="vikdesai"/>

# 為您的 Microsoft Azure 訂用帳戶設定帳單通知

您在乎您的 Azure 訂用帳戶每個月花費多少錢嗎？ 如果您是 Azure 訂用帳戶的帳戶管理員，您可以使用「Azure 計費警示服務」來建立自訂計費警示，以協助您監視和管理您 Azure 帳戶的計費活動。

這項服務是預覽服務，因此您需要做的第一件事就是註冊，請瀏覽 Azure 帳戶管理入口網站中的<a href="https://account.windowsazure.com/PreviewFeatures">預覽功能頁面</a>來執行此動作。

## 設定警示閾值與電子郵件收件者

在收到已為您的訂用帳戶開啟計費服務的電子郵件確認之後，請瀏覽帳戶入口網站中的<a href="https://account.windowsazure.com/Subscriptions">訂用帳戶頁面</a>。按一下您想要監視的訂用帳戶，然後按一下 [**警示**]。

![][Image1]

接著，按一下 [**加入警示**] 來建立您的第一個警示 - 每一訂用帳戶總計可以設定 5 個具有不同臨界值的警示，每個警示最多可以設定兩個電子郵件收件者。

![][Image2]

新增警示時，您將為它提供一個唯一的名稱、選擇消費臨界值，以及選擇要接收警示的電子郵件地址。設定臨界值時，您可以從 [**警示對象**] 清單中選擇 [**計費總計**] 或 [**貨幣信用額度**]。如果是計費總計，當訂用帳戶消費超過臨界值時，就會傳送警示。如果是貨幣信用額度，當貨幣信用額度低於限制時，就會傳送警示。貨幣信用額度通常適用於與 MSDN 帳戶關聯的免費試用和訂用帳戶。

![][Image3]

Azure 支援任何電子郵件地址，但不會驗證電子郵件地址是否有效，所以請仔細檢查是否有錯字。

## 檢查您的通知

設定警示之後，[帳戶中心] 會列出這些警示，並顯示您還可以設定多少個警示。針對每個警示，您會看到其傳送日期和時間 (不論是 [計費總計] 還是 [貨幣信用額度] 警示)，以及您所設定的限制。日期和時間格式是 24 小時制的世界標準時間 (UTC)，而日期為 yyyy-mm-dd 格式。您可以按一下清單中某個警示的加號來編輯該警示，或按一下資源回收筒圖示來將它刪除。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png

<!---HONumber=July15_HO1-->