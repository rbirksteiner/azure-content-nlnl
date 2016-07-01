<properties
    pageTitle="Een Universal Windows-platform (UWP)-app maken in Azure App Service - Mobile Apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met back-ends voor mobiele apps van Azure voor Universal Windows Platform (UWP)-app-ontwikkeling in C#, Visual Basic of JavaScript."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="glenga"/>

#Een Windows-app maken

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Overzicht

Deze zelfstudie laat zien hoe u een back-endservice toevoegt aan een Universal Windows Platform (UWP)-app in de cloud. Zie [What are Mobile Apps](app-service-mobile-value-prop.md) (Wat zijn Mobile Apps?) voor meer informatie. Hier volgen enkele schermopnamen van een voltooide app:

![Voltooide bureaublad-app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Uitgevoerd op bureaublad. 

![Voltooide telefoon-app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Uitgevoerd op telefoon.

Het voltooien van deze zelfstudie is een vereiste voor alle andere zelfstudies over Mobile Apps voor UWP-apps. 

##Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal tien gratis mobiele apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

* [Visual Studio Community 2015] of een nieuwere versie.

>[AZURE.NOTE] Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://tryappservice.azure.com/?appServiceName=mobile). Daar kunt u direct een tijdelijke en eenvoudige mobiele app maken in App Service: u hebt geen creditcard nodig en u bent tot niets verplicht.

##Een nieuwe back-end voor mobiele apps van Azure maken

Volg deze stappen voor het maken van een nieuwe back-end voor mobiele apps.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

U hebt nu een back-end voor mobiele apps van Azure ingericht, die kan worden gebruikt door uw mobiele-clienttoepassingen. Nu gaat u een serverproject downloaden voor een eenvoudige back-end voor takenlijsten en deze publiceren naar Azure.

## Het serverproject configureren

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Het clientproject downloaden en uitvoeren

Zodra u de back-end voor mobiele apps hebt geconfigureerd, kunt u een nieuwe client-app maken of een bestaande app wijzigen om verbinding te maken met Azure. In deze sectie download u een sjabloonproject voor een UWP-app die is aangepast om verbinding te kunnen maken met de back-end voor uw mobiele apps.

1. Op de blade **Snel starten** voor de back-end voor uw mobile app klikt u op **Een nieuwe app maken** > **Downloaden**. Pak de gecomprimeerde projectbestanden uit op de lokale computer.

    ![Het project Windows-snelstartgids downloaden](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. Optioneel: Voeg het UWP-appproject toe aan dezelfde oplossing als het serverproject. Hierdoor kunt u desgewenst zowel de app als de back-end makkelijker debuggen en testen in dezelfde Visual-Studio-oplossing. Als u een UWP-app-project aan de oplossing wilt toevoegen, dient u Visual Studio 2015 of een nieuwere versie te gebruiken.

4. Met de UWP-app als opstartproject, drukt u op de F5-toets om de app te implementeren en uit te voeren.

5. Typ zinvolle tekst in de app, zoals *Voltooi de zelfstudie*, in het tekstvak **Nieuwe taak invoegen** en klik op **Opslaan**.

    ![Windows-snelstartgids: bureaublad voltooien](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure.

6. Optioneel: Stop de app en start deze opnieuw op een ander apparaat of mobiele emulator.

    ![Windows-snelstartgids: telefoon voltooien](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    De gegevens die in de vorige stap zijn opgeslagen, worden vanuit Azure geladen nadat de UWP-app is gestart. 

##Volgende stappen

* [Verificatie toevoegen aan uw app ](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* [Pushmeldingen toevoegen aan uw app](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Informatie over het toevoegen van ondersteuning van pushmeldingen aan uw app en het configureren van de backend voor mobiele apps voor gebruik van Azure Notification Hubs voor het verzenden van pushmeldingen.

* [Offlinesynchronisatie voor uw Windows-app inschakelen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Informatie over het toevoegen van offlineondersteuning aan uw app met een back-end voor mobiele apps. Met offlinesynchronisatie kunnen eindgebruikers interactie aangaan met een mobiele app&mdash;gegevens weergeven, toevoegen of wijzigen&mdash;ook als er geen netwerkverbinding is.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[SDK voor Mobile Apps]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Jun16_HO2-->


