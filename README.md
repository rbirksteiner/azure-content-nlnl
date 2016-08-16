# Handleiding voor bijdragers aan de technische documentatie voor Azure

U hebt de GitHub-repository gevonden met de bron voor de technische documentatie die wordt gepubliceerd in het documentatiecentrum van Azure ([http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation)).

Deze repository bevat ook instructies waarmee u kunt bijdragen aan onze technische documentatie.  Raadpleeg de [index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md) voor een lijst van de artikelen in de handleiding voor bijdragers.

## Bijdragen aan de Azure-documentatie

Bedankt voor uw interesse in de Azure-documentatie.

* [Manieren om bij te dragen](#ways-to-contribute)
* [Gedragscode](#code-of-conduct)
* [Over uw bijdragen aan Azure-inhoud](#about-your-contributions-to-azure-content)
* [Organisatie van de repository](#repository-organization)
* [GitHub, Git en deze repository gebruiken](#use-github-git-and-this-repository)
* [Hoe u markdown kunt gebruiken om uw onderwerp op te maken](#how-to-use-markdown-to-format-your-topic)
* [Feedback, opmerkingen en ondersteuning](./contributor-guide/feedback-and-comments.md)
* [Meer bronnen](#more-resources)
* [Index van alle artikelen in de handleiding voor bijdragers](./contributor-guide/contributor-guide-index.md) (opent een nieuwe pagina)

## Manieren om bij te dragen 

U kunt op diverse manieren bijdragen aan de [Azure-documentatie](http://azure.microsoft.com/documentation/):

* Doe mee aan een [forumdiscussie](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Plaats Disqus-opmerkingen onder aan artikelen.
* U kunt eenvoudig bijdragen aan technische artikelen in de GitHub-gebruikersinterface. Zoek het artikel in deze repository op of ga naar het artikel via [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation). Klik vervolgens op de link in het artikel die u naar de GitHub-bron van het artikel leidt.
* Als u belangrijke wijzigingen doorvoert in een bestaand artikel, afbeeldingen toevoegt of wijzigt, of een nieuw artikel maakt, moet u deze repository vertakken, Git Bash en Markdown Pad installeren en een aantal Git-opdrachten leren.

##Gedragscode

Op dit project is de [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Open Source-gedragscode) van toepassing. Raadpleeg voor meer informatie de [Microsoft Open Source Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Veelgestelde vragen over de Microsoft Open Source-gedragscode). Als u aanvullende vragen of opmerkingen hebt, neemt u contact op met [opencode@microsoft.com](mailto:opencode@microsoft.com).

##Over uw bijdragen aan Azure-content

###Kleine correcties

Op kleine correcties of verduidelijkingen die u toevoegt aan de documentatie of codevoorbeelden in deze repository, zijn de [Gebruiksvoorwaarden voor de Microsoft Azure-website](http://azure.microsoft.com/support/legal/website-terms-of-use/) van toepassing.


###Grotere wijzigingen

Als u een pull-aanvraag indient met nieuwe of belangrijke wijzigingen in de documentatie of codevoorbeelden, ontvangt u in GitHub een bericht met het verzoek een online licentieovereenkomst voor bijdragen (Contribution License Agreement, CLA) aan te vragen als u lid bent van een van deze groepen:

* Leden van de Microsoft Open Technologies-groep.
* Bijdragers die niet voor Microsoft werken.

U moet het online formulier invullen voordat we uw pull-aanvraag kunnen accepteren.

U vindt alle informatie via [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## Organisatie van de repository

De inhoud van de repository azure-content volgt de documentatiestructuur op [Azure.Microsoft.com](http://azure.microsoft.com). Deze bibliotheek bevat twee hoofdmappen:

### \articles

De map *\articles* bevat de documentatieartikelen die zijn opgemaakt als markdownbestanden met de indeling *.md*.

Artikelen in de hoofdmap worden naar Azure.Microsoft.com gepubliceerd via het pad *http://azure.microsoft.com/documentation/articles/{artikelnaam-zonder-md}/*.

* **Bestandsnamen van artikelen:** raadpleeg [onze richtlijnen voor de naamgeving van bestanden](./contributor-guide/file-names-and-locations.md).

Artikelen in een eigen servicemap worden naar Azure.Microsoft.com gepubliceerd via het pad *http://azure.microsoft.com/documentation/articles/service-folder/{artikelnaam-zonder-md}/*.

* **Mediasubmappen:** de map *\articles* bevat de map *\media* voor mediabestanden bij artikelen in de hoofdmap. Binnen deze map bevinden zich submappen met de afbeeldingen voor elk artikel.  De servicemappen bevatten een afzonderlijke mediamap voor de artikelen in elke servicemap. De mappen met afbeeldingen voor de artikelen hebben dezelfde naam als het artikelbestand, maar zonder de bestandsextensie *.md*.

### \includes

U kunt herbruikbare blokken met conAtent maken die moeten worden opgenomen in een of meer artikelen. Raadpleeg [Custom extensions used in our technical content](./contributor-guide/custom-markdown-extensions.md) (Aangepaste extensies die in onze technische inhoud worden gebruikt).

### \markdown templates

Deze map bevat onze standaardmarkdownsjabloon met de basismarkdownopmaak die u voor een artikel nodig hebt.

### \contributor-guide

Deze map bevat artikelen die deel uitmaken van onze handleiding voor bijdragers.  

## GitHub, Git en deze repository gebruiken

Raadpleeg [Install and set up tools for authoring in GitHub](./contributor-guide/tools-and-setup.md) (Hulpprogramma’s voor ontwerpen in GitHub installeren) voor meer informatie over hoe u kunt bijdragen, hoe u de GitHub-gebruikersinterface kunt gebruiken om kleine wijzigingen aan te brengen, en hoe u de repository kunt vertakken en klonen voor grotere wijzigingen.

Als u GitBash installeert en ervoor kiest om lokaal te werken, volgt u de stappen voor het maken van een nieuwe lokale werkvertakking, het aanbrengen van wijzigingen en het verzenden van deze wijzigingen naar de hoofdvertakking. Deze stappen vindt u in [Git commands for creating a new article or updating an existing article](./contributor-guide/git-commands-for-master.md) (Git-opdrachten voor het maken van een nieuw artikel of het bijwerken van een bestaand artikel).

### Vertakkingen

We raden u aan om lokale werkvertakkingen te maken die zijn gericht op specifieke wijzigingen. Elke vertakking moet worden beperkt tot één concept/artikel om de werkstroom te stroomlijnen en de kans op samenvoegconflicten te verminderen.  In de volgende gevallen is een nieuwe vertakking een goed idee:

* U maakt een nieuw artikel (met bijbehorende afbeeldingen).
* U corrigeert spel- en grammaticafouten in een artikel.
* U past één wijziging in de opmaak toe op een groot aantal artikelen (u maakt bijvoorbeeld nieuwe voettekst met betrekking tot het auteursrecht).

## Hoe u markdown kunt gebruiken om uw onderwerp op te maken

Alle artikelen in deze bibliotheek gebruiken GitHub-markdown.  Hier volgt een lijst van resources.

- [Basisbeginselen van markdown](https://help.github.com/articles/markdown-basics/)

- [Afdrukbare cheatsheet voor markdown](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Raadpleeg [het onderwerp over hulpprogramma's en installatie](./contributor-guide/tools-and-setup.md#install-a-markdown-editor) voor onze lijst met markdowneditors.

## Metadata van het artikel

De metadata van het artikel worden op de website azure.microsoft.com voor diverse functies gebruikt, zoals toewijzing aan een auteur, toewijzing aan een bijdrager, breadcrumbs, artikelbeschrijvingen, SEO-optimalisatie en rapportage. Microsoft gebruikt deze functies om de prestaties van de inhoud te evalueren. De metadata zijn dus belangrijk. [Hier vindt u de richtlijnen waarmee u ervoor zorgt dat uw metadata op de juiste manier worden gebruikt](./contributor-guide/article-metadata.md).

## Meer bronnen

Raadpleeg de [index van onze handleiding voor bijdragers](./contributor-guide/contributor-guide-index.md) voor alle onderwerpen over onze richtlijnen.



<!--HONumber=Aug16_HO1-->


