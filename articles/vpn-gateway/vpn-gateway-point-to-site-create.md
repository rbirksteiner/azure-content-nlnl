<properties
   pageTitle="設定點對站 VPN 連線到 Azure 虛擬網路"
   description="建立點對站 VPN 連線來連接到您的虛擬網路安全性。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2015"
   ms.author="cherylmc"/>

# 設定點對站 VPN 連線到 Azure 虛擬網路

雖然點對站連線的設定需採取多個步驟才能完成，不過，在電腦和虛擬網路間具備安全連線，而不取得及設定 VPN 裝置，仍不失為一個好方法。設定點對站 VPN 包含 3 個主要部分：虛擬網路和閘道、用於驗證的憑證，以及用來連線到虛擬網路的 VPN 用戶端。各個部分的設定順序極為重要，因此請勿略過或跳過任何步驟。

1. [設定虛擬網路和動態路由閘道](#configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [建立您的憑證](#create-your-certificates)
3. [設定 VPN 用戶端](#configure-your-VPN-client)

## 設定虛擬網路和動態路由閘道

點對站連線需要具備動態路由閘道的虛擬網路。以下步驟將逐步引導您完成上述兩者的建立作業。

### 建立虛擬網路

1. 登入**管理入口網站**。
1. 按一下螢幕左下角的 [新增]。在導覽窗格中依序按一下 [網路服務] 和 [虛擬網路]。按一下 [Custom Create] 開始組態精靈。
1. 在 [虛擬網路詳細資料] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需詳細資料頁面上設定的詳細資訊，請參閱[虛擬網路詳細資料頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails)。
	- **名稱**：為虛擬網路命名。例如 "VNetEast"。這是您將 VM 和 PaaS 執行個體部署到此 VNet 時參照的名稱。
	- **位置**：位置會與您要存放資源 (VM) 的實體位置 (區域) 直接相關。例如，如果您要讓部署到此虛擬網路的 VM 實際上位於 East US 中，請選取該位置。建立關聯之後，您就無法變更與虛擬網路相關聯的區域。
1. 在 [DNS Servers and VPN Connectivity] 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需詳細資訊，請參閱 [DNS 伺服器和 VPN 連線能力頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS)。
	- **DNS 伺服器**：輸入 DNS 伺服器名稱和 IP 位址，或從下拉式清單中選取先前註冊的 DNS 伺服器。此設定不會建立 DNS 伺服器，它可讓您指定您想要用於此虛擬網路的名稱解析的 DNS 伺服器。如果您想要使用 Azure 預設的名稱解析服務，請讓此區段保留空白。
	- **設定點對站 VPN**：勾選此核取方塊。
1. 在 [點對站連線能力] 頁面中，指定 VPN 用戶端在連線時將接收 IP 位址的 IP 位址範圍。關於可指定的位址範圍，您必須遵守幾項規則。請務必確認您指定的範圍未與內部部署網路中的任何範圍重疊。如需詳細資訊，請參閱[點對站連線能力頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT)。
1. 輸入下列資訊，然後按 [下一步] 箭頭。
 - **位址空間**：包含起始 IP 和 CIDR (位址計數)。
 - **加入位址空間**：唯有當您的網路設計需要時才需加入。
1. 在 [虛擬網路位址空間] 頁面上，指定您想要用於虛擬網路的位址範圍。這些是將指派給 VM 的動態 IP 位址 (DIP)，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址 (DIP)。虛擬網路位址空間有極多的相關規則，因此您也許希望參閱 [虛擬網路位址空間] 頁面來了解詳細資訊。特別重要的是，您選取的範圍不得與用於內部部署網路的任何範圍重疊。您將需要與網路管理員協調，商討如何從內部部署網路位址空間中切割出一段 IP 位址範圍，以供虛擬網路之用。
1. 輸入下列資訊，然後按一下勾號，開始建立您的虛擬網路。
 - **位址空間**：加入要用於此虛擬網路的內部 IP 位址範圍，包括起始 IP 和計數。虛擬網路位址空間有極多的相關規則，因此您也許希望參閱[虛擬網路位址空間頁面](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS)來了解詳細資訊。特別重要的是，您選取的範圍不得與用於內部部署網路的任何範圍重疊。您將需要與網路管理員協調，商討如何從內部部署網路位址空間中切割出一段 IP 位址範圍，以供虛擬網路之用。
 - **加入子網路**：不需要其他子網路，但您可能希望為將擁有靜態 DIP 的 VM 建立個別子網路。或者，您想要讓 VM 與其他角色執行個體位於不同的子網路。
 - **加入閘道子網路**：點對站 VPN 需要閘道子網路。按一下以加入閘道子網路。閘道子網路僅用於虛擬網路閘道。
1. 建立虛擬網路時，您將在管理入口網站的網路頁面上看見 [狀態] 下列出 [已建立]。待虛擬網路建立後，您便可建立動態路由閘道。

### 建立動態路由閘道

1. 在 [管理入口網站] 的 [網路] 頁面上，按一下您剛才建立的虛擬網路，然後瀏覽到 [儀表板] 頁面。
1. 按一下 [儀表板] 頁面底部的 [建立閘道]。畫面上將會出現一則訊息，詢問**您是否想要為虛擬網路 "yournetwork" 建立閘道**。按一下 [是] 即可開始建立閘道。建立閘道可能需要大約 15 分鐘。

## 建立您的憑證

憑證是用於點對站 VPN 之 VPN 用戶端驗證。此程序包含多個步驟。請使用以下連結，依序完成每個步驟。

1. [產生自我簽署的根憑證](#generate-a-self-signed-root-certificate) - 目前僅支援自我簽署的根憑證
2. [將根憑證檔案上傳至管理入口網站 ](#upload-the-root-certificate-file-to-the-Management-Portal)
3. [產生用戶端憑證 ](#generate-a-client-certificate)
4. [匯出及安裝用戶端憑證 ](#export-and-install-the-client-certificate)

### 建立自我簽署的根憑證

1. 建立 X.509 憑證的一個方式是使用憑證建立工具 (makecert.exe)。若要使用 Makecert，請下載並安裝免費的 [Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx)。
2. 瀏覽至 **Visual Studio Tools** 資料夾，然後以系統管理員身分啟動命令提示字元。
3. 以下範例中的命令會建立根憑證並安裝在電腦的個人憑證存放區，也會建立對應的 *.cer* 檔案，稍後您會將該檔案上傳至管理入口網站。
4. 將目錄切換至要放置 .cer 檔案的目錄，然後執行以下所列的命令，其中 *RootCertificateName* 是用於憑證的名稱。如果您依原樣執行以下的範例，就會產生根憑證和對應的 *RootCertificateName.cer* 檔案。

>[AZURE.NOTE]因為您已經建立會產生用戶端憑證的根憑證，您可能會想要將此憑證及其私密金鑰匯出，並將它儲存到可復原的安全位置。

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### 將根憑證檔案上傳至管理入口網站

1. 在先前的程序中，當您產生自我簽署的根憑證時，也會建立 *.cer* 檔案。現在您要將該檔案上傳至管理入口網站。請注意，.cer 檔案未包含根憑證的私密金鑰。
1. 在管理入口網站中，於虛擬網路的 [憑證] 頁面上，按一下 [上傳根憑證]。
1. 在 [上傳憑證] 頁面上，瀏覽到 .cer 根憑證，然後按一下勾號。

### 產生用戶端憑證

1. 在用來建立自我簽署之根憑證的相同電腦上，以系統管理員身分開啟 [Visual Studio 命令提示字元] 視窗。
2. 將目錄切換至您要儲存用戶端憑證檔案的位置。*RootCertificateName* 是指您產生的自我簽署根憑證。如果您執行以下範例 (將 RootCertificateName 變更為根憑證的名稱)，就會在個人憑證存放區中產生一個名為 "ClientCertificateName" 的用戶端憑證。
3. 輸入以下命令：

    makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. 所有憑證都會儲存在電腦上的個人憑證存放區中。檢查 *certmgr* 來驗證。您可以視需要根據這個程序來產生許多用戶端憑證。建議您最好針對要連接到虛擬網路的每部電腦建立唯一的用戶端憑證。

### 匯出及安裝用戶端憑證

1. 您想要連接到虛擬網路的每一部電腦都必須安裝用戶端憑證。這表示您可能要建立多個用戶端憑證，並且需要匯出這些憑證。若要匯出用戶端憑證，請使用 *certmgr.msc*。以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。
2. 匯出具有私密金鑰的「用戶端憑證」。這將是 *.pfx* 檔。請務必記下或牢記您為這個憑證設定的密碼 (金鑰)。
3. 將 *.pfx* 檔案複製到用戶端電腦。在用戶端電腦上按兩下 *.pfx* 檔案，即可進行安裝。在系統要求時輸入密碼。請勿修改安裝位置。

## 設定 VPN 用戶端

若要連接到虛擬網路，您還需要設定 VPN 用戶端。用戶端需要具備用戶端憑證及適當的 VPN 用戶端組態，才能順利連接。

### 建立 VPN 用戶端組態封裝

1. 在管理入口網站中，於虛擬網路的 [儀表板] 頁面上，瀏覽到右邊角落的 [快速概覽] 功能表，然後按一下與您想要連接到虛擬網路之用戶端有關的 VPN 封裝。以下為支援的用戶端作業系統：
 - Windows 7 (32 位元和 64 位元)
 - Windows Server 2008 R2 (僅限 64 位元)
 - Windows 8 (32 位元和 64 位元)
 - Windows 8.1 (32 位元和 64 位元)
 - Windows Server 2012 (僅限 64 位元)
 - Windows Server 2012 R2 (僅限 64 位元)

1. 選取對應至即將安裝之目標用戶端作業系統的下載封裝：
 - 如果是 32 位元用戶端，請選取 [下載 32 位元用戶端 VPN 封裝]
 - 如果是 64 位元用戶端，請選取 [下載 64 位元用戶端 VPN 封裝]
1. 建立您的用戶端封裝將需要幾分鐘的時間。一旦封裝完成之後，您就可以下載檔案。您下載的 *.exe* 檔案可以安全地儲存在本機電腦上。
1. 從管理入口網站產生及下載 VPN 用戶端封裝之後，您便可將用戶端封裝安裝至要連接到虛擬網路的用戶端電腦上。如果您計劃將 VPN 用戶端封裝安裝至多部用戶端電腦上，請確認這些電腦每部也都安裝了用戶端憑證。VPN 用戶端封裝含有設定 Windows 內建之 VPN 用戶端軟體所需的組態資訊。此封裝不會安裝其他軟體。

### 在用戶端安裝 VPN 組態封裝並啟動連線

1. 在本機將組態檔複製到您要連接到虛擬網路的電腦上，然後按兩下 .exe 檔案。一旦封裝安裝完成後，您就可以啟動 VPN 連線。請注意，組態封裝並非由 Microsoft 所簽署。您可能想要使用組織的簽署服務來簽署封裝，或是自行使用 [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764(v=vs.85).aspx) 來簽署。即使沒有簽署，還是可以使用封裝。不過，如果封裝未簽署，當您安裝封裝時將會出現警告。
2. 在用戶端電腦上瀏覽到 VPN 連線，然後找出剛建立的 VPN 連線。它的名稱將會與虛擬網路相同。按一下 [連接]。
3. 此時會出現一個快顯訊息，此訊息是用來針對閘道端點建立自我簽署的憑證。按一下 [繼續] 以使用較高的權限。
4. 在 [連線] 狀態頁面上，按一下 [連接] 以便開始連接。
5. 如果出現 [選取憑證] 畫面，請確認顯示的用戶端憑證是要用來連接的憑證。如果不是，請使用下拉箭頭來選取正確的憑證，然後按一下 [確定]。
6. 您現在已經連接到您的虛擬網路，而且對於虛擬網路中裝載的所有服務和虛擬機器都具有完整存取權。

### 驗證 VPN 連線

1. 若要驗證您的 VPN 連線為作用中狀態，請開啟提升權限的命令提示字元，並執行 *ipconfig/all*。
2. 檢視結果。請注意，您接收到的 IP 位址是建立 VNet 時所指定之點對站連線位址範圍內的其中一個位址。結果應該類似下面的內容：

範例：



    PPP adapter VNetEast:
		Connection-specific DNS Suffix .:
		Description.....................: VNetEast
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled



## 另請參閱


您可以進一步了解本文中虛擬網路的跨單位連線：[關於虛擬網路安全的跨單位連線](https://msdn.microsoft.com/library/azure/dn133798.aspx)

如果您想要設定站對站 VPN 連線，請參閱[設定站對站 VPN 連線](vpn-gateway-site-to-site-create.md)

您可以將虛擬機器加入您的虛擬網路。請參閱[如何建立自訂虛擬機器](../virtual-machines/virtual-machines-create-custom.md)

如果您想要使用 RRAS 設定 VNet 連線，請參閱 [使用 Windows Server 2012 路由及遠端存取服務 (RRAS) 設定站對站 VPN](https://msdn.microsoft.com/library/dn636917.aspx)
 

<!---HONumber=July15_HO2-->