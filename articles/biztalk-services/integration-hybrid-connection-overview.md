<properties
    pageTitle="Overzicht van hybride verbindingen | Microsoft Azure"
    description="Meer informatie over hybride verbindingen, beveiliging, TCP-poorten en ondersteunde configuraties. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/10/2016"
    ms.author="mandia"/>


# Overzicht van hybride verbindingen
Inleiding tot hybride verbindingen met een lijst met de ondersteunde configuraties en een lijst met de vereiste TCP-poorten.


## Wat is een hybride verbinding?

Hybride verbindingen zijn een functie van Azure BizTalk Services. Hybride verbindingen zijn een eenvoudige en handige manier om de functie Web Apps in Azure App Service (voorheen Websites) en de functie Mobile Apps in Azure App Service (voorheen Mobile Services) te verbinden met on-premises resources achter de firewall.

![Hybride verbindingen][HCImage]

De voordelen van hybride verbindingen zijn onder andere:

- Web-apps en mobiele apps hebben beveiligde toegang tot bestaande on-premises gegevens en services.
- Meerdere web-apps of mobiele apps kunnen een hybride verbinding voor toegang tot een on-premises resource delen.
- Voor toegang tot uw netwerk is een minimaal aantal TCP-poorten vereist.
- Toepassingen die gebruikmaken van hybride verbindingen, krijgen alleen toegang tot de specifieke on-premises resource die via de hybride verbinding is gepubliceerd.
- Er kan verbinding worden gemaakt met een on-premises resource die gebruikmaakt van een statische TCP-poort, zoals SQL Server, MySQL, HTTP-web-API's en de meeste aangepaste webservices.

    > [AZURE.NOTE] TCP-services die gebruikmaken van dynamische poorten (zoals de passieve modus van FTP of de uitgebreide passieve modus), worden momenteel niet ondersteund. LDAP wordt evenmin ondersteund. LDAP gebruikt een statische TCP-poort, maar kan ook UDP gebruiken. Als gevolg hiervan wordt LDAP niet ondersteund.

- Geschikt voor gebruik met alle frameworks die worden ondersteund door web-apps (.NET, PHP, Java, Python, Node.js) en mobiele apps (Node.js, .NET).
- Web-apps en mobiele apps hebben op dezelfde manier toegang tot on-premises resources als in uw lokale netwerk. U kunt bijvoorbeeld de verbindingsreeks die u on-premises gebruikt, ook in Azure gebruiken.


Daarnaast bieden hybride verbindingen beheerders in een onderneming controle op en inzicht in de bedrijfsresources waartoe de hybride toepassingen toegang hebben. Ze hebben de volgende opties tot hun beschikking:

- Met instellingen voor groepsbeleid kunnen beheerders hybride verbindingen toestaan op het netwerk. Daarnaast kunnen ze resources opgeven waartoe hybride toepassingen toegang hebben.
- Gebeurtenis- en auditlogboeken op het bedrijfsnetwerk bieden inzicht in de resources waartoe hybride verbindingen toegang hebben.


## Voorbeeldscenario 's

Hybride verbindingen ondersteunen de volgende combinaties van framework en toepassing:

- .NET Framework-toegang tot SQL Server
- .NET Framework-toegang tot HTTP-/HTTPS-services met WebClient
- PHP-toegang tot SQL Server, MySQL
- Java-toegang tot SQL Server, MySQL en Oracle
- Java-toegang tot HTTP-/HTTPS-services

Wanneer u hybride verbindingen gebruikt om toegang te krijgen tot on-premises SQL Server, houd dan rekening met het volgende:

- Benoemde exemplaren van SQL Express moeten worden geconfigureerd voor het gebruik van statische poorten. Standaard gebruiken benoemde exemplaren van SQL Express dynamische poorten.
- Benoemde exemplaren van SQL Express gebruiken een statische poort, maar TCP moet zijn ingeschakeld. TCP is standaard niet ingeschakeld.
- Wanneer u clusters of beschikbaarheidsgroepen gebruikt, wordt de modus `MultiSubnetFailover=true` momenteel niet ondersteund.
- De `ApplicationIntent=ReadOnly` wordt momenteel niet ondersteund.
- Mogelijk is SQL-verificatie vereist als end-to-end-autorisatiemethode die wordt ondersteund door de Azure-toepassing en de on-premises SQL Server.


