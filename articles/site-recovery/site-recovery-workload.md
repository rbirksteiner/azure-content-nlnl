<properties
    pageTitle="Welke workloads kunt u met Azure Site Recovery beveiligen?" 
    description="Azure Site Recovery beveiligt uw workloads en toepassingen door de replicatie, failovers en het herstel van on-premises virtuele machines en fysieke servers naar Azure of een secundaire on-premises site te coördineren." 
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

# Welke workloads kunt u met Azure Site Recovery beveiligen?


In dit artikel wordt beschreven welke workloads en toepassingen u met Azure Site Recovery kunt beveiligen.

Eventuele opmerkingen of vragen kunt u onder aan dit artikel plaatsen of op het [Azure Recovery Services-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Overzicht

Organisaties hebben een BCDR-strategie (strategie voor bedrijfscontinuïteit en noodherstel) nodig die bepaalt op welke wijze toepassingen, workloads en gegevens beschikbaar blijven tijdens geplande en ongeplande uitval, en ervoor zorgt dat deze zo snel mogelijk worden hersteld naar een normale manier van functioneren.

Site Recovery is een Azure-service die bijdraagt aan uw BCDR-strategie door u in staat te stellen op toepassingen gerichte flexibiliteitsoplossingen te implementeren. Hiertoe organiseert Site Recovery de replicatie van fysieke on-premises servers en virtuele machines in de cloud (Azure) of naar een secundaire site. U kunt Site Recovery gebruiken om replicatie, noodhersteltests, failovers en failbacks uit te voeren. Hierbij maakt het niet uit of u Windows-apps of Linux-apps gebruikt, en of deze worden uitgevoerd op fysieke servers of op virtuele VMware- of Hyper-V-machines.


Site Recovery kan worden geïntegreerd met diverse Microsoft-toepassingen, waaronder SharePoint, Exchange, Dynamics, SQL Server en Active Directory. Microsoft werkt ook nauw samen met toonaangevende leveranciers zoals Oracle, SAP, IBM en Red Hat, zodat hun toepassingen en services goed werken op Microsoft-platforms zoals Azure. U kunt uw oplossing per app aanpassen.

## Waarom u Site Recovery moet gebruiken voor het beveiligen van toepassingen

Site Recovery biedt als volgt beveiliging op toepassingsniveau en herstel: 

- Bijna-synchrone replicatie met RPO’s van slechts 30 seconden. Hiermee wordt voldaan aan de behoeften van zelfs de meest kritieke zakelijke apps.
- Toepassingsconsistente momentopnamen voor toepassingen met één of meer lagen.
- Integratie met SQL Server AlwaysOn en samenwerking met replicatietechnologieën die op toepassingsniveau actief zijn, waaronder AD-replicatie, SQL AlwaysOn, Exchange Database-beschikbaarheidsgroepen (DAG’s) en Oracle Data Guard.
- Flexibele herstelplannen waarmee u een volledige reeks toepassingen kunt herstellen met één klik. U kunt in uw plan ook externe scripts en handmatige acties opnemen. 
- Geavanceerd netwerkbeheer in Site Recovery en Azure om de netwerkvereisten voor apps te vereenvoudigen. Dit omvat onder andere de mogelijkheid om IP-adressen te reserveren, om load balancing te configureren en om te integreren met Azure Traffic Manager, zodat u met een laag RTO kunt schakelen tussen netwerken.
-  Een uitgebreide Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik, en kunnen worden gedownload en geïntegreerd met herstelplannen.



##Workloadoverzicht

Met Site Recovery kan elke app worden gerepliceerd die wordt uitgevoerd op een ondersteunde virtuele machine. Bovendien wordt er samengewerkt met productteams om aanvullende toepassingsspecifieke tests uit te voeren.

**Workload** | **Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-machines repliceren naar Azure**
---|---|---|---|---
Active Directory, DNS | J | J | J | J 
Web-apps (IIS, SQL) | J | J | J | J
SCOM | J | J | J | J
SharePoint | J | J | J | J
SAP<br/><br/>SAP-site repliceren naar Azure voor niet-cluster | J (door Microsoft getest) | J (door Microsoft getest) | J (door Microsoft getest) | J (door Microsoft getest)
Exchange (niet-DAG) | J | Binnenkort beschikbaar | J | J
Extern bureaublad/VDI | J | J | J | N.v.t. 
Linux (besturingssysteem en apps) | J (door Microsoft getest) | J (door Microsoft getest) | J (door Microsoft getest) | J (door Microsoft getest) 
Dynamics AX | J | J | J | J
Dynamics CRM | J | Binnenkort beschikbaar | J | Binnenkort beschikbaar
Oracle | J (door Microsoft getest) | J (door Microsoft getest) | J (door Microsoft getest) | J (door Microsoft getest)
Windows-bestandsserver | J | J | J | J


## Active Directory en DNS repliceren

Voor de meeste zakelijke apps zijn een Active Directory- en DNS-infrastructuur essentieel. Tijdens het herstel na noodgevallen moet u deze infrastructuuronderdelen beveiligen en herstellen voordat u uw workloads en apps herstelt.

U kunt Site Recovery gebruiken om een plan voor volledig geautomatiseerd noodherstel te maken voor Active Directory en DNS. Als u bijvoorbeeld voor SharePoint en SAP een failover wilt uitvoeren van een primaire naar een secundaire site, kunt u een herstelplan opstellen waarin eerst een failover wordt uitgevoerd voor Active Directory, met een aanvullend toepassingsspecifiek herstelplan om daarna een failover uit te voeren voor de apps die afhankelijk zijn van Active Directory.

[Meer informatie](site-recovery-active-directory.md) over het beveiligen van Active Directory en DNS.

## SQL Server beveiligen

SQL Server biedt een Data Services-basis voor Data Services voor vele zakelijke apps in een on-premises datacenter.  Site Recovery kan in combinatie met SQL Server HA-/DR-technologieën worden gebruikt om zakelijke apps met meerdere lagen te beveiligen die gebruikmaken van SQL Server. Site Recovery biedt:

- Een eenvoudige en voordelige oplossing voor herstel na noodgevallen voor SQL Server. Replicatie van meerdere versies en edities van zelfstandige SQL Server-servers en -clusters naar Azure of een secundaire site.  
- Integratie met SQL AlwaysOn-beschikbaarheidsgroepen voor het beheren van failovers en failbacks met Azure Site Recovery-herstelplannen.
- End-to-endherstelplannen voor alle lagen in een toepassing, met inbegrip van de SQL Server-databases.
- Het schalen van SQL Server voor tijdelijk grote workloads met Site Recovery door ze door te sturen naar grotere virtuele IaaS-machines in Azure.
- Eenvoudig testen van SQL Server-noodherstel. U kunt testfailovers uitvoeren om gegevens te analyseren en om nalevingscontroles uit te voeren zonder dat dit invloed heeft op de productieomgeving.

[Meer informatie](site-recovery-sql.md) over het beveiligen van SQL Server.

##SharePoint beveiligen

Met Azure Site Recovery kunnen SharePoint-implementaties als volgt worden beveiligd:

- Er is geen stand-byfarm voor noodherstel meer nodig, zodat u ook geen kosten meer maakt voor de bijbehorende infrastructuur. Met Site Recovery kunt u een volledige farm (met web-, app- en databaselagen) repliceren naar Azure of naar een secundaire site.
- Site Recovery vereenvoudigt het beheer en de implementatie van toepassingen. Updates die worden geïmplementeerd op de primaire site, worden automatisch gerepliceerd en zijn dus na uitvoering van een failover en herstel van een farm beschikbaar op een secundaire site. Site Recovery vermindert bovendien de beheercomplexiteit en verlaagt de kosten die samenhangen met het up-to-date houden van een stand-byfarm.
- Site Recovery vereenvoudigt het ontwikkelen en testen van SharePoint-toepassingen, omdat er een op de productieomgeving lijkende replica-omgeving wordt gemaakt, die naar wens kan worden gebruikt voor testen en foutopsporing.
- Door Site Recovery te gebruiken om SharePoint-implementaties te migreren naar Azure, wordt de overstap naar de cloud vereenvoudigd.

[Meer informatie](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) over het beveiligen van SharePoint.


## Dynamics AX beveiligen

Met Azure Site Recovery kunt u op de volgende manier uw Dynamics AX ERP-oplossing beveiligen:

- Site Recovery organiseert de replicatie van uw volledige Dynamics AX-omgeving (web- en AOS-lagen, databaselagen, SharePoint) naar Azure of een secundaire site.
- Site Recovery vereenvoudigt de migratie van Dynamics AX-implementaties naar de cloud (Azure).
- Site Recovery vereenvoudigt de ontwikkeling en het testen van Dynamics AX-toepassingen door een op de productieomgeving lijkende replica-omgeving te bieden, zodat u naar wens kunt testen en fouten kunt opsporen.

[Meer informatie](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) over het beveiligen van Dynamic AX.

## Extern bureaublad-services beveiligen 

Met Extern bureaublad-services (ook wel RDS genoemd) worden virtuele-desktopinfrastructuren (VDI) en op sessies gebaseerde bureaubladen en toepassingen ingeschakeld, waardoor gebruikers waar dan ook kunnen werken. Met Azure Site Recovery kunt u het volgende doen:

- Beheerde of onbeheerde gepoolde virtuele bureaubladen repliceren naar een secundaire site, en externe toepassingen en sessies naar een secundaire site of Azure.
- Dit is wat u kunt repliceren:

**RDS** | **Virtuele Hyper-V-machines repliceren naar een secundaire site** | **Virtuele Hyper-V-machines repliceren naar Azure** | **Virtuele VMware-machines repliceren naar een secundaire site** | **Virtuele VMware-machines repliceren naar Azure** | **Fysieke servers repliceren naar een secundaire site** | **Fysieke servers repliceren naar Azure**
---|---|---|---|---|---|---
**Gepoold virtueel bureaublad (onbeheerd)** | Ja | Nee | Ja | Nee | Ja | Nee
**Gepoold virtueel bureaublad (beheerd en zonder UDP)** | Ja | Nee | Ja | Nee | Ja | Nee
**Externe toepassingen en bureaubladsessies (zonder UDP)** | Ja | Ja | Ja | Ja | Ja | Ja


[Meer informatie](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) over het beveiligen van RDS.


## Exchange beveiligen

Met Site Recovery wordt Exchange als volgt beveiligd:

- Bij kleine implementaties van Exchange, met één server of met niet-geclusterde servers, kan Site Recovery een replicatie verzorgen en een failover uitvoeren naar Azure of een secundaire site.
- Bij grotere implementaties kan Site Recovery worden geïntegreerd met Exchange DAG’s.
- Exchange DAG's zijn de aanbevolen oplossing voor Exchange-noodherstel binnen grote bedrijven.  De Site Recovery-herstelplannen kunnen DAG’s bevatten, zodat er DAG-failovers kunnen worden uitgevoerd tussen verschillende sites.


[Meer informatie](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) over het beveiligen van Exchange.

## SAP beveiligen

Gebruik Site Recovery om uw SAP-implementatie als volgt te beveiligen: 

- Beveilig de gehele SAP-implementatie door verschillende implementatielagen te repliceren naar Azure of naar een secundaire site.
- Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
- Vereenvoudig SAP-ontwikkeling en -tests door naar behoeven een op de productieomgeving lijkende replica-omgeving te maken, zodat u toepassingen kunt testen en foutopsporing kunt uitvoeren.

[Meer informatie](http://aka.ms/asr-sap) over het beveiligen van SAP.

## Volgende stappen

[Voorbereiding](site-recovery-best-practices.md) op de implementatie van Site Recovery




<!--HONumber=Jun16_HO2-->


