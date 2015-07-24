<properties title="List of Oracle Virtual Machine Images" pageTitle="Oracle 虛擬機器映像清單" description="在 Azure 資源庫中取得 Oracle 映像清單，並了解如何以其中一個映像為基礎建立 Oracle 虛擬機器。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Oracle 虛擬機器映像清單
若要建立以 Oracle 映像為基礎的虛擬機器，請登入 [Azure 入口網站](https://ms.portal.azure.com/)，按一下 [Marketplace]，按一下 [計算]，然後在搜尋方塊中輸入 **Oracle**。選擇映像並遵循指示在 Microsoft Azure 上設定映像。請注意，在 [Azure 入口網站](https://ms.portal.azure.com/)中由 Microsoft 提供的 Oracle 映像在 Windows 上執行，而由 Oracle 提供的 Oracle 映像則在 Oracle Linux 上執行。

![](media/virtual-machines-oracle-list-oracle-virtual-machine-images/image1.png)

##Windows 虛擬機器映像
以下是可用的 Oracle 虛擬機器映像清單，可在 Azure 的 Windows Server 上執行。這些映像是「隨用隨付」，表示 Oracle 授權費用已包含在這些映像的使用量中。您也可以使用自己的授權，在 Windows 或 Linux 上執行 Oracle 軟體。在 [VM 與 VM 資源庫映像的定價與授權](http://azure.microsoft.com/pricing/details/virtual-machines/#oracle-software)中有完整的詳細資料。如需特定的 Oracle 定價詳細資料，請按一下 [Oracle] 索引標籤。

###Oracle 資料庫虛擬機器映像
- Oracle Database 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c Standard Edition on Windows Server 2012
- Oracle Database 12c 包含常用選項
- Oracle Database 12c 包含進階選項
- Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2
- Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2
- Oracle Database 11g R2 EE 包含常用選項
- Oracle Database 11g R2 EE 包含進階選項  

###Oracle WebLogic Server 虛擬機器映像
- Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle WebLogic Server 12c Standard Edition on Windows Server 2012
- Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2
- Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2  

###Oracle 資料庫與 WebLogic Server 虛擬機器映像  
- Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012
- Windows Server 2008 R2 上的 Oracle Database 11g 與 WebLogic Server 11g Enterprise Edition
- Windows Server 2008 R2 上的 Oracle Database 11g 與 WebLogic Server 11g Standard Edition

### Java 虛擬機器映像
-	JDK 8 on Windows Server 2012 R2
-	JDK 7 on Windows Server 2012
-	Windows Server 2012 上的 JDK 6


##Oracle Linux 虛擬機器映像
以下是可用的預先設定 Oracle 虛擬機器映像清單，可在 Azure 的 Oracle Linux 上執行。使用者預期針對這些映像使用您自己的授權，因為 Oracle 授權費用並未包含使用這些預先設定的 VM 映像。您也可以在 Windows 或 Linux 的自訂虛擬機器上使用自己的授權安裝並執行 Oracle 軟體。在 [Azure 上的 Oracle 授權](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)中有完整的詳細資料。而在使用[您自己的映像](virtual-machines-create-upload-vhd-windows-server.md)建立 VM 有詳細資料。若要深入了解移轉 Oracle 與其他工作負載至 Azure 的方法，請參閱[建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)。

- Oracle Linux 上的 Oracle Database 12c Enterprise Edition
- Oracle Linux 上的 Oracle Database 12c Standard Edition
- Oracle Linux 上的 Oracle WebLogic Server 12c Enterprise Edition
- Oracle Linux 6.4.0.0.0 
- Oracle Linux 7.0.0.0.0 

##其他資源
[Azure Marketplace 中新的全方位 Oracle 映像](https://msopentech.com/blog/2015/02/19/new-one-oracle-images-azure-marketplace/)

[Oracle 虛擬機器映像 - 其他考量](#miscellaneous-considerations-for-oracle-virtual-machine-images-new-article)

<!---HONumber=July15_HO2-->