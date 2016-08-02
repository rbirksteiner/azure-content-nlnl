<properties
 pageTitle="Scheduler: concepten, termen en entiteiten | Microsoft Azure"
 description="Azure Scheduler-concepten, -terminologie en -entiteitenhiërarchie, inclusief jobs en jobverzamelingen.  Toont een uitgebreid voorbeeld van een geplande job."
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
 ms.topic="get-started-article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Schedulerconcepten, -terminologie en -entiteitenhiërarchie

## Scheduler-entiteitenhiërarchie

In de volgende tabel worden de belangrijkste resources beschreven die door de Scheduler-API beschikbaar worden gemaakt of worden gebruikt:

|Resource | Beschrijving |
|---|---|
|**Jobverzameling**|Een jobverzameling bevat een aantal jobs en onderhoudt instellingen, quota en vertragingen die worden gedeeld door jobs binnen de verzameling. Een jobverzameling wordt gemaakt door de eigenaar van een abonnement en groepeert jobs op basis van gebruiks- of toepassingsgrenzen. Een jobverzameling is beperkt tot één regio. U kunt er ook quota mee afdwingen om het gebruik van alle jobs in die verzameling te beperken. De quota omvatten MaxJobs en MaxRecurrence.|
|**Job**|Een job definieert één terugkerende actie, met eenvoudige of complexe strategieën, die moet worden uitgevoerd. Acties omvatten mogelijk HTTP-, opslagwachtrij-, Service Bus-wachtrij- of Service Bus-onderwerpaanvragen.|
|**Jobgeschiedenis**|Een jobgeschiedenis bevat de details van de uitvoering van een job. Hierin wordt aangegeven of de job is geslaagd of mislukt. U vindt hierin ook alle responsdetails.|

## Scheduler-entiteitsbeheer

Op een hoog niveau maken de Scheduler-API en de Service Management-API de volgende bewerkingen beschikbaar op de resources:

|Mogelijkheid|Beschrijving en URI-adres|
|---|---|
|**Beheer van jobverzameling**|GET-, PUT- en DELETE-ondersteuning voor het maken en wijzigen van jobverzamelingen en de daarin opgenomen jobs. Een jobverzameling is een container voor jobs en is toegewezen aan quota en gedeelde instellingen. Voorbeelden van quota, die verderop worden beschreven, zijn maximum aantal jobs en kleinste terugkeerpatroon. <p>PUT en DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Jobbeheer**|Ondersteuning voor GET, PUT, POST, PATCH en DELETE voor het maken en wijzigen van jobs. Alle jobs moeten behoren tot een jobverzameling die al bestaat. Er wordt dus niet impliciet een gemaakt. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Beheer van jobgeschiedenis**|Ondersteuning voor GET voor het ophalen van een geschiedenis van 60 dagen van uitgevoerde jobs, zoals de verstreken tijd van jobs en de resultaten van het uitvoeren van jobs. Voegt ondersteuning toe voor querytekenreeksparameters om te filteren op basis van toestand en status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## Jobtypen

Er zijn meerdere typen jobs: HTTP-jobs (inclusief HTTPS-jobs die ondersteuning bieden voor SSL), opslagwachtrijjobs Service Bus-wachtrijjobs en Service Bus-onderwerpjobs. HTTP-jobs zijn ideaal als u een eindpunt van een bestaande workload of service hebt. U kunt opslagwachtrijjobs gebruiken om berichten te posten naar opslagwachtrijen, zodat die jobs ideaal zijn voor workloads die gebruikmaken van opslagwachtrijen. Zo zijn Service Bus-jobs ook ideaal voor workloads die gebruikmaken van Service Bus-wachtrijen en -onderwerpen.

## De entiteit "job" in detail

Op basisniveau bevat een geplande job verschillende onderdelen:

- De actie die moet worden uitgevoerd wanneer de timer van de job wordt gestart  

- (Optioneel) De tijd voor het uitvoeren van de job  

- (Optioneel) Wanneer en hoe vaak de job moet worden herhaald  

- (Optioneel) Een actie die moet worden gestart als de primaire actie mislukt  

Intern bevat een geplande job ook door het systeem geleverde gegevens, zoals de volgende geplande uitvoeringstijd.

De volgende code geeft een uitgebreid voorbeeld van een geplande job. Details hierover vindt u in de volgende secties.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Zoals u in het voorbeeld van een geplande job hierboven ziet, heeft een jobdefinitie verschillende onderdelen:

- Begintijd ("startTime")  

- Actie ("action"), die een foutactie ("errorAction") bevat

- Terugkeerpatroon ("recurrence")  

- Toestand ("state")  

