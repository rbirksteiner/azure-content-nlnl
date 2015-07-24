<properties
   pageTitle="Azure 資源管理員的負載平衡器支援預覽版 | Microsoft Azure"
   description="在預覽中，搭配使用適用於負載平衡器的 PowerShell 與 Azure 資源管理員 (ARM)在負載平衡器中使用範本"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />


# Azure 資源管理員的 Azure 負載平衡器支援 

Azure 資源管理員 (ARM) 是 Azure 中新的服務管理架構。Azure 負載平衡器現在可使用以 Azure 資源管理員為基礎的 API 和工具來進行管理。若要深入了解 Azure 資源管理員，請參閱[使用資源群組管理您的 Azure 資源](../azure-preview-portal-using-resource-groups.md)。

## 概念

使用 ARM，Azure 負載平衡器包含下列子資源：

- 前端 IP 組態 - 負載平衡器可以包括一個或多個前端 IP 位址 (亦稱為虛擬 IP (VIP))。這些 IP 位址做為流量的輸入。

- 後端位址集區 - 這些是與虛擬機器網路介面卡 (NIC) 相關聯的 IP 位址，而負載會散發到虛擬機器網路介面卡。

- 負載平衡規則 - 規則屬性會將指定的前端 IP 與連接埠組合對應到一組後端 IP 位址與連接埠組合。使用負載平衡器資源的單一定義，您可以定義多個負載平衡規則，而每個規則都會反映與 VM 相關聯的前端 IP 與連接埠以及後端 IP 與連接埠組合。

- 探查 - 探查可讓您追蹤 VM 執行個體的健全狀況。如果健全狀況探查失敗，則 VM 執行個體不會自動進入輪替。

- 輸入 NAT 規則 - 定義流經前端 IP 並散發到後端 IP 之輸入流量的 NAT 規則。


![](https://acomdpsstorage.blob.core.windows.net/dpsmedia-prod/azure.microsoft.com/zh-tw/documentation/articles/resource-groups-networking/20150429054039/figure5.png)



## 快速入門範本
Azure 資源管理員可讓您使用宣告式範本佈建應用程式。在單一的範本中，您可以部署多個服務及其相依性。您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式

範本包含虛擬機器、虛擬網路、可用性設定組、網路介面 (NIC)、儲存體帳戶、負載平衡器、網路安全性群組和公開 IP。有了範本之後，您可以使用一個可以簽入及共同作業的簡單檔案，來建立複雜應用程式所需的所有項目。

[深入了解範本](http://go.microsoft.com/fwlink/?LinkId=544798)

[深入了解網路資源](../resource-groups-networking)

您可以在 [GitHub 儲存機制](https://github.com/Azure/azure-quickstart-templates) (裝載了一組社群產生的範本) 中找到使用 Azure 負載平衡器的範本

範本的範例：

- [負載平衡器中的 2 部 VM 和負載平衡規則](http://go.microsoft.com/fwlink/?LinkId=544799)

- [搭配內部負載平衡器的 VNET 中的 2 部 VM 和負載平衡器規則](http://go.microsoft.com/fwlink/?LinkId=544800)

- [負載平衡器中的 2 部 VM，並在 LB 上設定 NAT 規則](http://go.microsoft.com/fwlink/?LinkId=544801)


## 使用 PowerShell 或 CLI 設定 Azure 負載平衡器

[Azure 網路 Cmdlet](https://msdn.microsoft.com/library/azure/mt163510.aspx) 可用來建立負載平衡器。開始使用 ARM Cmdlet 和 REST API

- [如何使用 Azure 資源管理員建立負載平衡器](../load-balancer-arm-powershell)

- [搭配使用 Azure 資源管理與 Azure CLI](../xplat-cli-azure-resource-manager)

- [負載平衡器 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## 另請參閱

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->