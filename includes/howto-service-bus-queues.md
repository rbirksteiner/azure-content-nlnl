## Wat zijn Service Bus-wachtrijen?

Service Bus-wachtrijen ondersteunen een **Brokered Messaging**-communicatiemodel. Wanneer u gebruikmaakt van wachtrijen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een wachtrij die als intermediaire service (broker) fungeert. De maker van een bericht (afzender) draagt een bericht over aan de wachtrij en gaat vervolgens door met de verwerking ervan. Een gebruiker van een bericht (ontvanger) haalt het bericht asynchroon op uit de wachtrij en verwerkt het. De maker hoeft niet te wachten op een reactie van de gebruiker om door te kunnen gaan met het verwerken en verzenden van verdere berichten. Wachtrijen maken gebruik van het **FIFO-principe (first in, first out)** voor de berichtbezorging naar een of meer concurrerende gebruikers. Dat wil zeggen dat berichten doorgaans door de ontvangers worden ontvangen en verwerkt in de volgorde waarin ze zijn toegevoegd aan de wachtrij, en elk bericht wordt slechts door één berichtengebruiker ontvangen en verwerkt.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-wachtrijen is een technologie voor algemeen gebruik die voor een groot aantal verschillende scenario's kan worden gebruikt:

-   Communicatie tussen web- en werkrollen in een meerlaagse Azure-toepassing.
-   Communicatie tussen on-premises apps en in Azure gehoste apps in een hybride oplossing.
-   Communicatie tussen onderdelen van een gedistribueerde toepassing die on-premises wordt uitgevoerd in verschillende organisaties of afdelingen binnen een organisatie.

Door wachtrijen te gebruiken, kunt u uw toepassingen eenvoudiger schalen en kunt u meer tolerantie aan uw architectuur toevoegen.

## Een servicenaamruimte maken

Als u Service Bus-wachtrijen in Azure wilt gebruiken, moet u eerst een servicenaamruimte maken. Een naamruimte biedt een scopingcontainer voor het verwerken van Service Bus-resources in uw toepassing.

Ga als volgt te werk om een naamruimte te maken:

1.  Meld u aan bij de [klassieke Azure-portal][].

2.  Klik in het linkernavigatievenster van de portal op **Service Bus**.

3.  Klik in het onderste deelvenster van de portal op **Maken**.
    ![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Voer in het dialoogvenster **Een nieuwe naamruimte toevoegen** een naamruimtenaam in. Er wordt onmiddellijk gecontroleerd of de naam beschikbaar is.   
    ![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Nadat u hebt gecontroleerd of de naam voor de naamruimte beschikbaar is, kiest u het land of de regio waarin uw naamruimte moet worden gehost. Zorg er daarbij voor dat u hetzelfde land of dezelfde regio gebruikt waarin u uw rekenresources implementeert.

     > [AZURE.IMPORTANT] Kies **dezelfde regio** die u van plan bent te kiezen voor het implementeren van uw toepassing. Dat geeft de beste prestaties.

6.  Laat de standaardwaarden van de andere velden in het dialoogvenster ongewijzigd (**Berichten**- en **Standard**-laag) en klik vervolgens op het vinkje voor OK. Uw naamruimte wordt nu gemaakt en ingeschakeld. U moet wellicht enkele minuten wachten terwijl de resources voor uw account worden ingericht.

    ![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

Het duurt even voordat de naamruimte die u hebt gemaakt, wordt geactiveerd. Daarna wordt de naamruimte in de portal weergegeven. Wacht totdat de status van de naamruimte **Actief** is voordat u doorgaat.

## De standaardbeheerreferenties voor de naamruimte ophalen

Als u beheerbewerkingen wilt uitvoeren, zoals het maken van een wachtrij op de nieuwe naamruimte, moet u beheerreferenties voor de naamruimte ophalen. U kunt deze referenties ophalen via de [klassieke Azure-portal][].

###Beheerreferenties ophalen via de portal

1.  Klik in het linkernavigatievenster op het knooppunt **Service Bus** om de lijst met beschikbare naamruimten weer te geven:   
    ![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Selecteer de naamruimte die u zojuist hebt gemaakt in de lijst die wordt weergegeven:   
    ![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Klik op **Verbindingsgegevens**.   
    ![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Zoek in het deelvenster **Toegang tot verbindingsgegevens** de verbindingsreeks die de SAS-sleutel en de naam van de sleutel bevat.   

    ![](./media/howto-service-bus-queues/multi-web-45.png)
    
5.  Noteer de sleutel of kopieer de sleutel naar het Klembord.

  [klassieke Azure-portal]: http://manage.windowsazure.com




<!--HONumber=Jun16_HO2-->


