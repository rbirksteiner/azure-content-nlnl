<properties
    pageTitle="Aan de slag met SQL Server in Azure Virtual Machines | Microsoft Azure"
    description="Verplaats de werkbelasting van uw on-premises SQL Server-database naar de cloud met Azure Virtual Machines. Ga snel aan de slag met vooraf geconfigureerde installatiekopieën voor virtuele SQL-machines"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="06/03/2016"
    ms.author="jroth"/>

# Aan de slag met SQL Server in Azure Virtual Machines

Dit onderwerp beschrijft uw opties voor het uitvoeren van SQL Server op virtuele machines in Azure, en biedt richtlijnen en bronnen om aan de slag te gaan.

Mogelijk bent u een databasebeheerder en wilt u uw on-premises SQL Server-werkbelasting naar de cloud verplaatsen. Of misschien bent u een ontwikkelaar en overweegt u de relationele-databasemogelijkheden van SQL Server te benutten in uw Azure-toepassing. Wat is het voordeel van het uitvoeren van uw SQL Server-werkbelasting op virtuele machines in Azure? De volgende overzichtsvideo beschrijft de voordelen en biedt een technisch overzicht.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## De voordelen evalueren

Voordat u begint, moet u eerst evalueren wat de voordelen zijn van het gebruik van SQL Server op virtuele machines in Azure.

Als u andere werkbelastingen, zoals een bedrijfstoepassing, naar Azure verplaatst, is het handig ook alle afhankelijke SQL Server-databases naar Azure te verplaatsen. Dit zorgt voor betere prestaties. Maar het hosten van SQL Server op virtuele machines in Azure heeft nog meer voordelen. U hebt bijvoorbeeld automatisch toegang tot meerdere datacenters voor wereldwijde aanwezigheid en herstel na een noodgeval. Zie voor een volledige lijst met scenario's en voordelen de [productpagina van SQL Server op virtuele machines in Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] Wanneer u SQL Server op virtuele machines in Azure evalueert, bekijk dan ook de andere opslag- en SQL-opties in Azure, zoals [SQL Database](../sql-database/sql-database-technical-overview.md), [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) en [SQL Server Stretch Database](../sql     -server-stretch-database/sql-server-stretch-database-overview.md). Zie [Een SQL Server-optie voor de cloud kiezen: Azure SQL (PaaS) Database of SQL Server op virtuele machines in Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md) voor een gedetailleerde vergelijking.

Nadat u hebt besloten SQL Server uit te voeren op virtuele machines in Azure, is een van uw eerste beslissingen of u een installatiekopie voor een virtuele machine wilt gebruiken die de SQL Server-licentiekosten omvat. U kunt ook kiezen voor Bring Your Own License (BYOL). U betaalt dan alleen voor de virtuele machine zelf. In de volgende twee secties worden deze opties beschreven.

