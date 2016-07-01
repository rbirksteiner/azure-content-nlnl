<properties
    pageTitle="Aan de slag met Azure Mobile Apps voor Xamarin.Android-apps"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Apps voor Xamarin.Android-ontwikkeling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="glenga" />

#Een Xamarin.Android-app maken

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Overzicht

Deze zelfstudie laat zien hoe u een back-endservice toevoegt aan een Xamarin.Android-app in de cloud. Zie [What are Mobile Apps](app-service-mobile-value-prop.md) (Wat zijn Mobile Apps?) voor meer informatie.

Hieronder staat een schermafbeelding van de voltooide app:

![][0]

Het voltooien van deze zelfstudie is een vereiste voor alle andere Mobile Apps-zelfstudies voor Xamarin.Android-apps.

##Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure en maximaal tien gratis mobiele apps krijgen die u ook na de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

* Visual Studio met Xamarin. Zie [Installeren en instellen voor Visual Studio en Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) voor instructies.  
 
>[AZURE.NOTE] Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](https://tryappservice.azure.com/?appServiceName=mobile). Hier kunt u direct een tijdelijke en eenvoudige mobiele app maken in App Service. U hebt geen creditcard nodig en u doet geen toezeggingen.

## Een nieuwe back-end voor mobiele apps van Azure maken

Volg deze stappen voor het maken van een nieuwe back-end voor mobiele apps.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

U hebt nu een back-end voor mobiele apps van Azure ingericht, die kan worden gebruikt door uw mobiele-clienttoepassingen. Nu gaat u een serverproject downloaden voor een eenvoudige back-end voor takenlijsten en deze publiceren naar Azure.

## Het serverproject configureren

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## De Xamarin.Android-app downloaden en uitvoeren

1. Klik onder **Het Xamarin.Android-project downloaden en uitvoeren** op de knop **Downloaden**.

    Er wordt een project gedownload dat een clienttoepassing bevat die is verbonden met uw mobiele app. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

2. Druk op de toets **F5** om het project te bouwen en de app te starten.

3. Typ zinvolle tekst in de app, zoals _Voltooi de zelfstudie_, en klik vervolgens op de knop **Toevoegen**.

    ![][10]

    Hierdoor wordt een POST-aanvraag verzonden naar de nieuwe back-end voor mobiele apps die wordt gehost in Azure. Gegevens van de aanvraag worden opgenomen in de takentabel. Items die zijn opgeslagen in de tabel, worden geretourneerd door de back-end voor mobiele apps en de gegevens verschijnen in de lijst.

    > [AZURE.NOTE] U kunt de code die toegang heeft tot de back-end van uw mobiele app bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het C#-bestand ToDoActivity.cs.

##Volgende stappen

* [Verificatie toevoegen aan uw app ](app-service-mobile-xamarin-android-get-started-users.md)  
Ontdek hoe u gebruikers van uw app verifieert met een id-provider.
* [Pushmeldingen toevoegen aan uw Xamarin.Android-app](app-service-mobile-xamarin-android-get-started-push.md)  
Informatie over het toevoegen van pushmeldingen aan uw app.
* [De beheerde client gebruiken voor Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
Informatie over het werken met SDK voor beheerde clients in uw Xamarin-app. 


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Jun16_HO2-->


