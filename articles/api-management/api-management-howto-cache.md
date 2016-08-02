<properties
    pageTitle="Opslaan in cache toevoegen om de prestaties in Azure API Management te verbeteren | Microsoft Azure"
    description="Informatie over het verbeteren van de latentie, het bandbreedteverbruik en de webservicewerklast voor API Management-serviceaanroepen."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="sdanie"/>

# Opslaan in cache toevoegen om de prestaties in Azure API Management te verbeteren

Bewerkingen in API Management kunnen worden geconfigureerd voor het opslaan van antwoorden in de cache. Door het opslaan van antwoorden in de cache kan de API-latentie, het bandbreedteverbruik en de webservicewerklast aanzienlijk worden verminderd voor gegevens die niet vaak wijzigen.

In deze handleiding wordt getoond hoe u het opslaan van antwoorden in de cache toevoegt voor uw API en beleidsregels configureert voor de bewerkingen voor de voorbeeld-Echo-API. U kunt de bewerking vervolgens aanroepen vanuit de ontwikkelaarsportal om te controleren of opslaan in de cache werkt.

>[AZURE.NOTE] Zie [Aangepast opslaan in cache in Azure API Management](api-management-sample-cache-by-key.md) voor informatie over het opslaan van items in de cache per sleutel met behulp van beleidsexpressies.

## Vereisten

Voordat u de stappen in deze handleiding volgt, moet u een service-exemplaar van API Management hebben waarvoor een API en een product zijn geconfigureerd. Als u nog geen service-exemplaar van API Management hebt gemaakt, raadpleegt u [Service-exemplaar van API Management maken][] in de zelfstudie [Aan de slag met Azure API Management][].

## <a name="configure-caching"> </a>Een bewerking voor opslaan in cache configureren

In deze stap controleert u de cache-instellingen van de bewerking **GET Resource (in cache)** van de voorbeeld-Echo-API.

>[AZURE.NOTE] Elk service-exemplaar van API Management wordt al geconfigureerd geleverd met een Echo-API die kan worden gebruikt om te experimenteren met API Management en hier meer over te leren. Zie [Aan de slag met Azure API Management][] voor meer informatie.

Klik om aan de slag te gaan op **Beheren** in de klassieke Azure-portal voor uw API Management-service. Hiermee gaat u naar de publicatieportal van API Management.

![Publicatieportal][api-management-management-console]

Klik op **API's** in het menu **API Management** aan de linkerkant en klik vervolgens op **Echo-API**.

![Echo-API][api-management-echo-api]

Klik op het tabblad **Bewerkingen** en klik vervolgens op de bewerking **GET Resource (in cache)** in de lijst **Bewerkingen**.

![Bewerkingen Echo-API][api-management-echo-api-operations]

Klik op het tabblad **Opslaan in cache** om de cache-instellingen voor deze bewerking te bekijken.

![Tabblad Opslaan in cache][api-management-caching-tab]

Als u opslaan in cache wilt inschakelen voor een bewerking, schakelt u het selectievakje **Inschakelen** in. In dit voorbeeld is opslaan in cache ingeschakeld.

Elk bewerkingsantwoord heeft een sleutel, op basis van de waarden in de velden **Variëren op queryreeksparameters** en **Variëren op headers**. Als u meerdere antwoorden in de cache wilt opslaan op basis van queryreeksparameters of headers, kunt u deze configureren in deze twee velden.

Met **Duur** wordt het vervalinterval opgegeven van de antwoorden in de cache. In dit voorbeeld is het interval **3600** seconden, dat gelijk is aan één uur.

Met de cacheconfiguratie in dit voorbeeld wordt met de eerste aanvraag voor de bewerking **GET Resource (in cache)** een antwoord geretourneerd van de back-endservice. Dit antwoord wordt in de cache opgeslagen, met een sleutel per de opgegeven headers en querytekenreeksparameters. Voor volgende aanroepen voor de bewerking, met overeenkomende parameters, wordt het antwoord geretourneerd dat in de cache is opgeslagen, tot het cacheduurinterval is verlopen.

## <a name="caching-policies"> </a>De cachebeleidsregels controleren

In deze stap controleert u de cache-instellingen voor de bewerking **GET Resource (in cache)** van de voorbeeld-Echo-API.

Wanneer er cache-instellingen zijn geconfigureerd voor een bewerking op het tabblad **Opslaan in cache**, worden cachebeleidsregels toegevoegd voor de bewerking. Deze beleidsregels kunnen worden bekeken en bewerkt in de beleidseditor.

