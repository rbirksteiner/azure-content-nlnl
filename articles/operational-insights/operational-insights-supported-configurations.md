<properties
   pageTitle="Operational Insights 支援的組態"
   description="深入了解 Operational Insights 所需的組態"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Operational Insights 支援的組態

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

使用 Operational Insights 需要什麼？ 請查看下列資訊為 Operational Insights 進行準備。


## System Center 2012 Operations Manager 的組態

您可以使用 Operational Insights 作為在 System Center 2012 R2 或 System Center 2012 SP1 R2 中 Operations Manager 的附加服務。如此一來，您就可以在 Operations Manager 中使用 Operations 主控台來檢視 Operational Insights 警示和組態資訊。當您使用 Operational Insights 作為 Operations Manager 的附加服務時，代理程式會直接與管理伺服器通訊，它會依序與 Operational Insights 服務通訊。

使用 Operational Insights 作為附加服務有下列先決條件：


- 在 System Center 2012 SP1 Operations Manager 和 Operational Insights 之間進行整合必須要有 [Operational Insights Connector for Operations Manager](https://www.microsoft.com/zh-tw/download/details.aspx?id=38199) 中隨附的更新管理組件。您可以從 [Operational Insights Connector for Operations Manager](https://www.microsoft.com/zh-tw/download/details.aspx?id=38199) 下載並安裝管理組件。

- System Center 2012 SP1：Operations Manager 更新彙總套件 6，如果是更新彙總套件 7 更好。此更新必須套用到管理伺服器、代理程式以及 Operations 主控台，Operational Insights 才能作為附加服務。

- System Center 2012 R2：Operations Manager 更新彙總套件 2，如果是更新彙總套件 3 更好。此更新必須套用到管理伺服器、代理程式以及 Operations 主控台，Operational Insights 才能作為附加服務。

- 若要檢視產能管理資料，您必須啟用 Operations Manager 與 Virtual Machine Manager (VMM) 之間的連線。如需連接系統的詳細資訊，請參閱[如何連接 VMM 與 Operations Manager](https://technet.microsoft.com/zh-tw/library/hh882396.aspx)。

請參閱[檢視 Operational Insights 警示](http://go.microsoft.com/fwlink/?LinkID=293793)了解安裝和組態指示。

如果您要檢視有關 SharePoint Server 2010、Lync Server 2013、Lync Server 2010 或 System Center 2012 SP1 - Virtual Machine Manager 的 Operational Insights 警示，您必須為這些工作負載設定「執行身分」帳戶。如需如何設定執行身分帳戶的相關資訊，請參閱 [Operational Insights 的 Operations Manager 考量](operational-insights-operations-manager.md)。


### Operations Manager 作業系統

各種電腦都支援 Operations Manager 代理程式。請參閱[系統需求：System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx) 了解有關代理程式支援的詳細資料。

### Operations Manager 的必要軟體

若要檢視產能管理資料，您必須啟用 Operations Manager 與 VMM 之間的連線。如需連接系統的詳細資訊，請參閱[如何連接 VMM 與 Operations Manager](https://technet.microsoft.com/zh-tw/library/hh882396.aspx)。

## 直接連線到 Operational Insights 的代理程式

用來直接連線到服務的代理程式是 Microsoft Monitoring Agent。其系統需求列在 [Microsoft 下載中心](https://www.microsoft.com/zh-tw/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)頁面上。

## 瀏覽器

您可以使用下列任何一種瀏覽器搭配 Operational Insights：

- Windows Internet Explorer 11、Internet Explorer 10、Internet Explorer 9、Internet Explorer 8 或 Internet Explorer 7

- Mozilla Firefox 3.5 或更新版本

無論您使用何種瀏覽器，您也都必須安裝 Microsoft Silverlight 4。

## 您可以分析的技術

「Operational Insights 組態評估」會分析下列工作負載：

- Windows Server 2012 和 Microsoft Hyper-V Server 2012

- Windows Server 2008 和 Windows Server 2008 R2，包括：
    - Active Directory
	- Hyper-V 主機
	- 一般作業系統

- SQL Server 2012、SQL Server 2008 R2、SQL Server 2008
    - SQL Server Database Engine

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 和 Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 和 Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

對於 SQL Server，支援分析下列 32 位元和 64 位元版本：

- SQL Server 2008 和 2008 R2 Enterprise

- SQL Server 2008 和 2008 R2 Standard

- SQL Server 2008 和 2008 R2 Workgroup

- SQL Server 2008 和 2008 R2 Web

- SQL Server 2008 和 2008 R2 Express

此外，也支援在 WOW64 實作中執行的 32 位元版本 SQL Server。

<!---HONumber=July15_HO2-->