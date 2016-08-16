<properties
    pageTitle="Meer informatie over de functies in de edities van BizTalk Services | Microsoft Azure"
    description="Vergelijk de mogelijkheden van de edities van BizTalk Services: Free, Developer, Basic, Standard en Premium. MABS, WABS."
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
    ms.date="05/16/2016"
    ms.author="mandia"/>


# BizTalk Services: grafiek van edities

Azure BizTalk Services is beschikbaar in verschillende edities. Gebruik dit artikel om te bepalen welke editie geschikt is voor uw scenario en zakelijke behoeften.


## De edities vergelijken

**Free (voorbeeld)**

Kan hybride verbindingen maken en beheren. Een hybride verbinding is een eenvoudige manier om een Azure-website verbinding te laten maken met een on-premises systeem, zoals SQL Server.

**Developer**

Biedt hybride verbindingen, EAI- en EDI-berichtverwerking met een eenvoudig te gebruiken beheerportal voor handelspartners, ondersteuning voor algemene EDI-schema's en uitgebreide EDI-bewerking via X12 en AS2. U kunt algemene EAI-scenario's maken om verbinding te maken tussen services in de cloud en alle HTTP/S-, REST-, FTP-, WCF- en SFTP-protocollen om berichten te lezen en te schrijven.  U kunt gebruikmaken van de verbinding tussen on-premises LOB-systemen en kant-en-klare SAP-, Oracle eBusiness- Oracle DB-, Siebel- en SQL Server-adapters. Gebruik een op ontwikkelaars gerichte omgeving met Visual Studio-hulpprogramma’s, zodat u op een eenvoudige manier kunt ontwikkelen en implementeren. Alleen geschikt voor ontwikkelings- en testdoeleinden zonder Service Level Agreement (SLA).

**Basic**

Bevat het merendeel van de mogelijkheden van de editie Developer met verbeterde hybride verbindingen, EAI-bruggen, EDI-overeenkomsten en BizTalk Adapter Pack-verbindingen. Biedt ook hoge beschikbaarheid en de mogelijkheid om te schalen met een Service Level Agreement (SLA).

**Standard**

Bevat alle mogelijkheden van de editie Basic met verbeterde hybride verbindingen, EAI-bruggen, EDI-overeenkomsten en BizTalk Adapter Pack-verbindingen. Biedt ook hoge beschikbaarheid en de mogelijkheid om te schalen met een Service Level Agreement (SLA).

**Premium**

Bevat alle mogelijkheden van de editie Standard met verbeterde hybride verbindingen, EAI-bruggen, EDI-overeenkomsten en BizTalk Adapter Pack-verbindingen. Biedt ook archivering, hoge beschikbaarheid en de mogelijkheid om te schalen met een Service Level Agreement (SLA).


