<properties
    pageTitle="Overzicht van Service Bus Relayed Messaging | Microsoft Azure"
    description="Overzicht van Service Bus Relay."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="05/06/2016"
    ms.author="sethm"/>


# Service Bus Relayed Messaging

Het centrale onderdeel van Service Bus is een gecentraliseerde Relay-service (echter met maximale taakverdeling) waarmee u hybride toepassingen kunt ontwikkelen die zowel in een Azure-datacenter als in uw eigen on-premises bedrijfsomgeving kunnen worden uitgevoerd.  De Relay-service ondersteunt een groot aantal verschillende transportprotocollen en webservicestandaarden, waaronder SOAP, WS-* en zelfs REST. De Service Bus Relay vereenvoudigt het uitvoeren van uw hybride toepassingen doordat u WCF-services (Windows Communication Foundation) die zich in een bedrijfsnetwerk bevinden, veilig kunt blootstellen aan de openbare cloud zonder dat een firewallverbinding moet worden geopend of wijzigingen in de infrastructuur van een bedrijfsnetwerk vereist zijn. 

![Relay-concepten](./media/service-bus-relay-overview/sb-relay-01.png)

De Relay-service ondersteunt traditionele berichten in één richting, aanvraag-/antwoordberichten en peer-to-peerberichten. De service ondersteunt tevens gebeurtenisdistributie via internet voor scenario's voor publiceren/abonneren en bidirectionele socket-communicatie voor verbeterde point-to-point-efficiëntie. 

In het Relayed Messaging-patroon maakt een on-premises service verbinding met de Relay-service via een uitgaande poort en wordt een bidirectionele socket voor communicatie gemaakt die is gekoppeld aan een bepaald rendezvous-adres. De client kan vervolgens met de on-premises service communiceren door berichten te verzenden naar de Relay-service die gericht is op het rendezvous-adres. De Relay-service stuurt vervolgens berichten door ('relay') naar de on-premises service via de reeds aanwezige bidirectionele socket. De client heeft geen rechtstreekse verbinding met de on-premises service nodig en hoeft niet te weten waar de service zich bevindt. Voor de on-premises service is niet vereist dat poorten voor inkomend verkeer zijn geopend in de firewall.

U start de verbinding tussen uw on-premises service en de Relay-service met een reeks WCF 'Relay'-bindingen. Achter de schermen worden de Relay-bindingen toegewezen aan nieuwe transportbindingselementen die zijn ontworpen om WCF-kanaalonderdelen te maken die kunnen worden geïntegreerd met de Service Bus in de cloud. 

## Volgende stappen

Zie de volgende onderwerpen voor meer informatie over de Service Bus Relay.

- [Overzicht van Azure Service Bus-architectuur](service-bus-fundamentals-hybrid-solutions.md)
- [De Service Bus Relay-service gebruiken](service-bus-dotnet-how-to-use-relay.md)

 


<!--HONumber=Jun16_HO2-->


