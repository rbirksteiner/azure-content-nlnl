<properties
   pageTitle="Azure Traffic Manager-profielen beheren | Microsoft Azure"
   description="In dit artikel wordt uitgelegd hoe u de geschiedenis van een Azure Traffic Manager-profiel maakt, uitschakelt, inschakelt, verwijdert en weergeeft."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Een Azure Traffic Manager-profiel beheren

U gebruikt een Traffic Manager-profiel om de cloudservices of website-eindpunten op te geven die moeten worden bewaakt door Traffic Manager en om aan te geven welke verkeersrouteringsmethode u wilt gebruiken om de verbindingen naar deze eindpunten te distribueren.

## Een Traffic Manager-profiel maken met Snelle invoer

U kunt in de klassieke Azure-portal met Snelle invoer snel een Traffic Manager-profiel maken. Met Snelle invoer kunt u profielen met standaardconfiguratie-instellingen maken. U kunt Snelle invoer echter niet gebruiken voor instellingen als de set eindpunten (cloudservices en websites), de failovervolgorde, de routeringsmethode voor het failoververkeer en bewakingsinstellingen. Zodra u het profiel hebt gemaakt, kunt u deze instellingen configureren in de klassieke Azure-portal. Traffic Manager biedt ondersteuning voor maximaal 200 eindpunten per profiel. Voor de meeste gebruiksscenario's zijn echter slechts een beperkt aantal eindpunten vereist. 

### Een nieuw Traffic Manager-profiel maken

