<properties 
	pageTitle="Proxy 服務的 Cloud App Discovery 登錄設定" 
	description="本主題的目標是為您提供在執行 Cloud App Discovery 代理程式的電腦上設定必要連接埠所需的步驟。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Proxy 服務的 Cloud App Discovery 登錄設定

根據預設，Cloud App Discovery 代理程式設定為僅使用連接埠 80 或 443。如果您計劃要在具有使用自訂連接埠 (既不是 80 也不是 443) 的 Proxy 伺服器的環境中安裝 Cloud App Discovery，您必須設定代理程式使用此連接埠。設定是以登錄機碼為基礎。


本主題的目標是為您提供在執行 Cloud App Discovery 代理程式的電腦上設定必要連接埠所需的步驟。



**若要修改執行 Cloud App Discovery 代理程式的電腦所使用的連接埠，請執行下列步驟：**


1. 啟動 [登錄編輯器]。<br> ![執行](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. 瀏覽至或建立下列登錄機碼：<br> **HKLM\_LOCAL\_MACHINE\\Software\\Microsoft\\Cloud App Discovery\\Endpoint**

3. 建立新的**多字串**值，稱為**連接埠**。![新增](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. 若要開啟 [編輯多字串] 對話方塊中，按兩下 [連接埠] 值。


5. 在 [數值資料] 文字方塊中輸入以下的值，然後新增您組織所使用的所有自訂連接埠：<br><br> **80** <br> **8080** <br> **8118** <br> **8888** <br> **81** <br> **12080** <br> **6999** <br> **30606** <br> **31595** <br> **4080** <br> **443** <br> **1110** <br><br> ![編輯多字串](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. 按一下 [確定]，關閉 [編輯多字串] 對話方塊。



**其他資源**


* [如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=August15_HO6-->