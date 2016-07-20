<a name="tellmevm"></a>
## Informatie over virtuele machines

Met virtuele machines van Azure kunt u virtuele machines in de cloud maken en gebruiken. De technologie van virtuele machines, ook wel *Infrastructure as a Service (IaaS)* genoemd, kan op verschillende manieren worden gebruikt. Een aantal voorbeelden:

- **Virtuele machines (VM's) voor ontwikkelen en testen.** Ontwikkelgroepen werken veel met virtuele machines, omdat deze een snelle en eenvoudige manier bieden om een computer te maken met specifieke configuraties die nodig zijn voor de code van een toepassing en het testen ervan. Virtuele machines van Azure bieden een eenvoudige en voordelige manier om deze VM's te maken, te gebruiken en te verwijderen wanneer ze niet meer nodig zijn.
- **Toepassingen uitvoeren in de cloud.** Het is financieel aantrekkelijk om sommige toepassingen uit te voeren in de openbare cloud. Een voorbeeld hiervan is een toepassing met grote pieken in de vraag. Alhoewel u uw eigen datacenter kunt uitrusten met voldoende hardware om pieken op te kunnen vangen, wordt deze software de rest van de tijd onvoldoende gebruikt. Als u deze toepassing in Azure uitvoert, kunt u betalen voor extra VM's wanneer u deze nodig hebt en kunt u ze afsluiten als u ze niet nodig hebt. Of stelt u zich voor dat u een beginnend bedrijf bent dat snel en zonder verplichtingen computerresources op aanvraag nodig heeft. Ook dan kan Azure de juiste keuze zijn.
- **Uw eigen datacenter uitbreiden naar de openbare cloud.** Met Azure Virtual Network kan uw organisatie een virtueel netwerk (VNET) maken ter uitbreiding van de eigen on-premises netwerken en VM's aan dit VNET toevoegen. Hierdoor kunnen toepassingen als [SharePoint](../articles/virtual-machines/virtual-machines-windows-sharepoint-farm.md) en [SQL Server](../articles/virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) worden uitgevoerd op een VM van Azure. Deze aanpak is wellicht eenvoudiger te implementeren of minder duur dan de toepassingen uitvoeren op VM's in uw eigen datacenter.   
- **Herstel na noodgevallen.** Met op IaaS gebaseerd herstel na noodgevallen betaalt u alleen voor computermiddelen wanneer u deze nodig hebt en hebt u dus niet te maken met vaste kosten voor een back-updatacenter dat slechts zelden wordt gebruikt.  Als uw primaire datacenter bijvoorbeeld uitvalt, kunt u met VM's op Azure essentiële toepassingen uitvoeren en deze weer afsluiten wanneer ze niet meer nodig zijn.

Net als andere virtuele machines heeft een VM van Azure een besturingssysteem, opslagruimte en netwerkmogelijkheden, en kunt u vele toepassingen op de VM uitvoeren. U kunt een installatiekopie van Azure, een installatiekopie van een van de partners of uw eigen installatiekopie gebruiken. Voorbeelden zijn verschillende versies, edities en configuraties van:
 
- Linux-servers zoals Suse, Ubuntu en CentOS
- Windows Server 
- SQL Server
- BizTalk Server 
- SharePoint Server

Virtuele machines maken gebruik van virtuele harde schijven (VHD's) voor de opslag van het besturingssysteem (OS) en de gegevens. VHD's worden ook gebruikt voor de installatiekopieën waarmee u een besturingssysteem kunt installeren. In onderstaande afbeelding vindt u een illustratie hiervan, plus twee van de tools voor het maken en beheren van uw VM's.

<a name="fig_createvms"></a>
![vm_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Afbeelding: Virtuele machines van Azure bieden Infrastructure as a Service.**

VM's kunnen worden beheerd via een op een browser gebaseerde portal, opdrachtregelprogramma's met ondersteuning voor het uitvoeren van scripts of rechtstreeks via de REST API. Microsoft-partners zoals RightScale en ScaleXtreme bieden ook beheerservices die de REST API gebruiken. 

Behalve het besturingssysteem hebt u met VM's ook andere configuratie-opties, zoals:

- De grootte, die factoren bepaalt als het aantal schijven dat u kunt koppelen en de processorsnelheid. Azure biedt een groot aantal verschillende grootten voor verschillende manieren van gebruik. Zie [Grootten voor virtuele machines](../articles/virtual-machines/virtual-machines-linux-sizes.md) voor meer informatie.  
- De Azure-regio waar uw nieuwe VM zal worden gehost, zoals in de Verenigde Staten, Europa of Azië. 
- VM-extensies voor extra mogelijkheden voor uw virtuele machine, zoals een antivirusprogramma of het gebruik van DSC (Desired State Configuration) van Windows PowerShell.

Andere voordelen van VM's zijn:

**Betalen per gebruik** --Azure rekent een uurprijs op basis van de grootte en het besturingssysteem van de VM. Voor niet-hele uren worden alleen de minuten van gebruik in rekening gebracht. De opslag wordt afzonderlijk berekend en in rekening gebracht. Zie [Prijzen van virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/) voor meer informatie.

**Tolerantie** --Azure bewaakt de fysieke hardware die als host fungeert voor elke actieve VM. Als een fysieke server waarop een VM wordt uitgevoerd uitvalt, verplaatst Azure de VM naar nieuwe hardware en wordt de VM opnieuw opgestart. Dit proces wordt ook wel service healing genoemd. Azure beschermt ook de gegevens van een virtuele machine door herstelexemplaren van de VHD's in de blobopslag te bewaren. 






<!--HONumber=Jun16_HO2-->