1. **Implementeer uw cloudservices en websites naar uw productieomgeving.** Zie [Cloudservices](http://go.microsoft.com/fwlink/p/?LinkId=314074) voor meer informatie over cloudservices. Zie [Aanbevolen procedures](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile) voor meer informatie over cloudservices. Zie [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327) voor meer informatie over websites.

2. **Meld u aan bij de klassieke Azure-portal.** Als u een nieuw Traffic Manager-profiel wilt maken, klikt linksonder in de portal op **Nieuw**. Vervolgens klikt u op **Network Services > Traffic Manager** en op **Snelle invoer** om uw profiel te configureren.
3. **Configureer het DNS-voorvoegsel.** Geef voor het DNS-voorvoegsel van uw Traffic Manager-profiel een unieke naam op. U kunt alleen het voorvoegsel voor een Traffic Manager-domeinnaam opgeven.
4. **Selecteer het abonnement.** Selecteer het juiste Azure-abonnement. Elk profiel is gekoppeld aan één abonnement. Als u slechts één abonnement hebt, wordt deze optie niet weergegeven.
5. **Selecteer de verkeersrouteringsmethode.** Selecteer bij **Traffic Routing Policy** (Beleid voor verkeersroutering) de verkeersrouteringsmethode. Zie [Over de verkeersrouteringsmethoden voor Traffic Manager](traffic-manager-routing-methods.md) voor meer informatie over verkeersrouteringsmethoden.
6. **Klik op Maken om het nieuwe profiel te maken**. Zodra het profiel is geconfigureerd, kunt u in het deelvenster Traffic Manager van de klassieke Azure-portal zoeken uw profiel zoeken.
7. **Configureer eindpunten, de bewaking en andere instellingen in de klassieke Azure-portal.** Omdat u met Snelle invoer alleen basisinstellingen kunt configureren, moet u aanvullende instellingen configureren, zoals de lijst met eindpunten en de failovervolgorde voor eindpunten, om de gewenste configuratie te voltooien. 


## Een profiel uitschakelen, inschakelen of verwijderen

U kunt een bestaand Traffic Manager-profiel uitschakelen zodat het profiel geen gebruikersaanvragen naar de geconfigureerde eindpunten voor het profiel verwijst. Wanneer u een Traffic Manager-profiel uitschakelt, blijven het profiel zelf en de informatie in het profiel intact en kunt u het profiel en de inhoud bewerken in de Traffic Manager-interface. Als u het profiel opnieuw wilt inschakelen, kunt u eenvoudig doen in de klassieke Azure-portal en worden de verwijzingen hervat. Wanneer u in de klassieke Azure-portal een Traffic Manager-profiel maakt, wordt het profiel automatisch ingeschakeld. Als u een profiel niet meer nodig hebt, kunt u het verwijderen.

### Een profiel uitschakelen

1. Wijzig de DNS-resourcerecord op uw internet-DNS-server om het juiste recordtype en de juiste aanwijzer naar een andere naam of het IP-adres van een specifieke locatie op internet te gebruiken. Met andere woorden, wijzig de DNS-resourcerecord op uw Internet DNS-server zodat deze geen CNAME-resourcerecord meer gebruikt die naar de domeinnaam van uw Traffic Manager-profiel wijst.
2. Het verkeer wordt niet meer via de Traffic Manager-profielinstellingen omgeleid naar de eindpunten.
3. Selecteer het profiel dat u wilt uitschakelen. Als u het profiel wilt selecteren, markeert u het profiel op de pagina Traffic Manager door op de kolom naast de profielnaam te klikken. Klik niet op de naam van het profiel of de pijl naast de naam, aangezien u dan wordt omgeleid naar de instellingenpagina voor het profiel.
4. Nadat u het profiel hebt geselecteerd, klikt u onder aan de pagina op **Uitschakelen**.

### Een profiel inschakelen

1. Selecteer het profiel dat u wilt inschakelen. Als u het profiel wilt selecteren, markeert u het profiel op de pagina Traffic Manager door op de kolom naast de profielnaam te klikken. Klik niet op de naam van het profiel of de pijl naast de naam, aangezien u dan wordt omgeleid naar de instellingenpagina voor het profiel.
2. Nadat u het profiel hebt geselecteerd, klikt u onder aan de pagina op **Inschakelen**.
3. Wijzig de DNS-resourcerecord op uw Internet DNS-server zodanig dat er geen gebruik meer wordt gemaakt van het CNAME-recordtype, dat de domeinnaam van uw bedrijf koppelt aan de domeinnaam van uw Traffic Manager-profiel. Zie [Het internetdomein van een bedrijf naar een Traffic Manager-domein laten wijzen](traffic-manager-point-internet-domain.md) voor meer informatie.
4. Het verkeer wordt weer omgeleid naar de eindpunten.

### Een profiel verwijderen

1. Zorg ervoor dat de DNS-resourcerecord op uw Internet DNS-server geen CNAME-resourcerecord meer gebruikt die naar de domeinnaam van uw Traffic Manager-profiel wijst.
2. Selecteer het profiel dat u wilt verwijderen. Als u het profiel wilt selecteren, markeert u het profiel op de pagina Traffic Manager door op de kolom naast het profiel te klikken. Klik niet op de naam van het profiel of de pijl naast de naam, aangezien u dan wordt omgeleid naar de instellingenpagina voor het profiel.
4. Nadat u het profiel hebt geselecteerd, klikt u onder aan de pagina op **Verwijderen**.

## De wijzigingsgeschiedenis van uw Traffic Manager-profiel weergeven

U kunt de wijzigingsgeschiedenis voor uw Traffic Manager-profiel in de klassieke Azure-portal bekijken in Management Services.

### De wijzigingsgeschiedenis van uw Traffic Manager weergeven

1. Klik in het linkerdeelvenster van de klassieke Azure-portal op **Beheerservices**.
2. Klik op de pagina Beheerservices op **Bewerkingslogboeken**.
3. U kunt op de pagina Bewerkingslogboeken een filter toepassen om de wijzigingsgeschiedenis voor uw Traffic Manager-profiel weer te geven. Nadat u de filteropties hebt geselecteerd, klikt u op het vinkje om de resultaten weer te geven.
   - Als u de profielwijzigingen voor al uw profielen wilt weergeven, selecteert u uw abonnement en de gewenste periode. Vervolgens selecteert u in het snelmenu **Type** de optie **Traffic Manager**.
   - Als u wilt filteren op de profielnaam, typt u de naam van het profiel in het veld **Servicenaam** of selecteert u de naam in het snelmenu.
   - Als u de details voor elke afzonderlijke wijziging wilt weergeven, selecteert u de rij met de wijziging die u wilt weergeven en klikt u onder aan de pagina op **Details**. In het venster **Bewerkingsdetails** kunt u de XML-weergave van het API-object bekijken dat is gemaakt of bijgewerkt als onderdeel van de bewerking en kunt u de XML-code naar het klembord kopiëren.


## Volgende stappen

[Een eindpunt toevoegen](traffic-manager-endpoints.md)

[Routeringsmethode voor failover configureren](traffic-manager-configure-failover-routing-method.md)

[Routeringsmethode voor round robin configureren](traffic-manager-configure-round-robin-routing-method.md)

[Routeringsmethode voor prestaties configureren](traffic-manager-configure-performance-routing-method.md)

[Problemen met de gedegradeerde status van Traffic Manager oplossen](traffic-manager-troubleshooting-degraded.md)


<!--HONumber=Jun16_HO2-->


