<properties 
    pageTitle="Site Recovery: veelgestelde vragen | Microsoft Azure" 
    description="In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Site Recovery."
    services="site-recovery" 
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags 
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na" 
    ms.workload="storage-backup-recovery"
    ms.date="03/27/2016"
    ms.author="raynew"/>


# Azure Site Recovery: veelgestelde vragen

Lees dit artikel voor antwoorden op veelgestelde vragen over de service Azure Site Recovery.

Hebt u na het lezen van het artikel nog steeds vragen, dan kunt u deze onder aan dit artikel of op het [Azure Recovery Services-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) kwijt.

## Algemeen

### Wat doet Site Recovery?

Site Recovery draagt bij aan uw BCDR-strategie (strategie voor bedrijfscontinuïteit en herstel na noodgeval) door replicatie vanaf on-premises virtuele machines en fysieke servers naar Azure of naar een secundair datacenter te regelen en te automatiseren. [Meer informatie](site-recovery-overview.md).

### Wat kan er met Site Recovery worden gerepliceerd?

- **Virtuele Hyper-V-machines**: met Site Recovery kan elke workload worden gerepliceerd die wordt uitgevoerd op een ondersteunde virtuele Hyper-V-machine. 
- **Fysieke servers**: met Site Recovery kan elke workload worden gerepliceerd die wordt uitgevoerd op een ondersteunde fysieke server met Windows of Linux.
- **Virtuele VMware-V-machines**: met Site Recovery kan elke workload worden gerepliceerd die wordt uitgevoerd op een ondersteunde virtuele VMware-V-machine.


### Wat heb ik in Hyper-V nodig?

Wat u voor de Hyper-V-hostserver nodig hebt, is afhankelijk van het implementatiescenario. Controleer de lijst met vereisten voor Hyper-V in:

