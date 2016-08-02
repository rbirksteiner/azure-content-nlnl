<properties
    pageTitle="Hoe werkt Site Recovery? | Microsoft Azure"
    description="In dit artikel vindt u een overzicht van de architectuur van Site Recovery"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/27/2016"
    ms.author="raynew"/>

# Hoe werkt Azure Site Recovery?

Lees dit artikel om inzicht te krijgen in de onderliggende architectuur van de service Azure Site Recovery en de onderdelen daarvan. 

Eventuele opmerkingen of vragen kunt u onder aan dit artikel plaatsen of op het [Azure Recovery Services-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Overzicht

Organisaties hebben een BCDR-strategie (strategie voor bedrijfscontinuïteit en noodherstel) nodig die bepaalt op welke wijze toepassingen, workloads en gegevens beschikbaar blijven tijdens geplande en ongeplande uitval, en ervoor zorgt dat deze zo snel mogelijk worden hersteld naar een normale manier van functioneren.

Site Recovery is een Azure-service die bijdraagt aan uw BCDR-strategie door replicatie van fysieke on-premises servers en virtuele machines in de cloud (Azure) of naar een secundaire locatie te organiseren. Als er uitval optreedt op uw primaire locatie, schakelt u over naar de secundaire locatie om toepassingen en workloads beschikbaar te houden. U schakelt terug naar de primaire locatie wanneer deze weer normaal functioneert.

Site Recovery kan worden geïmplementeerd voor de organisatie van replicatie in een aantal scenario's:

- **Virtuele VMware-machines repliceren**: u kunt on-premises virtuele VMware-machines repliceren naar [Azure](site-recovery-vmware-to-azure-classic.md) of naar een [secundair datacenter](site-recovery-vmware-to-vmware.md).
- **Fysieke machines repliceren**: u kunt fysieke computers waarop Windows of Linux wordt uitgevoerd, repliceren naar [Azure](site-recovery-vmware-to-azure-classic.md) of naar een [secundair datacenter](site-recovery-vmware-to-vmware.md).
- **Virtuele Hyper-V-machines repliceren die worden beheerd in System Center VMM-clouds**: u kunt on-premises virtuele Hyper-V-machines in VMM-clouds repliceren naar [Azure](site-recovery-vmm-to-azure.md) of naar een [secundair datacenter](site-recovery-vmm-to-vmm.md). 
- **Virtuele Hyper-V-machines (zonder VMM) repliceren**: u kunt virtuele Hyper-V-machines die niet door VMM worden beheerd, repliceren naar [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Virtuele machines migreren**: met Site Recovery kunt u [virtuele Azure IaaS-machines migreren](site-recovery-migrate-azure-to-azure.md) tussen regio's of [AWS Windows-exemplaren migreren](site-recovery-migrate-aws-to-azure.md) naar virtuele Azure IaaS-machines. Momenteel wordt alleen migratie ondersteund. Dat betekent dat er voor deze virtuele machines wel een failover is, maar geen failback.

Met Site Recovery kunt u de meeste toepassingen repliceren die op deze virtuele machines en fysieke servers worden uitgevoerd. Een volledig overzicht van de ondersteunde toepassingen vindt u in [Welke workloads kunt u met Azure Site Recovery beveiligen?](site-recovery-workload.md)

## On-premises virtuele VMware-machines of fysieke servers repliceren naar Azure

Op dit moment zijn er twee verschillende architecturen beschikbaar om virtuele VMware-machines of fysieke Windows-/Linux-servers te repliceren naar Azure:

- [Verouderde architectuur](site-recovery-vmware-to-azure-classic-legacy.md): gebruik deze architectuur niet voor nieuwe implementaties. 
- [Verbeterde architectuur](site-recovery-vmware-to-azure-classic.md): dit is de meest recente architectuur. Gebruik deze voor alle nieuwe implementaties. Als u dit scenario al hebt geïmplementeerd met de verouderde architectuur, [lees dan meer informatie over migratie](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) naar de verbeterde architectuur.

In de verbeterde architectuur stelt u een on-premises beheerserver in met alle onderdelen van Site Recovery. Op elke computer die u wilt beveiligen, pusht u automatisch (of installeert u handmatig) de Mobility-service. Na de eerste replicatie verzendt de Mobility-service op de computer deltareplicatiegegevens naar de processerver, die deze optimaliseert alvorens ze naar de Azure-opslag te verzenden.

![Verbeterd](./media/site-recovery-components/arch-enhanced.png)
![Verbeterd](./media/site-recovery-components/arch-enhanced2.png)

### On-premises
On-premises hebt u het volgende nodig:

- **Beheerserver**: u hebt een Windows Server 2012 R2-machine nodig die als beheerserver fungeert. Op deze server installeert u met één installatiebestand de volgende onderdelen van Site Recovery:

    - **Configuratieserveronderdeel**: coördineert de communicatie tussen uw on-premises omgeving en Azure, en beheert gegevensreplicatie en herstel.
    - **Processerveronderdeel**: fungeert als replicatiegateway. Dit onderdeel ontvangt replicatiegegevens van beveiligde bronmachines, optimaliseert de gegevens met caching, compressie en codering, en verzendt ze naar de Azure-opslag. Hier wordt ook de push-installatie van de Mobility-service naar beveiligde computers afgehandeld en wordt automatische detectie van virtuele VMware-machines uitgevoerd. Naarmate uw implementatie groeit, kunt u extra, afzonderlijk toegewezen processervers toevoegen om het toenemende replicatieverkeer af te handelen.
    - **Hoofddoelserveronderdeel**: hier worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld. 
- **VMware ESX/ESXi-hosts en vCenter-server**: u hebt een of meer ESX/ESXi-hostservers nodig waarop virtuele VMware-machines worden uitgevoerd. We raden u aan een vCenter-server te implementeren voor het beheer van deze hosts. **Opmerking:** **zelfs als u fysieke servers repliceert, is een failback ervan naar VMware** nodig. Als u een fysieke server repliceert, wordt deze als virtuele Azure-machine uitgevoerd wanneer u een failover uitvoert naar Azure. De failback vindt plaats als virtuele VMware-machine naar on-premises. 
    
- **Virtuele machines/fysieke servers**: op elke machine die u naar Azure wilt repliceren, moet de Mobility-service zijn geïnstalleerd. Deze service legt gegevens vast die naar de machine worden weggeschreven en stuurt ze door naar de processerver. Dit onderdeel kan handmatig worden geïnstalleerd, maar ook worden gepusht en vervolgens automatisch door de processerver worden geïnstalleerd wanneer u replicatie voor een machine inschakelt.

### Azure

In de Azure-infrastructuur hebt u het volgende nodig:     - **Azure-account**: u hebt een Microsoft Azure-account nodig.
    - **Azure-opslag**: u hebt een Azure-opslagaccount nodig om gerepliceerde gegevens op te slaan. Gerepliceerde gegevens worden opgeslagen in Azure-opslag en bij een failover worden virtuele Azure-machines ingezet. 
    - **Azure-netwerk**: u hebt een virtueel Azure-netwerk nodig waarmee de ingezette virtuele Azure-machines verbinding maken wanneer er een failover plaatsvindt. 
    
    
### Failback

De failback naar on-premises vindt altijd plaats naar virtuele VMware-machines, zelfs als de failover heeft plaatsgevonden naar een fysieke server. U hebt het volgende nodig:

- **Tijdelijke processerver in Azure**: als u na een failover een failback vanuit Azure wilt uitvoeren, moet u een virtuele Azure-machine hebben geconfigureerd als processerver, om de replicatie vanuit Azure af te handelen. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
- **VPN-verbinding**: voor de failback moet u een VPN-verbinding (of Azure ExpressRoute) hebben ingesteld van het Azure-netwerk naar de on-premises site.
- **Afzonderlijke on-premises hoofddoelserver**: op de on-premises hoofddoelserver wordt de failback afgehandeld. De hoofddoelserver wordt standaard op de beheerserver geïnstalleerd, maar als u een failback van grotere hoeveelheden verkeer uitvoert, kunt u voor dit doel beter een afzonderlijke on-premises hoofddoelserver instellen. 

![Failback in de verbeterde architectuur](./media/site-recovery-components/enhanced-failback.png)

[Lees meer](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) over de implementatievereisten voor de verbeterde architectuur.
[Lees meer](site-recovery-failback-azure-to-vmware-classic.md) over failback in de verbeterde architectuur.




## Virtuele Hyper-V-machines in VMM-clouds repliceren naar Azure

Als u dit scenario wilt implementeren, installeert u tijdens de implementatie van Site Recovery de Azure Site Recovery Provider op de VMM-server. De Provider coördineert en stuurt replicatie met de Site Recovery-service via internet. Tijdens de implementatie van Site Recovery wordt de Azure Recovery Services-agent op de Hyper-V-hostserver geïnstalleerd. De gegevens worden via HTTPS 443 tussen deze server en de Azure-opslag gerepliceerd. De communicatie vanuit zowel de Provider als de agent is beveiligd en versleuteld. De gerepliceerde gegevens in de Azure-opslag zijn eveneens versleuteld.

- On-premises: 
    - **VMM-server**: er moet ten minste één VMM-server met ten minste één privécloud in VMM zijn ingesteld. De server moet worden uitgevoerd op System Center 2012 R2 en mag geen verbinding hebben met internet. Als u ervoor wilt zorgen dat uw virtuele Azure-machines na een failover zijn verbonden met een netwerk, moet u netwerktoewijzing instellen. Daartoe moeten uw virtuele bronmachines zijn verbonden met een VMM-netwerk met virtuele machines. Dat netwerk met virtuele machines moet zijn gekoppeld aan een logisch netwerk dat verbinding heeft met de cloud.
    - **Hyper-V-server**: ten minste één Hyper-V-hostserver die zich in de VMM-cloud bevindt. Op de Hyper-V-hosts moet Windows Server 2012 R2 worden uitgevoerd.
    - **Beveiligde machines**: de bron-Hyper-V-hostserver moet ten minste één virtuele machine hebben die u wilt beveiligen.
    
- Azure: 
    - **Azure-account**: u hebt een Microsoft Azure-account nodig.
    - **Azure-opslag**: u hebt een Azure-opslagaccount nodig om gerepliceerde gegevens op te slaan. Gerepliceerde gegevens worden opgeslagen in Azure-opslag en bij een failover worden virtuele Azure-machines ingezet.
    - **Azure-netwerk**: als u netwerktoewijzing wilt instellen zodat uw virtuele Azure-machines na een failover zijn verbonden met een netwerk, moet u een Azure-netwerk instellen.

    ![VMM naar Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Lees meer over [de implementatievereisten](site-recovery-vmm-to-azure.md#before-you-start).

## Virtuele VMware-machines of fysieke servers repliceren naar een secundaire site

Als u virtuele VMware-machines of fysieke servers met Windows of Linux wilt repliceren naar een secundaire site, downloadt u InMage Scout, dat is opgenomen in het Azure Site Recovery-abonnement. U stelt de onderdeelservers in op elke site (configuratie, proces, hoofddoel) en installeert de Unified Agent op de machines die u wilt repliceren. Na de initiële replicatie verzendt de agent op elke machine deltareplicatiewijzigingen naar de processerver. De processerver optimaliseert de gegevens en brengt deze over naar de hoofddoelserver op de secundaire site. De configuratieserver beheert het replicatieproces.

![VMware naar VMware](./media/site-recovery-components/vmware-to-vmware.png)

### On-premises primaire site

- **Processerver**: stel het processerveronderdeel in op uw primaire site om caching, compressie en optimalisatie van gegevens af te handelen. Hier wordt ook de push-installatie van de Unified Agent afgehandeld naar computers die u wilt beveiligen. 
- **VMware ESX/ESXi- en vCenter-server**: als u virtuele VMware-machines beveiligt, hebt u een VMware EXS/ESXi-hypervisor en optioneel een VMware vCenter-server nodig om hypervisors te beheren.
- **Virtuele machines/fysieke servers**: op virtuele VMware-machines of fysieke servers met Windows of Linux die u wilt beveiligen, moet de Unified Agent zijn geïnstalleerd. De Unified Agent is ook geïnstalleerd op de computers die als hoofddoelserver fungeren. De agent fungeert als communicatieprovider tussen alle onderdelen. 
    
### Secundaire on-premises site
 
- **Configuratieserver**: dit is het eerste onderdeel dat u installeert. De configuratieserver wordt op de secundaire site geïnstalleerd om uw implementatie te beheren, te configureren en te bewaken met behulp van de beheerwebsite of de vContinuum-console. Per implementatie is er maar één configuratieserver. Deze moet worden geïnstalleerd op een computer waarop Windows Server 2012 R2 wordt uitgevoerd.
- **vContinuum-server**: deze wordt geïnstalleerd op dezelfde locatie (secundaire site) als de configuratieserver. De vContinuum-server biedt een console voor het beheren en controleren van de beveiligde omgeving. In een standaardinstallatie is de vContinuum-server de eerste hoofddoelserver. Hierop is de Unified Agent geïnstalleerd.
- **Hoofddoelserver**: de hoofddoelserver bevat gerepliceerde gegevens. De hoofddoelserver ontvangt gegevens van de processerver, maakt een replicamachine op de secundaire site en bevat de gegevensretentiepunten. Het aantal hoofddoelservers dat u nodig hebt, is afhankelijk van het aantal machines dat u wilt beveiligen. Als u failback wilt uitvoeren naar de primaire site, hebt u daar ook een hoofddoelserver nodig. 

### Azure

U implementeert dit scenario met InMage Scout. Voor het gebruik van InMage Scout hebt u een Azure-abonnement nodig. Nadat u een Site Recovery-kluis hebt gemaakt, downloadt u InMage Scout en installeert u de nieuwste updates om de implementatie in te stellen.


## Virtuele Hyper-V-machines repliceren naar Azure (zonder VMM)

Als u virtuele Hyper-V-machines die niet in VMM-clouds worden beheerd, wilt repliceren naar Azure, installeert u de Azure Site Recovery Provider en de Azure Recovery Services-agent tijdens de implementatie van Site Recovery op de Hyper-V-host. De Provider coördineert en stuurt replicatie met de Site Recovery-service via internet. De agent handelt de gegevens voor gegevensreplicatie af via HTTPS 443. De communicatie vanuit zowel de Provider als de agent is beveiligd en versleuteld. De gerepliceerde gegevens in de Azure-opslag zijn eveneens versleuteld.

![Hyper-V-site naar Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

### On-premises

- **Hyper-V-server**: ten minste één Hyper-V-hostserver. Op de Hyper-V-hosts moet Windows Server 2012 R2 worden uitgevoerd.
- **Beveiligde machines**: de bron-Hyper-V-hostserver moet ten minste één virtuele machine hebben die u wilt beveiligen.
    
### Azure

- **Azure-account**: u hebt een Microsoft Azure-account nodig.
- **Azure-opslag**: u hebt een Azure-opslagaccount nodig om gerepliceerde gegevens op te slaan. Gerepliceerde gegevens worden opgeslagen in Azure-opslag en bij een failover worden virtuele Azure-machines ingezet.

[Lees meer](site-recovery-hyper-v-site-to-azure.md#before-you-start) over de implementatievereisten.


## Virtuele Hyper-V-machines in VMM-clouds repliceren naar Azure

Als u dit scenario wilt implementeren, installeert u tijdens de implementatie van Site Recovery de Azure Site Recovery Provider op de VMM-server en de Azure Recovery Services-agent op de Hyper-V-host. De Provider coördineert en stuurt replicatie met de Site Recovery-service via internet. De agent handelt de gegevens voor gegevensreplicatie af via HTTPS 443. De communicatie vanuit zowel de Provider als de agent is beveiligd en versleuteld. De gerepliceerde gegevens in de Azure-opslag (niet actief) zijn eveneens versleuteld.

![VMM naar Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

### On-premises

- **VMM-server**: er moet ten minste één VMM-server met ten minste één privécloud in VMM zijn ingesteld. De server moet worden uitgevoerd op System Center 2012 R2 en mag geen verbinding hebben met internet. Als u ervoor wilt zorgen dat uw virtuele Azure-machines na een failover zijn verbonden met een netwerk, moet u netwerktoewijzing instellen. Hiervoor moet u virtuele bronmachines verbinden met een VMM-netwerk met virtuele machines. Dit netwerk moet zijn gekoppeld aan een logisch netwerk dat is verbonden met de cloud.
- **Hyper-V-server**: ten minste één Hyper-V-hostserver die zich in de VMM-cloud bevindt. Op de Hyper-V-hosts moet Windows Server 2012 R2 worden uitgevoerd.
- **Beveiligde machines**: de bron-Hyper-V-hostserver moet ten minste één virtuele machine hebben die u wilt beveiligen.
    
### Azure

- **Azure-account**: u hebt een Microsoft Azure-account nodig.
- **Azure-opslag**: u hebt een Azure-opslagaccount nodig om gerepliceerde gegevens op te slaan. Gerepliceerde gegevens worden opgeslagen in Azure-opslag en bij een failover worden virtuele Azure-machines ingezet.
- **Azure-netwerk**: als u ervoor wilt zorgen dat virtuele Azure-machines na een failover zijn verbonden met een netwerk, moet u netwerktoewijzing instellen. Daarvoor hebt u een Azure-netwerk nodig.

[Lees meer](site-recovery-vmm-to-azure.md#before-you-start) over de implementatievereisten.

## Virtuele Hyper-V-machines repliceren naar een secundair datacenter

Als u dit scenario wilt implementeren, installeert u tijdens de implementatie van Site Recovery de Azure Site Recovery Provider op de VMM-server. De Provider coördineert en stuurt replicatie met de Site Recovery-service via internet. Gegevens worden tussen de primaire en secundaire Hyper-V-hostservers via het LAN of VPN gerepliceerd met behulp van Kerberos of verificatie via certificaat. De communicatie vanuit de Provider en tussen de Hyper-V-hostservers is beveiligd en versleuteld. 

![On-premises naar on-premises](./media/site-recovery-components/arch-onprem-onprem.png)

### On-premises

- **VMM-server**: we adviseren een VMM-server op de primaire site en één op de secundaire site, elk met minimaal één VMM-privécloud. Op de server moet minimaal System Center 2012 SP1 met de meest recente updates worden uitgevoerd. De server moet zijn verbonden met internet. Voor clouds moet het Hyper-V-mogelijkheidsprofiel zijn ingesteld.
- **Hyper-V-server**: er moeten Hyper-V-hostservers aanwezig zijn in de primaire en secundaire VMM-clouds. Op de hostservers moet minimaal Windows Server 2012 met de nieuwste updates zijn geïnstalleerd. De servers moeten zijn verbonden met internet.
- **Beveiligde machines**: de bron-Hyper-V-hostserver moet ten minste één virtuele machine hebben die u wilt beveiligen.
    
### Azure

U hebt een Azure-abonnement nodig.

[Lees meer](site-recovery-vmm-to-vmm.md#before-you-start) over de implementatievereisten.


## Virtuele Hyper-V-machines met SAN-replicatie repliceren naar een secundair datacenter

In dit scenario installeert u tijdens de implementatie van Site Recovery de Azure Site Recovery Provider op de VMM-server. De Provider coördineert en stuurt replicatie met de Site Recovery-service via internet. Gegevens worden met synchrone SAN-replicatie tussen de primaire en de secundaire opslagmatrices gerepliceerd.

![SAN-replicatie](./media/site-recovery-components/arch-onprem-onprem-san.png)

### On-premises

- **SAN-matrix**: een [ondersteunde SAN-matrix](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), beheerd door de primaire VMM-server. De SAN deelt een netwerkinfrastructuur met een andere SAN-matrix in de secundaire site.
- **VMM-server**: we adviseren een VMM-server op de primaire site en één op de secundaire site, elk met minimaal één VMM-privécloud. Op de server moet minimaal System Center 2012 SP1 met de meest recente updates worden uitgevoerd. De server moet zijn verbonden met internet. Voor clouds moet het Hyper-V-mogelijkheidsprofiel zijn ingesteld.
- **Hyper-V-server**: Hyper-V-hostservers die zich bevinden in de primaire en secundaire VMM-clouds. Op de hostservers moet minimaal Windows Server 2012 met de nieuwste updates zijn geïnstalleerd. De servers moeten zijn verbonden met internet.
- **Beveiligde machines**: de bron-Hyper-V-hostserver moet ten minste één virtuele machine hebben die u wilt beveiligen.
    
### Azure

U hebt een Azure-abonnement nodig.  

[Lees meer](site-recovery-vmm-san.md#before-you-start) over de implementatievereisten.


## Levenscyclus van Hyper-V-beveiliging

In deze werkstroom ziet u het proces voor de beveiliging, replicatie en failover van virtuele Hyper-V-machines. 

1. **Beveiliging inschakelen**: u stelt de Site Recovery-kluis in, configureert replicatie-instellingen voor een VMM-cloud of Hyper-V-site en schakelt beveiliging voor virtuele machines in. Er wordt een taak met de naam **Beveiliging inschakelen** gestart. Deze kan worden bewaakt op het tabblad **Taken**. Met deze taak wordt gecontroleerd of de computer voldoet aan de vereisten en wordt de methode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) aangeroepen die replicatie naar Azure instelt op basis van de instellingen die u hebt geconfigureerd. Met de taak **Beveiliging inschakelen** wordt ook de methode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) aangeroepen om een volledige replicatie voor virtuele machines te initialiseren.
2. **Eerste replicatie**: er wordt een momentopname van de virtuele machine gemaakt en de virtuele harde schijven worden een voor een gerepliceerd tot ze allemaal zijn gekopieerd naar Azure of het secundaire datacenter. De tijd die nodig is om dit te voltooien, is afhankelijk van de grootte van de virtuele machine, van de netwerkbandbreedte en van de oorspronkelijke replicatiemethode. Als er tijdens de eerste replicatie schijfwijzigingen optreden, worden deze door de Hyper-V Replica Replication Tracker bijgehouden als Hyper-V-replicatielogboeken (.hrl), die zich in dezelfde map bevinden als de schijven. Elke schijf heeft een eigen HRL-bestand dat naar de secundaire opslag wordt verzonden. Houd er rekening mee dat de momentopname- en logboekbestanden schijfbronnnen in beslag nemen terwijl de eerste replicatie wordt uitgevoerd. Wanneer de eerste replicatie is voltooid, wordt de momentopname van de virtuele machine verwijderd, en worden de verschillen in het logboek gesynchroniseerd en samengevoegd.
3. **Beveiliging voltooien**: wanneer de eerste replicatie is voltooid, worden de netwerkinstellingen en de overige instellingen na de replicatie zo geconfigureerd dat de virtuele machine is beveiligd. Hiervoor wordt de taak **Beveiliging voltooien** gebruikt. Als u repliceert naar Azure, moet u mogelijk de instellingen voor de virtuele machine zo aanpassen dat deze gereed is voor failover. Nu kunt u een failovertest uitvoeren om te controleren of alles werkt zoals het hoort.
4. **Replicatie**: na de initiële replicatie start de deltasynchronisatie in overeenstemming met de replicatie-instellingen. 
    - **Replicatiefout**: als de deltareplicatie mislukt en een volledige replicatie veel bandbreedte of tijd zou kosten, wordt er een hersynchronisatie uitgevoerd. Als de HRL-bestanden bijvoorbeeld 50% van de schijfgrootte in beslag nemen, wordt de virtuele machine gemarkeerd voor hersynchronisatie. Met een hersynchronisatie wordt de hoeveelheid verzonden gegevens geminimaliseerd door controlesommen van de bron- en virtuele machines te berekenen en alleen de verschillen (delta) te verzenden. Na hersynchronisatie wordt de deltareplicatie hervat. Hersynchronisatie is standaard zo gepland dat deze automatisch buiten kantooruren wordt uitgevoerd, maar u kunt een virtuele machine ook handmatig opnieuw synchroniseren.
    - **Replicatiefout**: als er een replicatiefout optreedt, wordt de replicatie automatisch opnieuw geprobeerd. Als het een onherstelbare fout betreft, zoals een verificatie- of autorisatiefout, of als een replicamachine een ongeldige status heeft, wordt er geen nieuwe poging gedaan. Als het een herstelbare fout betreft, zoals een netwerkfout of weinig schijfruimte/geheugen, wordt er met steeds groter wordende intervallen (1, 2, 4, 8, 10 en vervolgens elke 30 minuten) een nieuwe poging gedaan.
4. **Geplande/niet-geplande failovers**: u kunt naar behoefte geplande of niet-geplande failovers uitvoeren. Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen. Nadat de replica’s van de virtuele machines zijn gemaakt, krijgen deze de status In behandeling. U moet ze doorvoeren om de failover te voltooien, tenzij u repliceert met SAN. In dat geval wordt er automatisch doorgevoerd. Wanneer de primaire site weer is hersteld, kan de failback worden uitgevoerd. Als u hebt gerepliceerd naar Azure, wordt er automatisch een omgekeerde replicatie uitgevoerd. In andere gevallen moet u de omgekeerde replicatie handmatig starten.
 

![werkstroom](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Volgende stappen

[Implementatie voorbereiden](site-recovery-best-practices.md)



<!--HONumber=Jun16_HO2-->


