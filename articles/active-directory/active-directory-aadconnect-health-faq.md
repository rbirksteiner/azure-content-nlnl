<properties 
	pageTitle="Azure AD Connect Health 常見問題集"
	description="此常見問題集會回答 Azure AD Connect Health 的相關問題。這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="billmath"/>


# Azure AD Connect Health 常見問題集 (FAQ)

此常見問題集會回答 Azure AD Connect Health 的相關問題。這個常見問題集涵蓋使用服務的相關問題，包括計費模型、功能、限制及支援。

## 一般問題



**問：我在 Azure Active Directory 中有幾個租用戶。我如何切換到包含 Azure Active Directory Premium 的租用戶？**

選取左導覽列上的 [首頁]，然後在右上角選取目前登入的 [使用者名稱]，再選擇正確的租用戶帳戶，就可以切換 Azure AD 租用戶。如果此處未列出租用戶帳戶，選取 [登出]，然後再使用 Azure Active Directory Premium 租用戶的全域租用戶系統管理員認證登入。


## 安裝問題



**問：在個別的伺服器上安裝 Azure AD Connect Health 代理程式有什麼影響？**

ADFS 伺服器上安裝 Microsoft Identity Health 代理程式對於 CPU、記憶體耗用量、網路頻寬和儲存體的影響非常少。

以下的數字是近似值。

- CPU 耗用量：增加約 1%
- 記憶體耗用量：最多 10% 的系統總記憶體
- 網路頻寬使用量：每 1000 個 ADFS 要求約 1 MB
>[AZURE.NOTE]若代理程式無法與 Azure 進行通訊，代理程式會在本機儲存資料，上限為系統記憶體總計的 10%。當代理程式達到實體記憶體總計的 10% 時，若代理程式無法將資料上傳至服務，則新的 ADFS 交易將會以「近期服務最少」為基礎來覆寫任何「快取」交易。

- AD Health 代理程式的本機緩衝區儲存體：約 20 MB
- 稽核通道所需的資料存放區


建議您為 AD FS 稽核通道佈建 1024 MB (1 GB) 的磁碟空間，AD Health 代理程式才能處理所有資料。

**問：在安裝 Azure AD Connect Health 代理程式期間，我是否需要重新啟動我的伺服器？**

否，安裝代理程式不需要您重新啟動伺服器。不過，安裝某些先決條件的步驟可能需要重新啟動伺服器。

例如，在 Windows Server 2008 R2 上安裝 .Net 4.5 Framework 需要重新啟動伺服器。


**問：Azure AD Connect Health 服務是否透過通過 http proxy 運作？**

是，註冊程序與正常的操作可以透過明確的 proxy 設定運作，以轉送輸入 http 要求。在此情況下，"Netsh WinHttp set Proxy" 無法運作，因為代理程式使用 System.Net (而不是 Microsoft Windows HTTP 服務) 提出 Web 要求。

在執行 Register-AdHealthAgent (安裝的最後一個步驟) 之前的任何時間執行


- 步驟 1 – 將項目加入至 machine.config 檔案中


找出 machine.config 檔案。此檔案位於 %windir%\\Microsoft.NET\\Framework64[version]\\config\\machine.config</li>

在 machine.config 檔案的 <configuration></configuration> 元素底下加入下列項目。
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

您可以在 [這裡] (https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110)) 找到額外的 <defaultProxy> 資訊。

此設定會設定整個 .NET 應用程式系統在提出 http.NET 要求時，使用明確定義的 Proxy。不建議修改每個個別的 app.config，因為在自動更新期間，將會復原這個檔案。如果您只修改 machine.config，您只需要變更一個檔案，它就會透過更新保存。

- 步驟 2 - 在 [網際網路選項] 中設定 Proxy

開啟 Internet Explorer -> [設定] -> [網際網路選項] -> [連線]-> [LAN 設定]。

選取 [為您 LAN 使用 Proxy 伺服器]

如果您有不同的 Proxy 連接埠供 HTTP 和 HTTPS/安全使用，選取 [進階]




**問：連線到 Http Proxy 時，Azure AD Connect Health 服務是否支援基本驗證？**

否，目前不支援為基本驗證指定任意使用者名稱/密碼的機制。





## 操作問題



**問：我是否需要在 AD FS 應用程式 Proxy 伺服器或 Web 應用程式 Proxy 伺服器上啟用稽核？**

否，在 AD FS 應用程式 Proxy 伺服器或 Web 應用程式 Proxy 伺服器上都不需要啟用稽核。只需要在 AD FS 同盟伺服器上啟用即可。



**問：Azure AD Connect Health 警示如何獲得解決？**

Azure AD Connect Health 警示會在成功情況下獲得解決。Azure AD Connect Health 代理程式會定期偵測成功情況，並向服務回報。對於少數幾個警示，隱藏是以時間為基礎。也就是說，如果在警示產生的 48 小時內未觀察到相同的錯誤狀況，就會自動解決警示。




**問：我需要開放哪些防火牆連接埠，Azure AD Connect Health 代理程式才能運作？**

您必須開放 TCP/UDP 連接埠 80 和 443，Azure AD Connect Health 代理程式才能夠與 Azure AD Health 服務端點進行通訊。

## 相關連結

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [適用於 AD FS 的 Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install-adfs.md)
* [在 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)

<!---HONumber=August15_HO9-->