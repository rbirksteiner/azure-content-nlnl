<properties
    pageTitle="Azure Cool Storage voor Blobs | Microsoft Azure"
    description="Opslaglagen voor Blob Storage bieden voordelige opslag voor objectgegevens op basis van toegangspatronen. Azure Cool Storage is geoptimaliseerd voor gegevens die minder regelmatig worden geopend."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Azure Blob Storage: Hot Storage-laag en Cool Storage-laag

## Overzicht

Azure Storage biedt nu twee opslaglagen voor Blob Storage (objectopslag), zodat u gegevens zeer voordelig kunt opslaan afhankelijk van hoe u deze gebruikt. De Azure **Hot Storage-laag** is geoptimaliseerd voor het opslaan van gegevens die regelmatig worden geopend. De Azure **Cool Storage-laag** is geoptimaliseerd voor het opslaan van gegevens die niet regelmatig worden geopend en een lange levensduur hebben. Voor gegevens in de Cool Storage-laag is een iets lagere beschikbaarheid toegestaan, maar ze vereisen nog steeds een hoge duurzaamheid en een gelijke tijdsduur voor toegang en doorvoer als gegevens in de Hot Storage-laag. Voor gegevens in de Cool Storage-laag zijn een SLA met een iets lagere beschikbaarheid en hogere toegangskosten aanvaardbaar vanwege de veel lagere opslagkosten.

Het aantal gegevens dat is opgeslagen in de cloud, groeit vandaag de dag exponentieel. Om de kosten voor uw groeiende opslagbehoeften te beheren, is het nuttig de gegevens te ordenen op basis van kenmerken als toegangsfrequentie en geplande bewaarperiode. Er bestaan grote verschillen in de manier waarop gegevens die in de cloud zijn opgeslagen, tijdens hun levensduur worden gegenereerd, benaderd en verwerkt. Sommige gegevens worden tijdens hun hele levensduur actief geopend en gewijzigd. Andere gegevens worden in het begin van hun levensduur zeer regelmatig geopend, terwijl dit naarmate de tijd verstrijkt, aanzienlijk minder vaak gebeurt. Weer andere gegevens in de cloud zijn inactief en worden, als ze eenmaal zijn opgeslagen, zelden tot nooit geopend.

Het is nuttig om voor elk van deze scenario‘s voor toegang tot gegevens die hierboven worden beschreven, een gedifferentieerde opslaglaag te maken die is geoptimaliseerd voor een specifiek toegangspatroon. Dankzij de introductie van de Hot Storage- en Cool Storage-toegangslagen in Azure Blob Storage wordt voorzien in deze behoefte aan gedifferentieerde opslaglagen met afzonderlijke prijsmodellen.

## Blob Storage-accounts

**Blob Storage-accounts** zijn gespecialiseerde opslagaccounts voor het opslaan van ongestructureerde gegevens als blobs (objecten) in Azure Storage. Met Blob Storage-accounts kunt u nu kiezen tussen Hot Storage- en Cool Storage-toegangslagen. Zo kunt u minder regelmatig geopende gegevens opslaan tegen lagere opslagkosten en regelmatiger geopende gegevens tegen lagere toegangskosten. Blob Storage-accounts zijn vergelijkbaar met de bestaande opslagaccounts voor algemeen gebruik en bieden dezelfde hoogwaardige kenmerken op het gebied van duurzaamheid, beschikbaarheid, schaalbaarheid en prestaties waarover u nu al beschikt, inclusief 100 procent API-consistentie voor blok-blobs en toevoeg-blobs.

> [AZURE.NOTE] Blob Storage-accounts ondersteunen alleen blok-blobs en toevoeg-blobs. Pagina-blobs worden niet ondersteund.

Blob Storage-accounts maken het kenmerk **Toegangslaag** beschikbaar. Dit stelt u in staat om de opslaglaag te specificeren als **Hot** of **Cool**, afhankelijk van de gegevens die in het account zijn opgeslagen. Als er een wijziging optreedt in het gebruikspatroon van de gegevens, kunt u op elk gewenst moment schakelen tussen deze toegangslagen.

