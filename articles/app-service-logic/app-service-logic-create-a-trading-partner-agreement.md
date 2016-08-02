<properties 
   pageTitle="Een handelspartnerovereenkomst maken in Azure App Service | Microsoft Azure" 
   description="Handelspartnerovereenkomsten maken" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Een handelspartnerovereenkomst maken   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Handelspartners zijn de entiteiten die betrokken zijn bij B2B-communicatie (Business-to-Business). Wanneer twee partners een relatie tot stand brengen, wordt deze aangeduid als een *overeenkomst*. De gedefinieerde overeenkomst wordt gebaseerd op het type communicatie dat de twee partners willen realiseren: protocol of transportspecifiek. De volgende B2B-protocollen en -transporten worden ondersteund in Azure App Service:

- AS2 (Applicability Statement 2)
- EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
- X12 (ASC X12)

### BizTalk API Apps die ondersteuning bieden voor B2B-scenario's
In de volgende API Apps kunt u deze mogelijkheden inschakelen via een uitgebreide en intuïtieve ervaring in Azure Portal.


## BizTalk Trading Partner Management (TPM)
- Partners, profielen en identiteiten maken
- EDI-schema's opslaan en beheren
- Certificaten maken en beheren (gebruikt in AS2-protocol)
- AS2-overeenkomsten maken en beheren
- EDIFACT-overeenkomsten maken en beheren (inclusief batchverwerking aan de verzendkant)
- X12-overeenkomsten maken en beheren (inclusief batchverwerking aan de verzendkant)

![][1]


## AS2-connector
- AS2-overeenkomsten uitvoeren zoals is gedefinieerd in het bijbehorende TPM API-app-exemplaar
- AS2-verwerkingsinformatie/-traceringsinformatie beschikbaar maken voor probleemoplossing


## BizTalk EDIFACT
- EDIFACT-overeenkomsten uitvoeren zoals is gedefinieerd in het bijbehorende TPM API-app-exemplaar
- EDIFACT-verwerkingsinformatie/-traceringsinformatie beschikbaar maken voor probleemoplossing
- Statusbeheer van batches (starten en stoppen) zoals is gedefinieerd in EDIFACT-overeenkomst(en) in het gerelateerde TPM API-app-exemplaar


## BizTalk X12
- X12-overeenkomsten uitvoeren zoals is gedefinieerd in het bijbehorende TPM API-app-exemplaar 
- X12-verwerkingsinformatie/-traceringsinformatie beschikbaar maken voor probleemoplossing
- Statusbeheer van batches (starten en stoppen) zoals is gedefinieerd in X12-overeenkomst(en) in het gerelateerde TPM API-app-exemplaar

Zoals eerder is vermeld, is voor de API Apps AS2, X 12 en EDIFACT een TPM API-app vereist die naar verwachting functioneert.


## Aan de slag
Ga als volgt te werk om handelspartnerovereenkomsten te maken:

1. Maak een exemplaar van de connector **BizTalk Trading Partner Management**. Hiervoor hebt u een lege SQL-database nodig. Voordat u aan de slag gaat, moet u ervoor zorgen dat u een lege database hebt en dat deze klaar is voor gebruik.
2. Upload de schema's en certificaten die vereist zijn voor de overeenkomsten. Hiervoor bladert u door het gemaakte TPM-exemplaar en selecteert u het onderdeel Schemas en/of Certificates.
3. Blader naar het gemaakt TPM-exemplaar en selecteer het onderdeel **Partners**.
4. Maak de gewenste partners. Desgewenst kunt u de profielen bewerken en de vereiste identiteiten toevoegen.
5. Gebruik nu het onderdeel **Agreements** om de overeenkomsten te maken. Tijdens het maken van een overeenkomst moet u het protocol selecteren dat moet worden gebruikt. De resterende configuratieopties zijn gebaseerd op het protocol dat u hebt geselecteerd.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 



<!--HONumber=Jun16_HO2-->