## Grafiek van edities
In de volgende tabel kunt u de verschillen zien.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free (voorbeeld)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Beginprijs</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Prijzen van Azure BizTalk Services</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full"> Azure-prijscalculator</a></td>
</tr>
<tr>
<td><strong>Minimale standaardconfiguratie</strong></td>
<td>1 Free-eenheid</td>
<td>1 Developer-eenheid</td>
<td>1 Basic-eenheid</td>
<td>1 Standard-eenheid</td>
<td>1 Premium-eenheid</td>
</tr>
<tr>
<td><strong>Schalen</strong></td>
<td>Schalen niet mogelijk</td>
<td>Schalen niet mogelijk</td>
<td>Ja, in stappen van 1 Basic-eenheid</td>
<td>Ja, in stappen van 1 Standard-eenheid</td>
<td>Ja, in stappen van 1 Premium-eenheid</td>
</tr>
<tr>
<td><strong>Maximaal toegestane uitschaling</strong></td>
<td>Schalen niet mogelijk</td>
<td>Schalen niet mogelijk</td>
<td>Maximaal 8 eenheden</td>
<td>Maximaal 8 eenheden</td>
<td>Maximaal 8 eenheden</td>
</tr>
<tr>
<td><strong>EAI-bruggen per eenheid</strong></td>
<td>Niet inbegrepen</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclusief TPM-overeenkomsten</td>
<td>Niet inbegrepen</td>
<td>Inbegrepen. 10 overeenkomsten per eenheid.</td>
<td>Inbegrepen. 50 overeenkomsten per eenheid.</td>
<td>Inbegrepen. 250 overeenkomsten per eenheid.</td>
<td>Inbegrepen. 1000 overeenkomsten per eenheid.</td>
</tr>
<tr>
<td><strong>Hybride verbindingen per eenheid</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Gegevensoverdracht in hybride verbindingen (GB) per eenheid</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>BizTalk Adapter Service-verbindingen met on-premises LOB-systemen</strong></td>
<td>Niet inbegrepen</td>
<td>1 verbinding</td>
<td>2 verbindingen</td>
<td>5 verbindingen</td>
<td>25 verbindingen</td>
</tr>
<tr>
<td align="left"><strong>Ondersteunde protocollen/systemen:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure Blob</li>
<li>REST-API’s</li>
</ul>
</td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
</tr>
<tr>
<td><strong>Hoge beschikbaarheid</strong>
<br/><br/>
Voor Service Level Agreements (SLA) raadpleegt u <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Prijzen van Azure BizTalk Services</a>.
</td>
<td>Niet inbegrepen</td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
</tr>
<tr>
<td><strong>Back-ups en herstellen</strong></td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
</tr>
<tr>
<td><strong>Tracering</strong></td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
</tr>
<tr>
<td><strong>Archiveren</strong><br/><br/>
Inclusief niet-afwijzing van ontvangst (NRR) en downloaden van getraceerde berichten</td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Niet inbegrepen</td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
</tr>
<tr>
<td><strong>Gebruik van aangepaste code</strong></td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
</tr>
<tr>
<td><strong>Gebruik van transformaties, waaronder aangepaste XSLT</strong></td>
<td>Niet inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
<td>Inbegrepen</td>
</tr>
</table>

> [AZURE.NOTE] Voor tolerantie tegen hardwarestoringen en hoge beschikbaarheid moet één BizTalk-eenheid meerdere virtuele machines bevatten.


## Veelgestelde vragen

#### Wat is een BizTalk-eenheid?
Een 'eenheid' is de kern van een Azure BizTalk Services-implementatie. Elke editie wordt geleverd met een eenheid met een andere rekencapaciteit en hoeveelheid geheugen. Een Basic-eenheid heeft bijvoorbeeld meer rekencapaciteit dan een Developer-eenheid, een Standard-eenheid heeft meer rekencapaciteit dan een Basic-eenheid, enzovoort. Wanneer u een BizTalk Service schaalt, schaalt u de eenheden.

#### Wat is het verschil tussen BizTalk Services en virtuele machines van Azure BizTalk?
BizTalk Services biedt een echte PaaS-architectuur (Platform-as-a-Service) voor het bouwen van integratieoplossingen in de cloud. Met het PaaS-model richt u zich volledig op de toepassingslogica en laat u het volledige beheer van de infrastructuur over aan Microsoft. Hierbij profiteert u van de volgende zaken:

- U hoeft geen virtuele machines te beheren of te patchen.
- Microsoft zorgt voor de beschikbaarheid.
- U bepaalt zelf wanneer u schaalt door meer of minder capaciteit aan te vragen via de Azure Portal.

BizTalk Server op virtuele machines van Azure biedt een IaaS-architectuur (Infrastructure-as-a-Service). U maakt virtuele machines en configureert ze, precies zoals u doet in uw on-premises omgeving. Hierdoor is het eenvoudiger om bestaande toepassingen uit te voeren in de cloud, zonder dat u code hoeft te wijzigen. Met IaaS bent u nog steeds zelf verantwoordelijk voor het configureren en beheren van de virtuele machines (u moet bijvoorbeeld patches voor software en het besturingssysteem installeren) en voor het ontwerpen van de toepassing voor hoge beschikbaarheid.

Als u nieuwe integratieoplossingen wilt bouwen waarbij u zo weinig mogelijk tijd kwijt bent met het beheer van de infrastructuur, gebruikt u BizTalk Services. Als u uw bestaande BizTalk-oplossingen snel wilt migreren of zoekt naar een omgeving op aanvraag waarin u BizTalk Server-toepassingen kunt ontwikkelen en testen, gebruikt u BizTalk Server op een virtuele machine van Azure.