## Beveiliging en poorten

Hybride verbindingen gebruiken autorisatie via SAS (Shared Access Signature) voor de beveiliging van de verbindingen van de Azure-toepassingen en het on-premises hybride verbindingsbeheer met de hybride verbinding. Er worden aparte verbindingssleutels gemaakt voor de toepassing en het on-premises hybride verbindingsbeheer. Deze verbindingssleutels kunnen onafhankelijk van elkaar worden geactiveerd en ingetrokken.

Hybride verbindingen zorgen voor een naadloze en veilige distributie van de sleutels naar de toepassingen en het on-premises hybride verbindingsbeheer.

Zie [Hybride verbindingen maken en beheren](integration-hybrid-connection-create-manage.md).

*De autorisatie van toepassingen verloopt gescheiden van de hybride verbinding*. U kunt elke willekeurige geschikte autorisatiemethode gebruiken. De autorisatiemethode is afhankelijk van de end-to-end-autorisatiemethoden die worden ondersteund in de Azure-cloud en de on-premises onderdelen. Stel dat uw Azure-toepassing toegang heeft tot een on-premises SQL Server. In dit scenario is SQL-autorisatie mogelijk de autorisatiemethode die end-to-end wordt ondersteund.

#### TCP-poorten
Hybride verbindingen vereisen alleen een uitgaande TCP- of HTTP-verbinding vanuit uw privénetwerk. U hoeft geen firewallpoorten te openen of de configuratie van uw netwerkperimeters te wijzigen om binnenkomende verbindingen in uw netwerk mogelijk te maken.

De volgende TCP-poorten worden gebruikt door hybride verbindingen:

Poort | Waarom u het nodig hebt
--- | ---
9350 - 9354 | Deze poorten worden gebruikt voor gegevensoverdracht. De Service Bus Relay-manager test poort 9350 om te bepalen of de TCP-verbinding beschikbaar is. Als deze beschikbaar is, wordt ervan uitgegaan dat ook poort 9352 beschikbaar is. Het gegevensverkeer wordt via poort 9352 geleid. <br/><br/>Sta uitgaande verbindingen via deze poorten toe.
5671 | Wanneer poort 9352 wordt gebruikt voor gegevensverkeer, wordt poort 5671 gebruikt als besturingskanaal. <br/><br/>Sta uitgaande verbindingen via deze poort toe.
80, 443 | Deze poorten worden gebruikt voor bepaalde gegevensaanvragen naar Azure. Als de poorten 9352 en 5671 niet beschikbaar zijn, worden *de poorten 80 en 443* gebruikt als alternatieve poorten voor de gegevensoverdracht en het besturingskanaal.<br/><br/>Sta uitgaande verbindingen via deze poorten toe. <br/><br/>**Opmerking** Het wordt afgeraden om deze poorten als alternatieve poorten te gebruiken in plaats van de TCP-poorten. In plaats van het systeemeigen TCP voor gegevenskanalen wordt als protocol namelijk HTTP/WebSocket gebruikt. Dit kan leiden tot lagere prestaties.



## Volgende stappen

[Hybride verbindingen maken en beheren](integration-hybrid-connection-create-manage.md)<br/>
[Verbinding maken tussen een Azure-website en een on-premises resource](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Verbinding maken met de on-premises SQL Server vanuit een Azure-web-app](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>
[Azure Mobile Services en hybride verbindingen](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md)


## Zie ook

[REST-API voor het beheren van BizTalk Services op Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[BizTalk Services: grafiek van edities](biztalk-editions-feature-chart.md)<br/>
[Een BizTalk Service maken met de Azure Portal](biztalk-provision-services.md)<br/>
[BizTalk Services: de tabbladen Dashboard, Bewaken en Schalen](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png



<!--HONumber=Jun16_HO2-->


