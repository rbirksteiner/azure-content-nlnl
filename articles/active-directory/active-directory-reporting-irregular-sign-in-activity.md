<properties
	pageTitle="異常的登入活動"
	description="這份報告包含由我們的機器學習演算法識別為「異常」的登入。"
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

# 異常的登入活動

異常登入是指機器學習服務演算法已經根據結合異常登入位置和裝置的「不可能的行進」條件識別的登入活動。這可能表示駭客已經使用此帳戶成功登入。如果我們在 30 天或更少的天數內遇到 10 個或更多異常的登入事件，我們會傳送電子郵件通知給全域管理員。請務必將 aad-alerts-noreply@mail.windowsazure.com 納入安全寄件者清單中。

<!---HONumber=August15_HO9-->