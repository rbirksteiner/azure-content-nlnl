<properties
 pageTitle="Wat is Azure Scheduler? | Microsoft Azure"
 description="Met Azure Scheduler kunt u verklarend acties beschrijven die in de cloud moeten worden uitgevoerd. En vervolgens worden deze acties automatisch gepland en uitgevoerd."
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

# Wat is Azure Scheduler?

Met Azure Scheduler kunt u verklarend acties beschrijven die in de cloud moeten worden uitgevoerd. En vervolgens worden deze acties automatisch gepland en uitgevoerd.  In Scheduler wordt dit gedaan met behulp van [de Azure Portal](scheduler-get-started-portal.md), programmacode, de [REST-API](https://msdn.microsoft.com/library/mt629143.aspx) of Azure PowerShell.

Scheduler maakt, onderhoudt en roept gepland werk aan.  Scheduler host geen workloads en voert geen programmacode uit. Scheduler _roept_ programmacode aan die elders wordt gehost, in Azure, on-premises of bij een andere provider. Dit aanroepen gebeurt via HTTP, HTTPS, een opslagwachtrij, een Service Bus-wachtrij of een Service Bus-onderwerp.

Met Scheduler worden [taken](scheduler-concepts-terms.md) gepland, de resultaten van taken in het verleden bijgehouden die men kan bekijken, en daarbij gaat Scheduler heel deterministisch en betrouwbaar te werk bij het plannen van workloads die moeten worden uitgevoerd. Azure WebJobs (onderdeel van de functie Web-apps in Azure App Service) en andere Azure-planfuncties maken op de achtergrond gebruik van Scheduler. De [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx) helpt met het beheer van de communicatie voor deze acties. Als zodanig ondersteunt Scheduler met gemak [complexe schema's en geavanceerde terugkeerpatronen](scheduler-advanced-complexity.md).

Er zijn verschillende scenario's die zich goed lenen voor het gebruik van Scheduler. Bijvoorbeeld:

+ _Terugkerende acties van toepassingen_: periodiek verzamelen van gegevens van Twitter in een feed.
+ _Dagelijks onderhoud_: het dagelijks verwijderen van logboeken, het uitvoeren van back-ups en overige onderhoudstaken. Zo kan een beheerder er bijvoorbeeld voor kiezen elke dag om 1:00 uur een back-up te maken van de database.

Met Scheduler kunt u programmacode gebruiken om taken en [taakverzamelingen](scheduler-concepts-terms.md) te maken, bij te werken, te verwijderen, weer te geven en te beheren, met behulp van scripts en in de portal.

## Zie ook

 [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)

 [Aan de slag met behulp van Scheduler in de Azure Portal](scheduler-get-started-portal.md)

 [Plannen en facturering in Azure Scheduler](scheduler-plans-billing.md)

 [Complexe schema's en geavanceerde terugkeerpatronen bouwen met Azure Scheduler](scheduler-advanced-complexity.md)

 [Naslaginformatie over REST API van Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)

 [Hoge beschikbaarheid en betrouwbaarheid van Azure Scheduler](scheduler-high-availability-reliability.md)

 [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)

 [Azure Scheduler uitgaande verificatie](scheduler-outbound-authentication.md)



<!--HONumber=Jun16_HO2-->


