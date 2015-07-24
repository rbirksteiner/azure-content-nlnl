<properties
   pageTitle="使用 Azure CLI 建立多個 VM 的部署 | Azure"
   description="了解如何使用 Azure CLI 建立多個 VM 的部署"
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst;kasing"/>

# 使用 Azure CLI 建立多個 VM 的部署

下列指令碼將示範如何在 VNET 中，使用 Azure 命令列介面 (Azure CLI) 設定多個 VM 的多雲端服務部署。

下圖說明您的部署在指令碼完成之後的外觀：

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

指令碼會在雲端服務 **servercs** 中建立一個 VM (**servervm**) 並連接兩個資料磁碟，以及在雲端服務 **workercs** 中建立兩個 VM (**clientvm1、clientvm2**)。這兩個雲端服務都放置於 VNET **samplevnet** 中。**servercs** 雲端服務也已針對外部連線設定端點。

## 讓它成真的 CLI 指令碼
設定此功能的程式碼相當簡單：

>[AZURE.NOTE]您可能需要將雲端服務名稱 servercs 和 workercs 變更為唯一的雲端服務名稱

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-zh-tw-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small –n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-zh-tw-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-zh-tw-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

卸除此功能的程式碼也同樣簡單：

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*–q 選項會抑制刪除物件的互動式確認，-b 會清除與 VM 相關聯的磁碟 / Blob。*

## 所使用的一般命令格式

儘管您可以在任何 Azure CLI 命令上使用 –help 選項來找到更多資訊，但上述所使用之每個命令的一般格式如下：

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## 後續步驟


* [Azure 上的 Linux 和開放原始碼運算](virtual-machines-linux-opensource.md)
* [如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)
 

<!---HONumber=July15_HO2-->