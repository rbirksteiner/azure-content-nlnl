<properties 
	pageTitle="疑難排解存取在 Azure 虛擬機器上執行的應用程式" 
	description="如果您無法存取在 Azure 虛擬機器上執行的應用程式，請使用下列步驟釐清問題的來源。"
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="josephd"/>

# 疑難排解存取在 Azure 虛擬機器上執行的應用程式

如果您無法存取在 Azure 虛擬機器上執行的應用程式，本文將說明以井然有序的方式，釐清問題的來源並更正。

> [AZURE.NOTE]如需連接到 Azure 虛擬機器的說明，請參閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)或[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)。

有四個主要區域來疑難排解在 Azure 虛擬機器執行上之應用程式的存取。

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)
 
1.	在 Azure 虛擬機器上執行的應用程式。
2.	Azure 虛擬機器
3.	Azure 雲端服務的端點包含虛擬機器 (適用於服務管理中建立的虛擬機器)、輸入 NAT 規則 (適用於資源管理員中建立的虛擬機器)，以及網路安全性群組。
4.	您的網際網路邊緣裝置。

針對正透過站對站 VPN 或 ExpressRoute 連線存取應用程式的用戶端電腦，可能造成問題的主要區域是應用程式和 Azure 虛擬機器。若要判斷問題的來源並更正，請遵循下列步驟。

## 步驟 1：您可以從目標虛擬機器存取應用程式嗎？

請使用本機主機名稱、本機 IP 位址，或迴路位址 (127.0.0.1)，嘗試以適當的用戶端程式從正在執行應用程式的虛擬機器存取應用程式。
 
![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

例如，如果應用程式是 Web 伺服器，則在虛擬機器上執行瀏覽器，並嘗試存取在虛擬機器上託管的網頁。

如果您可以存取應用程式，請移至[步驟 2](#step2)。

如果您無法存取應用程式，請檢查下列項目：

- 應用程式正在目標虛擬機器上執行。
- 應用程式正在接聽預期的 TCP 和 UDP 連接埠。

在 Windows 和 Linux 虛擬機器兩者上，使用 **netstat -a** 命令顯示作用中的接聽連接埠。檢查應用程式應該接聽之預期連接埠的輸出。重新啟動應用程式，或將它設定使用預期的連接埠 (如果有需要)。

## <a id="step2"></a>步驟 2：您可以從相同虛擬網路中的另一部虛擬機器存取應用程式嗎？

請使用正在執行應用程式之虛擬機器使用的虛擬機器主機名稱、或 Azure 指派之公開、私人，或提供者 IP 位址，嘗試從相同虛擬網路中的其他虛擬機器存取應用程式。針對在服務管理中建立的虛擬機器，請勿使用雲端服務的公開 IP 位址。
 
![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

例如，如果應用程式是 Web 伺服器，請嘗試在相同虛擬網路中其他的虛擬機器使用瀏覽器存取網頁。

如果您可以存取應用程式，請移至[步驟 3](#step3)。

如果您無法存取應用程式，請檢查下列項目：

- 目標虛擬機器上的主機防火牆允許輸入要求與輸出回應的流量。
- 在目標虛擬機器上執行的入侵偵測或網路監視軟體允許流量。
- 網路安全性群組允許流量。
- 在您的虛擬網路中，虛擬機器與測試虛擬機器間的路徑執行的個別元件 (例如負載平衡器或防火牆) 允許流量。

在 Windows 虛擬機器上，請使用「具有進階安全性的 Windows 防火牆」判斷防火牆規則是否排除了您應用程式的輸入與輸出流量。

## <a id="step3"></a>步驟 3：您可以從位於虛擬網路之外，但連接到與您電腦不同網路的電腦存取應用程式嗎？

請嘗試從位於虛擬網路之外執行應用程式之虛擬機器的電腦存取應用程式，但該電腦與您原始用戶端的電腦在不同的網路上。

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)
 
例如，如果應用程式是 Web 伺服器，請嘗試從虛擬網路外的電腦執行瀏覽器來存取網頁。

如果您無法存取應用程式，請檢查下列項目：

- 針對服務管理中建立的虛擬機器，其虛擬機器的端點組態允許連入流量，特別是通訊協定 (TCP 或 UDP) 和公開與私人連接埠號碼。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。
- 針對在服務管理中建立的虛擬機器，其端點上的存取控制清單 (ACL) 未阻擋來自網際網路的流量。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。
- 針對資源管理員中建立的虛擬機器，其虛擬機器的輸入 NAT 規則組態允許連入流量，特別是通訊協定 (TCP 或 UDP) 和公開與私人連接埠號碼。 
- 網路安全性群組允許輸入要求與輸出回應的流量。如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](virtual-networks-nsg.md)。

如果虛擬機器或端點是負載平衡集的成員：

- 請確認探查通訊協定 (TCP 或 UDP) 和連接埠號碼正確。
- 如果探查通訊協定和連接埠與負載平衡集通訊協定和連接埠不同：
	- 請確認應用程式正在接聽探查通訊協定 (TCP 或 UDP) 和連接埠號碼 (在目標虛擬機器使用 **netstat –a**)。
	- 目標虛擬機器上的主機防火牆允許輸入探查要求與輸出探查回應的流量。

如果您可以存取應用程式，請確定您的網際網路邊緣裝置允許：

- 從您的用戶端電腦輸出到 Azure 虛擬機器的應用程式要求流量。
- 來自 Azure 虛擬機器的輸入應用程式回應流量。

## 後續步驟

如果您已經執行本文章的步驟 1 到 3，且需要其他協助以更正問題，您可以：

- 取得來自世界各地的 Azure 專家協助。提交您的問題至 MSDN Azure 或 Stack Overflow 論壇。如需詳細資訊，請參閱 [Microsoft Azure 論壇](http://azure.microsoft.com/support/forums/)。
- 提出 Azure 支援事件。請移至 [Azure 支援網站](http://azure.microsoft.com/support/options/)，然後在「技術與帳務支援」之下按一下 [取得支援]。

## 其他資源

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=July15_HO2-->