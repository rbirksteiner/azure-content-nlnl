<properties
	pageTitle="從可能受感染的裝置登入"
	description="包含從可能正在執行某些惡意程式碼 (惡意軟體) 的裝置執行的登入嘗試的報告。"
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

# 從可能受感染的裝置登入
<p>此報告會嘗試識別已經變成受感染，而且現在是殭屍網路 (也稱為殭屍部隊) 一部分的使用者裝置。我們會使使用者登入的 IP 位址與我們知道與殭屍網路伺服器聯繫的 IP 位址相互關聯。</p>
<p>建議：此報告會標示 IP 位址，而非使用者裝置。建議您連絡使用者並掃描所有使用者的裝置以便確定。使用者的個人裝置也可能受到感染，或者與使用者使用相同 IP 位址的其他使用者的裝置可能受到感染。</p>
<p>如需如何處理惡意程式碼感染的詳細資訊，請參閱 [惡意程式碼防護中心] (http://go.microsoft.com/fwlink/?linkid=335773)。</p>


![從可能受感染的裝置登入](./media/active-directory-reporting-sign-ins-from-possibly-infected-devices/signInsFromPossiblyInfectedDevices.PNG)

<!---HONumber=August15_HO9-->