Klik op **Beleidsregels** in het menu **API Management** menu aan de linkerkant en selecteer vervolgens **Echo-API / GET Resource (in cache)** in de vervolgkeuzelijst **Bewerking**.

![Bewerking beleidsbereik][api-management-operation-dropdown]

Hiermee worden de beleidsregels voor deze bewerking in de beleidseditor weergegeven.

![Beleidseditor API Management][api-management-policy-editor]

De beleidsdefinitie voor deze bewerking bevat de beleidsregels waarmee de cacheconfiguratie wordt gedefinieerd die is gecontroleerd met het tabblad **Opslaan in cache** in de vorige stap.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Wijzigingen die via de beleidseditor in de cachebeleidsregels worden aangebracht, worden weergegeven op het tabblad **Opslaan in cache** van een bewerking en vice versa.

## <a name="test-operation"> </a>Een bewerking aanroepen en het opslaan in de cache testen

Als u het opslaan in de cache in werking wilt zien, kunnen we de bewerking aanroepen vanuit de ontwikkelaarsportal. Klik op **ontwikkelaarsportal** in het menu rechtsboven.

![Ontwikkelaarsportal][api-management-developer-portal-menu]

Klik op **API's** in het bovenste menu en selecteer **Echo-API**.

![Echo-API][api-management-apis-echo-api]

>Als er slechts één API is geconfigureerd of zichtbaar is voor uw account, gaat u wanneer u op API's klikt rechtstreeks naar de bewerkingen voor die API.

Selecteer de bewerking **GET Resource (in cache)** en klik op **Console openen**.

![Console openen][api-management-open-console]

Met de console kunt u bewerkingen rechtstreeks vanuit de ontwikkelaarsportal aanroepen.

![Console][api-management-console]

Behoud de standaardwaarden voor **param1** en **param2**.

Selecteer de gewenste sleutel in de vervolgkeuzelijst **subscription-key**. Als uw account slechts één abonnement heeft, is de sleutel al geselecteerd.

Voer **sampleheader:value1** in het tekstvak **Aanvraagheaders** in.

Klik op **HTTP Get** en noteer de antwoordheaders.

Voer **sampleheader:value2** in het tekstvak **Aanvraagheaders** in en klik vervolgens op **HTTP Get**.

Merk op dat de waarde van **sampleheader** nog steeds **value1** in het antwoord is. Probeer een aantal verschillende waarden en merk op dat het antwoord in de cache van de eerste aanroep wordt geretourneerd.

Voer **25** in het veld **param2** in en klik vervolgens op **HTTP Get**.

Merk op dat de waarde van **sampleheader** in het antwoord nu **value2** is. Omdat de bewerkingsresultaten een sleutel per querytekenreeks hebben, is het vorige antwoord in de cache niet geretourneerd.

## <a name="next-steps"> </a>Volgende stappen

-   Bekijk de andere onderwerpen in de zelfstudie [Aan de slag met geavanceerde API-configuratie][].
-   Zie [Cachebeleidsregels][] in [Naslaginformatie over beleid voor API Management][] voor meer informatie over cachebeleidsregels.
-   Zie [Aangepast opslaan in cache in Azure API Management](api-management-sample-cache-by-key.md) voor informatie over het opslaan van items in de cache per sleutel met behulp van beleidsexpressies.

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[Bewerkingen toevoegen aan een API]: api-management-howto-add-operations.md
[Een product toevoegen en publiceren]: api-management-howto-add-products.md
[Bewaking en analytische gegevens]: api-management-monitoring.md
[API's toevoegen aan een product]: api-management-howto-add-products.md#add-apis
[Een product publiceren]: api-management-howto-add-products.md#publish-product
[Aan de slag met Azure API Management]: api-management-get-started.md
[Aan de slag met geavanceerde API-configuratie]: api-management-get-started-advanced.md

[Naslaginformatie over beleid voor API Management]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Cachebeleidsregels]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Service-exemplaar van API Management maken]: api-management-get-started.md#create-service-instance

[Een bewerking voor opslaan in cache configureren]: #configure-caching
[De cachebeleidsregels controleren]: #caching-policies
[Een bewerking aanroepen en het opslaan in de cache testen]: #test-operation
[Volgende stappen]: #next-steps



<!--HONumber=Jun16_HO2-->


