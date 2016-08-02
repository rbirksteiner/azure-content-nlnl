<properties
   pageTitle="Verwachtingen voor SQL Data Warehouse-preview | Microsoft Azure"
   description="Samenvatting van openbare preview-mogelijkheden en de doelstellingen voor algemene beschikbaarheid van SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Verwachtingen voor SQL Data Warehouse-preview

In dit artikel worden de preview-mogelijkheden van SQL Data Warehouse en de doelstellingen voor de service voor algemene beschikbaarheid (GA) beschreven. Deze informatie wordt telkens bijgewerkt wanneer we de openbare preview-mogelijkheden verbeteren.

Doelstellingen voor SQL Data Warehouse:

- Voorspelbare prestaties en lineaire schaalbaarheid tot petabytes aan gegevens
- Hoge betrouwbaarheid voor alle datawarehousebewerkingen
- Korte tijd tussen het laden van gegevens en het verkrijgen van inzicht in relationele en niet-relationele gegevens

Deze doelstellingen worden continu nagestreefd bij de verdere ontwikkeling van de preview-versie van SQL Data Warehouse.

## Voorspelbare en schaalbare prestaties

In Azure SQL Data Warehouse worden Data Warehouse Units (DWU's) gebruikt voor het meten van de beschikbare computerbronnen (CPU, geheugen, I/O-opslag) voor het datawarehouse. Hoe meer DWU's, hoe meer bronnen. Naarmate het aantal DWU's stijgt, voert SQL Data Warehouse parallelle bewerkingen (zoals het laden van gegevens of het uitvoeren van een query) uit op meer gedistribueerde bronnen. Dit vermindert de latentie en verbetert de prestaties.

Elk datawarehouse wordt gekenmerkt door twee fundamentele maatstaven voor de prestaties:

- Laadsnelheid. Het aantal records dat in het datawarehouse kan worden geladen per seconde. We meten specifiek het aantal records dat met PolyBase uit Azure Blob Storage kan worden geïmporteerd in een tabel met een geclusterde kolomopslagindex.
- Scansnelheid. Het aantal records dat sequentieel uit het datawarehouse kan worden opgehaald per seconde. We meten specifiek het aantal records dat wordt geretourneerd door een query op een geclusterde kolomopslagindex.

Enkele belangrijke prestatieverbeteringen worden nog gemeten. De verwachte snelheden daarvan worden binnenkort bekendgemaakt. Tijdens de preview worden continu verbeteringen (bijvoorbeeld grotere compressie en cacheopslag) aangebracht om deze snelheden te verhogen en ervoor te zorgen dat ze schalen zoals verwacht.  

## Gegevensbeveiliging

Alle SQL Data Warehouse-gegevens worden opgeslagen in Azure-opslag met lokaal redundante opslag. In het lokale datacentrum worden meerdere synchrone kopieën van de gegevens bewaard voor gegarandeerd transparante gegevensbeveiliging tegen gelokaliseerde fouten. 

## Back-ups

In Azure SQL Data Warehouse wordt ten minste elke 8 uur een back-up van alle gegevens gemaakt in de vorm van Azure-opslagmomentopnamen. Deze momentopnamen worden 7 dagen bewaard. Hiermee kunt u gegevens terugzetten van ten minste 21 tijdstippen in de afgelopen 7 dagen tot aan het tijdstip waarop de laatste momentopname is gemaakt. U kunt gegevens van een momentopname terugzetten met PowerShell of REST-API's.

## Betrouwbaarheid van query's

Het ontwerp van SQL Data Warehouse is gebaseerd op een MPP-architectuur (Massively Parallel Processing). Reken- en controleknooppuntfouten worden in SQL Data Warehouse automatisch gedetecteerd en gemigreerd. Een bewerking (zoals het laden van gegevens of het uitvoeren van een query) kan echter toch mislukken als gevolg van een knooppuntfout of migratie. Tijdens de preview brengen we continu verbeteringen aan zodat bewerkingen toch met succes kunnen worden voltooid, ook als er knooppuntfouten optreden.


## Updates en uitvaltijd

SQL Data Warehouse wordt regelmatig bijgewerkt om nieuwe functies en kritieke updates te installeren.  Deze upgrades kunnen leiden tot uitvaltijd. Upgrades worden op dit moment niet uitgevoerd volgens een vast schema.  Als u dit proces te verstorend vindt, kunt u het beste [een ondersteuningsticket maken][] zodat we u kunnen helpen om dit proces te omzeilen.


## Volgende stappen

[Aan de slag][] met openbare preview.

<!--Image references-->

<!--Article references-->
[een ondersteuningsticket maken]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Aan de slag]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Jun16_HO2-->


