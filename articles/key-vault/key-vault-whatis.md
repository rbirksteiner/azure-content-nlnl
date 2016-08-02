<properties
    pageTitle="Wat is Azure Sleutelkluis? | Microsoft Azure"
    description="Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Klanten kunnen met Azure Sleutelkluis sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels, PFX-bestanden en wachtwoorden) door gebruik te maken van sleutels die worden beveiligd met HSM's."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/06/2016"
    ms.author="cabailey"/>



# Wat is Azure Sleutelkluis?

Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Sleutelkluis](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

## Inleiding

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. U kunt met Sleutelkluis sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels, PFX-bestanden en wachtwoorden) door gebruik te maken van sleutels die worden beveiligd met HSM's. Voor extra zekerheid kunt u de sleutels importeren of genereren in HSM's. Als u hiervoor kiest, verwerkt Microsoft uw sleutels in FIPS 140-2 Level 2-gevalideerde HSM's (hardware en firmware).  

Sleutelkluis stroomlijnt het beheerproces voor sleutels en zorgt dat u de controle houdt over de sleutels waarmee uw gegevens toegankelijk zijn en worden versleuteld. Ontwikkelaars kunnen binnen enkele minuten sleutels voor ontwikkel- en testdoeleinden maken en deze vervolgens probleemloos migreren naar productiesleutels. Beveiligingsadministrator kunnen wanneer dit nodig is machtigingen aan sleutels verlenen (en intrekken).

Gebruik de volgende tabel om beter te begrijpen hoe Sleutelkluis u kan helpen om aan de behoeften van ontwikkelaars en beveiligingsadministrators te voldoen.





| Rol        | Probleemformulering           | Opgelost door Azure Sleutelkluis  |
| ------------- |-------------|-----|
| Ontwikkelaar voor een Azure-toepassing      | "Ik wil een toepassing voor Azure schrijven die voor de ondertekening en versleuteling gebruikmaakt van sleutels. Dit moeten echter wel externe sleutels zijn, zodat de oplossing geschikt is voor een toepassing die geografisch wordt gedistribueerd. <br/><br/>Ik wil ook dat deze sleutels en geheimen worden beveiligd, zonder dat ik zelf code hoef te schrijven. Daarnaast moeten ze eenvoudig vanuit mijn toepassingen kunnen worden gebruikt met optimale prestaties." | √ De sleutels worden opgeslagen in een kluis en wanneer dit nodig is, aangeroepen via een URI.<br/><br/> √ De sleutels worden beveiligd door Azure. Hiervoor wordt gebruikgemaakt van algoritmen, sleutellengten en HSM's die voldoen aan de industriestandaard).<br/><br/> √ Sleutels worden verwerkt in HSM's die zich in de dezelfde Azure-datacenters bevinden als de toepassingen. Dit resulteert in een betere betrouwbaarheid en kortere wachttijd dan wanneer de sleutels zich op een afzonderlijke locatie, bijvoorbeeld on-premises, bevinden.|
| SaaS-ontwikkelaar (Software as a Service)      |"Ik wil niet de verantwoordelijk of potentiële aansprakelijkheid voor de tenantsleutels en -geheimen van mijn klant op mij nemen. <br/><br/>Ik wil dat klanten hun sleutels zelf in eigendom hebben en beheren, zodat ik mij kan concentreren op hetgeen waar ik het beste in ben, namelijk het leveren van de belangrijkste softwarefuncties." | √ Klanten kunnen hun eigen sleutels in Azure importeren en beheren. Wanneer een SaaS-toepassing cryptografiebewerkingen moet uitvoeren met de sleutels van de klant, voert Sleutelkluis deze bewerkingen namens de toepassing uit. De toepassing krijgt de sleutels van de klant niet te zien.|
| Chief Security Officer (CSO) | "Ik wil weten of onze toepassingen voldoen aan de FIPS 140-2 Level 2 HSM's voor beveiligd sleutelbeheer. <br/><br/>Ik wil ervoor zorgen dat mijn organisatie de controle heeft over de levenscyclus van de sleutel en het sleutelgebruik kan controleren. <br/><br/>En hoewel we meerdere Azure-services en -resources gebruiken, wil ik de sleutels kunnen beheren vanaf één locatie in Azure."     |√ HSM's zijn FIPS 140-2 Level 2-gevalideerde modules.<br/><br/>√ Sleutelkluis is zodanig ontworpen dat Microsoft uw sleutels niet kan zien of extraheren.<br/><br/>√ Het sleutelgebruik wordt vrijwel in realtime geregistreerd in een logboek.<br/><br/>√ De kluis biedt één interface, ongeacht het aantal kluizen dat u in Azure hebt, welke regio's ze ondersteunen en door welke toepassingen ze worden gebruikt. |


Iedereen met een Azure-abonnement kan sleutelkluizen maken en gebruiken. Hoewel Sleutelkluis voordelen biedt voor ontwikkelaars en beveiligingsadministrators, kan de service worden geïmplementeerd en beheerd door een beheerder die ook andere Azure-services voor een organisatie beheert. De beheerder kan zich bijvoorbeeld aanmelden met een Azure-abonnement, vervolgens een kluis voor de organisatie maken waarin sleutels worden opgeslagen en zich ontfermen over operationele taken, zoals:

+ Een sleutel of geheim maken of importeren.
+ Een sleutel of geheim intrekken of verwijderen.
+ Gebruikers of toepassingen machtigingen verlenen voor toegang tot de sleutelkluis, zodat ze de sleutels en geheimen in de kluis kunnen beheren of gebruiken.
+ Sleutelgebruik configureren (bijvoorbeeld ondertekenen of versleutelen).
+ Sleutelgebruik bewaken.

De beheerder kan de ontwikkelaars vervolgens voorzien van URI's die kunnen worden aangeroepen vanuit hun toepassingen. Daarnaast kan de beheerder de beveiligingsadministrator logboekregistratiegegevens over het gebruik van de sleutel verstrekken. 

   ![Overzicht van Azure Sleutelkluis][1]

Ontwikkelaars kunnen de sleutels ook rechtstreeks beheren door gebruik te maken van API's. Zie [Ontwikkelaarshandleiding voor Sleutelkluis](key-vault-developers-guide.md) voor meer informatie.

## Volgende stappen

Zie [Aan de slag met Azure Sleutelkluis](key-vault-get-started.md) voor een inleidende zelfstudie voor beheerders.

Zie [Logboekregistratie van Azure Sleutelkluis](key-vault-logging.md) voor meer informatie over de gebruiksregistratie voor Sleutelkluis.

Zie [Over sleutels en geheimen](https://msdn.microsoft.com/library/azure/dn903623.aspx) voor meer informatie over het gebruik van sleutels en geheimen met Azure Sleutelkluis.


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png



<!--HONumber=Jun16_HO2-->


