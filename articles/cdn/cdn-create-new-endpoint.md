<properties
     pageTitle="Azure CDN gebruiken"
     description="In dit onderwerp leert u hoe u het Content Delivery Network (CDN) voor Azure inschakelt. In deze zelfstudie wordt uitgelegd hoe u een nieuw CDN-profiel en -eindpunt maakt."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="05/24/2016" 
     ms.author="casoper"/>

# Azure CDN gebruiken  

In dit onderwerp wordt uitgelegd hoe u Azure CDN inschakelt door een nieuw CDN-profiel en -eindpunt te maken.

>[AZURE.IMPORTANT] Zie [Overzicht van CDN](./cdn-overview.md) voor een inleiding tot de werking van CDN en een lijst met functies.

## Nieuwe CDN-profielen maken

Een CDN-profiel is een verzameling van CDN-eindpunten.  Elk profiel bevat een of meer CDN-eindpunten.  Mogelijk wilt meerdere profielen gebruiken om de CDN-eindpunten te ordenen op basis van het internetdomein, de webtoepassing of andere criteria.

> [AZURE.NOTE] Eén Azure-abonnement is standaard beperkt tot acht CDN-profielen. Elk CDN-profiel is beperkt tot tien CDN-eindpunten.
>
> De prijzen van CDN worden standaard toegepast op het niveau van het CDN-profiel. Als u verschillende Azure CDN-prijscategorieën wilt gebruiken, hebt u meerdere CDN-profielen nodig.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Nieuwe CDN-eindpunten maken

**Een nieuw CDN-eindpunt maken**

1. Navigeer in [Azure Portal](https://portal.azure.com) naar uw CDN-profiel.  Mogelijk hebt u het profiel in de vorige stap vastgemaakt aan het dashboard.  Als dit niet het geval is, kunt u het dashboard zoeken door op **Bladeren** en vervolgens **CDN-profielen** te klikken en op het profiel te klikken die u aan het eindpunt wilt toevoegen.

    De blade CDN-profiel wordt weergegeven

    ![CDN-profiel][cdn-profile-settings]

2. Klik op de knop **Eindpunt toevoegen**.

    ![De knop Eindpunt toevoegen][cdn-new-endpoint-button]

    De blade **Een eindpunt toevoegen** wordt weergegeven.

    ![De blade Een eindpunt toevoegen][cdn-add-endpoint]

3. Voer een **naam** voor dit CDN-eindpunt.  Deze naam wordt gebruikt voor toegang tot uw resources in de cache in het domein `<endpointname>.azureedge.net`.

4. Selecteer in de vervolgkeuzelijst **Oorsprongtype** het type oorsprong.  Selecteer **Storage** voor een Azure Storage-account, **Cloudservice** voor een Azure Cloud-service, **Web App** voor een Azure-webtoepassing of **Aangepaste oorsprong** voor een andere openbaar toegankelijke webserveroorsprong (gehost in Azure of ergens anders).

    ![Oorsprongtype CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Selecteer of typ in de vervolgkeuzelijst **Hostnaam van oorsprong** uw brondomein.  De vervolgkeuzelijst bevat alle beschikbare oorsprongen van het type dat u hebt opgegeven in stap 4.  Als u voor *Oorsprongtype* de optie **Aangepaste oorsprong** hebt geselecteerd, voert u het domein in van uw aangepaste oorsprong.

6. In het tekstvak **Oorsprongspad** voert u het pad in naar de resources die u wilt opslaan in de cache. U kunt het vak ook leeg laten zodat alle resource in het domein dat u in stap 5 hebt opgegeven kunnen worden opgeslagen in de cache.

7. Geef in het vak **Host-header van oorsprong** de host-header op die voor elke aanvraag door het CDN moet worden verzonden of laat de standaardinstelling staan.

    > [AZURE.WARNING] Bepaalde oorsprongtypen, zoals Azure Storage en Web Apps, vereisen dat de hostheader overeenkomt met het domein van de oorsprong. Tenzij u een oorsprong hebt waarvoor een andere host-header is vereist dan die van het domein, laat u de standaardwaarde ongewijzigd.

8. Geef voor **Protocol** en **Poort van oorsprong** de protocollen en poorten op die worden gebruikt voor toegang tot uw resources die aan de oorsprong liggen.  Er moet minimaal één protocol (HTTP of HTTPS) worden geselecteerd.
    
    > [AZURE.NOTE] De **Poort van oorsprong** is alleen van invloed op de poort die door het eindpunt wordt gebruikt om informatie op te halen bij de oorsprong.  Het eindpunt zelf is alleen beschikbaar voor eindclients op de standaard-HTTP- en -HTTPS-poorten (80 en 443), ongeacht de **Poort van oorsprong**.  
    >
    > Eindpunten van **Azure CDN van Akamai** staan niet het volledige TCP-poortbereik voor oorsprongen toe.  Zie [Informatie over het gedrag van Azure CDN van Akamai](cdn-akamai-behavior-details.md) voor een volledige lijst met oorsprongspoorten die niet zijn toegestaan.  
    >
    > Voor de toegang tot CDN-inhoud via HTTPS gelden de volgende beperkingen:
    > 
    > - U moet het SSL-certificaat gebruiken dat door het CDN is verstrekt. Certificaten van derden worden niet ondersteund.
    > - Voor toegang tot HTTPS-inhoud moet u het domein gebruiken dat is verstrekt door het CDN (`<endpointname>.azureedge.net`). HTTPS-ondersteuning is niet beschikbaar voor aangepaste domeinnamen (CNAME's), aangezien het CDN op dit moment geen ondersteuning biedt voor aangepaste certificaten.

9. Klik op de knop **Toevoegen** om het nieuwe eindpunt te maken.

10. Zodra het eindpunt is gemaakt, wordt deze weergegeven in een lijst met eindpunten voor het profiel. In de lijstweergave kunt u zien welke URL u moet gebruiken voor toegang tot de content in cache en het brondomein.

    ![CDN-eindpunt][cdn-endpoint-success]

    > [AZURE.IMPORTANT] Het eindpunt is niet onmiddellijk beschikbaar voor gebruik, aangezien het enige tijd vergt om de registratie door te geven in CDN.  Profielen van <b>Azure CDN van Akamai</b> worden doorgaans binnen één minuut doorgegeven.  Profielen van <b>Azure CDN van Verizon</b> worden doorgaans binnen 90 minuten doorgegeven, maar in sommige gevallen kan dit langer duren.
    >    
    > Gebruikers die de CDN-domeinnaam proberen te gebruiken voordat de eindpuntconfiguratie is doorgegeven aan de POP's, ontvangen HTTP 404-responscodes.  Zie [Problemen oplossen met CDN-eindpunten die 404-statusberichten retourneren](cdn-troubleshoot-endpoint.md) als u een paar uur nadat u uw eindpunt hebt gemaakt, nog steeds 404-responsberichten ontvangt.


##Zie ook
- [Het cachegedrag van aanvragen beheren met queryreeksen](cdn-query-string.md)
- [CDN-inhoud toewijzen aan een aangepast domein](cdn-map-content-to-custom-domain.md)
- [Vooraf assets op een Azure CDN-eindpunt laden](cdn-preload-endpoint.md)
- [Een Azure CDN-eindpunt leegmaken](cdn-purge-endpoint.md)
- [Problemen oplossen voor CDN-eindpunten die 404-statusberichten retourneren](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png



<!--HONumber=Jun16_HO2-->


