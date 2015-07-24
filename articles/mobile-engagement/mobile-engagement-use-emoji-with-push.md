<properties 
	pageTitle="在推播通知內使用 Emoji 表情符號" 
	description="如何在推播通知內使用 Emoji 表情符號"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#在推播通知內使用 Emoji 表情符號

您可以在推播通知內包含 Emoji 表情符號。目前，Azure Mobile Engagement 對於應用程式內外的文字通知僅支援設定 3 個位元組的 Emoji 表情符號。請依照下列步驟執行：

1.  首先，您必須尋找 3 個位元組的 Emoji 表情符號文件庫。您可以在下列[連結](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not)找到所有可用的 Emoji 表情符號。

	![][1]

2. 移至 Azure Mobile Engagement 入口網站的 [觸達] 索引標籤。

3. 選取您的推播通知類型 (通知、投票等等)。對於此範例，我們選擇公告推播。

4. 指定通知的不同欄位，直到您到達通知的文字。這就是您加入 Emoji 表情符號的位置。您可以選擇將它放在標題、訊息或兩者中。

	![][2]

5. 從上述的連結剪下您想要使用的 Emoji 表情符號。將它直接貼上至標題和/或訊息中您所選擇的位置。

6. 完成通知的其他欄位，然後儲存。

7. 當您執行測試或啟動公告時，您會看到具有指定表情符號的通知。

	![][3] ![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!---HONumber=July15_HO2-->