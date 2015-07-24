<properties 
	pageTitle="使用條件式存取管理風險" 
	description="此主題說明如何允許在任何地方從符合原則的已知裝置存取特定資源，以及不允許從遺失、遭竊、不符合原則的裝置來存取。" 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>


# 使用條件式存取管理風險

當前員工的工作方式、生產力如何提升及工作上使用的工具正快速變化。員工開始將個人裝置帶入工作中。這些個人裝置上，有他們在個人的數位生活中使用的應用程式。他們對這種自由和所帶來的功能習以為常。他們想要在工作上使用這些相同的應用程式，也想要在工作上享有他們在個人數位生活中同樣的彈性。現今的企業員工可能從任何地點，使用他們選擇的裝置來工作，並順暢地連接和存取商務應用程式。

讓使用者自由選擇工作地點、時間和方式的這種彈性，也帶來更高的風險。這些裝置的許多資料點經常遭竊、錯置或遺失。許多這些智慧型手機和平板電腦上，儲存著大量敏感性和機密性的客戶和企業內容。這是 IT 架構設計人員、安全性專家及 IT 系統管理員嘗試維持的一種微妙的平衡。這種平衡是一方面讓使用者在他們喜愛的所有裝置上發揮生產力，另一方面對存在於這些裝置上的企業內容提供適度的安全性和保護。

透過 Azure Active Directory、Office 365 和 Microsoft Intune 提供的多個條件式存取功能，IT 系統管理員可以解決下列問題：

- 允許員工從任何地方存取，但不對網際網路上的任何人敞開大門。
- 允許在任何地方從符合原則的已知裝置存取特定資源。
- 不允許從遺失、遭竊、不符合原則的裝置來存取。

![][1]

## 接下來

下列主題討論在組織中設定條件式存取原則的各種不同機制。

- [Azure Active Directory 裝置註冊概觀](https://msdn.microsoft.com/library/azure/dn903763.aspx)
- [使用 Azure Active Directory 裝置註冊設定內部部署條件式存取](https://msdn.microsoft.com/library/azure/dn788908.aspx)
- [Office 365 服務的條件式存取裝置原則](https://msdn.microsoft.com/library/azure/dn903766.aspx)
- [SaaS 應用程式的 Azure 條件式存取預覽](https://msdn.microsoft.com/library/azure/dn906877.aspx)


<!--Image references-->
[1]: ./media/active-directory-conditional-access/condaccoverviewvsdx1.png
 

<!---HONumber=62-->