Database Transaction Unit (DTU) is de maateenheid in SQL Database die de relatieve kracht van databases weergeeft op basis van een realistische maatstaf: de database-transactie. We hebben een aantal bewerkingen genomen die kenmerkend zijn voor een OLTP-aanvraag (Online Transaction Processing) en vervolgens gemeten hoeveel transacties er per seconde bij volledige belasting kunnen worden voltooid (dit is de beknopte versie, zie [Overzicht van benchmarks](../articles/sql-database/sql-database-benchmark-overview.md) als u alle details wilt weten). 

Zo biedt een Premium P11-database met 1750 DTU's 350 keer meer DTU aan rekenvermogen dan een Basic-database met 5 DTU's. 

![Inleiding tot de SQL Database: DTU's van individuele database per laag en niveau.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Als u een bestaande SQL Server-database migreert, kunt u met een hulpprogramma van derden, [Azure SQL Database DTU Calculator](http://dtucalculator.azurewebsites.net/), een schatting maken van het prestatieniveau en de servicelaag die uw database in Azure SQL Database mogelijk vereist.

### DTU versus eDTU

De DTU voor individuele databases wordt rechtstreeks omgezet in de eDTU voor elastische databases. Zo biedt een database in een Basic-pool voor elastische databases maximaal 5 eDTU's. Dat zijn dezelfde prestaties als die van één Basic-database. Het verschil is dat de elastische database pas eDTU's uit de groep gebruikt wanneer dat nodig is. 

![Inleiding tot de SQL Database: elastische pools per laag.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Een eenvoudig voorbeeld helpt. Stel, we hebben een Basic-pool voor elastische databases met 1000 DTU's en zetten daar 800 databases in. Zolang er op elk moment in de tijd maar 200 van de 800 databases worden gebruikt (5 DTU x 200 = 1000), wordt de capaciteit van de pool niet bereikt en nemen de prestaties van de database niet af. Dit voorbeeld is voor de helderheid vereenvoudigd. De werkelijke berekening is iets gecompliceerder. Deze berekening wordt door de uitgevoerd en u krijgt een aanbeveling op basis van historisch databasegebruik. Zie [Prijs- en prestatieoverwegingen voor een pool voor elastische databases](../articles/sql-database/sql-database-elastic-pool-guidance.md) als u wilt weten hoe de aanbevelingen werken of als u de berekening zelf wilt uitvoeren. 



<!--HONumber=Jun16_HO2-->