- [Replicating Hyper-V VMs (without VMM) to Azure (Virtuele Hyper-V-machines (zonder VMM) repliceren naar Azure)](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicating Hyper-V VMs (with VMM) to Azure (Virtuele Hyper-V-machines (met VMM) repliceren naar Azure)](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicating Hyper-V VMs to a secondary datacenter (Virtuele Hyper-V-machines repliceren naar een secundair datacenter)](site-recovery-vmm-to-vmm.md#before-you-start)

Voor gasten die worden uitgevoerd op de Hyper-V-hostserver:

- Als u repliceert naar een secundaire site, lees dan [Supported guest operating systems for Hyper-V VMs](https://technet.microsoft.com/library/mt126277.aspx) (Ondersteunde gastbesturingssystemen voor virtuele Hyper-V-machines).
- Als u repliceert naar Azure, ondersteunt Site Recovery dezelfde gastbesturingssystemen die worden [ondersteund door Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### Kan ik virtuele machines beveiligen als Hyper-V wordt uitgevoerd op een clientbesturingssysteem?

Nee, virtuele machines moeten zich bevinden op een Hyper-V-hostserver die wordt uitgevoerd op een ondersteunde Windows-servercomputer. Als u een clientcomputer moet beveiligen, kunt u deze als fysieke machine repliceren naar [Azure](site-recovery-vmware-to-azure-classic.md) of naar een [secundair datacenter](site-recovery-vmware-to-vmware.md).


### Welke workloads kan ik met Site Recovery beveiligen?

Met Site Recovery kan elke workload worden gerepliceerd die wordt uitgevoerd op een ondersteunde virtuele machine of fysieke server. Site Recovery biedt ondersteuning voor toepassingsgevoelige replicatie, zodat apps kunnen worden hersteld naar een intelligente status. Site Recovery kan worden geïntegreerd met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory, en werkt uitstekend samen met producten van toonaangevende leveranciers, waaronder Oracle, SAP, IBM en Red Hat. Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.


### Moeten mijn Hyper-V-hosts zich in System Center VMM-clouds bevinden om ze met Site Recovery te kunnen repliceren? 

Als u wilt repliceren naar een secundair datacenter, moeten uw virtuele Hyper-V-machines zich op Hyper-V-hostservers in een VMM-cloud bevinden. Als u wilt repliceren naar Azure, kunt u dit doen met virtuele machines op Hyper-V-hostservers met of zonder VMM-cloud. [Meer informatie](site-recovery-hyper-v-site-to-azure.md). 

### Kan ik Site Recovery met VMM implementeren als ik maar één VMM-server heb? 

Ja. U kunt virtuele machines op Hyper-V-servers in de VMM-cloud naar Azure repliceren, maar u kunt ook repliceren naar een VMM-cloud op dezelfde server. Voor replicatie van on-premises naar on-premises doet u er verstandig aan op zowel de primaire als de secundaire site een VMM-server te hosten. [Meer informatie](site-recovery-single-vmm.md)

### Welke fysieke servers kan ik beveiligen?

U kunt fysieke servers waarop ondersteunde Windows- en Linux-besturingssystemen worden uitgevoerd, beveiligen door deze te repliceren naar Azure of een secundaire site. Lees [hier](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) informatie over besturingssysteemvereisten voor replicatie naar Azure of een secundaire site. In geval van een failover worden fysieke servers die worden gerepliceerd naar Azure-opslag, uitgevoerd als virtuele machines in Azure. Als u een failback van Azure naar uw on-premises site uitvoert, wordt een failback naar een fysieke server momenteel nog niet ondersteund. U kunt alleen een failback uitvoeren naar een virtuele machine die wordt uitgevoerd in VMware.

### Welke virtuele VMware-machines kan ik beveiligen?

Als u virtuele VMware-machines wilt beveiligen, hebt u een vSphere-hypervisor nodig, evenals virtuele machines waarop VMware-hulpprogramma's worden uitgevoerd. We raden ook het gebruik aan van een VMware vCenter-server om de hypervisors te beheren. Lees [hier](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) meer informatie over de juiste vereisten voor het repliceren van VMware-servers en virtuele VMware-machines naar Azure of een secundaire site.

### Kan ik met Site Recovery herstel na noodgeval voor mijn filialen beheren?

Ja. Wanneer u Site Recovery gebruikt om replicatie en failover in uw filialen te organiseren, kunt u de workloads van al uw filialen op een centrale locatie bekijken en organiseren. Vanuit het hoofdkantoor kunt u eenvoudig failovers uitvoeren en herstel na noodgeval beheren voor alle filialen. Het is niet nodig de afzonderlijke filialen te bezoeken. 

## Beveiliging


### Worden er replicatiegegevens verzonden naar de Site Recovery-service?

Nee, Site Recovery ontvangt geen gerepliceerde gegevens of informatie over wat er op uw virtuele machines of fysieke servers wordt uitgevoerd.

Er worden alleen replicatiegegevens uitgewisseld tussen uw on-premises Hyper-V-hosts, VMware-hypervisors of fysieke servers en de Azure-opslag of uw secundaire site.  Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden. 

Site Recovery is conform ISO 27001:2005 gecertificeerd. De HIPAA-, DPA- en FedRAMP JAB-beoordelingen zijn in behandeling.

### Uit nalevingsoogpunt moeten zelfs metagegevens van onze on-premises omgevingen binnen dezelfde geografische regio blijven. Is dit mogelijk met Site Recovery?

Ja. Wanneer u in een bepaalde regio een Site Recovery-kluis maakt, zorgen wij ervoor dat alle metagegevens die we nodig hebben om replicatie en failover in te schakelen en te organiseren, binnen de geografische grenzen van die regio blijven.

### Wordt replicatie met Site Recovery versleuteld?
Voor virtuele machines en fysieke servers die worden gerepliceerd tussen on-premises sites, wordt transitversleuteling ondersteund. Voor virtuele machines en fysieke servers die worden gerepliceerd naar Azure, wordt zowel transitversleuteling als rustversleuteling (in Azure) ondersteund. 



## Replicatie

### Zijn er vereisten voor het repliceren van virtuele machines naar Azure?

Als u virtuele machines naar Azure wilt repliceren, moeten ze voldoen aan de [Azure-vereisten](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Kan ik virtuele machines van de tweede generatie Hyper-V repliceren naar Azure?

Ja. Met Site Recovery worden virtuele machines van generatie 2 tijdens failover geconverteerd naar generatie 1. Bij de failback wordt de virtuele machine weer teruggezet naar generatie 2. [Meer informatie](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Hoe betaal ik voor virtuele Azure-machines als ik naar Azure repliceer? 

Tijdens een normale replicatie worden gegevens gerepliceerd naar geo-redundante Azure-opslag en hoeft u geen kosten voor virtuele Azure IaaS-machines te betalen.  Wanneer u een failover naar Azure uitvoert, maakt Site Recovery automatisch virtuele Azure IaaS-machines. Vervolgens worden er kosten in rekening gebracht voor de rekenresources die u in Azure verbruikt.

### Is er een SDK waarmee ik Site Recovery-werkstromen kan automatiseren?

Ja. U kunt Site Recovery-werkstromen automatiseren met de Rest API-, PowerShell- of Azure-SDK. Lees meer over [hoe u Site Recovery implementeert met behulp van PowerShell en Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).

### Als ik repliceer naar Azure, wat voor opslagaccount heb ik dan nodig?

U hebt een opslagaccount met [standaard geografisch redundante opslag](../storage/storage-introduction.md#replication-for-durability-and-high-availability) nodig. Premium-opslag wordt momenteel niet ondersteund.

### Hoe vaak kan ik gegevens repliceren?

- **Hyper-V:** virtuele Hyper-V-machines die worden uitgevoerd op Windows Server 2012 R2, kunnen elke 30 seconden, elke 5 minuten of elke 15 minuten worden gerepliceerd. Als u SAN-replicatie hebt ingesteld, verloopt replicatie synchroon.
- **VMware en fysieke servers:** hier is de replicatiefrequentie niet relevant. Replicatie vindt doorlopend plaats. 

### Kan ik de replicatie van een bestaande herstelsite uitbreiden naar een andere herstelsite?

Uitgebreide of gekoppelde replicatie wordt niet ondersteund. [Stuur ons feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) over deze functie.


### Kan ik de eerste keer dat ik naar Azure repliceer, offline replicatie uitvoeren? 

Nee, dit wordt niet ondersteund. [Stuur ons feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) over deze functie.

### Kan ik bepaalde schijven uitsluiten van replicatie?

Nee, dit wordt niet ondersteund. [Stuur ons feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) over deze functie.

### Kan ik virtuele machines met dynamische schijven repliceren?

Dynamische schijven worden ondersteund bij het repliceren van virtuele Hyper-V-machines. Ze worden ook ondersteund bij het repliceren van virtuele VMware-machines en fysieke machines als u gebruikmaakt van de [uitgebreide implementatie](site-recovery-vmware-to-azure-classic.md). De besturingssysteemschijf moet wel een standaardschijf zijn.

### Kan ik de bandbreedte beperken die is toegewezen voor Hyper-V-replicatieverkeer?
- Als u repliceert tussen virtuele Hyper-V-machines op twee on-premises sites, kunt u Windows QoS gebruiken. Hier volgt een voorbeeld van een script: 

        New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
        gpupdate.exe /force

- Als u virtuele Hyper-V-machines repliceert naar Azure, kunt u beperkingen configureren met de volgende voorbeeld-cmdlet van PowerShell:

        Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- Lees [hier](https://support.microsoft.com/en-us/kb/3056159) meer informatie over het beperken van Hyper-V-verkeer.
- Lees [hier](site-recovery-vmware-to-azure-classic.md#capacity-planning) meer informatie over het beperken van VMware-replicatie naar Azure.


## Failover

### Hoe krijg ik toegang tot virtuele Azure-machines na een failover naar Azure? 

U kunt de virtuele Azure-machines benaderen via een beveiligde internetverbinding, via een VPN tussen sites of via Azure ExpressRoute. Communicatie via een VPN-verbinding vindt plaats naar interne poorten op het Azure-netwerk waarop de virtuele machine zich bevindt. Communicatie via internet wordt toegewezen aan de openbare eindpunten in de Azure-cloudservice voor de virtuele machines.

### Hoe zorgt Azure er bij een failover naar Azure voor dat mijn gegevens herstelbaar blijven?

Azure is ontworpen voor herstelbaarheid. Site Recovery voert automatisch een failover uit naar een secundair Azure-datacenter (in overeenstemming met de serviceovereenkomst van Azure) als de noodzaak zich voordoet. Als dit gebeurt, zorgen wij ervoor dat uw metagegevens en kluizen binnen het geografische gebied blijven dat u voor de kluizen hebt gekozen. 

### Als ik tussen twee sites repliceer, wat gebeurt er dan als er op mijn primaire site een stroomstoring optreedt?

U kunt een ongeplande failover vanuit de secundaire site activeren. Site Recovery heeft geen connectiviteit vanuit de primaire site nodig om de failover uit te voeren.

### Vindt failover automatisch plaats?

Failover wordt niet automatisch uitgevoerd. U start een failover met één klik in de portal of u kunt [Site Recovery PowerShell-cmdlets](https://msdn.microsoft.com/library/dn850420.aspx) gebruiken om een failover te activeren. Failback bestaat uit een reeks acties in de Site Recovery-portal.

Als u failover wilt automatiseren, kunt u on-premises Orchestrator of Operations Manager gebruiken om een probleem met een virtuele machine te detecteren, en vervolgens de failover activeren met de SDK.


## 
### Ik ben een serviceprovider. Werkt Site Recovery ook voor toegewezen of gedeelde infrastructuurmodellen?
Ja, Site Recovery werkt voor zowel toegewezen als gedeelde infrastructuurmodellen.

### Wordt de identiteit van mijn tenants gedeeld met de Site Recovery-service?
Nee. Uw tenants hebben geen toegang nodig tot de Site Recovery-portal. Alleen de beheerder van de serviceprovider communiceert met de portal.


### Worden de toepassingsgegevens van mijn tenants naar Azure verplaatst?

Bij replicatie tussen sites van serviceproviders worden er nooit toepassingsgegevens naar Azure overgezet. Gegevens worden in transit versleuteld en rechtstreeks gerepliceerd tussen de sites van de serviceprovider.

Als u naar Azure repliceert, worden er toepassingsgegevens verzonden naar Azure-opslag, maar niet naar de Site Recovery-service. Gegevens worden in transit versleuteld en blijven versleuteld in Azure. 

### Ontvangen mijn tenants een factuur voor Azure-services?

Nee. Azure-services worden rechtstreeks aan de serviceprovider gefactureerd. Serviceproviders zijn zelf verantwoordelijk voor het genereren van facturen voor hun tenants.

### Als ik naar Azure repliceer, moet ik dan altijd een virtuele machine in Azure uitvoeren?

Nee. Gegevens worden gerepliceerd naar een Azure-opslagaccount met geografische redundantie in uw abonnement. Wanneer u een failovertest (details voor DR) of een werkelijke failover uitvoert, maakt Site Recovery automatisch virtuele machines in uw abonnement.

### Wordt er gezorgd voor isolatie op tenantniveau wanneer ik naar Azure repliceer?

Ja.

### Welke platforms worden er momenteel ondersteund?

Wij ondersteunen implementaties van Azure Pack, Cloud Platform System en System Center (2012 en hoger). Lees meer over de integratie van Azure Pack in [Configure protection for virtual machines](https://technet.microsoft.com/library/dn850370.aspx) (Beveiliging voor virtuele machines configureren).

### Worden implementaties van één Azure Pack en één VMM-server ondersteund?

Ja, u kunt virtuele Hyper-V-machines en Azure repliceren of repliceren tussen sites van serviceproviders.  Als u repliceert tussen sites van serviceproviders, is Azure-runbookintegratie niet beschikbaar.


## Volgende stappen

- Lees het [Site Recovery-overzicht](site-recovery-overview.md)
- Informatie over de [Site Recovery-architectuur](site-recovery-components.md)  



<!--HONumber=Jun16_HO2-->


