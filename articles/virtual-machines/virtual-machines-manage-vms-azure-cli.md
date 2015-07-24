<properties
   pageTitle="使用適用於 Mac、Linux 和 Windows 的 Azure CLI 管理 Azure VM | Microsoft Azure"
   description="描述如何使用適用於 Mac、Linux 和 Windows 的 Azure CLI 建立、管理以及刪除 Azure VM。"
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# 使用適用於 Mac、Linux 和 Windows 的 Azure CLI 管理虛擬機器

當您進行 VM 的日常管理時，使用 Azure CLI 可以自動完成很多工作。這篇文章提供了幾個簡單工作的範例命令，另外也提供顯示用來完成更複雜的工作之命令的文章連結。

>[AZURE.NOTE]如果您尚未安裝和設定 Azure CLI，請按一下[這裡](../xplat-cli-install.md)以取得操作方法。如果您想要 PowerShell 中相同工作的快速入門，請參閱[使用 Azure PowerShell 管理 VM](virtual-machines-manage-vms-powershell.md)。

## 如何使用範例命令
命令中的某些文字必須換成適合您環境的文字。 < and > 符號表示您需要取代的文字。當您取代文字時，請移除符號，但將引號保留在原處。

> [AZURE.NOTE]如果您想要以程式設計方式儲存和操作主控台命令的輸出，可以使用 JSON 剖析工具，例如 **[jq](https://github.com/stedolan/jq)**、**[jsawk](https://github.com/micha/jsawk)** 或工作適合的語言程式庫。

## 顯示 VM 的相關資訊

這是您會經常使用的基本工作。使用它來取得 VM 的相關資訊、在 VM 上執行工作，或取得輸出以儲存至變數中。

若要取得 VM 的相關資訊，請執行這個命令並取代引號中的任何內容，包括 < and > 字元：

     azure vm show -g <group name> -n <virtual machine name>

若要將 $vm 變數中的輸出儲存為 JSON 文件，請執行：

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

或者，您可以透過管道將 stdout 傳送至檔案。

## 登入 Linux 型虛擬機器

通常 Linux 機器是透過 SSH 連接的。如需詳細資訊，請參閱[如何在 Azure 上的 Linux 使用 SSH](virtual-machines-linux-use-ssh-key.md)。Azure Resource Manager 概觀
## 停止 VM

請執行這個命令：

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]萬一它是雲端服務的最後一個 VM，您可以使用這個參數來保留雲端服務的虛擬 IP (VIP)。<br><br> 如果使用 StayProvisioned 參數，還是需要支付 VM 的費用。

## 啟動 VM

請執行這個命令：Azure 資源管理員概觀 azure vm start <group name> <virtual machine name>

## 附加資料磁碟

您也需要決定是否要附加新的磁碟或附加已經包含資料的磁碟。如果是新的磁碟，這個命令會建立 .vhd 檔案，然後將它附加在同一個命令中。

若要附加新的磁碟，請執行這個命令：

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

若要附加現有的資料磁碟，請執行這個命令：

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## 建立 Linux VM

若要建立新的 Linux 型 VM，您需要準備好幾個值，包括資源群組名稱、位置、映像名稱、VM 名稱以及儲存體帳戶，用來儲存 .vhd 備份映像。需要的資訊準備齊全之後，Azure CLI 會建立互動式工作階段，提示您輸入這些值：

    azure vm create

當然，如果您已經有這些值，就可以找到適當的參數，直接傳遞給它們，請輸入 `azure help vm create`：

## 後續步驟

如需其他有關 Azure CLI 搭配 **arm** 模式使用的範例，請參閱[搭配 Azure 資源管理使用適用於 Mac、Linux 和 Windows 的 Microsoft Azure CLI](../xplat-cli-resource-manager.md)。若要深入了解 Azure 資源和概念，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。
 

<!---HONumber=July15_HO2-->