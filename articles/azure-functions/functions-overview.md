<properties
   pageTitle="Overzicht van Azure Functions | Microsoft Azure"
   description="Informatie over het gebruik van Azure Functions voor het optimaliseren van asynchrone workloads."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="05/08/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# Overzicht van Azure Functions

Azure Functions is een oplossing voor het eenvoudig uitvoeren van kleine stukjes code, ofwel ‘functies’, in de cloud. U hoeft alleen de code te schrijven die u op dat moment nodig hebt, zonder dat u een complete toepassing of de bijbehorende infrastructuur nodig hebt. Hierdoor wordt ontwikkelen nog efficiënter en kunt u de door u gewenste programmeertaal  gebruiken, zoals C#, Node.js, Python of PHP. U betaalt alleen voor de tijd dat uw code wordt uitgevoerd en Azure zorgt het eventuele schalen.

Dit onderwerp bevat een globaal overzicht van Azure Functions. Als u meteen aan de slag wilt met Azure Functions, kunt u beginnen met [Uw eerste Azure-functie maken](functions-create-first-azure-function.md). Als u behoefte hebt aan meer technische informatie over Functions, raadpleegt u de [naslaginformatie voor ontwikkelaars](functions-reference.md).

## Functies

Hier volgen een aantal essentiële functies van Azure Functions:
    
