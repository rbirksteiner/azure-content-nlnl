<properties
    pageTitle="Lijst met beschikbare connectors en API Apps | Microsoft Azure App Service"
    description="Meer informatie over connectors en API Apps in Azure App Service"
    services="app-service\logic"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/11/2016"
    ms.author="mandia"/>


# Lijst met connectors en API Apps voor gebruik in Logic Apps
>[AZURE.NOTE] Deze versie van het artikel is van toepassing Logic Apps-previewschemaversie 2014-12-01. Klik voor de previewschemaversie 2015-08-01 op [New Connectors List](../connectors/apis-list.md) (Lijst met nieuwe connectors).

Lees meer over de beschikbare connectors en API Apps die door Microsoft zijn gemaakt en die u kunt gebruiken in Logic Apps.

Zie [Prijzen van App Service](https://azure.microsoft.com/pricing/details/app-service/) voor informatie over prijzen en een overzicht van wat is opgenomen in elke servicecategorie.

> [AZURE.NOTE] Als u aan de slag wilt met Azure Logic Apps en u zich nog niet hebt aangemeld voor een Azure-account, gaat u naar [Try Logic Apps](https://tryappservice.azure.com/?appservice=logic) (Logic Apps uitproberen). U kunt onmiddellijk een tijdelijke logische app in App Service maken. U hebt geen creditcard nodig en u doet geen toezeggingen.

## Basisconnectors
In de volgende tabel ziet u de beschikbare connectors en API Apps die zijn gemaakt door Microsoft en die beschikbaar zijn als basisconnectors.

Naam | Beschrijving
--- | ---
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | Hiermee kunnen berichten vanuit de Service Bus-wachtrijen en -onderwerpen worden verzonden en kunnen berichten vanuit de Service Bus-wachtrijen en abonnementen worden ontvangen.
[Bing Vertalen](https://azure.microsoft.com/marketplace/partners/microsoft_com/bingtranslator) | Gebruik Bing om tekst in een andere taal te vertalen.
[HTTP](app-service-logic-connector-http.md) | De HTTP-listener opent een eindpunt dat als een HTTP-server fungeert en luistert naar inkomende HTTP- of HTTPS-aanvragen. Voor de HTTP-actie is geen API App vereist en de actie wordt op systeemeigen wijze ondersteund in Logic Apps.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Met de Office 365-connector kunt u e-mails verzenden en ontvangen, uw agenda beheren en uw contactpersonen beheren met uw Office 365-account.
[QuickBooks](app-service-logic-connector-quickbooks.md) | U kunt verschillende taken uitvoeren, zoals het maken, bijwerken en het uitvoeren van query's op verschillende entiteiten van Intuit QuickBooks, zoals klanten, artikelen en facturen.
[Slack](app-service-logic-connector-slack.md) | U kunt verbinding maken met Slack en berichten plaatsen in ongebruikte kanalen.
[Wait](app-service-logic-connector-wait.md) | Met deze connector kunt u de uitvoering van uw app vertragen. U kunt de app gedurende een bepaalde periode vertragen of totdat een gebeurtenis op een specifiek tijdstip optreedt.


## Enterprise Integration-connectors
In de volgende tabel ziet u de beschikbare connectors en API Apps die zijn gemaakt door Microsoft en die beschikbaar zijn als Enterprise Integration-connectors.

Naam  | Beschrijving
------------- | -------------
[AS2-connector](app-service-logic-connector-as2.md) | Hiermee kunt u berichten ontvangen en verzenden met het transportprotocol AS2. Gegevens worden veilig en betrouwbaar getransporteerd met behulp van digitale certificaten en versleuteling.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Hiermee kunt u berichten in business-to-business-communicatie ontvangen en verzenden met het protocol EDIFACT.
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | Biedt interoperabiliteit tussen gegevens in platte bestanden (zoals Excel en CSV) en XML-gegevens. Met deze API App kunt u een exemplaar van een plat bestand converteren naar XML en omgekeerd.
[BizTalk JSON Encoder](app-service-logic-connector-jsonencoder.md) | Een encoder en decoder waarmee u in uw app kunt zorgen voor interopabiliteit tussen JSON- en XML-gegevens. Hiermee kunt u een JSON-exemplaar converteren naar XML en omgekeerd.
[BizTalk Rules](app-service-logic-use-biztalk-rules.md) | Gebruik BizTalk Rules om de bedrijfslogica binnen een organisatie te definiëren en te beheren. Hiermee kunt u het bedrijfsbeleid bijwerken zonder dat u hiervoor de gekoppelde toepassingen opnieuw hoeft te compileren of te implementeren.
[BizTalk Trading Partner Management](app-service-logic-connector-tpm.md) | Hiermee kunt u business-to-business-relaties definiëren en persistent maken met behulp van partners, overeenkomsten, schema's en certificaten die in overeenkomsten worden gebruikt. Deze relaties worden afgedwongen met behulp van de API Apps AS2, EDIFACT en X12.
[BizTalk Transform Service](app-service-logic-transform-xml-documents.md) | Hiermee worden gegevens van de ene indeling geconverteerd naar een andere indeling. U kunt ook een bestaande toewijzing (TRFM-bestand) uploaden, de koppelingen tussen de bron- en doelschema's weergeven en de testfunctionaliteit gebruiken met XML-inhoud met voorbeeldinvoer. Daarnaast kunt u verschillende ingebouwde functies gebruiken, waaronder tekenreeksmanipulatie, voorwaardelijke toewijzingen, en meer.
[BizTalk X12](app-service-logic-connector-x12.md) | Hiermee kunt u berichten in business-to-business-communicatie ontvangen en verzenden met het protocol X12.
[BizTalk XML Validator](app-service-logic-xml-validator.md) | Hiermee kunt u XML-gegevens valideren op basis van vooraf gedefinieerde XML-schema's. U kunt bestaande schema's gebruiken of schema's genereren op basis van een exemplaar van een plat bestand, een JSON-exemplaar of bestaande connectors.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Hiermee kunt u de gegevens uit XML-inhoud opzoeken en ophalen op basis van het gewenste XPath.
[DB2-connector](app-service-logic-connector-db2.md) | Met deze connector kunt u verbinding maken met een IBM DB2-database, on-premises en via een virtuele Azure-machine waarop een Windows-besturingssysteem wordt uitgevoerd. Hiermee kunnen Web-API- en OData-API-bewerkingen worden toegewezen aan Informix Structured Query Language-opdrachten. <br/><br/>Er zijn geen triggers. Acties omvatten tabel selecteren, invoegen, bijwerken, verwijderen en aangepaste instructies.<br/><br/>Met deze connector kan Microsoft Client for DRDA ook verbinding maken met een Informix-server via een TCP/IP-netwerk.
[File](app-service-logic-connector-file.md) | Met deze connector kunt u verbinding maken met het on-premises bestandssysteem of netwerk en verschillende bestandstaken uitvoeren, waaronder het uploaden, verwijderen en weergeven van bestanden.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Hiermee kunt u verbinding maken met een FTP-/FTPS-server en verschillende FTP-taken uitvoeren, waaronder het uploaden, ophalen en verwijderen van bestanden.
[Informix](app-service-logic-connector-informix.md) | Hiermee kunt verbinding maken met een IBM Informix-database, on-premises en via een virtuele Azure-machine waarop een Windows-besturingssysteem wordt uitgevoerd. Hiermee kunnen Web-API- en OData-API-bewerkingen worden toegewezen aan Informix Structured Query Language-opdrachten.<br/><br/>Er zijn geen triggers. Acties omvatten tabel selecteren, invoegen, bijwerken, verwijderen en aangepaste instructies.<br/><br/>Bij on-premises gebruik, kunt u VPN of Azure ExpressRoute gebruiken. Met deze connector kan Microsoft Client for DRDA ook verbinding maken met een Informix-server via een TCP/IP-netwerk.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Hiermee kunt u verbinding maken met on-premises SQL Server of Azure SQL Database. U kunt items in een SQL-databasetabel maken, bijwerken, ophalen en verwijderen.
MQ | Hiermee kunt verbinding maken met IBM WebSphere MQ Server versie 8, on-premises en via een virtuele Azure-machine waarop een Windows-besturingssysteem wordt uitgevoerd. Bij on-premises gebruik, kunt u VPN of Azure ExpressRoute gebruiken. De connector bevat ook Microsoft Client voor MQ.<br/><br/>Er zijn geen triggers. Er zijn geen acties.<br/><br/>**Opmerking** Kan momenteel niet worden gebruikt met Logic Apps.
[Oracle Database](app-service-logic-connector-oracle.md) | Hiermee kunt u verbinding maken met on-premises Oracle Database en items in een databasetabel maken, bijwerken, ophalen en verwijderen.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Hiermee kunt u verbinding maken met een POP3-server om e-mails met bijlagen op te halen.
[SAP](app-service-logic-connector-sap.md) | Hiermee kunt u verbinding maken met een on-premises SAP-server en RFC's, BAPI's en tRFC's oproepen, en IDOC's verzenden.

## Connectors als triggers
Verschillende connectors bieden triggers voor Logic Apps. Er zijn twee typen triggers:

1. Poll-triggers: Met deze triggers wordt uw service met een opgegeven frequentie gepeild om te controleren of er nieuwe gegevens zijn. Als er nieuwe gegevens beschikbaar zijn, wordt een nieuw exemplaar van uw logische app uitgevoerd waarbij de gegevens als invoer worden gebruikt. Om te voorkomen dat dezelfde gegevens meerdere keren wordt gebruikt, kunnen met de trigger gegevens worden opgeschoond die zijn gelezen en aan de logische app zijn doorgegeven. Voorbeelden van dergelijke connectors zijn File, SQL en Azure Storage.
2. Push-triggers: Met deze triggers wordt geluisterd naar gegevens op een eindpunt of wordt geluisterd of een gebeurtenis plaatsvindt. Vervolgens wordt een nieuw exemplaar van een logische app getriggerd. Voorbeelden van dergelijke connectors zijn HTTP Listener en Twitter.

## Connectors als acties
Connectors kunnen ook worden gebruikt als acties in uw logische app. Acties zijn handig voor het opzoeken van gegevens in de logische app, die vervolgens in de uitvoering kunnen worden gebruikt. U kunt tijdens het afhandelen van een order bijvoorbeeld gegevens in een SQL-database opzoeken voor meer informatie over een klant. Of misschien wilt u gegevens op een doel schrijven, bijwerken of verwijderen. U kunt dit doen met de acties die via de connectors beschikbaar zijn. Acties worden toegewezen aan bewerkingen in API Apps (zoals gedefinieerd door de Swagger-metagegevens).

## Uw eigen connectors en API Apps maken
[Naslaginformatie over connectors en API Apps](http://aka.ms/appservicesconnectorreference)  
[API App-triggers voor Azure App Service](../app-service-api/app-service-api-dotnet-triggers.md)  
[Naslaginformatie over Logic Apps](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## Meer informatie over connectors en API Apps
[Wat zijn connectors en BizTalk API Apps?](app-service-logic-what-are-biztalk-api-apps.md)  
[Hybride verbindingsbeheer gebruiken in Azure App Service](app-service-logic-hybrid-connection-manager.md)  
[Ingebouwde API Apps en connectors beheren en bewaken](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Jun16_HO2-->


