<properties
    pageTitle="Aan de slag met Mobile Apps met behulp van Xamarin.Forms"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Apps voor Xamarin.Forms-ontwikkeling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="glenga"/>

#Een Xamarin.Forms-app maken

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Overzicht

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een mobiele Xamarin.Forms-app met een back-end voor Azure Mobile Apps. U maakt zowel een nieuwe back-end voor Mobile Apps als een eenvoudige Xamarin.Forms-app voor _takenlijsten_ die app-gegevens opslaat in Azure.

Het voltooien van deze zelfstudie is een vereiste voor alle andere zelfstudies over Mobile Apps voor Xamarin.Forms.

##Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal 10 gratis Mobile Apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

* Visual Studio met Xamarin. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) voor instructies. 

* Een Mac met Xcode v7.0 of hoger en waarop Xamarin Studio Community is geïnstalleerd. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) en [Setup, install, and verifications for Mac users](https://msdn.microsoft.com/library/mt488770.aspx) (Instructies voor installatie, configuratie en verificatie voor Mac-gebruikers) (MSDN).
 
>[AZURE.NOTE] Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://tryappservice.azure.com/?appServiceName=mobile). Hier kunt u direct een tijdelijke en eenvoudige Mobile App maken in App Service. U hebt geen creditcard nodig en u doet geen toezeggingen.

## Een nieuwe back-end voor Azure Mobile Apps maken

Volg deze stappen voor het maken van een nieuwe back-end voor Mobile Apps.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


U hebt nu een back-end voor Azure Mobile Apps ingericht, die kan worden gebruikt door uw mobiele clienttoepassingen. Nu gaat u een serverproject downloaden voor een eenvoudige back-end voor takenlijsten en deze publiceren naar Azure.

## Het serverproject configureren

Volg onderstaande stappen voor het configureren van het serverproject voor het gebruik van de Node.js- of .NET-back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (Optioneel) Uw back-endproject lokaal testen

Als u hierboven een .NET-back-endconfiguratie hebt gekozen, kunt u eventueel de back-end lokaal testen.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]


##De Xamarin.Forms-oplossing downloaden en uitvoeren

U hebt hier een aantal opties. U kunt de oplossing downloaden naar een Mac en openen in Xamarin Studio of u kunt de oplossing downloaden naar een Windows-computer en openen in Visual Studio met een Mac in een netwerk voor het bouwen van de iOS-app. Zie [Setup and install for Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Installeren en instellen voor Visual Studio en Xamarin) als u meer gedetailleerde instructies voor de Xamarin-installatiescenario's nodig hebt.

Laten we beginnen:

 1. Op uw Mac of Windows-computer opent u [Azure Portal] in een browservenster.
 2. Klik op de blade Instellingen voor uw mobiele app op **Aan de slag** (onder Mobiel) > **Xamarin.Forms**. Klik in stap 3 op  **Een nieuwe app maken** als deze optie nog niet is geselecteerd.  Klik vervolgens op de knop **Downloaden**.

    Er wordt een project gedownload dat een clienttoepassing bevat die is verbonden met uw mobiele app. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

 3. Pak het project uit dat u hebt gedownload en open het in Xamarin Studio of Visual Studio.

    ![][9]

    ![][8]


##(Optioneel) Het iOS-project uitvoeren

Deze sectie gaat over het uitvoeren van het Xamarin iOS-project voor iOS-apparaten. Als u niet met iOS-apparaten werkt, kunt u deze sectie overslaan.

####In Xamarin Studio

1. Klik met de rechtermuisknop op het iOS-project en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Uitvoeren** op **Foutopsporing starten** om het project te bouwen en de app te starten in de iPhone-emulator.

####In Visual Studio
1. Klik met de rechtermuisknop op het iOS-project en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Bouwen** op **Configuration Manager**.
3. In het dialoogvenster **Configuration Manager** schakelt u de selectievakjes **Bouwen** en **Implementeren** van het iOS-project in.
4. Druk op de toets **F5** om het project te bouwen en de app te starten in de iPhone-emulator.

    >[AZURE.NOTE] Als u problemen ondervindt bij het bouwen, voert u NuGet Package Manager uit en voert u een update uit naar de nieuwste versie van de Xamarin-ondersteuningspakketten. Soms kunnen de Quickstart-projecten achterblijven bij updates naar de nieuwste versie.    

