<properties
   pageTitle="Windows 上的自訂指令碼延伸模組"
   description="在 Windows 上使用自訂指令碼延伸模組來將 Azure 虛擬機器組態工作自動化"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2015"
   ms.author="kundanap"/>

# Windows 的自訂指令碼延伸模組

本文提供使用 Azure Powershell Cmdlet，在 Windows 上使用自訂指令碼延伸模組的概觀。


Microsoft 和受信任的協力廠商發行者建置的虛擬機器 (VM) 延伸模組，可延伸 VM 的功能。如需 VM 延伸模組的詳細概觀，請參閱 <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">MSDN 文件</a>。

## 自訂指令碼延伸模組概觀

Windows 的自訂指令碼延伸模組讓您能夠在遠端虛擬機器上執行 Powershell 指令碼，而不需登入它。指令碼可以在佈建 VM 之後執行，或者在 VM 生命週期期間隨時執行，而不需在該 VM 上開啟任何其他連接埠。自訂指令碼的最常用案例包含在 VM 後續佈建上安裝和設定其他軟體。

### 執行自訂指令碼延伸模組的先決條件

1. 從<a href="http://azure.microsoft.com/downloads" target="_blank">這裡</a>安裝 Azure PowerShell Cmdlets V0.8.0 或以上版本。
2. 如果指令碼將在現有 VM 上執行，請確定會在該 VM 上啟用 VM 代理程式，如果沒有，請依照這篇<a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">文章</a>安裝一個 VM。
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。指令碼可以來自單一或多個儲存體容器。
4. 指令碼應該以由延伸模組依序要啟動的項目指令碼啟動其他指令碼的方式來撰寫。

## 自訂指令碼延伸模組案例：

 ### 將檔案上傳到預設容器：如果訂用帳戶之預設帳戶的儲存體容器中含有您的指令碼，則下列 Cmdlet 程式碼片段會示範如何在 VM 上執行它們。下列範例中的 ContainerName 是您上傳的指令碼。預設的儲存體帳戶可以使用 ‘Get-AzureSubscription –Default’ Cmdlet 來驗證

注意：這個使用案例會建立新的 VM，但相同作業也可以在現有的 VM 上完成。

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### 將檔案上傳到非預設的儲存體容器。

這個使用案例示範如何使用相同訂閱內或不同訂閱中的非預設儲存體上傳指令碼/檔案。此處將使用現有的 VM，但相同作業也可以在建立新的 VM 時完成。

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### 將指令碼上傳到橫跨不同儲存體帳戶的多個容器。如果指令碼檔案儲存在多個容器中，則目前若要執行這些指令碼，您必須提供這些檔案的完整 SAS URL。

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### 從入口網站加入自訂指令碼延伸模組。
瀏覽至 <a href="https://portal.azure.com/ " target="_blank">Azure Preview 入口網站</a>中的虛擬機器，然後藉由指定要執行的指令碼檔案來加入該延伸模組。![][5]

  ### 解除安裝自訂指令碼延伸模組。

您可以使用下列 Cmdlet，從 VM 解除安裝自訂指令碼延伸模組

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### 敬請期待

我們即將新增適用於 Linux 的自訂指令碼用法和範例，敬請期待。

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png
 

<!---HONumber=July15_HO1-->