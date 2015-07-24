<properties 
 pageTitle="使用 Azure 自動化管理 Azure 流量管理員" 
 description="了解如何使用 Azure 自動化服務管理 Azure 流量管理員。" 
 services="traffic-manager, automation" 
 documentationCenter="" 
 authors="eamonoreilly" 
 manager="jwinter" 
 editor=""/>

<tags 
 ms.service="traffic-manager" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.date="04/13/2015" 
 ms.author="eamono"/>


#使用 Azure 自動化管理 Azure 流量管理員

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure 流量管理員的管理。

## 什麼是 Azure 自動化？

[Azure 自動化](http://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。透過 Azure 自動化，長時間執行、手動、容易發生錯誤和經常重複的工作都可以自動化，以提高可靠性、效率，並為您的組織縮短創造價值時程。

Azure 自動化提供非常可靠且高度可用的工作流程執行引擎，可隨著組織的成長根據您的需求進行調整。在 Azure 自動化中，程序可透過手動方式、經由協力廠商系統，或依照排程的間隔啟動，讓工作精準地在需要時執行。

將您的雲端管理工作交由「Azure 自動化」自動執行，以降低營運負擔並釋出 IT/開發維運人力，使其專注於能夠為企業創造價值的工作上。


## Azure 自動化如何協助管理 Azure 流量管理員？

流量管理員可透過 [Azure PowerShell 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)中提供的 PowerShell Cmdlet，在 Azure 自動化中受到管理。Azure 自動化可以呼叫這些 PowerShell 指令程式，以便讓所有的流量管理員管理工作服務內執行。您也可以在 Azure 自動化中將這些 Cmdlet 與其他 Azure 服務的 Cmdlet 配對，將跨 Azure 服務和協力廠商系統的複雜工作自動化。


## 後續步驟

了解 Azure 自動化的基本概念以及如何用它來管理 Azure 流量管理員之後，請參考下列連結，以深入了解 Azure 自動化。

* 請參閱 Azure 自動化[入門指南](http://go.microsoft.com/fwlink/?LinkId=390560)
 

<!---HONumber=July15_HO2-->