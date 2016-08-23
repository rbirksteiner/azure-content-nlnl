<properties
    pageTitle="Azure BizTalk Services maken in de Azure Portal | Microsoft Azure"
    description="Meer informatie over het inrichten of maken van Azure BizTalk Services in de Azure Portal; MABS, WABS"
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
    ms.topic="hero-article"
    ms.date="05/16/2016"
    ms.author="mandia"/>



# BizTalk Services maken met de Azure Portal

Azure BizTalk Services maken in de Azure Portal

> [AZURE.TIP] Als u zich wilt aanmelden bij de Azure Portal, moet u een Azure-account en een Azure-abonnement hebben. Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## Een BizTalk Service maken
Afhankelijk van de versie die u kiest, zijn mogelijk niet alle BizTalk Service-instellingen beschikbaar.

1. Meld u aan bij de [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecteer in het onderste navigatievenster **NIEUW**:  
![Selecteer de knop Nieuw][NEWButton]

3. Selecteer **APP SERVICES** > **BIZTALK SERVICE** > **AANGEPAST MAKEN**:  
![Selecteer BizTalk Service en selecteer Aangepast maken][NewBizTalkService]

4. Geef de BizTalk Service-instellingen op:

    <table border="1">
    <tr>
    <td><strong>De naam van de BizTalk Service</strong></td>
    <td>U kunt elke willekeurige naam invoeren, maar houd deze specifiek. Voorbeelden zijn:<br/><br/>
    <em>mijnbedrijf</em>.biztalk.windows.net<br/>
    <em>mijnbedrijfmijntoepassing</em>.biztalk.windows.net<br/>
    <em>mijntoepassing</em>.biztalk.windows.net<br/><br/>'.biztalk.windows.net' wordt automatisch toegevoegd aan de naam die u invoert. Hiermee maakt u een URL die wordt gebruikt voor uw BizTalk Service, bijvoorbeeld <strong>https://<em>mijntoepassing</em>.biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Editie</strong></td>
    <td>Als uw project zich in de test-/ontwikkelingsfase bevindt, kiest u <strong>Developer</strong>. Als uw project zich in de productiefase bevindt, gebruikt u de <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">grafiek van edities van BizTalk Services</a>. Hiermee kunt u bepalen of <strong>Premium</strong>, <strong>Standard</strong> of <strong>Basic</strong> de beste keuze is voor uw bedrijfsscenario.
    </td>
    </tr>
    <tr>
    <td><strong>Regio</strong></td>
    <td>Selecteer de geografische regio waarin u uw BizTalk Service wilt hosten.</td>
    </tr>
    <tr>
    <td><strong>Domein-URL</strong></td>
    <td><strong>Optioneel</strong>. De domein-URL is standaard <em>NaamVanUwBizTalkService</em>.biztalk.windows.net. U kunt ook een aangepast domein invoeren. Als uw domein bijvoorbeeld <em>contoso</em> is, kunt u het volgende opgeven: <br/><br/>
    <em>MijnBedrijf</em>.contoso.com<br/>
    <em>MijnBedrijfMijnToepassing</em>.contoso.com<br/>
    <em>MijnToepassing</em>.contoso.com<br/>
    <em>NaamVanUwBizTalkService</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
Selecteer de pijl VOLGENDE.

5. Voer de opslag- en database-instellingen in:

    <table border="1">
    <tr>
    <td><strong>Opslagaccount voor bewaken/archiveren</strong></td>
    <td>Selecteer een bestaand opslagaccount of maak een nieuw opslagaccount. <br/><br/>Als u een nieuw opslagaccount maakt, voert u de <strong>naam van het opslagaccount</strong> in.</td>
    </tr>
    <tr>
    <td><strong>Traceringsdatabase</strong></td>
    <td>Als u een bestaande Azure SQL Database gebruikt, kan deze niet worden gebruikt door een andere BizTalk Service. U hebt de aanmeldingsgegevens nodig die u hebt ingevoerd bij het maken van de Azure SQL Database-server.<br/><br/><strong>TIP</strong> Maak de traceringsdatabase en het opslagaccount voor bewaken/archiveren in dezelfde regio als de BizTalk Service.</td>
    </tr>
    </table>
Selecteer de pijl VOLGENDE.

6. Voer de database-instellingen in:

    <table border="1">
    <tr>
    <td><strong>Naam</strong></td>
    <td>Beschikbaar als in het vorige scherm <strong>Een nieuw SQL Database-exemplaar</strong> is geselecteerd.
    <br/><br/>
    Voer de naam van de SQL Database in die moet worden gebruikt door uw BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Server</strong></td>
    <td>Beschikbaar als in het vorige scherm <strong>Een nieuw SQL Database-exemplaar</strong> is geselecteerd.
    <br/><br/>
    Selecteer een bestaande SQL Database-server of maak een nieuwe SQL Database-server.</td>
    </tr>
    <tr>
    <td><strong>Aanmeldingsnaam voor server</strong></td>
    <td>Voer de aanmeldingsnaam in.</td>
    </tr>
    <tr>
    <td><strong>Aanmeldingswachtwoord voor server</strong></td>
    <td>Voer het aanmeldingswachtwoord in.</td>
    </tr>
    <tr>
    <td><strong>Regio</strong></td>
    <td>Beschikbaar als <strong>Een nieuw SQL Database-exemplaar</strong> is geselecteerd. Selecteer de geografische regio waarin u uw SQL Database wilt hosten.</td>
    </tr>
    </table>

Klik op het vinkje om de wizard te voltooien. Het voortgangspictogram wordt weergegeven:  
![Het voortgangspictogram wordt weergegeven als u klaar bent][ProgressComplete]

Als u klaar bent, wordt de Azure BizTalk Service gemaakt. Deze kunt u nu gebruiken met uw toepassingen. De standaardinstellingen zijn voldoende. Als u de standaardinstellingen wilt wijzigen, selecteert u **BIZTALK SERVICES** in het navigatiedeelvenster links. Selecteer vervolgens uw BizTalk Service. Er worden extra instellingen weergegeven in de [tabbladen Dashboard, Bewaken en Schalen](biztalk-dashboard-monitor-scale-tabs.md) bovenaan.

Afhankelijk van de status van de BizTalk Service zijn er bepaalde bewerkingen die niet kunnen worden voltooid. Ga naar de [statusgrafiek van BizTalk Services](biztalk-service-state-chart.md) voor een lijst van deze bewerkingen.


## Stappen na de inrichting

-  [Het certificaat installeren op een lokale computer](#InstallCert)
-  [Een certificaat dat gereed is voor productie toevoegen](#AddCert)
-  [De Access Control-naamruimte ophalen](#ACS)

#### <a name="InstallCert"></a>Het certificaat installeren op een lokale computer
Bij het inrichten van de BizTalk Service wordt er een zelfondertekend certificaat gemaakt en gekoppeld aan uw BizTalk Service-abonnement. U moet dit certificaat downloaden en installeren op de computers waarop u BizTalk Service-toepassingen wilt implementeren of waarmee u berichten wilt verzenden naar een BizTalk Service-eindpunt.

1. Meld u aan bij de [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecteer **BIZTALK SERVICES** in het navigatiedeelvenster links. Selecteer vervolgens uw BizTalk Service-abonnement.
3. Selecteer het tabblad **Dashboard**.
4. Selecteer **SSL-certificaat downloaden**:  
![SSL-certificaat wijzigen][QuickGlance]
5. Dubbelklik op het certificaat en doorloop de wizard om het certificaat te installeren. Zorg ervoor dat u het certificaat installeert in het archief van de **vertrouwde basiscertificeringsinstanties**.

#### <a name="AddCert"></a>Een certificaat dat gereed is voor productie toevoegen
Het zelfondertekende certificaat wordt automatisch gemaakt wanneer BizTalk Services wordt gemaakt. Het is alleen bedoeld voor gebruik in ontwikkelomgevingen. Vervang het in productiescenario's door een certificaat dat gereed is voor productie.

1. Selecteer op het tabblad **Dashboard** de optie **SSL-certificaat bijwerken**.
2. Blader naar uw persoonlijke SSL-certificaat (*Certificaatnaam*.pfx) dat de naam van uw BizTalk Service bevat, voer het wachtwoord in en klik op het vinkje.

#### <a name="ACS"></a>De Access Control-naamruimte ophalen

1. Meld u aan bij de [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecteer **BIZTALK SERVICES** in het navigatiedeelvenster links. Selecteer vervolgens uw BizTalk Service.
3. Selecteer in de taakbalk **Verbindingsgegevens**:  
![Verbindingsgegevens selecteren][ACSConnectInfo]

4. Kopieer de Access Control-waarden.

Wanneer u een BizTalk Service-project implementeert vanuit Visual Studio, voert u deze Access Control-naamruimte in. De Access Control-naamruimte wordt automatisch gemaakt voor uw BizTalk Service.

De Access Control-waarden kunnen worden gebruikt met elke gewenste toepassing. Wanneer u Azure BizTalk Services maakt, bepaalt deze Access Control-naamruimte de verificatie met uw BizTalk Service-implementatie. Als u het abonnement wilt wijzigen of de naamruimte wilt beheren, selecteert u **ACTIVE DIRECTORY** in het navigatiedeelvenster links. Selecteer vervolgens de naamruimte. In de taakbalk ziet u de opties.

Als u op **Beheren** klikt, wordt de Access Control-beheerportal geopend. In de Access Control-beheerportal gebruikt de BizTalk Service **service-identiteiten**:  
![De Access Control Service-identiteiten in de Access Control-beheerportal][ACSServiceIdentities]

De Access Control Service-identiteit is een set referenties waarmee toepassingen of clients rechtstreeks via Access Control kunnen verifiëren en een token kunnen ontvangen.

> [AZURE.IMPORTANT] De BizTalk Service gebruikt **Eigenaar** als standaardservice-identiteit en de waarde **Wachtwoord**. Als u de waarde Symmetrische sleutel gebruikt in plaats van de waarde Wachtwoord, kan de volgende fout optreden.<br/><br/>*Kan geen verbinding maken met het Access Control-beheerserviceaccount met de opgegeven referenties*

[Uw ACS-naamruimte beheren](https://msdn.microsoft.com/library/azure/hh674478.aspx) geeft een lijst weer met de volgende richtlijnen en aanbevelingen.

## Uitleg van de vereisten

Deze vereisten zijn niet van toepassing op de editie Free.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Wat u nodig hebt</strong></td>
        <td><strong>Waarom u het nodig hebt</strong></td>
</tr>
<tr>
<td>Azure-abonnement</td>
<td>Het abonnement bepaalt wie zich kan aanmelden bij de Azure Portal. De accounthouder maakt het abonnement via <a HREF="https://account.windowsazure.com/Subscriptions"> Azure-abonnementen</a>.
<br/><br/>
Het Azure-account kan meerdere abonnementen hebben en worden beheerd door iedereen die is gemachtigd. De houder van uw Azure-account kan bijvoorbeeld een abonnement met de naam <em>BizTalkServiceAbonnement</em> maken en de BizTalk-beheerders binnen uw bedrijf (bijvoorbeeld ContosoBTSBeheerders@live.com) toegang geven tot dit abonnement. In dit scenario melden de BizTalk-beheerders zich aan bij de Azure Portal en hebben zij volledige beheerdersrechten voor alle gehoste services in het abonnement, waaronder Azure BizTalk Services. De BizTalk-beheerders zijn niet de houders van het Azure-account en hebben daarom geen toegang tot factureringsgegevens.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577"> Abonnementen en opslagaccounts beheren in de Azure Portal</a> geeft u meer informatie.
</td>
</tr>
<tr>
<td>Azure SQL Database</td>
<td>Slaat de tabellen, weergaven en opgeslagen procedures op die worden gebruikt door de BizTalk Service, inclusief de traceringsgegevens.
<br/><br/>
Wanneer u een BizTalk Service maakt, kunt u een bestaande Azure SQL Server of Azure SQL Database gebruiken. U kunt ook automatisch een nieuwe server of database maken.
<br/><br/>
De schaal van de SQL Database wordt automatisch geconfigureerd. Meestal is de standaardschaal voldoende voor een BizTalk Service. Als u de schaal wijzigt, heeft dit invloed op de prijzen. Zie <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930"> Accounts en facturering in Azure SQL Database</a>
<br/><br/>
<strong>Opmerkingen</strong>
<br/>
<ul>
<li> Wanneer u een nieuwe Azure SQL Server en Database maakt, wordt Azure Services automatisch ingeschakeld. Het is voor de BizTalk Service vereist dat Azure Services is ingeschakeld.</li>
<li>Als u een nieuwe Azure SQL Database maakt op een bestaande Azure SQL Server, worden de firewallregels van de server niet gewijzigd. Als gevolg hiervan hebben andere Azure Services mogelijk geen toegang tot de databases van de server.</li>
</ul>
</td>
</tr>
<tr>
<td>Azure Access Control-naamruimte</td>
<td>Voert de verificatie met Azure BizTalk Services uit. Wanneer u een BizTalk Service-project implementeert vanuit Visual Studio, voert u deze Access Control-naamruimte in. De Access Control-naamruimte wordt automatisch gemaakt wanneer u een BizTalk Service maakt.</td>
</tr>

<tr>
<td>Azure-opslagaccount</td>
<td>Geeft toegang tot tabellen, blobs en wachtrijen die door uw BizTalk Service worden gebruikt om de volgende bestanden op te slaan:

<ul>
<li>Logboekbestanden die de BizTalk Service bewaken. De uitvoer van de bewaking wordt ook weergegeven op het tabblad **Bewaking** in de Azure Portal.</li>
<li>Bij het maken van een X12- of AS2-overeenkomst tussen partners kunt u de functie Archiveren inschakelen om de eigenschappen van berichten op te slaan. Deze gegevens worden opgeslagen in het opslagaccount.</li>
</ul>
<br/>
Wanneer u een BizTalk Service maakt, kunt u een bestaand opslagaccount gebruiken of automatisch een nieuw opslagaccount maken.
<br/><br/>
Meestal zijn de standaardinstellingen voor opslag voldoende voor een BizTalk Service.
<br/><br/>
Wanneer u een opslagaccount maakt, worden automatisch een primaire en secundaire sleutel gemaakt. Deze sleutels beheren de toegang tot uw opslagaccount. De BizTalk Service gebruikt automatisch de primaire sleutel.
<br/><br/>
Zie <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">Opslag</a> voor meer informatie.
</td>
</tr>

<tr>
<td>Persoonlijk SSL-certificaat</td>
<td>
Wanneer er een Azure BizTalk Service wordt gemaakt, wordt er ook een HTTPS-URL met de naam van uw BizTalk Service gemaakt. Deze URL wordt automatisch geconfigureerd voor het gebruik van een zelfondertekend certificaat dat alleen geschikt is voor ontwikkelingsdoeleinden. Voor de productie hebt u een persoonlijk SSL-certificaat nodig.
<br/><br/>
<strong>Belangrijke informatie over SSL-certificaten</strong>

<ul>
<li>De vervaldatum van het certificaat moet korter zijn dan 5 jaar.</li>
<li>Voor alle persoonlijke certificaten is een wachtwoord vereist. Onthoud dit wachtwoord. U doet er verstandig aan het wachtwoord te delen met uw beheerders.</li>
<li>Zelfondertekende certificaten worden gebruikt in een test-/ontwikkelingsomgeving. Wanneer u zelfondertekende certificaten gebruikt, importeert u het certificaat in uw persoonlijke certificaatarchief en in het certificaatarchief van vertrouwde basiscertificeringsinstanties.</li>
</ul>
<br/>Geef bij het verzenden van de aanvraag voor een productiecertificaat naar uw certificeringsinstantie (CA) de volgende certificaateigenschappen op:
<br/>

<ul>
<li><strong>Verbeterd sleutelgebruik</strong>: voor Azure BizTalk Services is minimaal serververificatie vereist.</li>
<li><strong>Algemene naam</strong>: voer de FQDN (Fully Qualified Domain Name) van uw Azure BizTalk Service-URL in. Zie <a HREF="#BizTalk">Een BizTalk Service maken</a> in dit artikel.</li>
</ul>
<br/>
Er kan een nieuw of ander certificaat worden toegevoegd nadat de BizTalk Service is gemaakt.
</td>
</tr>
</table>



## Hybride verbindingen

Wanneer u een Azure BizTalk Service maakt, is het tabblad **Hybride verbindingen** beschikbaar:

![Tabblad Hybride verbindingen][HybridConnectionTab]

Hybride verbindingen worden gebruikt om verbinding te maken tussen een Azure-website of mobiele service van Azure en een on-premises resource die gebruikmaakt van een statische TCP-poort, zoals SQL Server, MySQL, HTTP-web-API's, Mobile Services en de meeste aangepaste webservices.  Hybride verbindingen en de BizTalk Adapter Service zijn twee verschillende zaken. De BizTalk Adapter Service wordt gebruikt om verbinding te maken tussen Azure BizTalk Services en een on-premises LOB-systeem (Line of Business).

 Zie [Hybride verbindingen](integration-hybrid-connection-overview.md) voor meer informatie, waaronder het maken en beheren van hybride verbindingen.


## Volgende stappen

Nu u een BizTalk Service hebt gemaakt, is het tijd om uzelf bekend te maken met [de tabbladen Dashboard, Bewaken en Schalen van BizTalk Services](biztalk-dashboard-monitor-scale-tabs.md). U kunt nu toepassingen maken met uw BizTalk Service. Ga naar [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197) om te beginnen met het maken van toepassingen.

## Zie ook
- [BizTalk Services: grafiek van edities](biztalk-editions-feature-chart.md)<br/>
- [BizTalk Services: statusgrafiek](biztalk-service-state-chart.md)<br/>
- [BizTalk Services: back-ups maken en herstellen](biztalk-backup-restore.md)<br/>
- [BizTalk Services: beperking](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services: naam en sleutel van verlener](biztalk-issuer-name-issuer-key.md)<br/>
- [De Azure BizTalk Services SDK gaan gebruiken](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Hybride verbindingen](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png



<!--HONumber=Jun16_HO2-->