## Optie 1: een virtuele SQL-machine implementeren (licentieverlening per minuut)
De volgende tabel bevat een matrix met beschikbare SQL Server-installatiekopieën in de galerie met virtuele machines. Klik op een koppeling om te beginnen met het maken van een nieuwe virtuele SQL-machine met de versie, de editie en het besturingssysteem die u hebt opgegeven. Alle installatiekopieën zijn inclusief [SQL Server-licentiekosten](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Stapsgewijze richtlijnen zijn beschikbaar in de zelfstudie [Een virtuele SQL Server-machine inrichten in de Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Bekijk ook de [Aanbevolen procedures voor de beste prestaties voor virtuele SQL Server-machines](virtual-machines-windows-sql-performance.md), waarin wordt uitgelegd hoe u de juiste machinegrootte selecteert, evenals andere functies die tijdens het inrichten beschikbaar zijn.

|SQL Server-versie|Besturingssysteem|SQL Server-editie|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL Server 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Optie 2: een virtuele SQL-machine implementeren (BYOL)
De tweede mogelijkheid is Bring Your Own License (BYOL). In dit scenario betaalt u alleen voor de virtuele machine, zonder eventuele extra kosten voor SQL Server-licentieverlening. Als u uw eigen licentie wilt gebruiken, raadpleegt u onderstaande matrix met SQL Server-versies, SQL Server-edities en besturingssystemen. In de portal worden de namen van deze installatiekopieën voorafgegaan door **{BYOL}**.

> [AZURE.IMPORTANT] Voor het gebruik van installatiekopieën voor virtuele machines op basis van BYOL moet u beschikken over een Enterprise-overeenkomst met [licentiemobiliteit via Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Ook hebt u een geldige licentie nodig voor de SQL Server-versie/-editie die u wilt gebruiken. U moet [de benodigde BYOL-informatie aan Microsoft verstrekken](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) binnen **10** dagen na inrichting van uw virtuele machine.

De richtlijnen in de [Zelfstudie voor inrichting](virtual-machines-windows-portal-sql-server-provision.md) zijn van toepassing, maar u moet een van de volgende **BYOL**-installatiekopieën gebruiken. Bekijk ook de [Aanbevolen procedures voor de beste prestaties voor virtuele SQL Server-machines](virtual-machines-windows-sql-performance.md), waarin wordt uitgelegd hoe u de juiste machinegrootte selecteert, evenals andere functies die tijdens het inrichten beschikbaar zijn.

|SQL Server-versie|Besturingssysteem|SQL Server-editie|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## Uw virtuele SQL-machine beheren
Na het inrichten van uw virtuele SQL Server-machine zijn er verschillende optionele beheertaken die u kunt uitvoeren. Met betrekking tot bepaalde aspecten kunt u SQL Server op exact dezelfde manier configureren en beheren als u on-premises zou doen. Andere taken zijn echter specifiek voor Azure. In de volgende secties worden enkele van deze taken uitgelicht. Ook vindt u er koppelingen naar meer informatie.

### Uw gegevens migreren

Als u een bestaande database hebt, doet u er verstandig aan deze te verplaatsen naar uw nieuw ingerichte virtuele SQL-machine. Zie [Een database migreren naar SQL Server op een virtuele machine in Azure](virtual-machines-windows-migrate-sql.md) voor een lijst met migratieopties en begeleiding bij de migratie.

### Hoge beschikbaarheid configureren

Als u hoge beschikbaarheid nodig hebt, overweeg dan SQL Server-beschikbaarheidsgroepen te configureren. Hierbij combineert u meerdere virtuele machines in Azure tot een virtueel netwerk. In de Azure Portal vindt u een sjabloon voor het instellen van deze configuratie. Zie [Een AlwaysOn-beschikbaarheidsgroep configureren op virtuele machines in Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) voor meer informatie. Als u de beschikbaarheidsgroep en de bijbehorende listener handmatig wilt configureren, raadpleegt u [AlwaysOn-beschikbaarheidsgroepen configureren op virtuele machines in Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Raadpleeg voor andere overwegingen met betrekking tot hoge beschikbaarheid [Hoge beschikbaarheid en herstel na een noodgeval voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

### Een back-up van uw gegevens maken
Sla databaseback-ups rechtstreeks op in Azure met behulp van Blob Storage. Zie [Azure Storage gebruiken voor SQL Server-back-up en -herstel](../sql-database/storage-use-storage-sql-server-backup-restore.md) voor meer informatie. Hoewel dit goed werkt voor virtuele SQL-machines, werkt het ook voor on-premises SQL Server-databases. Zie [Back-up en herstel voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md) voor een overzicht van opties voor back-up en herstel.

Virtuele machines in Azure kunnen ook profiteren van [automatische back-up](virtual-machines-windows-sql-automated-backup.md) en [automatisch patchen](virtual-machines-windows-sql-automated-patching.md) voor SQL Server.

### Programma voor kwaliteitsverbetering (CEIP)
Het programma voor kwaliteitsverbetering (CEIP) is standaard ingeschakeld. Dit is geen beheertaak, tenzij u CEIP na het inrichten wilt uitschakelen. U kunt het programma voor kwaliteitsverbetering aanpassen of uitschakelen door via Extern bureaublad verbinding te maken met de virtuele machine. Voer vervolgens het hulpprogramma **SQL Server-fouten- en gebruiksrapportage** uit. Volg de instructies voor het uitschakelen van rapportage.

## Volgende stappen
[Bekijk het leertraject](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) voor virtuele SQL Server-machines in Azure.

Nog vragen? Bekijk eerst de [Veelgestelde vragen over SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md). Maar u kunt uw vragen of opmerkingen ook onder aan elk onderwerp over virtuele SQL-machines toevoegen om te communiceren met Microsoft en de community.



<!--HONumber=Jun16_HO2-->


