<properties
    pageTitle="Een virtuele machine in Azure maken met behulp van PowerShell | Microsoft Azure"
    description="Gebruik Azure PowerShell en Azure Resource Manager om eenvoudig een nieuwe virtuele machine met Windows Server te maken."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/02/2016"
    ms.author="davidmu"/>

# Een virtuele Windows-machine maken met behulp van Resource Manager en PowerShell

Dit artikel laat zien hoe u met behulp van [Resource Manager](../resource-group-overview.md) en PowerShell snel een virtuele machine in Azure maakt met Windows Server, evenals de hiervoor benodigde resources. 

Alle stappen in dit artikel zijn vereist om een virtuele machine te maken. Het uitvoeren van deze stappen kost ongeveer 30 minuten.

## Stap 1: Azure PowerShell installeren

Zie [Azure PowerShell installeren en configureren](../powershell-install-configure.md) voor informatie over het installeren van de nieuwste versie van Azure PowerShell, het selecteren van het abonnement dat u wilt gebruiken en het aanmelden bij uw Azure-account.
        
## Stap 2: Een resourcegroep maken

Eerst maakt u een resourcegroep.

1. Haal een lijst op met beschikbare locaties waar resources kunnen worden gemaakt.

        Get-AzureLocation | sort Name | Select Name
        
    Deze lijst ziet er ongeveer zo uit:
    
        Name
        ----
        Australia East
        Australia Southeast
        Brazil South
        Central India
        Central US
        East Asia
        East US
        East US 2
        Japan East
        Japan West
        North Central US
        North Europe
        South Central US
        South India
        Southeast Asia
        West Europe
        West India
        West US

2. Vervang de waarde van **$locName** door een locatie in de lijst. Maak de variabele.

        $locName = "Central US"
        
3. Vervang de waarde van **$rgName** door de gewenste naam voor de nieuwe resourcegroep. Maak de variabele en de resourcegroep.

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Stap 3: Een opslagaccount maken

Een [opslagaccount](../storage/storage-introduction.md) is nodig voor het opslaan van de virtuele vaste schijf die wordt gebruikt door de virtuele machine die u maakt.

1. Vervang de waarde van **$stName** door de gewenste naam voor het opslagaccount. Test of de naam uniek is.

        $stName = "mystorage1"
        Test-AzureName -Storage $stName

    Als deze opdracht **onwaar** retourneert, is de voorgestelde naam uniek in Azure. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.
    
2. Voer nu de opdracht uit voor het maken van het opslagaccount.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Stap 4: Een virtueel netwerk maken

Alle virtuele machines maken deel uit van een [virtueel netwerk](../virtual-network/virtual-networks-overview.md).

1. Vervang de waarde van **$subnetName** door de gewenste naam voor het subnet. Maak de variabele en het subnet.
        
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Vervang de waarde van **$vnetName** door de gewenste naam voor het virtuele netwerk. Maak de variabele en het virtuele netwerk met het subnet.

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    Gebruik waarden die geschikt zijn voor uw toepassing en de omgeving.
        
## Stap 5: Een openbaar IP-adres en een netwerkinterface maken

Om te kunnen communiceren met de virtuele machine in het virtuele netwerk, hebt u een [openbaar IP-adres](../virtual-network/virtual-network-ip-addresses-overview-arm.md) en een netwerkinterface nodig.

1. Vervang de waarde van **$ipName** door de gewenste naam voor het openbare IP-adres. Maak de variabele en het openbare IP-adres.

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Vervang de waarde van **$nicName** door de gewenste naam voor de netwerkinterface. Maak de variabele en de netwerkinterface.

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Stap 6: Een virtuele machine maken

Nu u alle benodigde onderdelen hebt verzameld, is het tijd om de virtuele machine te maken.

1. Voer de opdracht uit voor het instellen van de naam en het wachtwoord van de beheerder voor de virtuele machine.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    Het wachtwoord moet tussen 8 en 123 tekens lang zijn en ten minste 3 van de volgende waarden bevatten: één kleine letter, één hoofdletter, één cijfer en één speciaal teken. 
        
2. Vervang de waarde van **$vmName** door de gewenste naam voor de virtuele machine. Maak de variabele en de configuratie voor de virtuele machine.

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Zie [Grootten voor virtuele machines in Azure](virtual-machines-windows-sizes.md) voor een lijst met beschikbare grootten voor een virtuele machine.
    
3. Vervang de waarde van **$compName** door de gewenste computernaam voor de virtuele machine. Maak de variabele en voeg informatie over het besturingssysteem toe aan de configuratie.

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Definieer de installatiekopie die moet worden gebruikt voor de inrichting van de virtuele machine. 

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Zie [Navigeren door en selecteren van installatiekopieën voor virtuele Windows-machines in Azure met PowerShell of de CLI](virtual-machines-windows-cli-ps-findimage.md) voor meer informatie over het selecteren van de te gebruiken installatiekopieën.
        
5. Voeg de netwerkinterface die u hebt gemaakt, toe aan de configuratie.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Vervang de waarde van **$blobPath** door een pad en bestandsnaam in de opslag waarvan de virtuele vaste schijf gebruik gaat maken. Het virtuele vasteschijfbestand wordt meestal opgeslagen in een container, bijvoorbeeld **vhds/WindowsVMosDisk.vhd**. Maak de variabelen.

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Vervang de waarde van **$diskName** door de gewenste naam voor de besturingssysteemschijf. Maak de variabele en voeg informatie over de schijf toe aan de configuratie.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Maak ten slotte de virtuele machine.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    U ziet de resourcegroep en alle resources in de Azure Portal en de status Geslaagd in het PowerShell-venster:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Volgende stappen

- Als er problemen met de implementatie zijn, raadpleegt u als volgende stap [Problemen met resourcegroepimplementaties in Azure Portal oplossen](../resource-manager-troubleshoot-deployments-portal.md).
- Informatie over het beheren van de virtuele machine die u zojuist hebt gemaakt, vindt u in [Virtuele machines beheren met Azure Resource Manager en PowerShell](virtual-machines-windows-ps-manage.md).
- Profiteer van het gebruik van een sjabloon voor het maken van een virtuele machine met behulp van de informatie in [Een virtuele Windows-machine maken met een Resource Manager-sjabloon](virtual-machines-windows-ps-template.md).



<!--HONumber=Jun16_HO2-->


