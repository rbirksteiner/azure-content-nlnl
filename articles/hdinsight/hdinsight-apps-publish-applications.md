<properties
    pageTitle="HDInsight-toepassingen publiceren | Microsoft Azure"
    description="Informatie over het maken en publiceren van HDInsight-toepassingen."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="06/01/2016"
    ms.author="jgao"/>

# HDInsight-toepassingen publiceren in Azure Marketplace

Een HDInsight-toepassing is een toepassing die gebruikers kunnen installeren op een op Linux gebaseerd HDInsight-cluster. Deze toepassingen kunnen zijn ontwikkeld door Microsoft, door onafhankelijke softwareleveranciers (ISV) of door u zelf. In dit artikel leert u hoe u een HDInsight-toepassing publiceert in Azure Marketplace.  Zie [Een aanbieding publiceren in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) voor algemene informatie over publiceren in Azure Marketplace.

HDInsight-toepassingen maken gebruik van het *BYOL-model (Bring Your Own License)*. Hierbij is de toepassingsprovider verantwoordelijk voor het verlenen van een licentie voor de toepassing aan eindgebruikers. Azure brengt eindgebruikers alleen kosten in rekening voor de resources die ze maken, zoals het HDInsight-cluster en de bijbehorende virtuele machines/knooppunten. Facturering voor de toepassing zelf loopt op dit moment niet via Azure.

Ander artikel over HDInsight-toepassingen:

- [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): informatie over het installeren en testen van aangepaste HDInsight-toepassingen.

 
## Vereisten

U moet een aangepaste toepassing maken en testen voordat u deze naar Marketplace kunt verzenden. Zie de volgende artikelen:

- [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): informatie over het installeren en testen van aangepaste HDInsight-toepassingen.

U moet ook een ontwikkelaarsaccount hebben geregistreerd. Zie [Een aanbieding publiceren in Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) en [Een Microsoft-ontwikkelaarsaccount maken](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Uw toepassing definiëren

Het publiceren van toepassingen in Azure Marketplace verloopt in twee stappen.  Eerst definieert u een **createUiDef.json**-bestand om aan te geven met welke clusters de toepassing compatibel is. Vervolgens publiceert u de sjabloon in de Azure Portal. Hier volgt een voorbeeld van een createUiDef.json-bestand.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Veld  | Beschrijving   | Mogelijke waarden|
|-------|---------------|----------------|
|typen  |De clustertypen waarmee de toepassing compatibel is. |Hadoop, HBase, Storm, Spark (of een combinatie hiervan)|
|lagen  |De clusterlagen waarmee de toepassing compatibel is. |Standaard, Premium (of beide)|
|versies|  De HDInsight-clustertypen waarmee de toepassing compatibel is.    |3.4|

## Pakkettoepassing

Maak een ZIP-bestand met alle vereiste bestanden voor het installeren van de HDInsight-toepassingen. U hebt dit ZIP-bestand nodig bij [Toepassing publiceren](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Bekijk een voorbeeld bij [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] De naam van het installatiescript voor de toepassing moet uniek zijn voor een bepaald cluster en gebruikmaken van onderstaande notatie. 
    
    >   name": "[concat('hue-install-v0','-' ,uniekestring(‘toepassingsnaam’)]"
        
    >Zoals u ziet, heeft de scriptnaam drie onderdelen:
        
    >   1. Een voorvoegsel dat de toepassingsnaam bevat of een naam die relevant is voor de toepassing.
    >   2. Een streepje (-) voor de leesbaarheid.
    >   3. Een unieke tekenreeksfunctie met de toepassingsnaam als parameter.

    >   Een voorbeeld van deze drie onderdelen samen ziet er in de lijst met persistente scriptacties als volgt uit: hue-install-v0-4wkahss55hlas. Zie [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json) voor een voorbeeld van JSON-nettolading.

- Alle vereiste scripts.

> [AZURE.NOTE] U vindt de toepassingsbestanden (inclusief webtoepassingsbestanden, indien aanwezig) op elk willekeurig eindpunt dat openbaar toegankelijk is.

## Uw toepassing publiceren

Volg de volgende stappen om een HDInsight- toepassing te publiceren:

1. Meld u aan bij de [Azure Publishing Portal](https://publish.windowsazure.com/).
2. Klik op **Oplossingssjablonen** om een nieuwe oplossingssjabloon te maken.
3. Klik op **Ontwikkelaarscentrumaccount maken en deelnemen aan het Azure-programma** om uw bedrijf te registreren, als u dit nog niet hebt gedaan.  Zie [Een Microsoft-ontwikkelaarsaccount maken](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Klik op **Topologieën definiëren om aan de slag te gaan**. Een oplossingssjabloon is een bovenliggende topologie voor alle bijbehorende topologieën. U kunt meerdere topologieën definiëren in één aanbieding/oplossingssjabloon. Wanneer een aanbieding wordt doorgegeven voor fasering, wordt deze doorgegeven met alle bijbehorende topologieën. 
5. Voeg een nieuwe versie toe.
6. Upload het ZIP-bestand dat u hebt voorbereid bij [Pakkettoepassing](#package-application).  
7. Klik op **Certificering aanvragen**. Het Microsoft-certificeringsteam beoordeelt de bestanden en certificeert de topologie.

## Volgende stappen

- [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): informatie over het implementeren van een niet-gepubliceerde HDInsight-toepassing op HDInsight.
- [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
- [Op Linux gebaseerde Hadoop-clusters maken in HDInsight met behulp van ARM-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informatie over het aanroepen van ARM-sjablonen om HDInsight-clusters te maken.


<!--HONumber=Jun16_HO2-->


