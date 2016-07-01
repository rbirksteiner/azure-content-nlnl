<properties
    pageTitle="Overzicht van Microsoft Azure Portal"
    description="Informatie over het gebruik van de Microsoft Azure Portal."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# Overzicht van Microsoft Azure Portal

De Microsoft Azure Portal is een centrale plaats waar u uw Azure-resources kunt inrichten en beheren.  In deze zelfstudie raakt u vertrouwd met de portal en ziet u hoe u een aantal van deze kernmogelijkheden gebruikt:
- Een **uitgebreide marketplace** waarmee u kunt bladeren door duizenden items (van Microsoft, maar ook van andere leveranciers) die kunnen worden aangeschaft en/of ingericht.
- Een **uniforme en schaalbare bladerervaring** waarmee u gemakkelijk kunt zoeken naar resources die u interesseren en verschillende bewerkingen kunt uitvoeren.
- **Consistente beheerpagina’s** (of blades) waarmee u een groot aantal Azure-services kunt beheren via een consistente manier voor het weergeven van instellingen, acties, factureringsinformatie, statusbewakings- en gebruiksgegevens, en nog veel meer.
- Een **persoonlijke ervaring** waarmee u een aangepast startscherm kunt maken waarin de gegevens worden weergegeven die u wilt zien wanneer u zich aanmeldt.  Bovendien kunt u alle beheerblades die tegels bevatten, aanpassen.

 ![Oriëntatie Azure Portal-gebruikersinterface][UIOrientation]

## Voordat u aan de slag gaat

U hebt een geldig Azure-abonnement nodig om deze zelfstudie te doorlopen.  Als u nog geen abonnement hebt, kunt u zich nu [aanmelden voor een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).  Beschikt u over een abonnement, dan kunt u toegang krijgen tot de portal via [https://portal.azure.com].

## Een resource maken

Azure heeft een marketplace met duizenden items die u vanaf één locatie kunt maken.  Stel dat u een nieuwe virtuele machine met Windows Server 2012 wilt maken.  De hub +NIEUW is uw ingangspunt voor een samengestelde verzameling aanbevolen categorieën van de marketplace.  Elke categorie bevat een kleine selectie items, samen met een koppeling naar de volledige marketplace waarin alle categorieën en zoekopties worden getoond. Ga als volgt te werk om een nieuwe virtuele machine met Windows Server 2012 te maken:  

1.  Windows Server 2012 is beschikbaar. U kunt dit item dus selecteren in de categorie Compute.  
2.  Vul een aantal basisgegevens in op een formulier.
3.  Klik op Maken en er wordt onmiddellijk begonnen met het inrichten van uw virtuele machine.

De meldingenhub meldt wanneer de resource is gemaakt. Vervolgens wordt er een beheerblade geopend (u kunt hiervoor ook later naar Resources bladeren).

![Portalcategorieën][PortalCategories]


## Resources zoeken

U kunt vaak gebruikte resources altijd vastmaken aan het startboard, maar mogelijk moet u bladeren naar items die u niet regelmatig gebruikt.  Onderstaande bladerhub is het startpunt om naar al uw resources te gaan.  Door te klikken op afzonderlijke items, kunt u filteren op abonnement, kolommen kiezen en het formaat hiervan wijzigen, en naar de beheerblades navigeren.

![Bladerhub][BrowseHub]

## Resources beheren en toegang tot resources delegeren

Vanaf deze blade kunt u verbinding maken met de virtuele machine met behulp van Extern bureaublad, de belangrijkste prestatiemetrieken bewaken, toegang tot de virtuele machine beheren met behulp van op rollen gebaseerd toegangsbeheer (RBAC), de virtuele machine configureren en andere belangrijke beheertaken uitvoeren.  Toegang delegeren op basis van rollen is essentieel voor beheer op de gewenste schaal.  Klik [hier](./active-directory/role-based-access-control-configure.md) voor meer informatie hierover. Ga als volgt te werk om toegang te delegeren aan een resource:

1.  Blader naar de resource.
2.  Klik in de sectie Essentials op Alle instellingen.
3.  Klik in de lijst met instellingen op Gebruikers.
4.  Klik in de opdrachtbalk op Toevoegen.
5.  Kies een gebruiker en een rol.

![Een resource beheren][ManageResource]

## Een resourceblade aanpassen

Azure configureert de blades voor uw resources vooraf, maar de tegels in deze blades kunt u zelf beheren.  U kunt eenvoudig de modus voor aanpassingen openen om tegels toe te voegen, tegels te verwijderen, het formaat ervan te wijzigen of uw tegels opnieuw te rangschikken. Ga als volgt te werk om een blade aan te passen:

1.  Blader naar de resource.
2.  Klik op ... boven aan de blade die u wilt aanpassen.
3.  Klik op Onderdelen toevoegen.
4.  Begin met het slepen en neerzetten van onderdelen.  

![Blades aanpassen][CustomizeBlades]

## Hulp krijgen

Als u ooit een probleem hebt, staan we voor u klaar.  De portal heeft een Help- en ondersteuningspagina die antwoord geeft op veel van uw vragen.  Afhankelijk van uw [ondersteuningsplan](https://azure.microsoft.com/support/plans/) kunt u ook rechtstreeks in de portal ondersteuningstickets maken.  Nadat u een ondersteuningsticket hebt gemaakt, kunt u de voortgang van het ticket vanuit de portal beheren. U opent de Help en -ondersteuningspagina door te navigeren naar Bladeren -> Help + ondersteuning.  

![Help en ondersteuning][HelpSupport]

## Samenvatting

Laten we eens op een rijtje zetten wat u in deze zelfstudie hebt geleerd:
- U hebt geleerd hoe u zich aanmeldt, een abonnement neemt en naar de portal bladert.
- U weet meer over de gebruikersinterface van de portal en hebt geleerd hoe u resources kunt maken en hierdoor kunt bladeren.
- U hebt geleerd hoe u resources kunt maken en hierdoor kunt bladeren.
- U hebt inzicht gekregen in de structuur (beheerblades) en in de wijze waarop u verschillende soorten resources consistent kunt beheren.
- U hebt geleerd hoe u de portal kunt aanpassen, zodat u de informatie die u belangrijk vindt, als eerste te zien krijgt.
- U hebt geleerd hoe u toegang tot resources krijgt met behulp van op rollen gebaseerd toegangsbeheer (RBAC)
- U hebt geleerd hoe u hulp en ondersteuning kunt krijgen

De Microsoft Azure Portal vereenvoudigt het maken en beheren van uw toepassingen in de cloud aanzienlijk.  Bezoek ons [beheerblog](https://azure.microsoft.com/blog/topics/management/) om up-to-date te blijven, aangezien we continu [luisteren naar uw feedback](https://feedback.azure.com/forums/223579-azure-preview-portal/) en verbeteringen aanbrengen.  Ook het [blog van ScottGu](http://weblogs.asp.net/scottgu) is een goede plaats om te worden bijgepraat over alle Azure-updates.

[Oriëntatie gebruikersinterface]: ./media/azure-portal-how-to-use/azure_portal_1.png
[Portalcategorieën]: ./media/azure-portal-how-to-use/azure_portal_2.png
[Bladerhub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[Resources beheren]: ./media/azure-portal-how-to-use/azure_portal_4.png
[Blades aanpassen]: ./media/azure-portal-how-to-use/azure_portal_5.png
[Help en ondersteuning]: ./media/azure-portal-how-to-use/azure_portal_6.png



<!--HONumber=Jun16_HO2-->


