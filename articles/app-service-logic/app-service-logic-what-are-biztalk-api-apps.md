<properties 
    pageTitle="Wat zijn connectors en BizTalk API Apps?" 
    description="Meer informatie over API Apps, connectors en BizTalk API Apps" 
    services="app-service\logic" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="04/20/2016" 
    ms.author="mandia"/>

# Wat zijn connectors en BizTalk API Apps?

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Azure App Services is gebouwd op een principe van uitbreidbaarheid en algemene connectiviteit via API Apps. Een *connector* is een type API-app dat gericht is op connectiviteit. Connectors worden, zoals elke andere API-app, gebruikt vanuit Web Apps, Mobile Apps en Logic Apps. Met connectors kunt u eenvoudig verbinding maken met bestaande services en kunt u eenvoudig verificatie beheren, bewaking bieden, analyses uitvoeren, en meer.

Ontwikkelaars kunnen hun eigen API Apps maken en deze privé implementeren. In de toekomst kunnen ontwikkelaars hun eigen API Apps delen en er geld mee verdienen via de marketplace. 

![API Apps Marketplace](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

Om ervoor te zorgen dat ontwikkelaars sneller oplossingen kunnen bouwen met Azure App Service, heeft het team van Azure een aantal connectors aan de marketplace toegevoegd dat tegemoetkomt aan algemene scenario's. Daarnaast is een aantal Premium- en BizTalk- mogelijkheden beschikbaar, waarmee het bereik van App Service kan worden uitgebreid naar complexe en geavanceerde integratiescenario's.

In Azure App Service zijn verschillende servicecategorieën beschikbaar. De categorie Alles bevat alle connectors en API Apps, met volledige functionaliteit.  

In [Prijzen van App Service](https://azure.microsoft.com/pricing/details/app-service/) vindt u een beschrijving van de servicecategorieën en leest u wat is opgenomen in deze categorieën. In de volgende secties vindt u een beschrijving van de verschillende categorieën van BizTalk API Apps en connectors.


## Hybride connectors 
Met de hybride connectors kunt u het bereik van App Services verder uitbreiden in de onderneming, met connectiviteit voor [SAP](app-service-logic-connector-sap.md), [Oracle](app-service-logic-connector-oracle.md), [DB2](app-service-logic-connector-db2.md), [Informix](app-service-logic-connector-informix.md) en WebSphere MQ. 

## EAI- en EDI-services
Voor het bouwen van essentiële zakelijke apps is meer nodig dan alleen connectiviteit. Het toonaangevende implementatieplatform van Microsoft, BizTalk Server, vormt de basis van BizTalk API Apps. BizTalk API Apps biedt geavanceerde integratiemogelijkheden die eenvoudig kunnen worden vastgemaakt aan Web Apps, Mobile Apps en Logic Apps. Voorbeelden van deze integratiemogelijkheden zijn [validatie](app-service-logic-xml-validator.md), [extractie](app-service-logic-xpath-extract.md), [transformatie](app-service-logic-transform-xml-documents.md), [encoders](app-service-logic-connector-jsonencoder.md), [handelspartnerbeheer](app-service-logic-connector-tpm.md) en ondersteuning voor EDI-indelingen zoals [X12](app-service-logic-connector-x12.md), [EDIFACT](app-service-logic-connector-edifact.md) en [AS2](app-service-logic-connector-as2.md).

Aanvullende bronnen: [Business-to-business connectors and API apps](app-service-logic-b2b-connectors.md) Business-to-business-connectors en API Apps)  
[Create a B2B process (Een B2B-proces maken)](app-service-logic-create-a-b2b-process.md)  
[Create a trading partner agreement (Een handelspartnerovereenkomst maken)](app-service-logic-create-a-trading-partner-agreement.md)  
[Track your B2B messages (Uw B2B-berichten bijhouden)](app-service-logic-track-b2b-messages.md)  


## Regels
Bedrijfsregels bestaan uit het beleid en de beslissingen die de bedrijfsprocessen regelen. Normaal gesproken zijn regels dynamisch en veranderen ze na verloop van tijd om verschillende redenen, bijvoorbeeld als gevolg van bedrijfsplannen, regelgevingen en diverse andere redenen. In [BizTalk Rules in App Services](app-service-logic-use-biztalk-rules.md) (BizTalk-regels in App Services) kunt u dit beleid loskoppelen van uw toepassingscode en het wijzigingsproces eenvoudiger en sneller maken.

## Lijst met connectors en API Apps
Zie [Connectors and API Apps List](app-service-logic-connectors-list.md) (Lijst met connectors en API Apps) voor een volledige lijst met connectors en API Apps die in elke categorie zijn opgenomen, waaronder de standaardconnectors, BizTalk EAI, premium-connectors, enzovoort.
 



<!--HONumber=Jun16_HO2-->


