<properties
    pageTitle="SQL (PaaS) Database vs. SQL Server in de cloud op VM’s (IaaS) | Microsoft Azure"
    description="Informatie over welke SQL Server-cloudoptie geschikt is voor uw toepassing: Azure SQL (PaaS) Database of SQL Server in de cloud op Azure Virtual Machines."
    services="sql-database, virtual-machines"
    keywords="SQL Server cloud, SQL Server in the cloud, PaaS database, cloud SQL Server, DBaaS"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/25/2016"
    ms.author="carlrab"/>

# Kies een SQL Server-cloudoptie: Azure SQL (PaaS) Database of SQL Server op virtuele machines van Azure (IaaS)

Azure heeft twee opties voor het hosten van SQL Server-workloads in de cloud:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): een systeemeigen SQL Database  in de cloud, ook wel bekend als een platform als een servicedatabase (PaaS) of als een database als een service (DBaaS) die is geoptimaliseerd voor ontwikkeling van apps voor software als een service (SaaS). Biedt compatibiliteit met de meeste functies van SQL Server.
* [SQL Server op Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server die is  geïnstalleerd en wordt gehost in de cloud op Windows Server Virtual Machines (VM's) die worden uitgevoerd op Azure, ook wel bekend als een infrastructuur als een service (IaaS).

Ontdek hoe elke optie in het Microsoft-gegevensplatform past en krijg hulp bij het vinden van de juiste optie voor uw zakelijke vereisten. Of u nu kostenbesparingen of minimaal beheer het belangrijkst vindt, met dit artikel kunt u beslissen welke benadering de bedrijfsvereisten levert die u het belangrijkst vindt.


## Gegevensplatform van Microsoft

Een van de eerste belangrijke punten in een discussie over Azure ten opzichte van on-premises SQL Server-databases is dat u alles kunt gebruiken. Het gegevensplatform van Microsoft maakt gebruik van SQL Server-technologie en maakt deze beschikbaar op fysieke on-premises machines, privécloudomgevingen, externe gehoste privécloudomgevingen en openbare cloud. Hiermee kunt u voldoen aan de unieke en diverse bedrijfsbehoeften door een combinatie van on-premises en in de cloud gehoste implementaties terwijl dezelfde set serverproducten, ontwikkelingsprogramma's en kennis wordt gebruikt in deze omgevingen.

   ![Opties SQL Server cloud: SQL server op IaaS of SaaS-SQL Database in de cloud.](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Zoals we zien in het diagram, wordt elke aanbieding gekenmerkt door het niveau van beheer dat u hebt over de infrastructuur (op de X-as) en de mate van kostenefficiëntie die wordt bereikt door consolidatie en automatisering op databaseniveau (op de Y-as).

Bij het ontwerpen van een toepassing zijn vier eenvoudige opties beschikbaar voor het hosten van het SQL Server-onderdeel van de toepassing:

- SQL Server op niet-gevirtualiseerde fysieke computers
- SQL Server in on-premises gevirtualiseerde machines (privécloud)
- SQL Server in Azure Virtual Machines (openbare cloud)
- Azure SQL Database (openbare cloud)

In de volgende gedeeltes vindt u informatie over SQL Server in de openbare cloud: Azure SQL Database en SQL Server op VM’s van Azure. Bovendien verkennen we algemene zakelijke motivators om te bepalen welke optie het meest geschikt is voor uw toepassing.

## De Azure SQL Database en SQL Server op Azure Virtual Machines

**Azure SQL Database** is een relationele database-as-a-service (DBaaS) die wordt gehost in de Azure-cloud en die in de branchecategorie van *Software-as-a-Service (SaaS)* en *Platform-as-a-Service (PaaS)* valt. SQL Database is gebouwd op gestandaardiseerde hardware en software die eigendom is van en die wordt gehost en beheerd door Microsoft. Met SQL Database kunt u rechtstreeks op de service ontwikkelen met behulp van de ingebouwde functies en functionaliteiten. Als u SQL Database gebruikt, betaalt u per gebruik met opties voor het omhoog schalen voor meer vermogen zonder onderbrekingen.

**SQL Server op Azure Virtual Machines** valt in de branchecategorie *infrastructuur als een service (IaaS)* en stelt u in staat SQL Server uit te voeren binnen een virtuele machine in de cloud. Net als bij SQL Database is deze gebouwd op gestandaardiseerde hardware die eigendom is van en die wordt gehost en beheerd door Microsoft. Wanneer u SQL Server gebruikt op een VM, kunt u uw eigen SQL Server-licentie of een vooraf gelicentieerde SQL Server-installatiekopie uit de Azure Portal gebruiken.

Over het algemeen zijn deze twee SQL-opties geoptimaliseerd voor verschillende doeleinden:

- **SQL Database** is geoptimaliseerd om de totale kosten te minimaliseren om veel databases in te richten en te beheren. Dit vermindert beheerkosten omdat u geen virtuele machines, besturingssysteem of database-software hoeft te beheren. Dit omvat upgrades, hoge beschikbaarheid en back-ups. Over het algemeen kan Azure SQL Database het aantal databases dat wordt beheerd door één IT of ontwikkelingsresource aanzienlijk verhogen.
- **SQL Server die wordt uitgevoerd op Azure Virtual Machines** is geoptimaliseerd om bestaande on-premises SQL Server-toepassingen uit te breiden naar de cloud in een hybride scenario of om een bestaande toepassing te implementeren naar Azure in een migratie- of dev/test-scenario. Een voorbeeld van het hybride scenario is het houden van secundaire databasereplica’s in Azure door [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) te gebruiken. Met SQL Server op Azure Virtual Machines hebt u de volledige beheerdersrechten van een speciaal exemplaar van SQL Server en een cloud-gebaseerde VM. Het is een ideale keuze als een organisatie al IT-bronnen beschikbaar heeft voor het onderhouden van de virtuele machines. Met SQL Server op virtuele machines, kunt u een aangepast systeem bouwen voor de specifieke prestaties en beschikbaarheidsvereisten van uw toepassing.

De volgende tabel geeft een overzicht van de belangrijkste kenmerken van de SQL Database en SQL Server op Azure Virtual Machines:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">SQL Database</th>
   <th align="left" valign="middle">SQL Server in Azure VM</th>

</tr>
<tr>
   <td valign="middle"><p><b>Ideaal voor</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Nieuwe in de cloud ontworpen toepassingen met tijdsbeperkingen op het gebied van ontwikkeling en marketing.
          <li type=round>Toepassingen waarvoor ingebouwde hoge beschikbaarheid, herstel na noodgevallen en upgrademechanismen nodig is.
          <li type=round>Teams die het onderliggende besturingssysteem en configuratie-instellingen niet willen beheren.
         <li type=round>Toepassingen die gebruikmaken van uitschaalpatronen.
         <li type=round>Databases met een grootte van maximaal 1 TB.
         <li type=round>Software als een service (SaaS)-toepassingen ontwikkelen.
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Bestaande toepassingen waar snelle migratie naar de cloud met minimale wijzigingen voor nodig is.
      <li type=round>SQL Server-toepassingen waarvoor toegang nodig is tot on-premises resources (zoals Active Directory) uit Azure via een beveiligde tunnel.
      <li type=round>Als u een aangepaste IT-omgeving met volledige beheerdersrechten nodig hebt.
      <li type=round>Snelle ontwikkelings- en testscenario's als u geen on-premises SQL Server-hardware wilt kopen die niet bestemd is voor productie.
      <li type=round>Herstel na noodgevallen voor on-premises SQL Server-toepassingen met [back-up naar Azure Storage](http://msdn.microsoft.com/library/jj919148.aspx) of AlwaysOn-replica's met Azure Virtual Machines.
      <li type=round>Grote databases die groter zijn dan 1 TB zijn.
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>Bronnen</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>U wilt geen gebruik maken van IT-bronnen voor ondersteuning en onderhoud van de onderliggende infrastructuur.
       <li type=round>U wilt zich richten op de toepassingslaag.
       </ul></td>
   <td valign="middle"><ul><li type=round>U hebt IT-bronnen voor ondersteuning en onderhoud.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Totale eigendomskosten</b></p></td>
   <td valign="middle"><ul><li type=round>Elimineert hardwarekosten. Vermindert beheerkosten.</ul></td>
   <td valign="middle"><ul><li type=round>Elimineert hardwarekosten. </ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Bedrijfscontinuïteit</b></p></td>
   <td valign="middle"><ul><li type=round>Azure SQL Database biedt naast ingebouwde mogelijkheden voor fouttolerantie in de infrastructuur functies, zoals herstel naar een bepaald tijdstip, Geo-Restore en geo-replicatie om zakelijke continuïteit te verhogen. Zie voor meer informatie [SQL Database business continuity overview](sql-database-business-continuity.md).</ul></td>
   <td valign="middle"><ul><li type=round>Met SQL Server op Azure VM's kunt u een oplossing met hoge beschikbaarheid en herstel na een noodgeval instellen voor de specifieke behoeften van uw database. Daarmee creëert u een systeem dat is geoptimaliseerd voor uw toepassing. U kunt zelf tests en failovers uitvoeren wanneer dit nodig is. Zie voor meer informatie [High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Hybride cloud</b></p></td>
   <td valign="middle"><ul><li type=round>Uw on-premises toepassing heeft toegang tot gegevens in Azure SQL Database.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Met SQL Server op Azure Virtual Machines krijgt u de toepassingen die deels in de cloud draaien en deels on-premises. Zo kunt u uw on-premises netwerk en Active Directory-domein uitbreiden naar de cloud met [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Bovendien kunt u on-premises gegevensbestanden opslaan in Azure Storage met behulp van [SQL Server-gegevensbestanden in Azure](http://msdn.microsoft.com/library/dn385720.aspx). Zie voor meer informatie [Inleiding voor SQL Server 2014 hybride cloud](http://msdn.microsoft.com/library/dn606154.aspx).
      <li type=round>Ondersteunt herstel na noodgevallen voor on-premises SQL Server-toepassingen met [SQL Server back-up en herstel met Azure Blob Storage](http://msdn.microsoft.com/library/jj919148.aspx) of [AlwaysOn-replica's in Azure VM's](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).
      </ul></td>

</tr>
</table>

## Zakelijke redenen om Azure SQL Database of SQL Server op Azure Virtual Machines te kiezen

### Kosten

Of u nu een  startup bent die geld nodig heeft of een team in een goedlopend bedrijf met een krap budget, beperkte middelen zijn vaak de belangrijkste reden bij het bepalen hoe u uw databases wilt hosten. In dit deel kunt u eerst lezen over de grondbeginselen van facturering en licenties in Azure met betrekking tot deze twee opties voor relationele databases: SQL Database en SQL Server op Azure Virtual Machines. Daarna bekijken we hoe de totale kosten van de toepassing berekend moeten worden.

#### Grondbeginselen facturering en licenties

**SQL Database** wordt als service verkocht aan klanten en niet met een licentie terwijl voor SQL Server op Azure VM’s traditionele SQL Server-licenties nodig zijn.

Op dit moment is **SQL Database** beschikbaar in verschillende servicelagen, die allemaal per uur worden gefactureerd tegen een vast tarief op basis van de servicelaag en het prestatieniveau dat u kiest. Bovendien wordt uitgaand internetverkeer in rekening gebracht. De servicecategorieën Basic, Standard en Premium zijn ontworpen om voorspelbare prestaties te leveren met meerdere prestatieniveaus om te voldoen aan de piekvereisten van uw toepassing. U kunt wisselen tussen servicecategorieën en prestatieniveaus om te voldoen aan de uiteenlopende doorvoerbehoeften van uw toepassing. Als er veel transacties plaatsvinden in uw database en er veel  gelijktijdige gebruikers ondersteund moeten worden, raden wij de Premium servicecategorie aan. Voor de meest recente informatie over de huidige ondersteunde servicecategorieën, zie [Azure SQL Database servicecategorieën](sql-database-service-tiers.md).

Met **SQL Database** wordt de databasesoftware automatisch geconfigureerd, hersteld en bijgewerkt door Microsoft, waardoor uw beheerkosten worden verlaagd. Bovendien kunt u met de [ingebouwde back-up](sql-database-business-continuity.md)mogelijkheden  aanzienlijk kosten besparen, vooral wanneer u een groot aantal databases hebt.

Met **SQL Server op virtuele machines van Azure** gebruikt u traditionele SQL Server-licenties. U kunt de door het platform geleverde SQL Server installatiekopie (met een licentie) gebruiken of  uw SQL Server-licentie meenemen. Wanneer u de door Azure geleverde installatiekopieën gebruikt, zijn de operationele kosten afhankelijk van de VM-grootte en de versie van SQL Server die u kiest. U betaalt licentiekosten van SQL Server en Windows Server per minuut, samen met de kosten van Azure Storage voor de VM-schijven, ongeacht de VM-grootte of versie van SQL Server. Met de optie factureren per minuut kunt u SQL Server blijven gebruiken zonder aanvullende SQL Server-licenties te kopen. Als u uw eigen SQL Server-licentie meeneemt naar Azure, worden alleen Windows Server en de kosten voor opslag in rekening gebracht. Zie voor meer informatie over meenemen van uw eigen licentie [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### De totale kosten voor de toepassing berekenen

Als u een cloudplatform gaat gebruiken, bestaan de kosten van het uitvoeren van uw toepassing vooral uit de ontwikkelings-,  beheer- en servicekosten die nodig zijn voor het openbare cloudplatform.

Dit is de gedetailleerde kostenberekening voor uw toepassing die wordt uitgevoerd in SQL Database en SQL Server op Azure VM’s:

**Als u Azure SQL Database gebruikt:**

*Totale kosten van de toepassing = geminimaliseerde beheerkosten + softwareontwikkelingskosten + servicekosten voor SQL Database*

**Als u SQL Server op VM’s van Azure gebruikt:**

*Totale kosten van de toepassing = geminimaliseerde softwareontwikkelings-/wijzigingskosten + beheerkosten + SQL Server en Windows Server-licentie kosten + Azure opslagkosten*

Zie de volgende bronnen voor meer informatie over prijzen:

- [Prijzen SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prijzen virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/) voor [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure prijscalculator](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Er is een kleine subset van functies op SQL Server die niet van toepassing zijn op of niet beschikbaar zijn in SQL Database. Zie [SQL Database General Limitations and Guidelines](sql-database-general-limitations.md) en [SQL Database Transact-SQL information](sql-database-transact-sql-information.md) voor meer informatie. Als u een bestaande SQL Server-oplossing naar de cloud verplaatst, raadpleegt u het artikel [Migrating a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md). Wanneer u een bestaande on-premises SQL Server-toepassing naar SQL Database migreert, dan kunt u de toepassing bijwerken om te profiteren van de mogelijkheden die cloudservices bieden. U kunt bijvoorbeeld [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) of [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) te gebruiken voor het hosten van uw toepassingslaag om meer kosten te besparen.

### Beheer

Veel bedrijven besluiten over te stappen naar een cloudservice zowel vanwege offloading van beheercomplexiteit als de kosten. Met **SQL Database**beheert Microsoft de onderliggende hardware, repliceert automatisch alle gegevens voor hoge beschikbaarheid, configureert de databasesoftware en werkt deze bij, beheert taakverdeling en voert transparante failover uit als er een serverfout optreedt. U kunt uw database blijven beheren, maar u hoeft de database-engine, het server-besturingssysteem of de hardware niet langer te beheren.  Voorbeelden van items die u kunt blijven beheren zijn onder meer databases en aanmeldingen, index en query afstemmen, en controle en beveiliging. 

Aan de andere kant kunt u interne expertise hebben en de wens om controle te blijven houden over databaselocatie tot aan de locatie van de schijf. Met **SQL Server die wordt uitgevoerd op Azure VM’s**, hebt u volledige controle over het besturingssysteem en de configuratie van het SQL Server-exemplaar. Met een VM kunt u zelf bepalen wanneer een upgrade van het besturingssysteem en de database-software nodig is en wanneer u aanvullende software zoals antivirus- en back-upprogramma's moet installeren. Bovendien kunt u de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren.  Zo kunt u met Azure de grootte van een VM indien nodig wijzigen. Zie voor meer informatie [Virtual Machine en Cloud Service Sizes for Azure](../virtual-machines/virtual-machines-linux-sizes.md).

### Service Level Agreement (SLA)

Voor veel IT-afdelingen is het voldoen aan uptimeverplichtingen van een Service Level Agreement (SLA) een topprioriteit. In dit gedeelte kijken we welke SLA van toepassing is op de databasehostingopties.

Voor **SQL Database** Basic, Standard en Premium servicelagen biedt Microsoft een beschikbaarheids- SLA van 99,99%. Zie [Service Level Agreement](https://azure.microsoft.com/support/legal/sla/sql-database/) voor de meest recente informatie. Zie [Servicecategorieën](sql-database-service-tiers.md) voor de meest recente informatie over SQL Database servicelagen en de ondersteunde plannen voor bedrijfscontinuïteit.

Voor **SQL Server die wordt uitgevoerd op Azure Virtual Machines**, biedt Microsoft een beschikbaarheids-SLA van 99,95% die alleen van toepassing is op de virtuele machine. Deze SLA heeft geen betrekking op de processen (zoals SQL Server) die worden uitgevoerd op de VM en vereist dat u ten minste twee exemplaren van de virtuele machine in een beschikbaarheidsset host. Voor de meest recente informatie, zie de [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Voor hoge beschikbaarheid (HA) van de database binnen virtuele machines, moet u een van de ondersteunde opties voor hoge beschikbaarheid in SQL Server configureren, zoals [AlwaysOn-beschikbaarheidsgroepen](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

### <a name="market"></a>Implementatietijd

**SQL Database** is de juiste oplossing voor toepassingen die zijn ontworpen voor de cloud wanneer de productiviteit van ontwikkelaars en snelle implementatietijd essentieel zijn. Met programmatische DBA-functies is het ideaal voor cloud-architecten en -ontwikkelaars aangezien het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert. U kunt bijvoorbeeld de [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) en [PowerShell-Cmdlets](http://msdn.microsoft.com/library/azure/dn546726.aspx) gebruiken om beheerbewerkingen te automatiseren en beheren voor duizenden databases. Met functies zoals [Pools voor elastische databases](sql-database-elastic-pool.md) kunt u zich richten op de toepassingslaag en uw oplossing sneller leveren aan de markt.

**SQL Server die wordt uitgevoerd op virtuele machines van Azure** is ideaal als uw bestaande of nieuwe toepassingen toegang tot en beheer van alle functies van een SQL Server-exemplaar vereisen. Het is ook geschikt wanneer u bestaande on-premises toepassingen en databases wilt migreren naar Azure. Omdat u de presentatie-, toepassings- en gegevenslagen niet hoeft te wijzigen, bespaart u tijd en geld op het opnieuw vormgeven van uw architectuur. In plaats daarvan kunt u zich richten op het migreren van uw oplossingen naar Azure en het uitvoeren van  prestatieoptimalisatie die mogelijk wordt vereist door het Azure-platform. Zie voor meer informatie [Best practices prestaties for SQL Server on Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-performance.md).

## Samenvatting

In dit artikel werden SQL Database en SQL Server op Azure Virtual Machines (VM's) verkend en algemene zakelijke motivators besproken die invloed kunnen hebben op uw beslissing. Hier volgt een samenvatting met enkele suggesties:

Kies voor een **Azure SQL Database** als:

- U nieuwe cloudgebaseerde toepassingen bouwt of als u uw bestaande SQL Server-oplossing wilt migreren om te profiteren van de kostenbesparingen en optimalisatie van de prestaties die cloudservices bieden. Deze aanpak biedt de voordelen van een volledig beheerde cloudservice, helpt de implementatietijd te verlagen en kan zorgen voor kostenoptimalisatie op de lange termijn.

- U wilt dat Microsoft algemene managementbewerkingen uitvoert op uw databases en vereist betere beschikbaarheids-SLA's voor databases.



Kies voor **SQL Server op Azure VM's** als:

- U beschikt over bestaande on-premises toepassingen en wilt stoppen met het beheren van uw eigen hardware, of u overweegt hybride-oplossingen. Met deze benadering krijgt u eerder toegang tot hoge capaciteit voor de database terwijl er verbinding wordt gemaakt met uw on-premises toepassingen via een beveiligde tunnel.

- U hebt bestaande IT-bronnen, vereist volledige beheerdersrechten over SQL Server en volledige compatibiliteit met on-premises SQL Server. Met deze benadering kunt u de kosten voor ontwikkeling of aanpassen van bestaande toepassingen minimaliseren met de flexibiliteit om de meeste toepassingen uit te voeren. Daarnaast biedt het volledig beheer van de VM, het besturingssysteem en de databaseconfiguratie.



> [AZURE.NOTE] Wilt u SQL Server 2016 CTP2 proberen? Meld u aan voor Microsoft Azure en maak [hier](http://aka.ms/sql2016vm "hier") een virtuele machine waarop SQL Server 2016 CTP2 al is geïnstalleerd.

## Volgende stappen
- Zie [SQL Database tutorial: maak in slechts enkele minuten een SQL database met behulp van de Azure portal](sql-database-get-started.md) om aan de slag te gaan met SQL Database.
- Zie [prijzen SQL Database] (https://azure.microsoft.com/pricing/details/sql-database/)
- Zie [Provision a SQL Server virtual machine in Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) om aan de slag te kunnen met SQL Server op Azure VM’s.



<!----HONumber=Jun16_HO2-->