> [AZURE.NOTE] Aan het wijzigen van de toegangslaag kunnen extra kosten zijn verbonden. Zie de sectie [Prijzen en facturering](storage-blob-storage-tiers.md#pricing-and-billing) voor meer informatie.

Enkele voorbeelden van gebruiksscenario's voor de Hot Storage-toegangslaag:

- Gegevens die actief worden gebruikt of waarvan wordt verwacht dat ze regelmatig worden geopend (lees- en schrijfbewerkingen).
- Gegevens die tijdelijk worden opgeslagen voor verwerking en uiteindelijk voor migratie naar de Cool Storage-laag.

Enkele voorbeelden van gebruiksscenario's voor de Cool Storage-toegangslaag:

- Gegevenssets waarvan een back-up is gemaakt, die zijn gearchiveerd of die na een noodgeval zijn hersteld.
- Oudere media-inhoud die niet meer regelmatig wordt bekeken, maar onmiddellijk beschikbaar moet zijn wanneer deze wordt geopend.
- Grote gegevenssets die voordelig moeten worden opgeslagen, terwijl er meer gegevens worden verzameld voor toekomstige verwerking. (*bijvoorbeeld*: langetermijnopslag van wetenschappelijke gegevens, onbewerkte telemetriegegevens van een fabrikant)
- Oorspronkelijke (onbewerkte) gegevens die moeten worden bewaard, zelfs nadat deze zijn verwerkt tot hun uiteindelijke, bruikbare vorm. (*bijvoorbeeld*: onbewerkte mediabestanden na transcodering naar andere indelingen)
- Compliance- en archiveringsgegevens die gedurende lange tijd moeten worden opgeslagen en bijna nooit worden geopend. (*bijvoorbeeld*: beeldmateriaal van beveiligingscamera‘s, oude röntgenfoto‘s/MRI‘s in ziekenhuizen, geluidsopnamen en transcripties van verkoopgesprekken voor financiële diensten)

Zie [Over Azure Storage-accounts](storage-create-storage-account.md) voor meer informatie over opslagaccounts.

Voor toepassingen waarvoor alleen de opslag van blok- of toevoeg-blobs is vereist, wordt aangeraden gebruik te maken van Blob Storage-accounts. Zo profiteert u maximaal van het gedifferentieerde prijsmodel voor gelaagde opslag. We begrijpen dat dit onder bepaalde omstandigheden, waarbij opslagaccounts voor algemeen gebruik beter werken, wellicht niet mogelijk is. Bijvoorbeeld:

- U maakt gebruik van tabellen, wachtrijen of bestanden en wilt de blobs opslaan in hetzelfde opslagaccount. Let op: opslaan van deze items in hetzelfde account biedt geen technisch voordeel behalve dezelfde gedeelde sleutels.
- U moet nog steeds gebruikmaken van het klassieke implementatiemodel. Blob Storage-accounts zijn alleen beschikbaar via het Azure Resource Manager-implementatiemodel.
- U moet gebruikmaken van pagina-blobs. Blob Storage-accounts bieden geen ondersteuning voor pagina-blobs. Over het algemeen raden we het gebruik van blok-blobs aan, tenzij u specifiek behoefte hebt aan pagina-blobs.
- U gebruikt een versie van de [REST API voor Storage Services](https://msdn.microsoft.com/library/azure/dd894041.aspx) die ouder is dan 2014-02-14 of een clientbibliotheek met een lagere versie dan 4.x en u uw toepassing niet kunt upgraden.

> [AZURE.NOTE] Blob Storage-accounts worden momenteel ondersteund in de meeste Azure-regio‘s. Verdere uitbreiding is gepland. Ga naar de pagina [Azure-services per regio](https://azure.microsoft.com/regions/#services) voor de bijgewerkte lijst met beschikbare regio‘s.

## Vergelijking tussen de toegangslagen

De volgende tabel maakt de verschillen tussen de twee toegangslagen inzichtelijk:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Hot Storage-toegangslaag</center></strong></td>
    <td><strong><center>Cool Storage-toegangslaag</center></strong></td
</tr>
<tr>
    <td><strong><center>Beschikbaarheid</center></strong></td>
    <td><center>99,9%</center></td>
    <td><center>99%</center></td>
</tr>
<tr>
    <td><strong><center>Beschikbaarheid<br>(RA-GRS-leesbewerkingen)</center></strong></td>
    <td><center>99,99%</center></td>
    <td><center>99,9%</center></td>
</tr>
<tr>
    <td><strong><center>Gebruikskosten</center></strong></td>
    <td><center>Hogere opslagkosten<br>Lagere toegangs- en transactiekosten</center></td>
    <td><center>Lagere opslagkosten<br>Hogere toegangs- en transactiekosten</center></td>
</tr>
<tr>
    <td><strong><center>Minimale objectgrootte<center></strong></td>
    <td colspan="2"><center>N.v.t.</center></td>
</tr>
<tr>
    <td><strong><center>Minimale opslagduur<center></strong></td>
    <td colspan="2"><center>N.v.t.</center></td>
</tr>
<tr>
    <td><strong><center>Latentie<br>(Tijd tot eerste byte)<center></strong></td>
    <td colspan="2"><center>milliseconden</center></td>
</tr>
<tr>
    <td><strong><center>Schaalbaarheids- en prestatiedoelen<center></strong></td>
    <td colspan="2"><center>Dezelfde als bij opslagaccounts voor algemeen gebruik</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Blob Storage-accounts bieden ondersteuning voor dezelfde schaalbaarheids- en prestatiedoelen als opslagaccounts voor algemeen gebruik. Zie [Schaalbaarheids- en prestatiedoelen in Azure Storage](storage-scalability-targets.md) voor meer informatie.

## Prijzen en facturering

Blob Storage-accounts maken gebruik van een nieuw prijsmodel voor het opslaan van blobs op basis van de toegangslaag. Als u een Blob Storage-account gebruikt, zijn de volgende factureringsvoorwaarden van toepassing:

- **Opslagkosten**: de kosten voor het opslaan van gegevens hangen niet alleen af van de hoeveelheid opgeslagen gegevens, maar ook van de gebruikte toegangslaag. De kosten per GB voor de Cool Storage-toegangslaag zijn lager dan die voor de Hot Storage-toegangslaag.
- **Kosten voor gegevenstoegang**: voor gegevens in de Cool Storage-toegangslaag worden kosten in rekening gebracht per GB aan gegevenstoegang voor lees- en schrijfbewerkingen.
- **Transactiekosten**: voor beide lagen worden kosten in rekening gebracht per transactie. De kosten per transactie zijn voor de Cool Storage-toegangslaag echter hoger dan voor de Hot Storage-toegangslaag.
- **Kosten voor gegevensoverdracht met geo-replicatie**: dit is alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.
- **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.
- **De toegangslaag wijzigen**: als u de toegangslaag wijzigt van Cool naar Hot, worden voor elke overgang kosten in rekening gebracht die overeenkomen met de kosten voor het lezen van alle bestaande gegevens in het opslagaccount. Het wijzigen van de toegangslaag van Hot naar Cool is echter gratis.

> [AZURE.NOTE] De kosten voor het wijzigen van de toegangslaag van Cool naar Hot zijn tot 30 juni 2016 niet van toepassing. Dit is om gebruikers in staat te stellen de nieuwe opslaglagen uit te proberen en de functionaliteit na het op de markt brengen van het product te valideren. Vanaf 1 juli 2016 zijn er kosten van toepassing op alle overgangen van Cool naar Hot. Ga naar de pagina [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie over het prijsmodel voor Blob Storage-accounts. Ga naar de pagina [Prijsinformatie voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/) voor meer informatie over de kosten voor uitgaande gegevensoverdracht.

## Snel starten

In deze sectie worden de volgende scenario‘s toegelicht, waarbij gebruik wordt gemaakt van de Azure Portal:

- Het maken van een Blob Storage-account.
- Het beheren van een Blob Storage-account.

### De Azure Portal gebruiken

#### Een Blob Storage-account maken met behulp van de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Selecteer in het menu Hub achtereenvolgens **Nieuw** -> **Gegevens en opslag** -> **Opslagaccount**.

3. Voer een naam in voor het opslagaccount.

4. Selecteer **Resource Manager** als het implementatiemodel.

5. Selecteer **Blob Storage** als het type opslagaccount.

6. Selecteer de toegangslaag: **Hot** of **Cool**. **Hot** is de standaardinstelling.

7. Selecteer de replicatieoptie voor het opslagaccount: **LRS**, **GRS** of **RA-GRS**. **RA-GRS** is de standaardinstelling. Zie [Azure Storage-replicatie](storage-redundancy.md) voor meer informatie over Azure Storage-replicatieopties.

8. Selecteer het abonnement waarin u het nieuwe opslagaccount wilt maken.

9. Geef een nieuwe resourcegroep op of selecteer een bestaande resourcegroep. Zie [De Azure Portal gebruiken om Azure-resources te beheren](../azure-portal/resource-group-portal.md) voor meer informatie over resourcegroepen.

10. Selecteer de geografische locatie voor het opslagaccount.

11. Klik op **Maken** om het opslagaccount te maken.

#### De toegangslaag voor een Blob Storage-account wijzigen via de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw opslagaccount.

2. Klik op **Alle instellingen** en vervolgens op **Configuratie** om de accountconfiguratie te bekijken en/of te wijzigen.

3. Geef de gewenste toegangslaag op: **Hot** of **Cool**.

    > [AZURE.NOTE] Aan het wijzigen van de toegangslaag kunnen extra kosten zijn verbonden. Zie de sectie [Prijzen en facturering](storage-blob-storage-tiers.md#pricing-and-billing) voor meer informatie.

## Migreren naar Blob Storage-accounts

Het doel van deze sectie is om gebruikers op soepele wijze te helpen migreren naar Blob Storage-accounts. Een Blob Storage-account is speciaal bedoeld voor het opslaan van blok-blobs en toevoeg-blobs. Bestaande opslagaccounts voor algemeen gebruik, waarin u naast blobs ook tabellen, wachtrijen, bestanden en schijven kunt opslaan, kunnen niet worden geconverteerd naar Blob Storage-accounts. Als u gebruik wilt maken van de toegangslagen, maakt u nieuwe Blob Storage-accounts en migreert u de bestaande gegevens naar deze nieuwe accounts.

### De migratie van bestaande gegevens plannen

Als u de gegevens verplaatst naar een Blob Storage-account, wilt u waarschijnlijk gebruikmaken van de Cool Storage-toegangslaag en zo besparen op de opslagkosten voor gegevens die minder regelmatig worden gebruikt. De eerste stap bij het plannen van de migratie van gegevens naar een Blob Storage-account in de Cool Storage-toegangslaag is het evalueren van uw bestaande gebruikspatroon. Op deze manier kunt u bepalen of migreren naar een Blob Storage-account voordelig voor u is. Doorgaans zijn de volgende gegevens hiervoor van belang:

- Wat is het gebruikspatroon voor de opslag? Hoeveel gegevens worden er opgeslagen en hoe wijzigt dit maandelijks?
- Wat zijn de toegangspatronen voor de opslag? Op hoeveel gegevens worden er lees- en/of schrijfbewerkingen uitgevoerd (inclusief nieuwe gegevens?) Hoeveel en welke transacties worden er gebruikt voor gegevenstoegang?

Zie [Metrische gegevens in Azure Storage inschakelen en weergeven](storage-enable-and-view-metrics.md) om uw bestaande opslagaccounts te controleren en deze gegevens te verzamelen. Met behulp van deze gegevens kunt u nu de [Azure Storage-prijscalculator](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) gebruiken om de kosten te berekenen.

### Bestaande gegevens migreren

U kunt de volgende methoden gebruiken om bestaande gegevens vanaf on-premises opslagapparaten, van cloudopslagproviders van derden of vanuit bestaande opslagaccounts voor algemeen gebruik in Azure te migreren naar Blob Storage-accounts:

#### AzCopy

AzCopy is een Windows-opdrachtregelprogramma dat is  ontworpen voor het high-performance kopiëren van gegevens van en naar Azure Storage. U kunt AzCopy gebruiken om gegevens uit bestaande opslagaccounts voor algemeen gebruik te kopiëren naar een Blob Storage-account en om gegevens van on-premises opslagaccounts te uploaden naar een Blob Storage-account.

Zie [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md) voor meer informatie.

#### Bibliotheek voor gegevensverplaatsing

De Azure Storage-bibliotheek voor gegevensverplaatsing voor .NET is gebaseerd op het basisframework voor gegevensverplaatsing van AzCopy. De bibliotheek is ontworpen voor high-performance, betrouwbare en eenvoudige gegevensoverdracht vergelijkbaar met AzCopy. Hierdoor kunt u in uw toepassing op systeemeigen wijze maximaal profiteren van de functies die AzCopy biedt, zonder dat u externe exemplaren van AzCopy hoeft uit te voeren of te controleren.

Zie [Bibliotheek voor gegevensverplaatsing van Azure Storage voor .Net](https://github.com/Azure/azure-storage-net-data-movement) voor meer informatie.

#### REST-API of clientbibliotheek

U kunt een aangepaste toepassing maken om gegevens naar een Blob Storage-account te migreren met behulp van een van de Azure-clientbibliotheken of de REST API voor Azure Storage-services. Azure Storage biedt uitgebreide clientbibliotheken voor meerdere talen en platforms, zoals  .NET, Java, C++, Node.JS, PHP, Ruby en Python. De clientbibliotheken bieden geavanceerde mogelijkheden, zoals pogingslogica, logboekregistratie en parallelle uploads. U kunt ook rechtstreeks met de REST API ontwikkelen. Deze kan worden aangeroepen in elke taal waarin HTTP-/HTTPS-verzoeken kunnen worden gemaakt.

Zie [Aan de slag met Azure Blob Storage](storage-dotnet-how-to-use-blobs.md) voor meer informatie.

> [AZURE.NOTE] Blobs die aan de clientzijde zijn versleuteld, bevatten versleutelingsgerelateerde metagegevens die samen met de blob zijn opgeslagen. Het is absoluut essentieel dat de metagegevens in de blob (en dan met name de versleutelingsgerelateerde metagegevens) bij het kopiëren behouden blijven. Als u de blobs kopieert zonder deze metagegevens, kan de inhoud van de blob niet meer worden opgehaald. Zie [Azure Storage-versleuteling aan de clientzijde](storage-client-side-encryption.md) voor meer informatie over versleutelingsgerelateerde metagegevens.

## Veelgestelde vragen

1. **Zijn de bestaande opslagaccounts nog steeds beschikbaar?**

    Ja, de bestaande opslagaccounts zijn nog steeds beschikbaar. De prijs en de functionaliteit hiervan zijn niet gewijzigd.  U kunt voor deze opslagaccounts echter geen toegangslagen kiezen. Dit zal in de toekomst niet veranderen.

2. **Waarom en wanneer is het een goed idee om Blob Storage-accounts te gebruiken?**

    Blob Storage-accounts zijn helemaal gericht op het opslaan van blobs en maken het introduceren van nieuwe blobfuncties mogelijk. Het is een goed idee om Blob Storage-accounts te gebruiken voor het opslaan van blobs, omdat er in de toekomst nieuwe mogelijkheden (bijvoorbeeld hiërarchische opslag en lagen) worden geïntroduceerd in dit accounttype. Op basis van uw bedrijfsvereisten bepaalt u echter helemaal zelf wanneer u uw gegevens migreert.

3. **Kan ik mijn bestaande opslagaccount converteren naar een Blob Storage-account?**

    Nee. Een Blob Storage-account is een ander soort opslagaccount. Daarom moet u dit account afzonderlijk maken en dient u de gegevens naar dit account te migreren zoals hierboven wordt uitgelegd.

4. **Kan ik objecten opslaan in beide toegangslagen van hetzelfde account?**

    Toegangslagen worden op accountniveau ingesteld en zijn van toepassing op alle objecten in het account. U kunt een toegangslaag niet instellen op objectniveau.

5. **Kan ik de toegangslaag in mijn Blob Storage-account wijzigen?**

    Ja. U kunt de toegangslaag in het opslagaccount wijzigen. Als u de toegangslaag op accountniveau wijzigt, is dit van toepassing op alle objecten die in het account zijn opgeslagen. Als u de toegangslaag wijzigt van Hot in Cool, worden er geen kosten in rekening gebracht. Als u echter de toegangslaag wijzigt van Cool in Hot, worden er kosten berekend per GB voor het lezen van alle gegevens in het account.

6. **Hoe vaak kan ik de toegangslaag in mijn Blob Storage-account wijzigen?**

    Let op: hoewel er geen beperking bestaat voor het aantal malen dat u de toegangslaag kunt wijzigen, worden er voor het wijzigen van de toegangslaag van Cool in Hot aanzienlijke kosten in rekening gebracht. We raden u daarom aan de toegangslaag niet te vaak te wijzigen.

7. **Gedragen blobs in de Cool Storage-toegangslaag zich anders dan blobs in de Hot Storage-toegangslaag?**

    Blobs in de Hot Storage-toegangslaag hebben dezelfde latentie als blobs in opslagaccounts voor algemeen gebruik. Blobs in de Cool Storage-toegangslaag hebben een gelijksoortige latentie (in milliseconden) als blobs in opslagaccounts voor algemeen gebruik.

    Blobs in de Cool Storage-toegangslaag hebben een lagere SLA (Service Level Availability) dan blobs die zijn opgeslagen in de Hot Storage-toegangslaag. Zie [SLA voor opslag](https://azure.microsoft.com/support/legal/sla/storage) voor meer informatie.

8. **Kan ik pagina-blobs en virtuele-machineschijven opslaan in Blob Storage-accounts?**

    Blob Storage-accounts ondersteunen alleen blok-blobs en toevoeg-blobs. Pagina-blobs worden niet ondersteund. Omdat virtuele machines van Azure gebruikmaken van pagina-blobs, kunnen virtuele-machineschijven niet worden opgeslagen in Blob Storage-accounts. Het is echter wel mogelijk om back-ups van de virtuele-machineschijven als blok-blobs op te slaan in een Blob Storage-account.

9. **Moet ik mijn bestaande toepassingen wijzigen om Blob Storage-accounts te kunnen gebruiken?**

    Blob Storage-accounts zijn voor 100 procent API-consistent met opslagaccounts voor algemeen gebruik voor blok- en toevoeg-blobs. Zolang uw toepassing gebruikmaakt van blok-blobs of toevoeg-blobs en u versie 2014-02-14 van de [REST API voor Storage Services](https://msdn.microsoft.com/library/azure/dd894041.aspx) gebruikt, zal uw toepassing geen problemen ondervinden. Als u een oudere versie van het protocol gebruikt, moet u uw toepassing bijwerken voor gebruik van de nieuwe versie, zodat deze naadloos werkt met beide typen opslagaccounts. Over het algemeen is het bijna altijd het beste om de meest recente versie te gebruiken, ongeacht het type opslagaccount dat u gebruikt.

10. **Is de gebruikerservaring gewijzigd?**

    Blob Storage-accounts zijn vergelijkbaar met opslagaccounts voor algemeen gebruik voor het opslaan van blok- en toevoeg-blobs. Hierbij maken ze maximaal gebruik van de kracht van Azure Storage op het gebied van duurzaamheid, beschikbaarheid, schaalbaarheid, prestaties en beveiliging. Afgezien van de hierboven beschreven functionaliteit en beperkingen die specifiek zijn voor Blob Storage-accounts en de bijbehorende toegangslagen, verandert er voor u niets.

## Volgende stappen

### Blob Storage-accounts evalueren

[Beschikbaarheid van Blob Storage-accounts per regio controleren](https://azure.microsoft.com/regions/#services)

[Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](storage-enable-and-view-metrics.md)

[Prijzen voor Blob Storage per regio controleren](https://azure.microsoft.com/pricing/details/storage/)

[Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)

### Blob Storage-accounts gebruiken

[Aan de slag met Azure Blob Storage](storage-dotnet-how-to-use-blobs.md)

[Gegevens verplaatsen naar en uit Azure Storage](storage-moving-data.md)

[Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)



<!--HONumber=Jun16_HO2-->


