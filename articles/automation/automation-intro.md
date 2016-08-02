<properties
    pageTitle="Wat is Azure Automation | Microsoft Azure"
    description="Informatie over welke waarde Azure Automation biedt en antwoorden op veelgestelde vragen, zodat u aan de slag kunt met het maken en gebruiken van runbooks en Azure Automation DSC."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="what is automation, azure automation, azure automation examples"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# Overzicht van Azure Automation

Microsoft Azure Automation biedt gebruikers een manier om de handmatige, langlopende, foutgevoelige en regelmatig herhaalde taken te automatiseren die vaak worden uitgevoerd in een cloud- en bedrijfsomgeving. Het bespaart tijd en verhoogt de betrouwbaarheid van regelmatige beheertaken. Deze worden zelfs gepland om met regelmatige intervallen automatisch te worden uitgevoerd. U kunt processen automatiseren met behulp van runbooks of configuratiebeheer automatiseren met behulp van Desired State Configuration. In dit artikel wordt een kort overzicht gegeven van Azure Automation en worden enkele veelgestelde vragen beantwoord. U kunt andere artikelen in deze bibliotheek raadplegen voor gedetailleerdere informatie over de verschillende onderwerpen.


## Processen automatiseren met runbooks

Een runbook is een set taken waarmee een geautomatiseerd proces in Azure Automation wordt uitgevoerd. Het kan een eenvoudig proces zijn, zoals het starten van een virtuele machine en het maken van een logboekvermelding, of u hebt mogelijk een complex runbook waarin andere kleinere runbooks worden gecombineerd voor het uitvoeren van een complex proces in meerdere resources of zelfs in meerdere clouds en on-premises omgevingen.  

U hebt bijvoorbeeld een bestaand handmatig proces voor het afkappen van een SQL-database als deze de maximale grootte nadert. Dit proces omvat meerdere stappen zoals verbinding maken met de server, verbinding maken met de database, de huidige grootte van de datebase ophalen, controleren of de drempel is overschreden en de database vervolgens afkappen en de gebruiker op de hoogte brengen. In plaats van het handmatig uitvoeren van deze stappen, kunt u een runbook maken waarmee al deze taken als één proces worden uitgevoerd. U start het runbook, geeft de vereiste informatie op, zoals de naam van de SQL-server, databasenaam en e-mail van de ontvanger en u hoeft verder niets te doen terwijl het proces wordt voltooid. 


## Wat kan met runbooks worden geautomatiseerd?

Runbooks in Azure Automation zijn gebaseerd op Windows PowerShell of Windows PowerShell Workflow, zodat ze alles kunnen dat PowerShell kan. Als een toepassing of service een API heeft, kan een runbook hiermee werken. Als u een PowerShell-module voor de toepassing hebt, kunt u die module in Azure Automation laden en die cmdlets opnemen in uw runbook. Azure Automation-runbooks worden uitgevoerd in de Azure-cloud en hebben toegang tot alle cloud-resources of externe bronnen die toegankelijk zijn vanuit de cloud. Met behulp van [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) kunnen runbooks in uw lokale datacentrum worden uitgevoerd voor het beheren van lokale resources. 


## Runbooks ophalen uit de community

