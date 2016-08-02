## Wat zijn Service Bus-onderwerpen en -abonnementen?

Service Bus-onderwerpen en -abonnementen bieden ondersteuning voor een berichtencommunicatiemodel op basis van *publiceren/abonneren*. Wanneer u gebruikmaakt van onderwerpen en abonnementen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een onderwerp dat als intermediair fungeert.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Anders dan bij Service Bus-wachtrijen, waarin elk bericht door een enkele gebruiker wordt verwerkt, bieden onderwerpen en abonnementen een 'één-naar-veel'-communicatiewijze, waarbij een patroon voor publiceren/abonneren wordt gebruikt. Het is mogelijk meerdere abonnementen voor een onderwerp te registreren. Wanneer een bericht naar een onderwerp wordt verzonden, wordt dit vervolgens beschikbaar gemaakt voor elk abonnement, waar het vervolgens zelfstandig wordt afgehandeld/verwerkt.

Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën van de berichten ontvangen die naar het onderwerp zijn verzonden. U kunt eventueel per abonnement filterregels voor een onderwerp registreren, waardoor u met behulp van een filter of een beperking kunt bepalen welke berichten naar een onderwerp door welke onderwerpabonnementen worden ontvangen.

Service Bus-onderwerpen en -abonnementen stellen u in staat om voor veel gebruikers en toepassingen een groot aantal berichten te schalen en te verwerken.

## Een naamruimte maken

Als u Service Bus-onderwerpen en -abonnementen in Azure wilt gebruiken, moet u eerst een *servicenaamruimte maken*. Een naamruimte biedt een scopingcontainer voor het verwerken van Service Bus-resources in uw toepassing.

Ga als volgt te werk om een naamruimte te maken:

1.  Meld u aan bij de [klassieke Azure-portal][].

2.  Klik in het linkernavigatievenster van de portal op **Service Bus**.

3.  Klik in het onderste deelvenster van de portal op **Maken**.   
    ![][0]

4.  Voer in het dialoogvenster **Een nieuwe naamruimte toevoegen** een naamruimtenaam in. Er wordt onmiddellijk gecontroleerd of de naam beschikbaar is.   
    ![][2]

5.  Nadat u hebt gecontroleerd of de naam voor de naamruimte beschikbaar is, kiest u het land of de regio waarin uw naamruimte moet worden gehost. Zorg er daarbij voor dat u hetzelfde land of dezelfde regio gebruikt waarin u uw rekenresources implementeert.

    > [AZURE.IMPORTANT] Kies **dezelfde regio** die u van plan bent te kiezen voor het implementeren van uw toepassing. Dat geeft de beste prestaties.

6.  Laat de standaardwaarden van de andere velden in het dialoogvenster ongewijzigd (**Berichten**- en **Standard**-laag) en klik vervolgens op het vinkje voor OK. Uw naamruimte wordt nu gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.

    ![][6]

## De standaardbeheerreferenties voor de naamruimte ophalen

Als u beheerbewerkingen wilt uitvoeren, zoals het maken van een onderwerp of abonnement op de nieuwe naamruimte, moet u beheerreferenties voor de naamruimte ophalen. U kunt deze referenties ophalen via de portal.

### Beheerreferenties ophalen via de portal

1.  Klik in het linkernavigatievenster op het knooppunt **Service Bus** om de lijst met beschikbare naamruimten weer te geven:   
    ![][0]

2.  Selecteer de naamruimte die u zojuist hebt gemaakt in de lijst die wordt weergegeven:   
    ![][3]

3.  Klik op **Verbindingsgegevens**.   
    ![][4]

4.  Zoek in het dialoogvenster **Toegang tot verbindingsgegevens** de verbindingsreeks die de SAS-sleutel en de naam van de sleutel bevat. Noteer deze waarden, want u gaat deze informatie later gebruiken om bewerkingen met de naamruimte uit te voeren. 


  [klassieke Azure-portal]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png




<!--HONumber=Jun16_HO2-->


