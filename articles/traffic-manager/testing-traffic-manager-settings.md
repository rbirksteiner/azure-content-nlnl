<properties
   pageTitle="測試流量管理員設定"
   description="本文將協助您測試流量管理員設定。"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# 測試流量管理員設定

測試流量管理員設定的最好方法是設定數個用戶端，然後分次關閉您設定檔中的各端點 (包含雲端服務和網站)。下列提示可協助您測試流量管理員設定檔：

## 基本測試步驟

-**設定非常低的 DNS TTL**，方便快速傳播變更 - 例如 30 秒。

-**知道正在測試的設定檔中您 Azure 雲端服務和網站的 IP 位址**。

-**使用可讓您將 DNS 名稱解析成 IP 位址的工具**，並顯示該位址。您正查看公司網域名稱是否解析成設定檔中端點的 IP 位址。他們的解析方法應與流量管理員設定檔的負載平衡方法一致。如果您是使用執行 Windows 的電腦，您可以從命令或 Windows PowerShell 命令提示字元使用 Nslookup.exe 工具。可讓您「挖掘」IP 位址的其他公開可用工具在網際網路上隨手可得。

### 使用 nslookup 檢查流量管理員設定檔

1-以系統管理員的身分開啟命令或 Windows PowerShell 命令提示字元。

2-為了排清 DNS 解析程式快取，請輸入 `ipconfig /flushdns`。

3-輸入 `nslookup <your Traffic Manager domain name>`。例如，下列命令會檢查前置詞為 *myapp.contoso* nslookup myapp.contoso.trafficmanager.net 的網域名稱。典型的結果會顯示下列項目：-正在存取 DNS 伺服器的 DNS 名稱和 IP 位址，藉此解析此流量管理員網域名稱。-您在命令行上輸入在 "nslookup" 之後的流量管理員網域名稱，和流量管理員網域名稱解析的 IP 位址。第二個 IP 位址是要檢查的重點。它應符合正在測試流量管理員設定檔中其中一個雲端服務或網站的 虛擬 IP (VIP)。

## 測試負載平衡方法


### 測試容錯移轉負載平衡方法

1-保留所有端點開啟狀態。2-使用單一用戶端。3-使用 Nslookup.exe 工具或類似的公用程式要求您公司網域名稱的 DNS 解析。4-確定取得之已解析的 IP 位址適用於您的主要端點 5 關閉您的主要端點或移除監視檔案，所以流量管理員會認為它已關閉。6-等候流量管理員設定檔的 DNS 存留時間 (TTL)，再加上 2 分鐘。例如，若您的 DNS TTL 為 300 秒 (5 分鐘)，則您必須等待 7 分鐘。7-排清您的 DNS 用戶端快取並要求 DNS 解析。在 Windows 中，您可以使用命令或 Windows PowerShell 命令提示字元發出 ipconfig /flushdns 命令排清 DNS 快取。8-確定取得的 IP 位址適用於您的次要端點。9-重複此程序，關閉次要端點，然後第三個，依此類推。每一次，請確定 DNS 解析傳回清單中下一個端點的 IP 位址。關閉所有端點之後，您應可再次取得主要端點的 IP 位址。

### 測試循環配置資源負載平衡方法

1-保留所有端點開啟狀態。2-使用單一用戶端。3-使用 Nslookup.exe 工具或類似的公用程式要求您公司網域的 DNS 解析。4-確定取得的 IP 位址是清單中的其中之一。5-排清您的 DNS 用戶端快取並不斷重複步驟 3 和 4。您應該可看到為每個端點傳回不同的 IP 位址。然後，此程序將會重複執行。

### 測試效能負載平衡方法

若要有效地測試效能負載平衡方法，您必須有位在世界各地的用戶端。您可以在 Azure 中建立嘗試透過公司網域名稱呼叫服務的用戶端。或者，如果您的公司是全球企業，您可以在世界各地遠端登入用戶端，並從這些用戶端中進行測試。

您可以找到免費的 Web 型 DNS 查閱和挖掘服務。其中部分提供您從不同位置檢查 DNS 名稱解析的能力。例如，搜尋 “DNS lookup” 相關資料。另一個選項是使用協力廠商解決方案 (如 Gomez 或 Keynote)，確認您的設定檔正如預期般分配流量。

## 另請參閱

[關於流量管理員的負載平衡方法](../about-traffic-manager-balancing-methods.md) [流量管理員組態工作](https://msdn.microsoft.com/library/azure/hh744830.aspx) [流量管理員](../traffic-manager.md)
 

<!---HONumber=July15_HO2-->