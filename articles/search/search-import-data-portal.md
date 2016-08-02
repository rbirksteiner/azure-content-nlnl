<properties
    pageTitle="Gegevens importeren in Azure Search met de indexeerfuncties in de Azure Portal | Microsoft Azure | Gehoste service voor zoeken in de cloud"
    description="Het gebruik van indexeerfuncties in de Azure Portal."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="heidist"/>

# Gegevens importeren in Azure Search via de portal

Azure Portal bevat de opdracht **Gegevens importeren** op het Azure Search-dashboard, waarmee u gegevens in Azure Search kunt opnemen. De opdracht is afhankelijk van de ingebouwde indexeerfuncties in een bestaande gegevensbron. Met deze opdracht maakt en uploadt u documenten op basis van de rijenset in de gegevensbron.

Met de wizard bestaat het importeren van gegevens uit drie delen:

- een gegevensbronverbinding
- een doelindex waarin gegevens worden geüpload (in de meeste gevallen kan deze door de wizard worden gegenereerd)
- een schema dat nu of regelmatig wordt uitgevoerd

Als u een indexeerfunctie of de opdracht **Gegevens importeren** wilt gebruiken, moet uw primaire gegevensbron een van de volgende ondersteunde gegevensbronnen zijn: Azure SQL Database, relationele SQL Server-database op een Azure VM of Azure DocumentDB.

U kunt slechts importeren uit één tabel, weergave of gelijkwaardige gegevensstructuur. Mogelijk moet u deze gegevensstructuur eerst in uw toepassingsgegevensbron maken om de juiste metagegevens en gegevensinvoer in uw zoekindex te verkrijgen.

U kunt deze werkstroom met voorbeeldgegevens uitproberen. Ga naar [Aan de slag met Azure Search in de Azure Portal](search-get-started-portal.md) om te beginnen.

##Gegevensimport configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Open het servicedashboard van uw Azure Search-service. U kunt het dashboard op verschillende manieren vinden.
    - Klik in de snelbalk op **Start**. De startpagina heeft tegels voor alle services in uw abonnement. Klik op de tegel om het servicedashboard te openen.
    - Klik in de snelbalk op **Browse All (Door alle bladeren) ** > **Filteren op** > **Services zoeken** om uw zoekservice in de lijst te vinden.

3. In het servicedashboard ziet u een opdrachtbalk bovenin, inclusief de opdracht **Gegevens importeren**. Klik op **Gegevens importeren** om de blade Gegevens importeren te openen.

4. Klik op **Verbinden met uw gegevens** om een gegevensbrondefinitie op te geven die door een indexeerfunctie wordt gebruikt. Een aantal opties:
    -   Een bestaande gegevensbron verwijst naar een gegevensbrondefinitie die u eerder hebt gemaakt voor een indexeerfunctie. Als u al indexeerfuncties hebt gedefinieerd in uw zoekservice, kunt u een gegevensbrondefinitie van een andere invoer wijzigen.
    -   Azure SQL wordt gebruikt om een gegevensbronverbinding op te geven naar een SQL-database op Azure of een SQL Server-database op een Azure VM.
    -   DocumentDB wordt gebruikt om een gegevensbronverbinding op te geven voor dit gegevensbrontype.

   Voor Azure SQL en DocumentDB moet de database in uw abonnement staan. Als u een verbindingsreeks weet, kunt u deze plakken, maar u kunt ook een gegevensbron kiezen die eerder is gemaakt door iemand met schrijfbevoegdheden voor uw abonnement.

5. Klik op **Doelindex aanpassen** om de standaardindex te voltooien.
    - De **sleutel** is vereist. Het veld dat u voor de sleutel selecteert, moet een tekenreeksveld met unieke waarden zijn.
    - De veldnaam en het veldtype worden doorgaans automatisch ingevuld. U kunt het gegevenstype wijzigen.
    - Selecteer de kenmerken voor elk veld:
        - Met Ophalen mogelijk wordt het veld in de zoekresultaten weergegeven.
        - Met Filterbaar kunt u in filterexpressies naar het veld verwijzen.
        - Met Sorteerbaar kan het veld in een sortering worden gebruikt.
        - Met Facetable (geschikt voor facetten) kunt u het veld gebruiken voor meervoudige navigatie.
        - Met Doorzoekbaar kunt u in het veld zoeken in volledige tekst.
    - Klik op het tabblad **Analyse** als u een taalanalyse op veldniveau wilt opgeven. Zie [Een index voor documenten in meerdere talen maken](search-language-support.md) voor meer informatie.
    - Klik op **Suggestie** als u Automatisch aanvullen of automatisch aangevulde querysuggesties wilt inschakelen.

6. Klik op **Uw gegevens importeren** om de importbewerking uit te voeren met de optie Nu uitvoeren of om een terugkerend schema in te stellen.

Tijdens het uitvoeren van de gegevensimportbewerking is op de achtergrond een indexeerfunctie gemaakt. U kunt onderdelen van de indexeerfunctie direct bewerken.

##Een bestaande indexeerfunctie bewerken

Dubbelklik in het servicedashboard op de tegel Indexeerfunctie om een lijst met alle indexeerfuncties voor uw abonnement weer te geven. Dubbelklik op een van de indexeerfuncties om deze uit te voeren, te bewerken of te verwijderen.



<!--HONumber=Jun16_HO2-->