* **Keuze van taal**: schrijf functies met C#, Node.js, Python, F#, PHP, batch, bash, Java of elk uitvoerbaar bestand.  
* **Betalen per gebruik**: betaal alleen voor de tijd die nodig is voor het uitvoeren van uw code. Zie de optie Dynamic App Service-abonnement in de sectie [prijzen](#pricing) hieronder.  
* **Breng uw eigen afhankelijkheden mee**: Functions ondersteunt NuGet en NPM, zodat u uw favoriete bibliotheken kunt gebruiken.  
* **Geïntegreerde beveiliging**: beveilig HTTP-geactiveerde functies met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en Microsoft-account.  
* **Eenvoudige integratie**: maak eenvoudig gebruik van Azure-services en Software-as-a-Service (SaaS)-producten. Zie de sectie [Integraties](#integrations) hieronder voor enkele voorbeelden.  
* **Flexibel ontwikkelen**: schrijf uw functies direct in de portal of stel doorlopende integratie in en implementeer uw code via GitHub, Visual Studio Team Services en andere [ondersteunde ontwikkelprogramma's](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Open-source**: de runtime van Functions is open-source en [beschikbaar op GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## Wat kan ik doen met Functions?

Azure Functions is de ideale oplossing voor het verwerken van gegevens, integreren van systemen, werken met het internet der dingen (IoT) en het bouwen van eenvoudige API's en microservices. Overweeg het gebruik van Functions voor taken zoals het verwerken van afbeeldingen of bestellingen, onderhouden van bestanden, uitvoeren van langlopende taken in een achtergrond-thread of voor andere taken die u wilt uitvoeren volgens een schema. 

Functions biedt sjablonen waarmee u meteen aan de slag kunt met essentiële scenario's, zoals:

* **BlobTrigger**: verwerk Azure Storage-blobs wanneer ze worden toegevoegd aan containers. Dit kunt u bijvoorbeeld gebruiken voor het wijzigen van de grootte van afbeeldingen.
* **EventHubTrigger**: reageer op gebeurtenissen die worden geleverd aan een Azure Event Hub. Dit is met name handig voor toepassingsinstrumentatie, verwerking van gebruikerservaringen of werkstromen en scenario's met betrekking tot het internet der dingen (IoT).
* **Generic webhook**: verwerk HTTP-webhook-aanvragen van services die webhooks ondersteunen.
* **GitHub webhook**: reageer op gebeurtenissen die plaatsvinden in uw GitHub-opslagplaatsen. Zie voor een voorbeeld [Een webhook of API-functie maken](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger**: activeer de uitvoering van de code met een HTTP-aanvraag.
* **QueueTrigger**: reageer op berichten die binnenkomen in een Azure Storage-wachtrij. Zie voor een voorbeeld [Een Azure-functie maken die wordt gebonden aan een Azure-service](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger**: koppel uw code aan andere Azure-services of on-premises services door te luisteren naar berichtenwachtrijen. 
* **ServiceBusTopicTrigger**: koppel uw code aan andere Azure-services of on-premises services met een abonnement op onderwerpen. 
* **TimerTrigger**: voer opschoonacties of andere batchtaken uit volgens een vooraf gedefinieerd schema. Zie voor een voorbeeld [Een functie voor gebeurtenisverwerking maken](functions-create-an-event-processing-function.md)

Azure Functions ondersteunt *triggers*, waarmee de uitvoering van code kan worden gestart, en *bindingen*, waarmee het coderen voor invoer- en uitvoergegevens kan worden vereenvoudigd. Zie [Naslaginformatie voor ontwikkelaars over triggers en bindingen van Azure Functions](functions-triggers-bindings.md) voor een gedetailleerde beschrijving van de triggers en bindingen van Azure Functions.


## <a name="integrations"></a>Integraties

Azure Functions integreert met diverse services van Azure en derden. U kunt deze gebruiken om uw functie te activeren en het uitvoeren te starten, of te laten fungeren als invoer en uitvoer voor uw code. De volgende service-integraties worden ondersteund door Azure Functions. 

* Azure DocumentDB
* Azure Event Hubs 
* Azure Mobile Apps (tabellen)
* Azure Notification Hubs
* Azure Service Bus (wachtrijen en onderwerpen)
* Azure Storage (blob, wachtrijen en tabellen) 
* GitHub (webhooks)
* On-premises (met Service Bus)

## <a name="pricing"></a>Wat kost Functions?

Azure Functions heeft twee soorten prijsstelling; kies de variant die het beste past bij uw behoeften: 

* **Dynamic Hosting**: wanneer de functie wordt uitgevoerd, biedt Azure alle benodigde rekenbronnen. U hoeft u geen zorgen te maken over het beheer van bronnen en betaalt alleen voor de tijd die nodig is voor het uitvoeren van de code. Volledige prijsinformatie is beschikbaar op de pagina [Prijzen voor Functions](/pricing/details/functions). 

* **App Service-abonnement**: voer uw functies net zo uit als uw web-apps, mobiele apps en API-apps. Wanneer u App Service al voor andere toepassingen gebruikt, voert u de functies kosteloos binnen hetzelfde abonnement uit. Volledige details vindt u op de pagina [Prijzen voor App Service](/pricing/details/app-service/).

Zie [Azure Functions schalen](functions-scale.md) voor meer informatie over het schalen van uw functies.

##Volgende stappen

+ [Uw eerste Azure-functie maken](functions-create-first-azure-function.md)  
Ga meteen aan de slag en maak uw eerste functie met de Azure Functions-snelstartgids. 
+ [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
Biedt meer technische informatie over de Azure Functions-runtime en bevat gedetailleerde informatie over het coderen van functies en het definiëren van triggers en bindingen.
+ [Azure Functions testen](functions-test-a-function.md)  
Beschrijft de verschillende hulpprogramma’s en technieken voor het testen van uw functies.
+ [Azure Functions schalen](functions-scale.md)  
Beschrijft de serviceabonnementen die beschikbaar zijn voor Azure Functions, zoals het serviceabonnement Dynamic, en helpt u bij het kiezen van het juiste abonnement. 
+ [Meer informatie over Azure App Service](../app-service/app-service-value-prop-what-is.md)  
Azure Functions maakt gebruik van het Azure App Service-platform voor kernfunctionaliteit zoals implementaties, omgevingsvariabelen en diagnostische procedures. 


<!--HONumber=Jun16_HO2-->


