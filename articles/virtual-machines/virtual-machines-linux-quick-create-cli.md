<properties
   pageTitle="Een virtuele Linux-machine in Azure maken met behulp van de CLI | Microsoft Azure"
   description="Een virtuele Linux-machine in Azure maken met behulp van de CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# Een virtuele Linux-machine in Azure maken met behulp van de CLI

Dit artikel laat zien hoe u in Azure snel een virtuele Linux-machine kunt implementeren met behulp van de opdracht `azure vm quick-create` van Azure CLI. Met de opdracht `quick-create` wordt een virtuele machine met een basisinfrastructuur eromheen geïmplementeerd. Deze virtuele machine kunt u gebruiken als prototype of voor het zeer snel testen van een concept (zie het als de snelste manier naar een Linux-bash-shell).  Voor de stappen in dit artikel is een Azure-account vereist ([registreer u voor een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/)] en moet [de Azure CLI](../xplat-cli-install.md) zijn aangemeld (`azure login`) en zijn ingesteld op de Resource Manager-modus (`azure config mode arm`).  U kunt een virtuele Linux-machine ook snel implementeren met behulp van de [Azure Portal](virtual-machines-linux-quick-create-portal.md).

## Beknopt overzicht van opdrachten

Eén opdracht voor het implementeren van een virtuele CoreOS-machine en het koppelen van de SSH-sleutel:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## De virtuele Linux-machine implementeren

In het navolgende wordt elke prompt weergegeven, samen met de verwachte uitvoer, om met de alias RHEL een virtuele machine met RedHat Enteprise Linux 7.2 te maken. Hierbij wordt gebruikgemaakt van dezelfde opdracht als hierboven.  

## Een ImageURN-alias gebruiken

De opdracht `quick-create` van Azure CLI beschikt over aliassen die aan de meest voorkomende OS-distributies zijn toegewezen. De volgende tabel bevat de distributie-aliassen (vanaf Azure CLI versie 0.10).  Alle implementaties die `quick-create` gebruiken, worden standaard ingesteld op virtuele machines met SSD-opslag en bieden hoge prestaties.

| Alias     | Uitgever | Aanbieding        | SKU         | Versie |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | meest recente  |
| CoreOS    | CoreOS    | CoreOS       | Stabiel      | meest recente  |
| Debian    | credativ  | Debian       | 8           | meest recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | meest recente  |
| RHEL      | RedHat    | RHEL         | 7.2         | meest recente  |
| SLES      | SLES      | SLES         | 12-SP1      | meest recente  |
| UbuntuLTS | Canonical | UbuntuServer | 14.04.4-LTS | meest recente  |



Voor de optie **ImageURN** (`-Q`) gebruiken we `RHEL` om een virtuele machine met RedHat Enterprise Linux 7.2 te implementeren. (Deze 7 aliassen vertegenwoordigen een klein gedeelte van de besturingssystemen die in Azure beschikbaar zijn. Meer installatiekopieën vindt u door in de Marketplace [naar een installatiekopie te zoeken](virtual-machines-linux-cli-ps-findimage.md). U kunt ook [uw eigen aangepaste installatiekopie uploaden](virtual-machines-linux-create-upload-generic.md).)

Vervang voor de volgende opdrachten de prompts door waarden uit uw eigen omgeving. Wij gebruiken voorbeeldwaarden.  

De prompts volgen en uw eigen namen invoeren

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

Uw uitvoer moet eruitzien als in het volgende uitvoerblok.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

U kunt nu SSH in uw virtuele machine gebruiken op standaard-SSH-poort 22, evenals de volledig gekwalificeerde domeinnaam (FQDN) die in bovenstaande uitvoer wordt weergegeven. (U kunt bovendien het vermelde IP-adres gebruiken.)

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
De aanmeldingsprocedure moet er ongeveer als volgt uitzien:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Volgende stappen

`azure vm quick-create` is een goede manier om snel een virtuele machine te implementeren, zodat u zich kunt aanmelden bij een bash-shell en aan de slag kunt gaan. Het gebruik van `vm quick-create` geeft u niet de extra voordelen van een complexe omgeving.  Voor het implementeren van een virtuele Linux-machine die is afgestemd op uw infrastructuur, volgt u de instructies in een van de onderstaande artikelen.

- [Een Azure Resource Manager-sjabloon gebruiken om een specifieke implementatie te maken](virtual-machines-linux-cli-deploy-templates.md)
- [Rechtstreeks uw eigen aangepaste omgeving maken voor een virtuele Linux-machine met Azure CLI-opdrachten](virtual-machines-linux-create-cli-complete.md).
- [Een met SSH beveiligde virtuele Linux-machine in Azure maken met behulp van sjablonen](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Aan de hand van deze artikelen kunt u aan de slag met het maken van uw Azure-infrastructuur, evenals met een aantal bedrijfseigen en open-source hulpprogramma’s voor implementatie, configuratie en indeling van uw infrastructuur.



<!--HONumber=Jun16_HO2-->


