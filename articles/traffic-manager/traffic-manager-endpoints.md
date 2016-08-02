<properties
   pageTitle="Eindpunten beheren in Azure Traffic Manager | Microsoft Azure"
   description="Dit artikel helpt u bij het toevoegen, verwijderen, inschakelen en uitschakelen van eindpunten vanuit Azure Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Eindpunten toevoegen, uitschakelen, inschakelen of verwijderen

De functie Web Apps in Azure App Service biedt al failover- en round-robinverkeersroutering voor websites in een datacenter, ongeacht de websitemodus. Met Azure Traffic Manager kunt u failover- en round-robinverkeersroutering opgeven voor websites en cloudservices in verschillende datacenters. Het eerste dat u moet doen om deze functionaliteit te gebruiken, is het cloudservice- of website-eindpunt toevoegen aan Traffic Manager.

>[AZURE.NOTE] Met de klassieke Azure Portal kunt u geen externe locaties of Traffic Manager-profielen als eindpunten toevoegen. U moet gebruikmaken van [Create Definition](http://go.microsoft.com/fwlink/p/?LinkId=400772) in REST-API of van [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774) in Windows PowerShell.

U kunt ook afzonderlijke eindpunten uitschakelen die deel uitmaken van een Traffic Manager-profiel. Eindpunten zijn zowel cloudservices als websites. Als u een eindpunt uitschakelt, blijft het onderdeel van het profiel, maar het profiel gedraagt zich alsof het eindpunt er niet in is opgenomen. Deze actie is erg handig als u een eindpunt dat in de onderhoudsmodus staat of opnieuw wordt geïmplementeerd, tijdelijk wilt verwijderen. Zodra het eindpunt opnieuw actief is, kan het worden ingeschakeld.

>[AZURE.NOTE] Het uitschakelen van een eindpunt heeft geen invloed op de implementatiestatus in Azure. Een eindpunt met een goede status blijft actief en is in staat om verkeer te verwerken, zelfs als het in Traffic Manager is uitgeschakeld. Bovendien heeft het uitschakelen van een eindpunt in een bepaald profiel geen invloed op de status ervan in een ander profiel.

## Een cloudservice- of website-eindpunt toevoegen


1. Zoek in het deelvenster Traffic Manager in de klassieke Azure Portal het Traffic Manager-profiel met de eindpuntinstellingen die u wilt wijzigen. Klik vervolgens op de pijl rechts van de profielnaam. Hiermee opent u de instellingenpagina voor het profiel.
2. Klik boven aan de pagina op **Eindpunten** om de eindpunten weer te geven die al deel uitmaken van uw configuratie.
3. Klik onder aan de pagina op **Toevoegen** om naar de pagina **Service-eindpunten toevoegen** te gaan. De cloudservices worden op deze pagina standaard onder **Service-eindpunten** weergegeven.
4. Als het cloudservices betreft, selecteert u de cloudservices in de lijst om ze in te schakelen als eindpunten voor dit profiel. Als u de naam van de cloudservice wist, wordt deze verwijderd uit de lijst met eindpunten.
5. Als het websites betreft, klikt u op de vervolgkeuzelijst **Servicetype** en selecteert u vervolgens **Web-app**.
6. Selecteer de websites in de lijst om ze toe te voegen als eindpunten voor dit profiel. Als u de naam van de website wist, wordt deze verwijderd uit de lijst met eindpunten. Houd er rekening mee dat u slechts één website per Azure-datacenter (ook wel bekend als een regio) kunt selecteren. Als u een website selecteert in een datacenter dat als host fungeert voor meerdere websites, kunt u de andere websites in hetzelfde datacenter niet meer selecteren nadat u de eerste website hebt geselecteerd. Houd er ook rekening mee dat alleen standaardwebsites worden weergegeven.
7. Nadat u de eindpunten voor dit profiel hebt geselecteerd, klikt u op het vinkje in de rechterbenedenhoek om uw wijzigingen op te slaan.

>[AZURE.NOTE] Als u de methode *Failover* voor verkeersroutering gebruikt en een eindpunt toevoegt of verwijdert, moet u de failover-prioriteitenlijst op de configuratiepagina zodanig aanpassen dat de gewenste failover-volgorde wordt gebruikt. Zie voor meer informatie [Configure Failover traffic routing](traffic-manager-configure-failover-routing-method.md) (Failover-verkeersroutering configureren).

## Een eindpunt uitschakelen

1. Zoek in het deelvenster Traffic Manager in de klassieke Azure Portal het Traffic Manager-profiel met de eindpuntinstellingen die u wilt wijzigen. Klik vervolgens op de pijl rechts van de profielnaam. Hiermee opent u de instellingenpagina voor het profiel.
2. Klik boven aan de pagina op **Eindpunten** om de eindpunten weer te geven die deel uitmaken van uw configuratie.
3. Klik op het eindpunt dat u wilt uitschakelen en klik vervolgens onder aan de pagina op **Uitschakelen**.
4. Er wordt geen verkeer meer naar het eindpunt geleid op basis van de DNS Time-to-Live (TTL) die voor de Traffic Manager-domeinnaam is geconfigureerd. U kunt de TTL wijzigen via de pagina Configuratie van het Traffic Manager-profiel.

## Een eindpunt inschakelen

1. Zoek in het deelvenster Traffic Manager in de klassieke Azure Portal het Traffic Manager-profiel met de eindpuntinstellingen die u wilt wijzigen. Klik vervolgens op de pijl rechts van de profielnaam. Hiermee opent u de instellingenpagina voor het profiel.
2. Klik boven aan de pagina op **Eindpunten** om de eindpunten weer te geven die deel uitmaken van uw configuratie.
3. Klik op het eindpunt dat u wilt inschakelen en klik vervolgens onder aan de pagina op **Inschakelen**.
4. Er wordt weer verkeer naar de service geleid op basis van de instellingen van het profiel.

## Een cloudservice- of website-eindpunt verwijderen


1. Zoek in het deelvenster Traffic Manager in de klassieke Azure Portal het Traffic Manager-profiel met de eindpuntinstellingen die u wilt wijzigen. Klik vervolgens op de pijl rechts van de profielnaam. Hiermee opent u de instellingenpagina voor het profiel.
2. Klik boven aan de pagina op **Eindpunten** om de eindpunten weer te geven die al deel uitmaken van uw configuratie.
3. Klik op de pagina Eindpunten op de naam van het eindpunt dat u uit het profiel wilt verwijderen.
4. Klik onder aan de pagina op **Verwijderen**.

>[AZURE.NOTE] Met de klassieke Azure Portal kunt u geen externe locaties of Traffic Manager-profielen als eindpunten verwijderen. U moet Windows PowerShell gebruiken. Zie voor meer informatie [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Volgende stappen


[Methode voor failover-routering configureren](traffic-manager-configure-failover-routing-method.md)

[Methode voor round-robinroutering configureren](traffic-manager-configure-round-robin-routing-method.md)

[Methode voor prestatieroutering configureren](traffic-manager-configure-performance-routing-method.md)

[Problemen met Traffic Manager in gedegradeerde status oplossen](traffic-manager-troubleshooting-degraded.md)

[Bewerkingen op Traffic Manager (REST API-referentiemateriaal)](http://go.microsoft.com/fwlink/p/?LinkID=313584)



<!--HONumber=Jun16_HO2-->


