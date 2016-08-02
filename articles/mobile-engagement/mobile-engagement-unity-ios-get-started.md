<properties
    pageTitle="Aan de slag met Azure Mobile Engagement voor Unity iOS-implementatie"
    description="Informatie over het gebruik van Azure Mobile Engagement met analyses en pushmeldingen voor implementaties van Unity-apps op iOS-apparaten."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager=""
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="03/25/2016"
    ms.author="piyushjo" />

# Aan de slag met Azure Mobile Engagement voor Unity iOS-implementatie

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In dit onderwerp leest u hoe u Azure Mobile Engagement gebruikt om inzicht te krijgen in het gebruik van uw apps, en om pushmeldingen te verzenden aan gesegmenteerde gebruikers van een Unity-toepassing bij implementatie op een iOS-apparaat.
Deze zelfstudie maakt gebruik van de bekende Unity-zelfstudie ‘Roll a Ball’ als uitgangspunt. Volg de stappen in deze [zelfstudie](mobile-engagement-unity-roll-a-ball.md) voordat u doorgaat met de Mobile Engagement-integratie die we in de onderstaande zelfstudie presenteren. 

Voor deze zelfstudie hebt u het volgende nodig:

+ [Unity Editor](http://unity3d.com/get-unity)
+ [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
+ XCode Editor

> [AZURE.NOTE] U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started) voor meer informatie.

##<a id="setup-azme"></a>Mobile Engagement instellen voor uw iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Uw app verbinden met de back-end van Mobile Engagement

###Het Unity-pakket importeren

1. Download het [Mobile Engagement Unity-pakket](https://aka.ms/azmeunitysdk) en sla het op uw lokale computer op. 

2. Ga naar **Assets -> Import Package -> Custom Package** en selecteer het pakket dat u in de vorige stap hebt gedownload. 

    ![][70] 

3. Zorg dat alle bestanden zijn geselecteerd en klik op de knop **Import**. 

    ![][71] 

4. Wanneer het importeren is voltooid, ziet u de geïmporteerde SDK-bestanden in het project.  

    ![][72] 

###De EngagementConfiguration bijwerken

1. Open het scriptbestand **EngagementConfiguration** in de SDK-map en werk **IOS\_CONNECTION\_STRING** bij met de verbindingsreeks die u eerder hebt verkregen van de Azure-portal.  

    ![][73]

2. Sla het bestand op. 

###De app voor eenvoudig bijhouden configureren

1. Open het script **PlayerController** dat is  gekoppeld aan het object Player om het te bewerken. 

2. Voeg het volgende toe met de instructie:

        using Microsoft.Azure.Engagement.Unity;

3. Het volgende toevoegen aan de methode `Start()`
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###De app implementeren en uitvoeren

1. Sluit een iOS-apparaat aan op de computer. 

2. Open **File -> Build Settings**. 

    ![][40]

3. Selecteer **iOS** en klik vervolgens op **Switch Platform**.

    ![][41]

    ![][42]

4. Klik op **Player settings** en geef een geldige bundel-id op. 

    ![][53]

5. Klik tot slot op **Build And Run**.

    ![][54]

6. U wordt mogelijk gevraagd een mapnaam op te geven voor het opslaan van het iOS-pakket. 

    ![][43]

7. Als alles goed gaat, wordt het project gecompileerd en moet u het kunnen openen in XCode. 

8. Zorg ervoor dat de **Bundel-id** juist is in het project.  

    ![][75]

10. Voer nu de app uit in XCode, zodat het pakket wordt geïmplementeerd op het aangesloten apparaat. Nu moet u de Unity-game zien op uw telefoon. 

##<a id="monitor"></a>App verbinden met realtime-bewaking

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Pushmeldingen en in-app-berichten inschakelen

Met Mobile Engagement kunt u communiceren met uw gebruikers en ze bereiken met pushmeldingen en in-app-berichten in de context van campagnes. Deze module heet REACH in de Mobile Engagement-portal.
U hoeft verder niets te configureren in uw app als deze is al ingesteld voor het ontvangen van meldingen.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png


<!--HONumber=Jun16_HO2-->


