<properties
	pageTitle="從多個地理區域登入"
	description="這份報告指出使用者有兩次登入似乎來自不同的地區，且使用者不可能在登入間的時間內在這兩個區域之間移動。"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# 從多個地理區域登入
<p>這份報告包含使用者的成功登入活動，而其中有兩次登入似乎來自不同的地區，且使用者不可能在登入間的時間內在這兩個區域之間移動。可能的原因包括：</p><ul><li>使用者與其他使用者共用其密碼</li><li>使用者正使用遠端桌面啟動網頁瀏覽器進行登入</li><li>駭客已從不同國家/地區登入使用者的帳戶。</li><li>使用者使用的是 VPN 或 Proxy</li><li>使用者同時從多個裝置登入，例如桌面和行動電話，而且行動電話的 IP 位址不尋常。</li></ul><p>這份報告的結果會顯示成功的登入事件，以及登入間的時間、登入似乎來自的地區和在這些區域之間移動的估計時間。</p><p>顯示的移動時間只是估計值，而且可能與兩地間的實際移動時間不同。</p>


![從多個地理區域登入](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)

<!---HONumber=August15_HO9-->