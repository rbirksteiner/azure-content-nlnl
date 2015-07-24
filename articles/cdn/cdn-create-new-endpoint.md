<properties 
	 pageTitle="如何啟用 Azure 內容傳遞網路 (CDN)" 
	 description="本主題說明如何啟用 Azure 內容傳遞網路 (CDN)。" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="06/03/2015" 
	 ms.author="mazha"/>



#如何啟用 Azure 內容傳遞網路 (CDN)  

您可透過 Azure 管理入口網站針對您的來源啟用 CDN。目前的可用來源類型包括：Web Apps、儲存體和雲端服務。您也可以針對您的 Azure 媒體服務啟用 CDN Azure 媒體服務串流端點。針對您的來源啟用 CDN 端點之後，所有可公開取得的物件皆能進行 CDN 邊緣快取。

請注意，現在您也可以建立自訂非 Azure 的來源。

##建立新的 CDN 端點  

1.	登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。
2.	在導覽窗格中，按一下 [**CDN**]。
3.	在功能區中，按一下 [**新增**]。在 [**新增**] 對話方塊中，依序選取 [**應用程式服務**]、[**CDN**]、[**快速建立**]。
4.	在 [**來源類型**] 下拉式清單中，從可用的來源類型清單中選取一個來源類型。
	
	可用的來源 URL 清單會顯示在 [**來源 URL**] 下拉式清單中。
	

	![createnew][createnew]

	如果您選取 [**自訂來源**]，您可以輸入自訂的來源 URL。此 URL 的來源不需為 Azure。

	![customorigin][customorigin]

	>[AZURE.NOTE]目前只支援 HTTP 的來源，您必須使用媒體服務延伸模組啟用 Azure 媒體服務串流端點的 Azure CDN。
	
5.	按一下 [**建立**] 按鈕，建立新的端點。


>[AZURE.NOTE]為端點建立的組態無法立即使用，最多需要 60 分鐘進行註冊，以透過 CDN 網路傳播。嘗試立即使用 CDN 網域名稱的使用者可能會收到狀態碼 400 (不正確的要求)，直到可透過 CDN 使用內容為止。

##另請參閱
[如何將內容傳遞網路 (CDN) 內容對應至自訂網域](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=62-->