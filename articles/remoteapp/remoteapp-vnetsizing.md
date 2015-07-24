
<properties 
    pageTitle="RemoteApp 中 VNET 的大小資訊"
    description="深入了解使用 VNET 執行之 RemoteApp 的 IP 位址需求" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# RemoteApp 中 VNET 的大小資訊

搭配使用 RemoteApp 與虛擬網路 (VNET) 時，RemoteApp 會使用子網路內的 IP 位址。根據 RemoteApp 服務的規模，您需要確保子網路具有足夠的 IP 位址可用於 RemoteApp 虛擬機器。雖然此大小調整指導方針未完美地指定 RemoteApp 如何動態旋轉集合內的虛擬機器，但是可協助您估計子網路範圍。這特別重要，因為在 VNET 中放置 RemoteApp 服務之後，需要移除 RemoteApp，才能增加子網路大小。

針對每個您想要以最大容量執行的 RemoteApp 集合，您應該有 100 個 IP 位址可用。例如，如果您在標準計畫中有一個 RemoteApp 集合，而且您最多想要有 500 位使用者，則該集合應該有 100 個 IP 位址。同樣地，在具有 800 位使用者的基本計畫中，RemoteApp 集合需要有 100 個 IP 位址。如果您想要有較少的使用者 (小於最大值)，則可以減少每個集合所需的 IP 位址。最少子網路大小需求是 30 個 IP 位址 (/27)。

<!--HONumber=52-->
 