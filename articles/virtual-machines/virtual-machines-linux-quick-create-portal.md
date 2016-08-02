<properties
    pageTitle="Een virtuele Linux-machine maken met behulp van de Azure Portal | Microsoft Azure"
    description="Een virtuele Linux-machine maken met behulp van de Azure Portal."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# Een virtuele Linux-machine in Azure maken met behulp van de portal

Dit artikel laat zien hoe u de [Azure Portal](https://portal.azure.com/) gebruikt om snel een virtuele Linux-machine te maken. De enige vereisten zijn [een Azure-account](https://azure.microsoft.com/pricing/free-trial/) en [openbare en persoonlijke SSH-sleutelbestanden](virtual-machines-linux-mac-create-ssh-keys.md).


1. Meld u bij de Azure Portal aan met uw Azure-account en klik in de linkerbovenhoek op **+ Nieuw**:

    ![scherm1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Klik in de **Marketplace** op **Virtual Machines** en vervolgens op **Ubuntu Server 14.04 TNS** in de installatiekopielijst **Aanbevolen apps**.  Controleer onderin of het implementatiemodel `Resource Manager` is en klik vervolgens op **Maken**.

    ![scherm2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Voer op de pagina **Grondbeginselen** het volgende in:
    - de gewenste naam voor de virtuele machine
    - de gebruikersnaam van de beoogde beheerder
    - het verificatietype (dit moet zijn ingesteld op **Openbare SSH-sleutel**)
    - uw openbare SSH-sleutel als tekenreeks (standaard uit uw `~/.ssh/`-map)
    - de gewenste naam voor uw nieuwe resourcegroep (als u een nieuwe implementatiegroep wilt maken) of de naam van een bestaande groep

    Klik vervolgens op **OK** om door te gaan en de grootte van uw virtuele machine te kiezen. Dit ziet er ongeveer als volgt uit:

    ![scherm3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Kies de **DS1**-grootte, waarmee Ubuntu wordt geïnstalleerd op een Premium-SSD, en klik op **Selecteren** om de instellingen te configureren.

    ![scherm4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. Laat bij **Instellingen** de standaardwaarden voor Opslag en Netwerk onveranderd en klik op **OK** om het overzicht weer te geven.  U ziet dat door uw keuze voor DS1 het schijftype is ingesteld op Premium-SSD (de **S** staat voor SSD).

    ![scherm5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Bevestig de instellingen voor uw nieuwe virtuele Ubuntu-machine en klik op **OK**.

    ![scherm6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Open het dashboard van de portal en kies in **Netwerkinterfaces** uw NIC.

    ![scherm7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Open onder de NIC-instellingen het menu Openbare IP-adressen.

    ![scherm8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. Voeg SSH toe aan het openbare IP-adres met behulp van uw openbare SSH-sleutel

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Volgende stappen

U hebt nu snel een virtuele Linux-machine gemaakt die u kunt gebruiken voor test- of demonstratiedoeleinden. Als u een virtuele Linux-machine wilt maken die is afgestemd op uw infrastructuur, volgt u de instructies in een van de onderstaande artikelen.

- [Een virtuele Linux-machine in Azure maken met behulp van sjablonen](virtual-machines-linux-cli-deploy-templates.md)
- [Een met SSH beveiligde virtuele Linux-machine in Azure maken met behulp van sjablonen](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Een virtuele Linux-machine maken met behulp van de Azure CLI](virtual-machines-linux-create-cli-complete.md)

Aan de hand van deze artikelen kunt u aan de slag met het maken van uw Azure-infrastructuur, evenals met een aantal bedrijfseigen en open-source hulpprogramma’s voor implementatie, configuratie en indeling van uw infrastructuur.



<!--HONumber=Jun16_HO2-->


