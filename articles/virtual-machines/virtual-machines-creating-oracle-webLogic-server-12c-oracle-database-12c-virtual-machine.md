<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="在 Azure 中建立 Oracle WebLogic Server 12c 與 Oracle Database 12c 虛擬機器" description="逐步完成在 Microsoft Azure 中建立於 Windows Server 2012 上執行之 Oracle WebLogic Server 12c 和 Oracle Database 12c 映像的範例。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#在 Azure 中建立 Oracle WebLogic Server 12c 與 Oracle Database 12c 虛擬機器
以下範例會向您說明如何在 Azure 中以由 Microsoft 所提供並在 Windows Server 2012 上執行的 Oracle WebLogic Server 12c 與 Oracle Database 12c 映像為基礎，建立虛擬機器。

##在 Azure 中建立 Oracle WebLogic Server 12c 與 Oracle Database 12c 虛擬機器

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.	按一下 [Marketplace]，按一下 [計算]，然後在搜尋方塊中輸入「Oracle」。

3.	選取 [Windows Server 2012 上的 Oracle Database 12c 與 WebLogic Server 12c Standard Edition] 或 [Windows Server 2012 上的 Oracle Database 12c 與 WebLogic Server 12c Enterprise Edition] 映像。檢閱與此映像有關的資訊 (例如建議的大小)，然後按一下 [下一步]。

4.	指定 VM 的 [主機名稱]。

5.	指定 VM 的 [使用者名稱]。注意，此使用者是用於從遠端登入 VM，這不是 Oracle 資料庫使用者名稱。

6.	指定並確認 VM 的密碼，或提供 SSH 公用金鑰。

7.	選擇一個 [定價層]。請注意，預設會顯示建議的定價層，以查看所有設定選項，請按一下右上方的 [檢視全部]。

8. 考量下列因素並依照需要設定選用設定：

	1. 維持 [儲存體帳戶] 不變，以使用 VM 名稱建立新的儲存體帳戶。

	2. 維持 [可用性設定] 為 [未設定]。

	3. 此時請勿新增任何 [端點]。

9.	選擇或建立[資源群組](resource-group-portal.md)

10. 選擇 [訂用帳戶]

11. 選擇 [位置]


##建立裝載在此虛擬機器中的資料庫
依照[在 Azure 中建立 Oracle Database 12c 虛擬機器](virtual-machines-creating-oracle-database-virtual-machine.md) 中，從**在 Azure 中使用 Oracle Database 12c 虛擬機器建立您的資料庫**小節開始的指示。

##設定裝載在此虛擬機器中的 Oracle WebLogic Server 12c
依照[在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md)中，從**在 Azrue 中設定 Oracle WebLogic Server 12c 虛擬機器**小節開始的指示。如果您想要設定 WebLogic Server 叢集，也請您參閱[在 Azure 中建立 Oracle WebLogic Server 12c 叢集](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md)。

##其他資源
[Oracle 虛擬機器映像 - 其他考量](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[適用於 Azure 的 Oracle 虛擬機器映像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[從 Java 應用程式連線到 Oracle 資料庫](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Microsoft Azure 上使用 Linux 的 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=July15_HO2-->