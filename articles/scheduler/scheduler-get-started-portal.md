<properties
 pageTitle="Aan de slag met Azure Scheduler in Azure-portal | Microsoft Azure"
 description="Aan de slag met Azure Scheduler in Azure-portal"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Aan de slag met Azure Scheduler in Azure-portal

Het is eenvoudig om geplande taken te maken in Azure Scheduler. In deze zelfstudie leert u een taak te maken: U komt ook te weten hoe de controle- en beheerfuncties van Scheduler werken.

## Een taak maken

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/).  

2.  Klik op **+Nieuw** > typ _Scheduler_ in het zoekvak > selecteer **Scheduler** in de resultaten > klik op **Maken**.

   ![][marketplace-create]

3.  We gaan een taak maken waarbij we eenvoudig http://www.microsoft.com/ bestoken met een GET-aanvraag. In het scherm **Scheduler-taak** voert u de volgende gegevens in:

    1.  **Naam:** `getmicrosoft`  

    2.  **Abonnement:** uw Azure-abonnement   

    3.  **Taakverzameling:** selecteer een bestaande taakverzameling of klik op **Nieuw** > typ een naam.

4.  Definieer vervolgens in **Actie-instellingen** de volgende waarden:

    1.  **Actietype:** ` HTTP`  

    2.  **Methode:** `GET`  

    3.  **URL:** ` http://www.microsoft.com`  

   ![][action-settings]

5.  Tot slot gaan we een planning definiëren. De taak kan worden gedefinieerd als een eenmalige taak, maar we kiezen een schema voor een terugkerende taak:

    1. **Terugkeerpatroon**: `Recurring`

    2. **Start**: de datum van vandaag

    3. **Herhaald elke**: `12 Hours`

    4. **Beëindigen op**: twee dagen na de huidige datum  

   ![][recurrence-schedule]

6.  Klik op **Maken**.

## Taken beheren en controleren

Zodra een taak is gemaakt, wordt deze weergegeven in het belangrijkste Azure-dashboard. Klik op de taak, waarna een nieuw venster wordt geopend met de volgende tabbladen:

1.  Eigenschappen  

2.  Actie-instellingen  

3.  Planning  

4.  Geschiedenis

5.  Gebruikers

   ![][job-overview]

### Eigenschappen

Deze eigenschappen zijn alleen-lezen en beschrijven de beheermetagegevens voor de Scheduler-taak.

   ![][job-properties]


### Actie-instellingen

Door op een taak in het scherm **Taken** te klikken, kunt u die taak configureren. Hiermee kunt u geavanceerde instellingen configureren, als u deze nog niet hebt geconfigureerd in de wizard Snelle invoer.

Voor alle actietypen kunt u het beleid voor opnieuw proberen en de foutactie wijzigen.

Voor de actietypen HTTP- en HTTPS-taak, kunt u de methode wijzigen in een toegestane HTTP-term. U kunt ook de koptekst en elementaire verificatiegegevens toevoegen, verwijderen of wijzigen.

Voor actietypen Opslagwachtrij kunt u het opslagaccount, de wachtrijnaam, de SAS-token en de hoofdtekst wijzigen.

Voor Service Bus-actietypen kunt u de naamruimte, het onderwerp/wachtrijpad, de verificatie-instellingen, het transporttype, de berichtkenmerken en de hoofdtekst van het bericht wijzigen.

   ![][job-action-settings]

### Planning

Hiermee kunt u het schema opnieuw configureren als u de planning wilt wijzigen die u hebt gemaakt in de wizard Snelle invoer.

Dit is een kans om [complexe schema's en geavanceerde terugkeerpatroon in uw taak in te bouwen](scheduler-advanced-complexity.md)

U kunt de startdatum en -tijd wijzigen evenals de einddatum en -tijd (als het om een terugkerende taak gaat.)

   ![][job-schedule]


### Geschiedenis

Op het tabblad **Geschiedenis** worden geselecteerde metrische gegevens weergegeven voor elke keer dat er in het systeem voor de geselecteerde taak een taak is uitgevoerd. Deze metrische gegevens bestaan uit realtime-waarden die betrekking hebben op de status van uw exemplaar van Scheduler:

1.  Status  

2.  Details  

3.  Nieuwe pogingen

4.  Terugkeerpatroon: 1e, 2e, 3e enzovoorts.

5.  Starttijd van de uitvoering  

6.  Eindtijd van de uitvoering

   ![][job-history]

U kunt op een uitvoering klikken om de **Geschiedenisdetails** ervan weer te geven, zoals onder andere het volledige antwoord voor elke uitvoering. Vanuit dit dialoogvenster kunt u ook het antwoord naar het Klembord kopiëren.

   ![][job-history-details]

### Gebruikers

Met op rollen gebaseerd toegangsbeheer (RBAC) van Azure beschikt u over geavanceerd toegangsbeheer voor Azure Scheduler. Voor meer informatie over het tabblad Gebruikers, zie [Op rollen gebaseerd toegangsbeheer in Azure](../active-directory/role-based-access-control-configure.md)


## Zie ook

 [Wat is Scheduler?](scheduler-intro.md)

 [Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)

 [Plannen en facturering in Azure Scheduler](scheduler-plans-billing.md)

 [Complexe schema's en geavanceerde terugkeerpatronen bouwen met Azure Scheduler](scheduler-advanced-complexity.md)

 [Naslaginformatie over REST API van Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Naslaginformatie over Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)

 [Hoge beschikbaarheid en betrouwbaarheid](scheduler-high-availability-reliability.md)

 [Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)

 [Scheduler uitgaande verificatie](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png



<!--HONumber=Jun16_HO2-->


