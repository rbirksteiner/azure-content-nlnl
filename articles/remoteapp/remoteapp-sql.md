<properties
   pageTitle="SQL Azure met Azure RemoteApp | Microsoft Azure"
   description="Lees hoe u SQL Azure gebruikt met Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="05/02/2016"
   ms.author="elizapo"/>

# SQL Azure met Azure RemoteApp

Wanneer klanten ervoor kiezen om hun Windows-toepassingen met Azure RemoteApp te hosten in de cloud, willen ze vaak ook hun gegevens, zoals SQL-servers, naar de cloud migreren voor een volledige cloudimplementatie. Hiermee ontstaat een volledige in de cloud gehoste oplossing die altijd en overal kan worden gebruikt door elk apparaat waarop Azure RemoteApp is geïnstalleerd. Hieronder vindt u koppelingen en verwijzingen evenals richtlijnen die u helpen bij dit proces.  

## Uw SQL-gegevens migreren

Begin met het [migreren van een SQL Server-database naar Azure SQL Database](../sql-database/sql-database-cloud-migrate.md). 

## Azure RemoteApp configureren
Een Windows-toepassing hosten in Azure RemoteApp. Hieronder volgt een zeer globaal stappenplan:

1.     Maak de [VM voor de Azure RemoteApp-sjabloon](remoteapp-imageoptions.md). 
2.     Installeer de vereiste toepassing op de VM.
3.     Configureer de toepassing zo dat deze verbinding maakt met de SQL DB en controleer of deze werkt.
4.     Sysprep en sluit de VM af. Leg dit vast als een installatiekopie voor gebruik met Azure. **Opmerking:** U moet ervoor zorgen dat de toepassing de databaseverbindingsgegevens gedurende het sysprep-proces behoudt. Als de toepassing de DB-verbindingsgegevens niet kan behouden, zou u contact kunnen opnemen met de leverancier van de toepassing om te vragen hoe we de verbindingsreeks kunnen opgeven.
5.     Importeer de aangepaste installatiekopie in de Azure RemoteApp-bibliotheek waarbij u de juiste geografische locatie van uw Azure SQL-implementatie selecteert. 
6.     Gebruik bovenstaande sjabloon om een RemoteApp-verzameling te implementeren in hetzelfde datacenter als uw SQL Azure-implementatie en publiceer de toepassing. De implementatie van Azure RemoteApp in hetzelfde datacenter als uw Azure SQL-implementatie zorgt voor de hoogste verbindingssnelheden en de laagste latentie. 

## Overwegingen voor app- en SQL-configuratie:
Er zijn enkele aandachtspunten bij het gebruik van Azure SQL met RemoteApp:

Lees [Een Azure SQL-databasefirewall configureren](../sql-database/sql-database-firewall-configure.md). In een fragment uit het artikel staat 'in eerste instantie is alle toegang tot uw Azure SQL Database-server geblokkeerd door de firewall. Als u de Azure SQL Database-server wilt gaan gebruiken, gaat u naar de klassieke portal en geeft u een of meer firewallregels op serverniveau op die toegang tot uw Azure SQL Database-server inschakelen. Gebruik de firewallregels om op te geven welke IP-adresbereiken van internet zijn toegestaan en of Azure-toepassingen al dan niet kunnen proberen verbinding te maken met uw Azure SQL Database-server.'

En wanneer een computer via internet verbinding probeert te maken met de databaseserver, wordt het oorspronkelijke IP-adres van de aanvraag door de firewall gecontroleerd met de volledige set firewallregels op serverniveau en (indien nodig) databaseniveau. 'Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de firewallregels op serverniveau, wordt de verbinding aan uw Azure SQL Database-server verleend.' Daarom kunnen we IP-bereiken gebruiken en niet alleen afzonderlijke bron-IP-adressen.

Volg de stapsgewijze instructies in [Firewall-instellingen configureren in SQL Database via Azure Portal](../sql-database/sql-database-configure-firewall-settings.md) om het IP-adresbereik op te geven. Geef bij de configuratie van de SQL-firewallregels het IP-adresbereik op van het subnet dat is opgegeven voor de Azure RemoteApp-verzameling. Op die manier kunnen de ARA-servers verbinding maken met SQL DB, ook al hebben ze dynamisch toegewezen IP-adressen.

## Problemen oplossen
Als het gebruik van een clienttoepassing, gehost in Azure RemoteApp die verbinding maakt met een SQL-database, traag is wanneer deze wordt gehost in Azure of on-premises, kan dit een aantal redenen hebben.  

- Netwerklatentie tussen uw apparaat en Azure is hoog. Voor de beste prestaties gebruikt u de best en snelst mogelijke netwerkverbinding. Gebruik [azurespeed.com](http://azurespeed.com/) als een algemeen hulpprogramma om latentie tussen uw apparaten en het Azure Datacenter te testen.  
- Client-app, gehost in Azure RemoteApp, is zwaar belast. U kunt de prestaties verbeteren wanneer u een ander abonnement kiest, bijvoorbeeld Premium. U zou ook de resources kunnen bewaken die door uw toepassing worden verbruikt: druk tijdens een actieve sessie achtereenvolgens op Ctrl, Alt en End om het SAS-scherm te openen. Selecteer Taakbeheer en observeer het resourceverbruik van uw app.
- SQL-server wordt zwaar belast of is niet geoptimaliseerd. Volg de SQL-richtlijnen voor probleemoplossing. 




<!--HONumber=Jun16_HO2-->