De [runbookgalerie](automation-runbook-gallery.md#runbooks-in-runbook-gallery) bevat runbooks van Microsoft en de community die u ongewijzigd in uw omgeving kunt gebruiken of kunt aanpassen voor uw eigen doeleinden. Ze zijn ook nuttig om raad te plegen voor informatie over het maken van uw eigen runbooks. U kunt zelfs uw eigen runbooks aan de galerie toevoegen als u denkt dat deze van nut kunnen zijn voor andere gebruikers. 


## Runbooks maken met Azure Automation 

U kunt [uw eigen runbooks volledig zelf maken](automation-creating-importing-runbook.md) of runbooks uit de [runbookgalerie](http://msdn.microsoft.com/library/azure/dn781422.aspx) wijzigen zodat ze voldoen aan uw eigen vereisten. Er zijn drie verschillende [runbooktypen](automation-runbook-types.md) waaruit u kunt kiezen op basis van uw vereisten en PowerShell-ervaring. Als u liever rechtstreeks met de PowerShell-code werkt, dan kunt u een [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) of [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) gebruiken dat u offline bewerkt of met de [teksteditor](http://msdn.microsoft.com/library/azure/dn879137.aspx) in de Azure Portal. Als u liever een runbook bewerkt zonder de onderliggende code te zien, dan kunt u een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) maken met behulp van de [grafische editor](automation-graphical-authoring-intro.md) in Azure Portal. 

Wilt u liever kijken dan lezen? Bekijk de onderstaande video van de Microsoft Ignite-sessie in mei 2015. Opmerking: Hoewel de concepten en functies die in deze video worden besproken correct zijn, is er veel vooruitgang geboekt met Azure Automation sinds deze video is opgenomen. Er is nu een uitgebreidere gebruikersinterface in Azure Portal en er worden aanvullende mogelijkheden ondersteund.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## Configuratiebeheer automatiseren met Desired State Configuration 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) is een beheerplatform waarmee u configuratie voor fysieke hosts en virtuele machines kunt beheren, implementeren en afdwingen met behulp van een declaratieve PowerShell-syntaxis. U kunt configuraties definiëren op een centrale DSC-pull-server die met doelmachines automatisch kunnen worden opgehaald en toegepast. DSC biedt een set PowerShell-cmdlets die u kunt gebruiken voor het beheren van configuraties en resources.  

[Azure Automation DSC](automation-dsc-overview.md) is een cloudoplossing voor PowerShell DSC waarmee services worden geboden die zijn vereist voor bedrijfsomgevingen.  U kunt uw DSC-resources in Azure Automation beheren en configuraties toepassen op virtuele of fysieke machines waarmee deze worden opgehaald van een DSC-pull-server in de Azure-cloud.  Het biedt ook rapportageservices waarmee u wordt geïnformeerd over belangrijke gebeurtenissen zoals wanneer knooppunten zijn afgeweken van de toegewezen configuratie en wanneer een nieuwe configuratie is toegepast. 


## Uw eigen DSC-configuraties maken met Azure Automation

Met [DSC-configuraties](automation-dsc-overview.md#azure-automation-dsc-terms) wordt de gewenste status van een knooppunt opgeven.  Dezelfde configuratie kan door meerdere knooppunten worden toegepast om ervoor te zorgen dat ze allemaal dezelfde status houden.  U kunt een configuratie maken met een teksteditor op uw lokale machine en deze vervolgens importeren in Azure Automation, waar u deze kunt compileren en op knooppunten kunt toepassen.


## Modules en configuraties ophalen 

U kunt [PowerShell-modules](automation-runbook-gallery.md#modules-in-powershell-gallery) ophalen die cmdlets bevatten die u kunt gebruiken in uw runbooks, en DSC-configuraties uit de [PowerShell-galerie](http://www.powershellgallery.com/). U kunt deze galerie starten vanuit Azure Portal en modules rechtstreeks in Azure Automation importeren, of ze handmatig downloaden en importeren. U kunt de modules niet rechtstreeks installeren vanuit Azure Portal, maar u kunt ze downloaden en installeren zoals u bij elke andere module zou doen. 


## Voorbeeld van praktische toepassingen van Azure Automation 

Hieronder vindt u enkele voorbeelden van de soorten automatiseringsscenario's met Azure Automation. 

* Virtuele machines in verschillende Azure-abonnementen maken en kopiëren. 
* Bestandskopieën van een lokale machine naar een Azure Blob Storage-container plannen. 
* Beveiligingsfuncties automatiseren zoals aanvragen van een client weigeren wanneer een Denial of Service-aanval wordt gedetecteerd. 
* Ervoor zorgen dat machines voortdurend zijn aangepast aan geconfigureerd beveiligingsbeleid.
* Continue implementatie van toepassingscode beheren in cloud- en on-premises infrastructuur. 
* Een Active Directory-forest in Azure bouwen voor uw testomgeving. 
* Een tabel in een SQL-database afkappen als de database bijna de maximale grootte heeft bereikt. 
* Omgevingsinstellingen voor een Azure-website extern bijwerken. 


## Hoe staat Azure Automation in verhouding tot andere automatiseringsprogramma's?

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) is bedoeld voor het automatiseren van beheertaken in de privécloud. Het wordt lokaal in uw datacentrum geïnstalleerd als een onderdeel van het [Microsoft Azure-pakket](https://www.microsoft.com/en-us/server-cloud/). Voor SMA en Azure Automation wordt dezelfde runbookindeling gebruikt op basis van Windows PowerShell en Windows PowerShell Workflow, maar in SMA worden [grafische runbooks](automation-graphical-authoring-intro.md) niet ondersteund.  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) is bedoeld voor automatisering van on-premises resources. In het programma wordt gebruikgemaakt van een andere runbookindeling dan in Azure Automation en Service Management Automation en het heeft een grafische interface om runbooks te maken zonder dat het uitvoeren van scripts is vereist. De runbooks bestaan uit activiteiten uit integratiepakketten die specifiek voor Orchestrator zijn geschreven. 


## Waar vind ik meer informatie? 

Er zijn verschillende bronnen beschikbaar waar u meer informatie kunt vinden over Azure Automation en het maken van uw eigen runbooks. 

* U bevindt zich nu in de **Azure Automation-bibliotheek**. De artikelen in deze bibliotheek bieden volledige documentatie over de configuratie en het beheer van Azure Automation en over het ontwerpen van uw eigen runbooks. 
* [Azure PowerShell-cmdlets](http://msdn.microsoft.com/library/jj156055.aspx) bevat informatie voor het automatiseren van Azure-bewerkingen met behulp van Windows PowerShell. Deze cmdlets worden in runbooks gebruikt om te werken met Azure-resources. 
* [Management Blog](https://azure.microsoft.com/blog/tag/azure-automation/) bevat de meest recente informatie over Azure Automation en andere beheertechnologieën van Microsoft. U moet u abonneren op deze blog als u op de hoogte wilt blijven van het nieuws van het Azure Automation-team. 
* Op het [Automation-forum](http://go.microsoft.com/fwlink/p/?LinkId=390561) kunt u vragen stellen over Azure Automation. Deze worden beantwoord door Microsoft en de Automation-community. 
* [Azure Automation Cmdlets](https://msdn.microsoft.com/library/mt244122.aspx) biedt informatie over het automatiseren van beheertaken. Het bevat cmdlets voor het beheren van Automation-accounts, -assets, -runbooks en -DSC.


## Mag ik feedback geven? 

**Ja, feedback is welkom.** Als u een Azure Automation-runbookoplossing of een integratiemodule zoekt, plaatst u een scriptaanvraag in Scriptcentrum. Als u feedback wilt geven of functieaanvragen hebt voor Azure Automation, plaatst u deze op [UserVoice](http://feedback.windowsazure.com/forums/34192--general-feedback). Bedankt! 





<!--HONumber=Jun16_HO2-->


