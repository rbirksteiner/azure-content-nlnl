<properties
    pageTitle="Verschillende manieren om een virtuele Linux-machine te maken | Microsoft Azure"
    description="Dit artikel biedt een overzicht van de verschillende manieren voor het maken van een virtuele Linux-machine in Azure, evenals koppelingen voor verdere instructies."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/12/2016"
    ms.author="iainfou"/>

# Verschillende manieren om een virtuele Linux-machine te maken met Resource Manager

Voor verschillende gebruikers en doelstellingen biedt Azure verschillende manieren om een virtuele machine te maken met behulp van het Resource Manager-implementatiemodel. Dit artikel bevat een overzicht van deze manieren en van de keuzes die u hebt wanneer u een virtuele Linux-machine wilt maken.

## Opties voor hulpprogramma’s

### Opdrachtshell: Azure CLI 

Gebruik vanuit de CLI de Azure-opdrachtregelinterface. Er is meer informatie beschikbaar over [het installeren van de Azure CLI](../xplat-cli-install.md) via npm, Docker-container of het installatiescript. In de volgende zelfstudies worden voorbeelden gegeven van het gebruik van de Azure CLI:

* [Een virtuele Linux-machine maken via Azure CLI voor ontwikkeling en tests](virtual-machines-linux-quick-create-cli.md) 

* [Een beveiligde virtuele Linux-machine maken met een Azure-sjabloon](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Een compleet nieuwe virtuele Linux-machine maken met behulp van de Azure CLI](virtual-machines-linux-create-cli-complete.md)

### Grafische gebruikersinterface: Azure Portal

De grafische gebruikersinterface van de [Azure Portal](https://portal.azure.com) biedt een eenvoudige manier om een virtuele machine uit te proberen, met name als u net begint met Azure, omdat u niets hoeft te installeren op uw systeem. De Azure Portal gebruiken om een virtuele machine te maken:

* [Een virtuele machine met Linux maken met behulp van de Azure Portal](virtual-machines-linux-quick-create-portal.md) 

## Opties voor besturingssysteem en installatiekopieën

Bij beide methoden kiest u een installatiekopie op basis van het besturingssysteem dat u wilt uitvoeren. Azure en de diverse partners bieden een groot aantal installatiekopieën. Sommige hiervan bevatten toepassingen en hulpprogramma’s die vooraf zijn geïnstalleerd. U kunt ook een van uw eigen installatiekopieën uploaden.

### Installatiekopieën van Azure

In alle hierboven genoemde artikelen kunt u eenvoudig een bestaande Azure-installatiekopie gebruiken om een virtuele machine te maken en deze vervolgens aanpassen op basis van de gewenste netwerkinstellingen, taakverdeling en meer. De portal biedt de Azure Marketplace voor door Azure geleverde installatiekopieën. U kunt vergelijkbare lijsten ophalen via de opdrachtregel. Voer bijvoorbeeld in Azure-CLI `azure vm image list` uit om een lijst op te halen met alle beschikbare installatiekopieën, per locatie en uitgever. Zie [Navigeren door en selecteren van installatiekopieën voor virtuele machines in Azure met de Azure CLI](virtual-machines-linux-cli-ps-findimage.md) voor voorbeelden van hoe u door de beschikbare installatiekopieën bladert en deze gebruikt.

### Uw eigen installatiekopie gebruiken

Als u specifieke aanpassingen nodig hebt, kunt u een installatiekopie gebruiken op basis van een bestaande virtuele machine in Azure, door de betreffende virtuele machine *vast te leggen*. U kunt ook uw eigen installatiekopie uploaden die is opgeslagen op een virtuele vaste schijf (VHD). Raadpleeg de volgende artikelen voor meer informatie over ondersteunde distributies en over hoe u uw eigen installatiekopieën kunt gebruiken:

* [Door Azure goedgekeurde distributies](virtual-machines-linux-endorsed-distros.md)

* [Informatie over niet-goedgekeurde distributies](virtual-machines-linux-create-upload-generic.md)

* [Een virtuele Linux-machine vastleggen als Resource Manager-sjabloon](virtual-machines-linux-capture-image.md). 

## Volgende stappen

* Probeer een van de zelfstudies voor het maken van een virtuele Linux-machine via de [portal](virtual-machines-linux-quick-create-portal.md), met de [CLI](virtual-machines-linux-quick-create-cli.md) of met behulp van een Azure Resource Manager-[sjabloon](virtual-machines-linux-cli-deploy-templates.md).

* Nadat u een virtuele Linux-machine hebt gemaakt, kunt u gemakkelijk [een gegevensschijf toevoegen](virtual-machines-linux-add-disk.md).

* Snelle stappen om [een wachtwoord of SSH-sleutels opnieuw in te stellen +en gebruikers te beheren](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Jun16_HO2-->