Typ in de app zinvolle tekst, zoals _Xamarin leren kennen_, en klik vervolgens op de knop **+**.

![][10]

Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. Gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

>[AZURE.NOTE] U vindt de code die toegang geeft tot de back-end voor mobiele apps, in het TodoItemManager.cs C#-bestand van het 'portable class library'-project van uw oplossing.

##(Optioneel) Het Android-project uitvoeren

Deze sectie gaat over het uitvoeren van het Xamarin Droid-project voor Android-apparaten. Als u niet met Android-apparaten werkt, kunt u deze sectie overslaan.

####In Xamarin Studio

1. Klik met de rechtermuisknop op het Android-project en klik vervolgens op **Instellen als opstartproject**.
2. Klik in het menu **Uitvoeren** op **Foutopsporing starten** om het project te bouwen en de app te starten in een Android-emulator.

####In Visual Studio
1. Klik met de rechtermuisknop op het Android-project (Droid) en klik vervolgens op **Instellen als opstartproject**.
4. Klik in het menu **Bouwen** op **Configuration Manager**.
5. In het dialoogvenster **Configuration Manager** schakelt u de selectievakjes **Bouwen** en **Implementeren** van het Android-project in.
6. Druk op de toets **F5** om het project te bouwen en de apps te starten in een Android-emulator.

    >[AZURE.NOTE] Als u problemen ondervindt bij het bouwen, voert u NuGet Package Manager uit en voert u een update uit naar de nieuwste versie van de Xamarin-ondersteuningspakketten. Soms kunnen de Quickstart-projecten achterblijven bij updates naar de nieuwste versie.    


Typ in de app zinvolle tekst, zoals _Xamarin leren kennen_, en klik vervolgens op de knop **+**.

![][11]

Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. Gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

> [AZURE.NOTE] U vindt de code die toegang geeft tot de back-end voor mobiele apps, in het TodoItemManager.cs C#-bestand van het 'portable class library'-project van uw oplossing.


##(Optioneel) Het Windows-project uitvoeren


Deze sectie gaat over het uitvoeren van het project Xamarin WinApp voor Windows-apparaten. Als u niet met Windows-apparaten werkt, kunt u deze sectie overslaan.


####In Visual Studio
1. Klik met de rechtermuisknop op een van de Windows-projecten en klik vervolgens op **Instellen als opstartproject**.
4. Klik in het menu **Bouwen** op **Configuration Manager**.
5. In het dialoogvenster **Configuration Manager** schakelt u de selectievakjes **Bouwen** en **Implementeren** in van het Windows-project dat u hebt gekozen.
6. Druk op de toets **F5** om het project te bouwen en de apps te starten in een Windows-emulator.

    >[AZURE.NOTE] Als u problemen ondervindt bij het bouwen, voert u NuGet Package Manager uit en voert u een update uit naar de nieuwste versie van de Xamarin-ondersteuningspakketten. Soms kunnen de Quickstart-projecten achterblijven bij updates naar de nieuwste versie.    


Typ in de app zinvolle tekst, zoals _Xamarin leren kennen_, en klik vervolgens op de knop **+**.

Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. Gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens worden weergegeven in de lijst.

![][12]

> [AZURE.NOTE] U vindt de code die toegang geeft tot de back-end voor mobiele apps, in het TodoItemManager.cs C#-bestand van het 'portable class library'-project van uw oplossing.

##Volgende stappen

* [Verificatie toevoegen aan uw app ](app-service-mobile-xamarin-forms-get-started-users.md)  
Informatie over het verifiëren van gebruikers van uw app met een id-provider.
* [Pushmeldingen toevoegen aan uw Xamarin.Android-app](app-service-mobile-xamarin-forms-get-started-push.md)  
Informatie over het toevoegen van pushmeldingen aan uw app.
* [De beheerde client gebruiken voor mobiele apps van Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Informatie over het werken met de SDK voor beheerde clients in uw  Xamarin-app. 


<!-- Anchors. -->
[Aan de slag met back-ends voor mobiele apps]:#getting-started
[Een nieuwe back-end voor mobiele apps maken]:#create-new-service
[Volgende stappen]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK voor mobiele apps]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/




<!--HONumber=Jun16_HO2-->


