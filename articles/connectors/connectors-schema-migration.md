<properties
    pageTitle="Logische apps migreren naar preview-schemaversie 2015-08-01 | Microsoft Azure App Service"
    description="U kunt uw logische apps eenvoudig migreren naar de nieuwste schemaversie. Volg deze stappen."
    services="app-service\logic"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="deonhe"/>

# Logische apps migreren naar preview-schemaversie 2015-08-01

U verplaatst uw bestaande logische apps als volgt naar het nieuwe schema:  
1. Open uw logische app in de Azure Portal  
2. Klik op Schema bijwerken:

 ![API-pictogram][step1]   
De pagina Schema bijwerken wordt weergegeven en bevat een koppeling naar een document dat gedetailleerde informatie bevat over de verbeteringen in het nieuwe schema: ![API-pictogram][step2]

>[AZURE.NOTE] Wanneer u **Schema bijwerken** selecteert, worden automatisch de migratiestappen uitgevoerd en krijgt u de code-uitvoer. U kunt deze gebruiken om uw definitie bij te werken, maar zorg ervoor dat u de richtlijnen voor codering volg, zoals die zijn beschreven in de **aanbevolen procedures** hieronder.

## Aanbevolen procedures voor het migreren van uw logische apps naar de nieuwste schemaversie:  

- Kopieer het gemigreerde script naar een nieuwe logische app. Overschrijf de oude pas nadat u deze hebt getest en hebt bevestigd dat de gemigreerde apps werken zoals verwacht.
- Uw logische app testen **voordat** u deze in productie neemt
- Nadat de migratie is voltooid, werkt u waar mogelijk uw logische apps bij, zodat deze gebruikmaken van de [beheerde API's](./apis-list.md). U kunt bijvoorbeeld starten met het gebruik van de Dropbox v2-versie overal waar u DropBox v1 gebruikt.


## Wat er volgt
-  [Meer informatie over het handmatig migreren uw logische apps](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[stap 1]: ./media/connectors-schema-migration/migrateschema1.png
[stap 2]: ./media/connectors-schema-migration/migrateschema2.png









<!--HONumber=Jun16_HO2-->


