<properties
    pageTitle="Uw eerste virtuele Windows-machine maken | Microsoft Azure"
    description="Informatie over het maken van uw eerste virtuele Windows-machine met behulp van Azure Portal."
    keywords="Windows virtual machine,create a virtual machine,virtual computer,setting up a virtual machine"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/05/2016"
    ms.author="cynthn"/>

# Uw eerste virtuele Windows-machine maken met behulp van Azure Portal

Deze zelfstudie leert u hoe eenvoudig het is om in enkele minuten een virtuele Windows-machine te maken met behulp van de Azure Portal.  

Als u geen Azure-abonnement hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/free/) maken.

Hier volgt een [video-overzicht](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal) van deze zelfstudie. 


## Een installatiekopie voor de virtuele machine kiezen in de marketplace

Als voorbeeld gebruiken we een installatiekopie voor Windows Server 2012 R2 Datacenter, maar dat is slechts een van de vele installatiekopieën die Azure biedt. Uw opties voor installatiekopieën zijn afhankelijk van uw abonnement. Bureaubladinstallatiekopieën zijn bijvoorbeeld mogelijk beschikbaar voor [MSDN-abonnees](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Klik in het menu Hub op **Nieuw** > **Virtuele machines** > **Windows Server 2012 R2 Datacenter**.

    ![Schermafbeelding van de installatiekopieën voor virtuele machines in Azure die in de portal beschikbaar zijn](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)


3. Controleer op de pagina **Windows Server 2012 R2 Datacenter** onder **Een implementatiemodel selecteren** of **Resource Manager** is geselecteerd. Klik op **Maken**.

    ![Schermafbeelding van het implementatiemodel dat voor de virtuele machine moet worden geselecteerd](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## De virtuele Windows-machine maken

Nadat u de installatiekopie hebt geselecteerd, kunt u voor het grootste deel van de configuratie de standaardinstellingen van Azure gebruiken en snel de virtuele machine maken.

1. Voer op de blade **Grondbeginselen** een **Naam** in voor de virtuele machine. De naam moet 1-15 tekens lang zijn en mag geen speciale tekens bevatten.

2. Voer een **Gebruikersnaam** en een sterk **Wachtwoord** in om te gebruiken voor het maken van een lokaal account op de virtuele machine. Het lokale account wordt gebruikt voor aanmelding bij en beheer van de virtuele machine. 

    Het wachtwoord moet tussen 8 en 123 tekens lang zijn en ten minste 3 van de volgende waarden bevatten: één kleine letter, één hoofdletter, één cijfer en één speciaal teken. 


3. Selecteer een bestaande [Resourcegroep](../resource-group-overview.md#resource-groups) of typ de gewenste naam voor een nieuwe resourcegroep. Typ een Azure-datacenter**locatie**, zoals **VS - west**. 

4. Wanneer u klaar bent, klikt u op **OK** om door te gaan naar de volgende sectie. 

    ![Schermafbeelding met de instellingen op de blade Grondbeginselen voor het configureren van een virtuele machine in Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)

    
5. Kies een [grootte](virtual-machines-windows-sizes.md) voor de virtuele machine en klik vervolgens op **Selecteren** om door te gaan. 

    ![Schermafbeelding van de blade Grootte, waarop de groottes worden getoond die u kunt selecteren voor een virtuele machine in Azure](./media/virtual-machines-windows-hero-tutorial/size-blade.png)

6. Op de blade **Instellingen** kunt u de opslag- en netwerkopties wijzigen. Voor uw eerste virtuele machine kunt u over het algemeen de standaardinstellingen accepteren. Als u voor uw virtuele machine een grootte hebt geselecteerd die hierdoor wordt ondersteund, kunt u Premium Storage uitproberen. Selecteer hiervoor onder **Schijftype** de optie **Premium SSD**. Wanneer u alle wijzigingen hebt aangebracht, klikt u op **OK**.

    ![Schermafbeelding van de blade Instellingen, waarop u optionele functies voor uw virtuele machine in Azure kunt configureren](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)

7. Klik op **Samenvatting** om uw keuzes te bekijken. Wanneer u klaar bent, klikt u op **OK**.

    ![Schermafbeelding van de pagina Samenvatting, met de gekozen configuratie voor de virtuele machine in Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)

8. Terwijl Azure de virtuele machine maakt, kunt u de voortgang volgen onder **Virtuele machines** in het menu Hub. 


## Verbinding maken met de virtuele machine en aanmelden

1.  Klik in het menu Hub op **Virtuele machines**.

2.  Selecteer de virtuele machine in de lijst.

3. Klik op de blade voor de virtuele machine op **Verbinden**. Er wordt nu een Remote Desktop Protocol-bestand (RDP-bestand) gemaakt en gedownload. Een RDP-bestand is een soort snelkoppeling om verbinding te maken met uw computer. Uit oogpunt van gemak is het mogelijk een goed idee om het bestand op uw bureaublad op te slaan. **Open** dit bestand om verbinding te maken met de virtuele machine.

    ![Schermafbeelding van de Azure Portal waarin wordt getoond hoe u verbinding maakt met uw virtuele machine.](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. U ontvangt een waarschuwing dat het RDP-bestand van een onbekende uitgever is. Dit is normaal. Klik in het venster Extern bureaublad op **Verbinden** om door te gaan.

    ![Schermafbeelding met waarschuwing over een onbekende uitgever](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Typ in het venster Windows-beveiliging de gebruikersnaam en het wachtwoord voor het lokale account dat u hebt gemaakt tijdens het maken van de virtuele machine. U voert de gebruikersnaam in als *naam_van_virtuele_machine*& #92;*gebruikersnaam*. Klik vervolgens op **OK**.

    ![Schermafbeelding van het invoeren van de naam van de virtuele machine, de gebruikersnaam en het wachtwoord](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  U ontvangt een waarschuwing dat het certificaat niet kan worden geverifieerd. Dit is normaal. Klik op **Ja** om de identiteit van de virtuele machine te controleren en het aanmelden te voltooien.

    ![Schermafbeelding met een bericht over het verifiëren van de identiteit van de virtuele machine](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Als u problemen ondervindt wanneer u verbinding probeert te maken, raadpleegt u [Problemen oplossen met Extern bureaublad-verbindingen met een virtuele Windows-machine in Azure](virtual-machines-windows-troubleshoot-rdp-connection.md).

U kunt de virtuele machine nu net zo gebruiken als elke andere server.

## De virtuele machine stoppen

Het is een goed idee om de virtuele machine te stoppen als u deze niet gebruikt. Zo voorkomt u dat de kosten oplopen. U stopt de virtuele machine door op de knop **Stoppen** te klikken en vervolgens op **Ja**.

![Schermafbeelding met de knop voor het stoppen van een virtuele machine](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)
    
Als u de virtuele machine opnieuw wilt opstarten of weer in gebruik wilt nemen, klikt u op de knop **Starten**.


## Volgende stappen

* U kunt ook experimenteren met het [koppelen van een gegevensschijf](virtual-machines-windows-attach-disk-portal.md) aan de virtuele machine. Gegevensschijven bieden meer opslagruimte voor uw virtuele machine.

* U kunt ook [een virtuele Windows-machine maken met behulp van Powershell](virtual-machines-windows-ps-create.md) of [ een virtuele Linux-machine maken](virtual-machines-linux-quick-create-cli.md) met behulp van de Azure CLI.



<!--HONumber=Jun16_HO2-->


