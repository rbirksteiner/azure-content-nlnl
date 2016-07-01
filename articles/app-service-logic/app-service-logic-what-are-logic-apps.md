<properties 
    pageTitle="Wat zijn Logic Apps?" 
    description="Meer informatie over Logic Apps in App Service" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="04/07/2016"
    ms.author="klam"/>

#Wat zijn Logic Apps?

| Snelzoekgids |
| --------------- |
| [Definitietaal van Logic Apps](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Documentatie voor connector Logic Apps](../connectors/apis-list.md) |
| [Forum voor Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Azure App Service is een volledig beheerde PaaS (Platform als a Service) voor ontwikkelaars waarmee zij eenvoudiger web-apps, mobiele apps en integratie-apps kunnen bouwen. Logic Apps maken deel uit van dit pakket en stellen elke technische gebruiker of ontwikkelaar in staat de uitvoering van bedrijfsprocessen en werkstromen te automatiseren met behulp van een gemakkelijk te gebruiken visuele ontwerper.

Bovendien kunnen Logic Apps worden gecombineerd met ingebouwde [beheerde API's][beheerdeapi's], zodat zelfs de lastigste integratiescenario's met gemak kunnen worden opgelost: 

![App-ontwerper voor stromen](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Zoals is vermeld, kunt u met logische apps bedrijfsprocessen automatiseren. Hieronder vindt u enkele voorbeelden:  
 
* U kunt nieuwe records automatisch repliceren in SQL Database en vervolgens een e-mail verzenden naar de frontdesk.   
* U kunt automatisch negatieve tweets zoeken en deze verzenden naar een ongebruikt kanaal.

Dergelijke scenario's kunnen worden geconfigureerd via de visuele ontwerper, zonder dat hiervoor ook maar één regel code hoeft te worden geschreven. Ga nu aan de slag met het [bouwen van een logische app][maken].

## Waarom Logic Apps?

Met Logic Apps kunnen ontwikkelaars werkstromen ontwerpen die na een trigger worden gestart. Vervolgens wordt een aantal stappen uitgevoerd. Hierbij wordt steeds een API aangeroepen terwijl er zorgvuldig wordt gelet op de verificatie en best practices, zoals het plaatsen van controlepunten en een duurzame uitvoering.

Als u een bedrijfsproces wilt automatiseren (als u bijvoorbeeld negatieve tweets wilt zoeken en deze in een intern ongebruikt kanaal wilt plaatsen of als u nieuwe klantrecords wilt repliceren vanuit SQL op het moment dat ze in uw CRM-systeem binnenkomen), gebruikt u Logic Apps om de integratie van verschillende gegevensbronnen van cloud naar on-premises eenvoudig te automatiseren. Bekijk onze [beheerde API's][beheerdeapi‘s] voor meer inspiratie en [ga nu aan de slag][maken] om te zien wat u kunt doen. 

Daarnaast kunt u met onze [beheerde API's van BizTalk][biztalk] schalen naar volwassen integratiescenario's met de kracht van een [regelengine][regels], [handelspartnerbeheer][tpm], enzovoort.

- **Eenvoudig te gebruiken ontwerpfuncties** -Logic Apps kunnen end-to-end in de browser worden ontworpen. Starten na een trigger - De trigger kan een eenvoudig schema zijn of het moment dat een tweet over uw bedrijf wordt geplaatst. Vervolgens kunt u een willekeurig aantal acties indelen met behulp van de uitgebreide galerie met connectors.

- **SaaS eenvoudige opstellen** - Zelfs samenstellingstaken die gemakkelijk te beschrijven zijn, zijn moeilijk in code te implementeren. Met Logic Apps kunt u moeiteloos verbinding maken met verschillende systemen. Wilt u een taak in uw CRM-software maken die is gebaseerd op de activiteit van uw Facebook- of Twitter-account? Wilt u uw marketingoplossing in de cloud verbinden met uw on-premises factureringssysteem? De snelste en betrouwbaarste manier om deze problemen op te lossen, is door gebruik te maken van Logic Apps.

- **Snel aan de slag met behulp van sjablonen** - Om u op weg te helpen, is er een [galerie met sjablonen][sjablonen] waarmee u snel een enkele algemene oplossingen kunt maken. De galerie is de snelste manier om de kracht van Logic Apps te ontdekken en bevat geavanceerde BizTalk-oplossingen, eenvoudige SaaS-connectiviteit en zelfs enkele sjablonen puur voor het plezier.

- **Standaard uitbreidbaar** - Kunt u de API die u nodig hebt niet vinden? Logic Apps is ontworpen voor gebruik met API-apps; u kunt eenvoudig uw eigen API-app maken en deze als aangepaste API gebruiken. Bouw een nieuwe app alleen voor uzelf, of deel uw app en verdien er geld mee in de marketplace.

- **Echte paardenkracht bij integratie** - Start eenvoudig en groei wanneer dat nodig is. Met Logic Apps kunt u de kracht van BizTalk, de toonaangevende integratieoplossing van Microsoft, gebruiken om integratieprofessionals de oplossingen te laten bouwen die ze nodig hebben. Zie [BizTalk capabilities provided with Logic Apps][biztalk] (BizTalk-mogelijkheden van Logic Apps) voor meer informatie.

## Concept van Logic Apps

In het onderstaande overzicht ziet u enkele belangrijke onderdelen van Logic Apps. 

- **Werkstroom** - Met Logic Apps kunt u uw bedrijfsprocessen grafisch weergeven als een reeks stappen of als een werkstroom.
- **Beheerde API's** - Uw logische apps moeten toegang hebben tot gegevens en services. Beheerde API's worden speciaal gemaakt om u te helpen bij het maken van verbinding en het werken met uw gegevens. Zie [Beheerde API's][beheerdeapi's] voor een lijst met beschikbare API's.
- **Triggers** - Sommige beheerde API's kunnen ook als trigger fungeren. Met een trigger wordt een nieuw exemplaar van een werkstroom gestart op basis van een bepaalde gebeurtenis, zoals de aankomst van een e-mail of een wijziging in uw Azure Storage-account.
-  **Acties** - Elke stap na de trigger in een werkstroom wordt een actie genoemd. Elke actie is doorgaans toegewezen aan een bewerking in uw beheerde of aangepaste API-apps.
- **BizTalk** - Voor meer geavanceerde integratiescenario's bevat Logic Apps mogelijkheden van Biztalk. BizTalk is het toonaangevende integratieplatform van Microsoft. Met de BizTalk API-apps kunt u eenvoudig validatie, transformatie, regels en meer opnemen in de werkstromen van uw logische app. Zie [What are BizTalk API apps][biztalk] (Wat zijn BizTalk API-apps?) voor meer informatie.

## Aan de slag  

 - Als u aan de slag wilt met Logic Apps, volgt u de zelfstudie [Create a Logic App][maken] (Een logische app maken).  
 - [Algemene voorbeelden en scenario's weergeven](app-service-logic-examples-and-scenarios.md)
 - [Met Logic Apps kunt u bedrijfsprocessen automatiseren.](http://channel9.msdn.com/Events/Build/2016/T694) 
 - [Meer informatie over hoe u Logic Apps kunt integreren in uw systemen](http://channel9.msdn.com/Events/Build/2016/P462)
- Zie [Azure App Service][appservice] voor meer informatie over het Azure App Service-platform.

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[maken]: app-service-logic-create-a-logic-app.md
[beheerdeapi's]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[regels]: app-service-logic-use-biztalk-rules.md
[sjablonen]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Jun16_HO2-->