#### Wat is het verschil tussen BizTalk Adapter Service en hybride verbindingen?
De BizTalk Adapter Service wordt gebruikt door een Azure BizTalk Service. De BizTalk Adapter Service gebruikt het BizTalk Adapter Pack om verbinding te maken met een on-premises LOB-systeem (Line of Business). Een hybride verbinding biedt een eenvoudige en handige manier om verbinding maken tussen Azure-toepassingen, zoals de functie Web Apps in Azure App Service en Azure Mobile Services, en een on-premises resource.

#### Wat houdt 'Gegevensoverdracht in hybride verbindingen (GB) per eenheid' in? Is dit per minuut/uur/dag/week/maand? Wat gebeurt er wanneer de limiet is bereikt?

De kosten voor een hybride verbinding per eenheid zijn afhankelijk van de editie van BizTalk Services. Met andere woorden: de kosten zijn afhankelijk van hoeveel gegevens u overdraagt. Als u bijvoorbeeld dagelijks 10 GB aan gegevens overdraagt, kost dit minder dan wanneer u dagelijks 100 GB overdraagt. Gebruik de [Prijscalculator](https://azure.microsoft.com/pricing/calculator/?scenario=full) voor BizTalk Services om de specifieke kosten te bepalen. Doorgaans worden de limieten dagelijks afgedwongen. Als u de limiet overschrijdt, wordt er $ 1 per extra GB in rekening gebracht.

#### Waarom gaat het aantal bruggen met twee omhoog in plaats van slechts één wanneer ik een overeenkomst maak in BizTalk Services? 

Elke overeenkomst bestaat uit twee verschillende bruggen: een communicatiebrug aan de verzendkant en een communicatiebrug aan de ontvangstkant.

####  Wat gebeurt er wanneer ik de quotumlimiet voor het aantal bruggen of overeenkomsten bereik?

Dan kunt u geen nieuwe bruggen implementeren en geen nieuwe overeenkomsten maken. Als u meer wilt implementeren, moet u opschalen naar meer eenheden voor de BizTalk Service of upgraden naar een hogere editie.

#### Hoe migreer ik van één BizTalk Services-laag naar een andere?

De editie Free kan niet worden gemigreerd of 'opgeschaald' naar een andere laag. Bovendien kunt u geen back-ups maken en herstellen naar een andere laag. Als u een andere laag nodig hebt, maakt u een nieuwe BizTalk Service met behulp van de nieuwe laag. Alle artefacten die zijn gemaakt met de editie Free, inclusief hybride verbindingen, moeten opnieuw worden gemaakt in de nieuwe BizTalk Service. 

In de andere edities kunt u back-ups maken en deze herstellen om uw artefacten te migreren van één laag naar een andere. U kunt bijvoorbeeld een back-up maken van uw artefacten in de laag Standard en deze herstellen in de laag Premium. In [BizTalk Services: back-ups maken en herstellen](biztalk-backup-restore.md) worden de ondersteunde migratiepaden beschreven en ziet u van welke artefacten back-ups worden gemaakt. Houd er rekening mee dat er geen back-ups worden gemaakt van hybride verbindingen. Nadat u uw back-ups hebt hersteld in een nieuwe laag, kunt u de hybride verbindingen opnieuw maken.  


#### Maakt de BizTalk Adapter Service deel uit van de service? Hoe ontvang ik de software?

Ja, de BizTalk Adapter Service en het BizTalk Adapter Pack zijn opgenomen in de Azure BizTalk Services SDK-[download](http://www.microsoft.com/download/details.aspx?id=39087).

## Volgende stappen

Als u Azure BizTalk Services wilt maken in de Azure Portal, gaat u naar [BizTalk Services: inrichten met de Azure Portal](biztalk-provision-services.md). Ga naar [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197) om te beginnen met het maken van toepassingen.

## Aanvullende resources
- [BizTalk Services: inrichten met de Azure Portal](biztalk-provision-services.md)<br/>
- [BizTalk Services: statusgrafiek voor de inrichting](biztalk-service-state-chart.md)<br/>
- [BizTalk Services: de tabbladen Dashboard, Bewaken en Schalen](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk Services: back-ups maken en herstellen](biztalk-backup-restore.md)<br/>
- [BizTalk Services: beperking](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services: naam en sleutel van verlener](biztalk-issuer-name-issuer-key.md)<br/>
- [De Azure BizTalk Services SDK gaan gebruiken](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>



<!--HONumber=Jun16_HO2-->