- Status ("status"  

- Beleid voor opnieuw proberen ("retryPolicy")  

Hieronder vindt u een toelichting van elk van deze onderdelen:

## startTime

De "startTime" is de starttijd. Hiermee kan de aanroeper bij de verbinding een tijdzoneverschil opgeven in [ISO 8601-notatie](http://en.wikipedia.org/wiki/ISO_8601).

## action en errorAction

De "action" is de actie die bij elke herhaling wordt aangeroepen en beschrijft een type serviceaanroep. De actie is datgene wat wordt uitgevoerd volgens de opgegeven planning. Scheduler biedt ondersteuning voor HTTP-, opslagwachtrij-, Service Bus-onderwerp- en Service Bus-wachtrijacties.

De actie in het bovenstaande voorbeeld is een HTTP-actie. Hieronder volgt een voorbeeld van een opslagwachtrijactie:

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Hieronder volgt een voorbeeld van een Service Bus-onderwerpactie.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Kan netMessaging of AMQP zijn "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Een bericht", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Hieronder volgt een voorbeeld van een Service Bus-wachtrijactie:


  "action": { "serviceBusQueueMessage": { "queueName": "q1",  
      "namespace": "mySBNamespace", "transportType": "netMessaging", // Kan netMessaging of AMQP zijn "authentication": {  
        "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Een bericht",  
      "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

De "errorAction" is de foutafhandeling, de actie die wordt aangeroepen wanneer de primaire actie mislukt. U kunt deze variabele gebruiken om een eindpunt voor foutafhandeling aan te roepen of een gebruikersmelding te verzenden. Dit kan worden gebruikt om een secundair eindpunt te bereiken in het geval dat het primaire eindpunt niet beschikbaar is (bijvoorbeeld in het geval van een noodgeval op de site van het eindpunt). Het kan ook worden gebruikt voor de kennisgeving van een eindpunt voor foutafhandeling. Net als de primaire actie kan ook de foutactie een eenvoudige of samengestelde logica zijn op basis van andere acties. Zie [Create and Use a Shared Access Signature](https://msdn.microsoft.com/library/azure/jj721951.aspx) (Een Shared Access Signature maken en gebruiken) voor meer informatie over het maken van een SAS-token.

## recurrence

Recurrence heeft meerdere onderdelen:

- Frequentie ("frequency"): minuut, uur, dag, week, maand of jaar  

- Interval ("interval"): interval voor de opgegeven herhaling  

- Voorgeschreven planning ("schedule"): geef de minuten, uren, weekdagen, maanden en maanddagen op van het terugkeerpatroon  

- Aantal ("count"): het aantal herhalingen  

- Eindtijd ("endTime"): na de opgegeven eindtijd worden geen jobs uitgevoerd  

Een job wordt herhaald als hiervoor in de JSON-definitie van de job een terugkerend object is opgegeven. Als zowel "count" als "endTime" worden opgegeven, wordt de voltooiingsregel die het eerst is vermeld, uitgevoerd.

## state

De toestand (state) van de job is een van deze vier waarden: enabled, disabled, completed, or faulted (ingeschakeld, uitgeschakeld, voltooid of mislukt). U kunt voor jobs een PUT of PATCH uitvoeren om ze bij te werken met de status enabled (ingeschakeld) of disabled (uitgeschakeld). Als een job is voltooid of mislukt, is dat een eindtoestand die niet kan worden bijgewerkt (hoewel de job nog steeds kan worden verwijderd met DELETE). De eigenschap state kan er bijvoorbeeld als volgt uitzien:


        "state": "disabled", // enabled, disabled, completed, or faulted
Voltooide en mislukte jobs worden na 60 dagen verwijderd.

## status

Nadat een Scheduler-job is gestart, wordt informatie over de huidige status van de job geretourneerd. Dit object kan niet worden ingesteld door de gebruiker, maar wordt door het systeem ingesteld. Het is echter opgenomen in het jobobject (in plaats van een afzonderlijke gekoppelde resource) zodat de status van een job eenvoudig kan worden verkregen.

De jobstatus bevat de tijd van de vorige uitvoering (indien van toepassing), het tijdstip van de volgende geplande uitvoering (voor jobs die in uitvoering zijn) en het aantal keer dat de job is uitgevoerd.

## retryPolicy

Als een Scheduler-job is mislukt, is het mogelijk om een beleid voor opnieuw proberen op te geven, om te bepalen of en hoe wordt geprobeerd om de actie opnieuw uit te voeren. Dit wordt bepaald door het object **retryType**: dat is ingesteld op **none** (geen) als er geen beleid voor opnieuw proberen is opgegeven, zoals hierboven. Het is ingesteld op **fixed** (vast) als er wel een beleid voor opnieuw proberen is opgegeven.

Om een beleid voor opnieuw proberen in te stellen, kunnen twee extra instellingen worden opgegeven: een interval voor nieuwe poging (**retryInterval**) en het aantal nieuwe pogingen (**retryCount**).

Het interval voor een nieuwe poging, dat met het object **retryInterval** wordt opgegeven, is het interval tussen nieuwe pogingen. De standaardwaarde is 30 seconden, de configureerbare minimumwaarde is 15 seconden en de maximumwaarde is 18 maanden. Jobs in gratis jobverzamelingen hebben een configureerbare minimumwaarde van 1 uur.  Dit interval wordt gedefinieerd in de ISO 8601-notatie. Op dezelfde manier wordt de waarde van het aantal nieuwe pogingen opgegeven met het object **retryCount**; dit is het aantal keren dat een nieuwe poging wordt gedaan. De standaardwaarde is 4 en de maximumwaarde is 20\.. Zowel **retryInterval** als **retryCount** zijn optioneel. Ze hebben hun standaardwaarden als **retryType** is ingesteld op **fixed** en er niet expliciet waarden zijn opgegeven.

## Zie ook

 [Wat is Scheduler?](scheduler-intro.md)

 [Aan de slag met behulp van Scheduler in de Azure Portal](scheduler-get-started-portal.md)

 [Plannen en facturering in Azure Scheduler](scheduler-plans-billing.md)

 [Complexe schema's en geavanceerde terugkeerpatronen bouwen met Azure Scheduler](scheduler-advanced-complexity.md)

 [Naslaginformatie over REST API van Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)

 [Hoge beschikbaarheid en betrouwbaarheid van Azure Scheduler](scheduler-high-availability-reliability.md)

 [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)

 [Azure Scheduler uitgaande verificatie](scheduler-outbound-authentication.md)



<!--HONumber=Jun16_HO2-->


