<properties 
	pageTitle="教學課程：建立站對站連線的跨單位虛擬網路" 
	description="了解如何在此教學課程中使用跨單位連線建立 Azure 虛擬網路。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="cherylmc"/>



# 教學課程：建立站對站連線的跨單位虛擬網路

本教學課程將逐步引導您建立具有站對站連線的跨單位虛擬網路範例。

如果您想要建立純雲端虛擬網路，請參閱[教學課程：在 Azure 中建立純雲端虛擬網路](../virtual-machines/create-virtual-network.md)。如果您想要使用憑證和 VPN 用戶端來建立點對網站 VPN，請參閱[使用管理入口網站精靈設定點對站 VPN](http://go.microsoft.com/fwlink/p/?LinkId=296653)。

本教學課程假設您先前沒有使用 Azure 的經驗。目的在於協助您熟悉建立範例跨單位虛擬網路所需的步驟。如果您在尋找有關虛擬網路的設計案例和進階資訊，請參閱[虛擬網路](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)。

完成本教學課程之後，您將具有範例跨單位虛擬網路。下圖顯示以本教學課程中的範例設定為基礎的詳細資料。

![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_12_TutorialCrossPremVNet.png)

如需此圖和可用來描繪您自身跨單位虛擬網路的圖表複本，請參閱[教學課程主題中的跨單位虛擬網路圖範例](http://gallery.technet.microsoft.com/Example-cross-premises-e5ecb8bb)。

請注意，本教學課程中使用的組態設定範例，並不是針對您組織的網路所自訂。若您使用本主題所述的組態設定範例，設定虛擬網路及站對站連線，會無法有效運作。若要設定有效的跨單位虛擬網路，您必須和 IT 部門及網路管理員合作，以取得正確的設定。如需詳細資訊，請參閱本主題的＜必要條件＞一節。

如需新增虛擬機器，以及將內部部署 Active Directory 延伸到 Azure 虛擬網路的相關資訊，請參閱以下內容：

-  [如何自訂建立虛擬機器](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制站](http://go.microsoft.com/fwlink/p/?LinkId=299877)

如需在 Azure 虛擬機器上部署 AD DS 的指導方針，請參閱[在 Azure 虛擬機器中部署 Windows Server Active Directory 的指導方針](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)。

如需其他虛擬網路組態程序和設定，請參閱 [Azure 虛擬網路組態工作](http://go.microsoft.com/fwlink/p/?LinkId=296652)。

##  目標

在本教學課程中，您將了解：

-  如何設定可加入 Azure 服務的跨單位 Azure 虛擬網路範例。

-  如何設定與組織網路通訊的虛擬網路。

##  必要條件

-  一個 Microsoft 帳戶，其中至少有一個有效作用中的 Azure 訂用帳戶。如果您還沒有 Azure 訂用帳戶，則可以在[試用 Azure](http://azure.microsoft.com/pricing/free-trial/) 註冊免費試用版。如果您擁有 MSDN 訂用帳戶，請參閱 [Microsoft Azure 特價：MSDN、MPN 及 Bizspark 優惠](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

若您要使用本教學課程，設定為組織自訂且能有效運作的跨單位虛擬網路，您需要下列項目：

-  虛擬網路及其子網路的私人 IPv4 位址空間 (使用 CIDR 標記法)。

-  內部部署 DNS 伺服器的名稱及 IP 位址。

-  具有公用 IPv4 位址的 VPN 裝置。您需要 IP 位址才能完成精靈。VPN 裝置不能位於網路位址轉譯器 (NAT) 後方，且必須符合最低裝置標準。如需詳細資訊，請參閱[關於虛擬網路的 VPN 裝置](http://go.microsoft.com/fwlink/p/?LinkID=248098)。

	注意：您可以使用 Windows Server 中的路由及遠端存取服務 (RRAS) 做為 VPN 解決方案的一部分。不過本教學課程不會逐步引導您進行 RRAS 的組態步驟。如需 RRAS 組態資訊，請參閱[路由及遠端存取服務範本](http://msdn.microsoft.com/library/windowsazure/dn133801.aspx)。

-  具備針對 IPsec 通道模式連線設定路由器的經驗，或可協助您完成此步驟的助手。

-  位址空間集 (使用 CIDR 標記法)，其中概括了您內部部署網路 (又稱為本機網路) 中可連線的位置。


## 高階步驟

1.	[建立虛擬網路](#CreateVN)

2.	[啟動閘道並收集網路管理員的資訊](#StartGateway)

3.  [設定 VPN 裝置](#ConfigVPN)

##  <a name="CreateVN">建立虛擬網路</a>

建立可連接公司網路的虛擬網路範例：

1.	登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。

2.	按一下螢幕左下角的 [新增]。在導覽窗格中依序按一下 [網路] 和 [虛擬網路]。按一下 [Custom Create] 開始組態精靈。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.	在 [虛擬網路詳細資料] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需詳細資料頁面中之設定的詳細資訊，請參閱**關於在管理入口網站中設定虛擬網路**的[虛擬網路詳細資料頁面](http://go.microsoft.com/fwlink/p/?LinkID=248092)一節。

	-  **名稱：**為虛擬網路命名。若為本教學課程的範例，請輸入 **YourVirtualNetwork**。

	-  **位置：**從下拉式清單中，選取想要的區域。將在指定區域中的 Azure 資料中心內建立虛擬網路。

	
4.	在 [DNS Servers and VPN Connectivity] 頁面中輸入以下資訊，然後按一下右下角的下一步箭頭。

> [AZURE.NOTE]您目前可以在此頁面中選取 [點對站] 和 [站對站] 組態。就本教學課程的目的，我們只選擇設定 [站對站]。如需此頁面設定的詳細資訊，請參閱**關於管理入口網站中的虛擬網路設定**中的 [DNS 伺服器和 VPN 連線能力](http://go.microsoft.com/fwlink/p/?LinkID=248092)頁面。

	-  **DNS SERVERS:** Enter the DNS server name and IP address that you want to use for name resolution. Typically this would be a DNS server that you use for on-premises name resolution. This setting does not create a DNS server. For the example in this tutorial, type **YourDNS** for the name and **10.1.0.4** for the IP address.
	-  **Configure Point-To-Site VPN:** Leave this field blank. 
	-  **Configure Site-To-Site VPN:** Select checkbox.
	-  **LOCAL NETWORK:** Select **Specify a New Local Network** from the drop-down list.
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

5.	在 [Site-To-Site Connectivity] 頁面中輸入以下資訊，然後按一下頁面右下角的核取記號。如需本頁面中之設定的詳細資訊，請參閱**關於在管理入口網站中設定虛擬網路**的[站對站連線能力](http://go.microsoft.com/fwlink/p/?LinkID=248092)頁面一節。 

	-  **名稱：**在本教學課程範例中，輸入 **YourCorpHQ**。

	-  **VPN 裝置 IP 位址：**在本教學課程範例中，輸入 **3.2.1.1**。否則，請輸入 VPN 裝置的公用 IP 位址。如果您缺少這項資訊，請先取得該資訊後再前往精靈的下一個步驟。請注意，VPN 裝置不得位於 NAT 後方。如需有關 VPN 裝置的詳細資訊，請參閱[關於虛擬網路的 VPN 裝置](http://msdn.microsoft.com/library/windowsazure/jj156075.aspx)。

	-  **位址空間：**在本教學課程範例中，輸入 **10.1.0.0/16**。
	-  **新增位址空間：**本教學課程不需要額外的位址空間。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

6.  在 [Virtual Network Address Spaces] 頁面中輸入以下資訊，然後按一下右下角的核取記號以設定網路。

	位址空間必須是以 CIDR 標記法指定的私人位址範圍 10.0.0.0/8、172.16.0.0/12 或 192.168.0.0/16 等位址空間 (如 RFC 1918 所指定)。如需此頁面中之設定的詳細資訊，請參閱**關於在管理入口網站中設定虛擬網路**的[虛擬網路位址空間頁面](http://go.microsoft.com/fwlink/?LinkID=248092)一節。

	-  **位址空間：**在本教學課程範例中，按一下右上角的 [CIDR]，然後輸入以下資訊：
		-  **起始 IP：**10.4.0.0
		-  **CIDR：** /16
	-  **新增子網路：**在本教學課程範例中，輸入下列資訊：
		-  將 **Subnet-1** 重新命名為 **FrontEndSubnet**，且起始 IP 為 **10.4.2.0/24**。
		-  加入稱為 **BackEndSubnet** 的子網路，且起始 IP 為 **10.4.3.0/24**。
		-  加入稱為 **ADDNSSubnet** 的子網路，且起始 IP 為 **10.4.4.0/24**。
		-  加入起始 IP 為 **10.4.1.0/24** 的閘道子網路。
	-  若為本教學課程的範例，請確認您現在已建立三個子網路和一個閘道子網路，然後按一下右下角的核取記號以建立虛擬網路。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

7.	按一下核取記號後，隨即會開始建立虛擬網路。建立虛擬網路時，您將在管理入口網站的網路頁面上看見 [狀態] 下列出 [已建立]。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

##  <a name="StartGateway">啟動閘道</a>

在建立 Azure 虛擬網路後，請使用以下程序來設定虛擬網路閘道，以便建立站台對站台 VPN。此程序須有滿足最低需求的 VPN 裝置。如需有關 VPN 裝置和裝置組態的詳細資訊，請參閱[關於虛擬網路的 VPN 裝置](http://go.microsoft.com/fwlink/p/?LinkID=248098)。

**若要啟動閘道：**

1.	當虛擬網路建立完成時，[網路] 頁面會將您的虛擬網路狀態顯示為 [已建立]。

	按一下 [名稱] 欄中的 [YourVirtualNetwork] (若為本教學課程中建立的範例) 以開啟儀表板。
 
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.	按一下頁面頂端的 [儀表板]。在 [儀表板] 頁面上，於頁面底部按一下 [CREATE GATEWAY]。針對您要建立的閘道類型選取 [動態路由] 或 [靜態路由]。

	請注意，除了站對站連線，如果您還想要將此虛擬網路用於點對站連線，必須選取 [動態路由] 做為閘道類型。在建立閘道之前，請確認 VPN 裝置可支援您要建立的閘道類型。請參閱[關於虛擬網路的 VPN 裝置](http://go.microsoft.com/fwlink/p/?LinkID=248098)。當系統提示您確認是否要建立閘道時，請按一下 [是]。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.	當閘道建立開始時，系統會顯示訊息來通知您閘道已啟動。

	閘道建立作業最多可能需要花費 15 分鐘的時間。

4.	待閘道建立完成後，您需要收集以下用來設定 VPN 裝置的資訊。

	-  閘道 IP 位址
	-  共用金鑰
	-  VPN 裝置組態指令碼範本

	接下來的步驟將逐步引導您完成此程序。

5.	若要找出閘道 IP 位址：閘道 IP 位址位於虛擬網路 [儀表板] 頁面。下列是一個範例：

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

6.	若要取得共用金鑰：共用金鑰位於虛擬網路 [儀表板] 頁面。按一下畫面底部的 [管理金鑰]，然後複製對話方塊所顯示的金鑰。您需要此金鑰才能為公司的 VPN 裝置設定 IPsec 通道。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

7.	若要下載 VPN 裝置組態指令碼範本：在儀表板上，按一下 [下載 VPN 裝置指令碼]。

8.	在 [Download a VPN Device Configuration Script] 對話方塊中，選取公司 VPN 裝置的廠商、平台及作業系統。按一下核取記號按鈕並儲存檔案。

	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

如果您在下拉式清單中找不到您的 VPN 裝置，請參閱 MSDN 文件庫中的[關於虛擬網路的 VPN 裝置](http://go.microsoft.com/fwlink/p/?LinkID=248098)，以取得其他指令碼範本。


##  <a name="ConfigVPN">設定 VPN 裝置 (網路管理員)</a>

由於 VPN 裝置不盡相同，因此本內容僅提供高階程序。此程序應交由網路管理員來執行。

您可以從管理入口網站或[關於虛擬網路的 VPN 裝置](http://go.microsoft.com/fwlink/p/?LinkId=248098)取得 VPN 組態指令碼，該文章亦說明與您選擇使用之路由組態相容的路由類型和裝置。

如需設定虛擬網路閘道的額外資訊，請參閱[在管理入口網站中設定虛擬網路閘道](http://go.microsoft.com/fwlink/p/?LinkId=299878)，並查閱 VPN 裝置文件。

此程序的假設如下：

-  設定 VPN 裝置的人員熟悉選用裝置的設定作業。由於與虛擬網路相容的裝置繁多，且每個裝置系列均有專用的組態，下列步驟不會逐步引導您完成詳細的裝置組態。因此，設定裝置的人員務必熟悉裝置和裝置的組態設定。 

-  您選擇使用的裝置與虛擬網路相容。請查閱[此處](http://go.microsoft.com/fwlink/p/?LinkID=248098)以了解裝置相容性。


**若要設定 VPN 裝置：**

1.	修改 VPN 組態指令碼。您需要設定以下內容：

	a.安全性原則

	b.連入通道

	c.連出通道

2.	執行經過修改的 VPN 組態指令碼以設定 VPN 裝置。

3.	執行以下任一命令以測試連線：

	<table border="1">
<tr>
<th>-</th>
<th>Cisco ASA</th>
<th>Cisco ISR/ASR</th>
<th>Juniper SSG/ISG</th>
<th>Juniper SRX/J</th>
</tr>

<tr>
<td><b>檢查主要模式 SA</b></td>
<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show crypto isakmp sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">get ike cookie</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show security ike security-association</FONT></td>
</tr>

<tr>
<td><b>檢查快速模式 SA</b></td>
<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show crypto ipsec sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">get sa</FONT></td>
<td><FONT FACE="courier" SIZE="-1">show security ipsec security-association</FONT></td>
</tr>
</table>


##  後續步驟
若要將內部部署 Active Directory 延伸到剛建立的虛擬網路，請繼續進行以下教學課程中的內容：

-  [如何自訂建立虛擬機器](http://go.microsoft.com/fwlink/p/?LinkID=294356)

-  [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制站](http://go.microsoft.com/fwlink/p/?LinkId=299877)

如果您想要將虛擬網路設定匯出到網路組態檔，以便備份組態或當做範本，請參閱[將虛擬網路設定匯出到網路組態檔](http://go.microsoft.com/fwlink/p/?LinkID=299880)。

## 另請參閱

-  [Azure 虛擬網路技術概觀](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [虛擬網路常見問題集](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [使用網路組態檔設定虛擬網路](virtual-networks-using-network-configuration-file.md)

-  [將虛擬機器新增至虛擬網路](../virtual-machines/virtual-machines-create-custom.md)

-  [關於虛擬網路的 VPN 裝置](http://msdn.microsoft.com/library/windowsazure/jj15] 75.aspx)

-  [VM 與角色執行個體的名稱解析](http://go.microsoft.com/fwlink/p/?LinkId=248097)
-  [設定用於測試的混合式雲端環境](virtual-networks-setup-hybrid-cloud-environment-testing.md)





 

<!---HONumber=July15_HO2-->