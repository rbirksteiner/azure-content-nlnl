<properties 
	pageTitle="教學課程：建立純雲端虛擬網路" 
	description="了解如何在此教學課程中建立範例純雲端 Azure 虛擬網路。" 
	services="virtual-machines, virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="cherylmc"/>

# 教學課程：在 Azure 中建立純雲端虛擬網路

本教學課程將逐步引導您在 Azure 管理入口網站中，建立包含兩個子網路的純雲端 Azure 虛擬網路範例。產生的虛擬網路看起來如下：

![createvnet](./media/create-virtual-network/createVNet_06_VNetExample.png)

例如，FrontEndSubnet 可用於 Web 伺服器，而 BackEndSubnet 可用於 SQL Server 或網域控制站。

本教學課程假設您先前沒有使用 Azure 的經驗。這能夠藉由逐步引導您進行範例設定，協助您熟悉建立自己的虛擬網路所需的步驟。如果您想要建立純雲端虛擬網路以適用於您特定的組態，請參閱[在管理入口網站中設定純雲端虛擬網路](http://msdn.microsoft.com/library/azure/dn631643.aspx)。如果您需要虛擬網路的設計案例和進階資訊，請參閱[虛擬網路](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)。


> [AZURE.NOTE]本教學課程不會逐步引導您建立跨單位組態，其中的虛擬網路會連接至您的組織網路。如需逐步引導您使用跨部署連線和網站對網站 VPN 連線 (亦即連線到位在公司內部的 Active Directory 或 SharePoint) 建立虛擬網路的教學課程，請參閱[教學課程：建立網站對網站連線的跨部署虛擬網路](../virtual-network/virtual-networks-create-site-to-site-cross-premises-connectivity.md)。


##  目標

在本教學課程中，您將了解如何設定含兩個子網路的基本 Azure 純雲端虛擬網路。

##  必要條件

*  一個 Microsoft 帳戶，其中至少有一個有效作用中的 Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以在[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 註冊免費試用版。如果您擁有 MSDN 訂用帳戶，請參閱 [Microsoft Azure 特價：MSDN、MPN 及 Bizspark 優惠](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

##  建立適用於本教學課程的虛擬網路

若要建立這個純雲端虛擬網路範例，請執行下列動作：

1. 登入管理入口網站。

2. 在畫面左下角，按一下 [新增] > [網路服務] > [虛擬網路]，然後按一下 [自訂建立] 開始組態精靈。

	![][Image1]

3. 在 [虛擬網路詳細資料] 頁面上，輸入下列資訊：

- **名稱 -** 輸入 **YourVirtualNetwork**。

- **區域 -** 將在位於指定區域的資料中心內建立虛擬網路。為了獲得最佳效能，請從下拉式清單中選取您所屬的區域。
 

	![][Image2]

4. 按一下右下角的下一步箭頭。如需此頁面中設定的詳細資訊，請參閱[關於管理入口網站中的虛擬網路設定](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)的＜虛擬網路詳細資料＞頁面一節。

5. 在 [DNS 伺服器和 VPN 連線能力] 頁面上，按一下右下角的下一步箭頭。Azure 會將具備網際網路架構的 Azure DNS 伺服器指派至已加入此虛擬網路的新虛擬機器，讓這些虛擬機器能夠存取網際網路資源。如需本頁面中設定的詳細資訊，請參閱[關於管理入口網站中的虛擬網路設定](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)的＜DNS 伺服器和 VPN 連線能力＞頁面。
	
6.	就像實際的網路一樣，虛擬網路需要一個 IP 位址範圍 (稱為位址空間) 來指派至您放置於其中的虛擬機器。此虛擬網路也支援子網路，這些子網路需要它們自己的位址空間 (衍生自虛擬網路位址空間)。針對本教學課程，我們將建立 BackEndSubnet 和 FrontEndSubnet。在 [虛擬網路位址空間] 頁面上，輸入下列資訊：

	- 針對位址空間，在 [CIDR (位址計數)] 中選取 [/16 (65535)]。

	- 針對子網路，在第一列的現有名稱上方輸入 **BackEndSubnet**，並針對起始 IP 輸入 **10.0.1.0**，然後在 [CIDR (位址計數)] 中選取 [/24 (256)]。按一下 [加入子網路]，然後針對名稱輸入 **FrontEndSubnet**，並針對起始 IP 輸入 **10.0.2.0**。
		
	![][Image4]

 回到我們的虛擬網路圖表，您已設定了下列位址空間：
 
	
- FrontEndSubnet：10.0.2.0/24
- BackEndSubnet：10.0.1.0/24

 如需此頁面中設定的詳細資訊，請參閱[關於管理入口網站中的虛擬網路設定](http://go.microsoft.com/fwlink/p/?linkid=248092&clcid=0x409)的＜虛擬網路位址空間＞頁面。


7. 按一下頁面右下角的核取記號，即會開始建立您的虛擬網路。建立了您的虛擬網路之後，您將在 Azure 管理入口網站的 [網路] 頁面上看見 [狀態] 下列出 [已建立]。  

	![][Image5]

您可以繼續透過下列內容來了解 Azure 基礎架構服務：

- [如何建立自訂虛擬機器](virtual-machines-create-custom.md) - 使用本主題，在您的虛擬網路上安裝虛擬機器。如需虛擬機器和安裝選項的詳細資訊，請參閱 [Azure 虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)。

- [在 Azure 虛擬網路上安裝新的 Active Directory 樹系](../active-directory-new-forest-virtual-machine.md) - 使用本主題安裝新的 Windows Server Active Directory (AD) 樹系，而完全不連線到其他任何網路。教學課程將說明對於新的樹系安裝建立虛擬機器 (VM) 所需的特定步驟。如果您計劃使用這個教學課程，請勿使用管理入口網站建立任何 VM。如需詳細資訊，請參閱[在 Azure 虛擬機器中部署 Windows Server Active Directory 的指導方針](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)。

若要移除這個虛擬網路，可選取它、按一下 [刪除]，然後按一下 [是]。

如果您準備好建立純雲端虛擬網路以適用於您特定的組態，請參閱[在管理入口網站中設定純雲端虛擬網路](http://msdn.microsoft.com/library/azure/dn631643.aspx)。

如果您需要虛擬網路的設計案例和進階資訊，請參閱[虛擬網路](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)。

如需其他虛擬網路組態程序和設定，請參閱 [Azure 虛擬網路組態工作](http://go.microsoft.com/fwlink/p/?linkid=296652&clcid=0x409)。


## 另請參閱

-  [Azure 虛擬網路常見問題集](http://go.microsoft.com/fwlink/p/?LinkId=296650)

-  [Azure 虛擬網路組態工作](http://go.microsoft.com/fwlink/p/?LinkId=296652)

-  [使用網路組態檔設定虛擬網路](../virtual-network/virtual-networks-using-network-configuration-file.md)

-  [VM 與角色執行個體的名稱解析](http://go.microsoft.com/fwlink/?LinkId=248097)


[Image1]: ./media/create-virtual-network/createVNet_01_OpenVirtualNetworkWizard.png
[Image2]: ./media/create-virtual-network/createVNet_02_VirtualNetworkDetails.png
[Image3]: ./media/create-virtual-network/createVNet_03_DNSServersandVPNConnectivity.png
[Image4]: ./media/create-virtual-network/createVNet_04_VirtualNetworkAddressSpaces.png
[Image5]: ./media/create-virtual-network/createVNet_05_VirtualNetworkCreatedStatus.png
[Image7]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png
[Image8]: ./media/create-virtual-network/createVNet_07_VNetExampleSpaces.png

 

<!---HONumber=July15_HO1-->