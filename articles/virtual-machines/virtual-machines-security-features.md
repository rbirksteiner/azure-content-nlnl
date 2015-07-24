<properties 
	pageTitle="Azure 虛擬機器的安全性產品" 
	description="Azure VM 重要安全性功能的快速概觀及詳細資料的連結" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Azure 虛擬機器的安全性產品

<p>在大多數情況下，Azure 虛擬機器會與任何其他虛擬機器或實體電腦有著相同的安全性工作和組態。這些包括基本項目如：使用強式密碼、沒有使用知名使用者名稱，以及建立作業系統和應用程式的更新原則和排程等。另外，我們建議下列做法，並提供您可用來實作這些做法的功能：

- 執行防毒/反惡意程式碼

- 在端點上使用網路存取控制清單 (ACL)
 
##執行防毒/反惡意程式碼

Azure 提供數個防毒/反惡意程式碼解決方案的選項，但管理則掌握在您手中。例如，您必須決定執行掃描和安裝更新的時間。您可以在建立虛擬機器時或在稍後的時間點上新增防毒支援。目前的三個解決方案會以安全性延伸模組的方式提供，您可以在新的和現有的虛擬機器上進行安裝：

- [如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [如何在 Azure VM 上安裝和設定 Trend Micro Deep Security as a Service](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [在 Azure 虛擬機器上部署反惡意程式碼解決方案](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)
 

##在虛擬機器端點上使用網路存取控制清單 (ACL)

網路存取控制清單 (ACL) 可讓您選擇性地允許或拒絕輸入虛擬機器端點的流量。此封包篩選功能提供了一層額外的安全性。如需此作業的運作方式和指示的連結，請參閱[關於網路存取控制清單 (ACL)](http://go.microsoft.com/fwlink/?LinkId=506655)。

##其他資源
Microsoft Azure 信任中心上的[資源](http://azure.microsoft.com/support/trust-center/resources/)


 

<!---HONumber=July15_HO2-->