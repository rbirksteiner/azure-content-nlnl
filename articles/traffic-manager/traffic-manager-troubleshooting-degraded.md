<properties
   pageTitle="疑難排解 Azure 流量管理員上的已降級狀態"
   description="如何在流量管理員顯示為降級狀態時疑難排解流量管理員設定檔。"
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="joaoma" />
# 疑難排解 Azure 流量管理員上的已降級狀態
此頁面將會描述如何疑難排解 Azure 流量管理員設定檔 (顯示已降級狀態)，並提供一些重點以了解流量管理員探查。


您已將流量管理員設定檔設定為指向部分的 .cloudapp.net 裝載服務，幾秒鐘之後您就會看到狀態為「降級」。

![degradedstate](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

如果您移至該設定檔的 [端點] 索引標籤，您會看到一或多個離線狀態的端點：

![離線](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## 流量管理員探查的重要事項

- 如果探查從探查路徑取回 200，流量管理員只會將端點視為線上。
- 30x 重新導向 (或任何其他非 200 的回應) 將會失敗，即使重新導向的 URL 傳回 200 也一樣。

- 若為 HTTP 探查，會忽略憑證錯誤。
 
- 只要傳回 200，探查路徑的實際內容並不重要。如果實際的網站內容不會傳回 200 (也就是說，如果 ASP 頁面重新導向至 ACS 登入頁面或一些其他的 CNAME URL)，常用的技巧是將路徑設為類似 "/favicon.ico" 的路徑。
 
- 最佳作法是將探查路徑設為具有足夠邏輯，以判斷網站是向上或向下的項目。在上述範例中，將路徑設為 "/favicon.ico"，您只有測試 w3wp.exe 是否有回應，而非您的網站是否狀況良好。更好的選項是將路徑設為如 "/Probe.aspx" 的路徑，而且 Probe.aspx 內包含足夠的邏輯，以判斷您的網站是否狀況良好 (也就是說，檢查效能計數器以確定您的 CPU 不是 100% 或接收大量的要求失敗，嘗試存取資源，例如資料庫或工作階段狀態，以確定應用程式的邏輯仍在運作等等)。
 
- 如果設定檔中的所有端點都已降級，流量管理員會將所有端點視為狀況良好並路由傳送流量至所有端點。這是為了確保探查機制 (會導致不正確失敗探查) 的任何潛在問題都不會導致服務的完全中斷。

  

## 疑難排解

疑難排解流量管理員探查失敗的一個工具是 wget。您可以從 [wget](http://gnuwin32.sourceforge.net/packages/wget.htm) 取得二進位檔和相依性封裝。請注意，您可以使用 Fiddler 或 curl 等其他程式，而不是 wget – 基本上您只需要會顯示未經處理 HTTP 回應的項目。

安裝 wget 之後，請移至命令提示字元，並對流量管理員中所設定的 URL + 探查連接埠 & 路徑執行 wget。這個範例中就會是 http://watestsdp2008r2.cloudapp.net:80/Probe。

![疑難排解](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

使用 Wget：

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

請注意，wget 會指出 URL 將 301 重新導向傳回 http://watestsdp2008r2.cloudapp.net/Default.aspx。如同我們已經從上述「流量管理員探查的重要事項」一節所得知的，流量管理員探查 會將 30x 重新導視為失敗，而這會讓探查回報離線。此時很容易檢查網站組態，並且確定 200 會從 /Probe 路徑傳回 (或將流量管理員探查重新設定為指向會傳回 200 的路徑)。

 

如果您的探查使用 HTTPs 通訊協定，您會想要新增 "--no-check-certificate" 參數到 wget，讓它忽略在 cloudapp.net URL 上的憑證不相符。


## 後續步驟


[關於流量管理員的負載平衡方法](traffic-manager-load-balancing-methods.md)

[流量管理員概觀](../traffic-manmager-overview.md)

[雲端服務](http://go.microsoft.com/fwlink/?LinkId=314074)

[網站](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[流量管理員的相關作業 (REST API 參考)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理員 Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=July15_HO2-->