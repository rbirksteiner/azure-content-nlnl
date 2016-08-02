<properties
   pageTitle="Aan de slag met Microsoft Power BI Embedded Preview"
   description="Power BI Embedded, voeg interactieve Power BI-rapporten toe aan uw BI-toepassing (Business Intelligence)"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="04/24/2016"
   ms.author="derrickv"/>

# Aan de slag met Microsoft Power BI Embedded Preview

**Microsoft Power BI Embedded** is een Azure-service waarmee applicatieontwikkelaars interactieve Power BI-rapporten aan hun eigen toepassingen kunnen toevoegen. **Power BI Embedded** werkt met bestaande toepassingen zonder dat het nodig is die toepassingen opnieuw te ontwerpen of de manier te wijzigen waarop gebruikers zich aanmelden.

Zie [Wat is Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md) voor meer informatie over Power BI Embedded.

Zoals beschreven in [Wat is Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md), worden resources voor **Microsoft Power BI Embedded** ingericht via de [Azure ARM API's](https://msdn.microsoft.com/library/mt712306.aspx). In dit geval is de resource die u inricht een **Power BI-werkruimteverzameling**. In de volgende sectie wordt beschreven hoe u een werkruimteverzameling maakt.

![](media\power-bi-embedded-get-started\introduction.png)

## Een werkruimteverzameling maken
Een **werkruimteverzameling** is een Azure-resource op het hoogste niveau en een container voor de inhoud die wordt ingesloten in uw toepassing. U kunt op twee manieren een **werkruimteverzameling** maken:

   -    Handmatig met de Azure Portal
   -    Programmatisch met behulp van de Azure Resource Manager (ARM) API's

Hier volgen de stappen voor het bouwen van een **werkruimteverzameling** met behulp van de Azure Portal.

   1.   Open de **Azure Portal**: [http://portal.azure.com](http://portal.azure.com) en meld u aan.

   2.   Klik op **+ Nieuw** boven in het venster.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Klik onder **Gegevens en analyse** op **Power BI Embedded**.
   4.   Voer op de **blade Maken** de vereiste gegevens in. Informatie over **prijzen** vindt in [Prijzen van Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Klik op **Maken**.

Het duurt even voordat de **werkruimteverzameling** is ingericht. Wanneer dit is voltooid, wordt u naar de **blade Werkruimteverzameling** gebracht.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

De **blade Maken** bevat informatie die u nodig hebt om de API's aan te roepen die werkruimten maken en om inhoud hierin te implementeren.

In de volgende sectie wordt beschreven hoe **toegangssleutels** worden gebruikt voor het genereren van **app-tokens** die worden gebruikt om uw API-aanvragen te verifiëren.

<a name="view-access-keys"/>
## Toegangssleutels voor Power BI API

Een van de belangrijkste stukjes informatie die nodig zijn om Power BI REST API's aan te roepen, zijn de **toegangssleutels**. Deze worden gebruikt voor het genereren van de **app-tokens** die worden gebruikt om uw API-aanvragen te verifiëren. U geeft uw **toegangssleutels** weer door te klikken op **Toegangssleutels** op de **blade Instellingen**. Meer informatie over **app-tokens** kunt u vinden in [Hoe de stroom voor app-tokens werkt](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

U ziet dat u twee sleutels hebt.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Kopieer deze sleutels en sla ze veilig op in uw toepassing. Het is belangrijk dat u deze sleutels net zo behandelt als wachtwoorden, omdat deze toegang bieden tot de inhoud in uw **werkruimteverzameling**.

Wanneer twee sleutels worden vermeld, hebt u slechts één sleutel nodig. De tweede sleutel biedt u de mogelijk periodiek opnieuw sleutels te genereren, zonder dat toegang tot de service wordt onderbroken.

Nu u een exemplaar van Power BI voor uw toepassing en **toegangssleutels** hebt, kunt u een rapport in uw eigen app importeren. Voordat u een rapport leert importeren, kunt u in de volgende sectie lezen hoe u Power BI-gegevenssets en -rapporten maakt om deze in een app in te sluiten.

## Power BI-gegevenssets en -rapporten maken om in een app in te sluiten

Nu u een exemplaar van Power BI voor uw toepassing hebt gemaakt en **toegangssleutels** hebt, moet u de Power BI-gegevenssets en -rapporten maken die u wilt insluiten. Gegevenssets en rapporten kunnen worden gemaakt met behulp van **Power BI Desktop**. U kunt [Power BI Desktop gratis](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/) downloaden. Of u downloadt de [Retail Analysis Sample PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547), als u snel aan de slag wilt. Meer informatie over het gebruik van **Power BI Desktop** vindt u in [Aan de slag met Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Met **Power BI Desktop** maakt u verbinding met uw gegevensbron door een kopie van de gegevens te importeren in **Power BI Desktop** of door een rechtstreekse verbinding te maken met de gegevensbron via **DirectQuery**.

Dit zijn de verschillen tussen **Importeren** en **DirectQuery**.

|Importeren | DirectQuery
|---|---
|Tabellen, kolommen *en gegevens* worden geïmporteerd of gekopieerd naar **Power BI Desktop**. Als u met visualisaties werkt, vraagt **Power BI Desktop** een kopie van de gegevens op. Als u wilt zien welke wijzigingen zijn doorgevoerd in de onderliggende gegevens, moet u deze vernieuwen of een volledige, actuele gegevensset opnieuw importeren.|Alleen *tabellen en kolommen* worden geïmporteerd of gekopieerd naar **Power BI Desktop**. Wanneer u met visualisaties werkt, voert **Power BI Desktop** query’s uit op de onderliggende gegevensbron. Dit betekent dat u altijd actuele gegevens bekijkt.

Meer informatie over het maken van een verbinding met een gegevensbron vindt u in [Verbinding maken met een gegevensbron](power-bi-embedded-connect-datasource.md).

Nadat u uw werk in **Power BI Desktop** hebt opgeslagen, wordt een PBIX-bestand gemaakt. Dit bestand bevat het rapport. Daarnaast bevat de PBIX ofwel de volledige gegevensset, als u gegevens importeert, ofwel, als u **DirectQuery** gebruikt, alleen een gegevensset-schema. U kunt met de [Power BI Import API](https://msdn.microsoft.com/library/mt711504.aspx) de PBIX programmatisch implementeren in uw werkruimte.

> [AZURE.NOTE] **Power BI Embedded** heeft aanvullende API's voor het wijzigen van de server en database waarnaar uw gegevensset wijst en het instellen van accountreferenties die door de gegevensset wordt gebruikt voor het maken van een verbinding met uw database. Zie [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) en [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## Volgende stappen
In de vorige stappen hebt u een werkruimteverzameling en uw eerste rapport en gegevensset gemaakt. Nu gaat u leren hoe u code schrijft voor **Power BI Embedded**. Om u te helpen hiermee aan de slag te gaan, hebben we een voorbeeld van een webapp gemaakt: [Aan de slag met het voorbeeld](power-bi-embedded-get-started-sample.md). Het voorbeeld laat u zien hoe u het volgende doet:

  - Inhoud inrichten
      - Een werkruimte maken
      - Een PBIX-bestand importeren
      - Verbindingstekenreeksen bijwerken en referenties voor gegevenssets instellen

  - Een rapport veilig insluiten

## Zie ook
- [Aan de slag met het voorbeeld](power-bi-embedded-get-started-sample.md)
- [Wat is Power BI Embedded?](power-bi-embedded-what-is-power-bi-embedded.md)
- [Aan de slag met Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Prijzen van Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527)



<!--HONumber=Jun16_HO2-->


