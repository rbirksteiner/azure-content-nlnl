<properties
    pageTitle="Outlook gebruiken in Azure RemoteApp | Microsoft Azure" 
    description="Lees hoe u Outlook configureert en gebruikt in Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/18/2016"
    ms.author="elizapo" />

# Microsoft Outlook gebruiken in Azure RemoteApp

Azure RemoteApp biedt ondersteuning voor Microsoft Outlook O365. Lees meer over hoe [Office werkt in Azure RemoteApp](remoteapp-officesubscription.md). Er zijn enkele aanbevolen instellingen voor Outlook bij gebruik in Azure RemoteApp.

## Cachemodus
Cachemodus is een aanbevolen configuratie wanneer u Outlook gebruikt in Azure RemoteApp. Wanneer u een Outlook 2013-account configureert om gebruik te maken van de Exchange-modus met cache, werkt Outlook 2013 vanuit een lokaal exemplaar van het Microsoft Exchange-postvak van de gebruiker, dat is opgeslagen in een offlinegegevensbestand (OST-bestand) op de computer van de gebruiker, samen met het offlineadresboek (OAB). Het postvak in de cache en het OAB worden regelmatig bijgewerkt vanuit de O365-service. Lees meer over [de verschillen tussen de cachemodus en onlinemodus](https://technet.microsoft.com/library/jj683103.aspx).

De gebruiker kan **Exchange-modus met cache** of **Onlinemodus** kiezen tijdens de accountconfiguratie of door de accountinstellingen te wijzigen. U kunt ook de ene of de andere modus implementeren met behulp van de Office Customization Tool (OCT) of Groepsbeleid.  

Lees [Stapsgewijze instructies over het inschakelen van de cachemodus](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## Zoeken
In Azure RemoteApp heeft het zoeken in Outlook beperkingen. In Azure RemoteApp worden gegroepeerde virtuele machines gebruikt om gebruikerssessies mogelijk te maken. Zoekindexering is afhankelijk van de machine-id. Deze is voor alle virtuele machines anders. Het is mogelijk dat een gebruiker telkens naar een nieuwe virtuele machine wordt omgeleid wanneer hij zich aanmeldt bij Azure RemoteApp. Als lokaal zoeken zou zijn ingeschakeld, zou dat betekenen dat de indexeerfunctie zou worden uitgevoerd telkens wanneer de computer-id verandert (wanneer de gebruiker zich op een andere virtuele machine bevindt). Afhankelijk van de grootte van het OST-bestand kan het lang duren vooraleer de indexeerfunctie klaar is, en kan deze resources in beslag nemen die nodig zijn voor andere apps. Zoeken zou niet alleen traag zijn maar zou misschien geen resultaten opleveren. Dit probleem zou kunnen worden omzeild door standaard online zoeken in te schakelen. Geïndexeerd/lokaal zoeken kan echter niet worden uitgeschakeld en online zoeken kan in Outlook 2013 helaas niet standaard worden ingeschakeld.

Outlook 2016 heeft een oplossing voor dit probleem in de vorm van een nieuwe onlinezoekervaring voor postvakken die worden gehost in Exchange 2016 (of worden gehost in Office 365). Hierbij worden de zoekresultaten van de server vergeleken met de lokale cache (OST). In sommige scenario's kan Outlook terugvallen op het gebruik van de zoekindexeerfunctie, maar voor de meeste zoekopdrachten wordt de onlinemodus gebruikt. Azure RemoteApp doet daarom de aanbeveling om Outlook 2016 te gebruiken als zoeken in e-mail een belangrijk scenario is.



<!--HONumber=Jun16_HO2-->


