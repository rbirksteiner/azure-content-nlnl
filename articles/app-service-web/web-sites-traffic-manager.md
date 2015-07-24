<properties 
	pageTitle="使用 Azure 流量管理員來控制 Azure Web 應用程式的流量" 
	description="本文提供與 Azure Web 應用程式相關之 Azure 流量管理員的摘要資訊。&quot;" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# 使用 Azure 流量管理員來控制 Azure Web 應用程式的流量

> [AZURE.NOTE]本文提供與 Azure App Service Web Apps 相關之 Microsoft Azure 流量管理員的摘要資訊。如需 Azure 流量管理員本身的詳細資訊，請造訪本文結尾的連結。

## 簡介
您可以使用 Azure 流量管理員，來控制如何將來自 Web 用戶端的要求分散至 Azure App Service 中的 Web 應用程式。將 Web 應用程式端點新增至 Azure 流量管理員設定檔時，Azure 流量管理員就會持續追蹤您 Web 應用程式的狀態 (執行中、已停止或已刪除)，以判定其中哪些端點應接收流量。

## 負載平衡方法
Azure 流量管理員使用三種不同的負載平衡方法。下列清單說明 Azure Web 應用程式專屬的這些方法。

* **容錯移轉**：如果您在不同地區有 Web 應用程式複本，就可以使用此方法，將某一個 Web 應用程式設定為服務所有 Web 用戶端流量，並在不同地區設定其他 Web 應用程式，以便在第一個 Web 應用程式無法使用時服務該流量。 
	
* **循環配置資源**：如果您在不同地區有 Web 應用程式複本，就可以使用此方法，將流量平均分散於不同地區的 Web 應用程式。
	
* **效能**：效能方法可根據前往用戶端的最短來回時間來分散流量。效能方法可用於相同地區內或不同地區中的 Web 應用程式。

如需 Azure 流量管理員中負載平衡的詳細資訊，請參閱[關於 Traffic Manager 負載平衡方法](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx)。

##Web 應用程式和流量管理員設定檔 
若要設定以控制 Web 應用程式流量，可在使用上述三種負載平衡方法的其中一種之 Azure 流量管理員中建立設定檔，然後新增要控制其設定檔流量的端點 (在此案例中為 Web 應用程式)。系統會定期與設定檔溝通您的 Web 應用程式狀態 (執行中、已停止或已刪除)，讓 Azure 流量管理員可相應地導向流量。

搭配使用 Azure 流量管理員與 Azure 時，請牢記下列重點：

* 若為相同地區內的純 Web 應用程式部署，Web Apps 已經提供與 Web 應用程式模式無關的容錯移轉和循環配置資源功能。

* 若是在搭配使用 Web Apps 與其他 Azure 雲端服務的相同地區中進行部署，您可以結合兩種端點類型來啟用混合案例。

* 在設定檔中，您只能針對每個地區指定一個 Web 應用程式。選取一個 Web 應用程式做為某一個地區的端點時，將無法為該設定檔選擇使用該地區中其餘的 Web 應用程式。

* 您在 Azure 流量管理員設定檔中指定的 Web 應用程式端點，將出現在設定檔中該 Web 應用程式之 [設定] 頁面的 [網域名稱] 區段下方，但您無法在該處進行設定。

* 將 Web 應用程式新增至設定檔後，在該 Web 應用程式之入口網站頁面的 [儀表板] 上，[網站 URL] 將顯示 Web 應用程式的自訂網域 URL (如果已設定一個)。否則會顯示流量管理員設定檔 URL (例如，`contoso.trafficmgr.com`)。Web 應用程式的直接網域名稱和流量管理員 URL 都會顯示在 Web 應用程式之 [設定] 頁面的 [網域名稱] 區段下方。

* 您的自訂網域名稱將如預期般運作，但除了將之新增至 Web 應用程式外，您還必須設定 DNS 對應以指向流量管理員 URL。如需有關如何設定 Azure Web 應用程式之自訂網域的詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱](web-sites-custom-domain-name.md)。

* 您只能將標準模式的 Web 應用程式新增至 Azure 流量管理員設定檔。

## 後續步驟

如需 Azure 流量管理員的概念和技術概觀，請參閱 [Traffic Manager 概觀](http://msdn.microsoft.com/library/windowsazure/hh744833.aspx)。

如需如何設定 Azure 流量管理員的相關資訊，包括適用於 Web Apps 用途，請參閱[流量管理員設定工作](http://msdn.microsoft.com/library/windowsazure/hh744830.aspx)。

如需 Azure 流量管理員中負載平衡的詳細資訊，請參閱[關於 Traffic Manager 負載平衡方法](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx)。

如需將流量管理員與 Web Apps 搭配使用的詳細資訊，請參閱[將 Azure 流量管理員與 Azure 網站搭配使用](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)和 [Azure 流量管理員現在可以與 Azure 網站整合](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)部落格文章。
 

<!---HONumber=62-->